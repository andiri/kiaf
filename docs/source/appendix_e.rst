runScript.sh
========================

.. code-block:: bash

  #!/bin/bash
  source $HOME/.alice_env/alice_env.conf
  if [ -z $ALIPHYSICS_VERSION ] ; then
    root_version="AliRoot::${ALICE_ROOT_VERSION}"
  else
    root_version="AliPhysics::${ALIPHYSICS_VERSION}"
  fi
  
  njob=$(( ($(cat $data_file |wc -l) + $nfile -1 ) / $nfile ))

  if [ $njob -gt 99999 ] ; then
  echo "Plese split job less than 100000"; exit
  fi
  
  mkdir ${work_dir} ${work_dir}/logs ${work_dir}/macro ${work_dir}/out
  
  cp /pool/macro/job_wrap.sh /pool/macro/multi_merge.sh /pool/macro/nomerge.sh /pool/macro/validation.sh ${work_dir}/macro/.
  
  tr_out_files=‘echo $out_file | sed -e ’s/ //g’ | sed -e ’s/,/ /g’‘
  out_file_array=(""$tr_out_files"")
  nout_files=${#out_file_array[*]}
  if [ ! -n "$USER_MAIL" ] ; then
  USER_MAIL=${USER}@sdfarm.kr
  fi
  
  for (( l=0 ; l<nout_files ; l++ )) ; do
  cat <<EOF > ${work_dir}/macro/merge$l.C
  void merge$l(){

  TFileMerger m$l;
  
  m$l.OutputFile("${main_dir}/merge${l}_${work_dir}_${out_file_array[$l]}");
  EOF 
  done
  
  cat <<EOF > ${work_dir}/macro/run_jds
  JobBatchName            = ${work_dir}_\$(JOB_NUM)
  Universe                = vanilla
  Executable              = ${work_dir}/macro/job_wrap.sh
  Log                     = ${work_dir}/logs/\$(JOB_NUM).log
  Output                  = ${work_dir}/logs/\$(JOB_NUM).out
  Error                   = ${work_dir}/logs/\$(JOB_NUM).error

  transfer_input_files    = ${run_macro},${input_files},\$(ListFile)
  should_transfer_files   = YES
  when_to_transfer_output = ON_EXIT_OR_EVICT
  #transfer_output_files  = $out_file
  arguments               = "${root_version} ${run_macro} \$Fnx(ListFile)"
  output_destination = file://${main_dir}/\$Fp(ListFile)
  Queue
  EOF

  split --numeric-suffixes=1 -a  5 -l $nfile $data_file ${work_dir}/macro/list.

  for k in $(seq -f "%05g" 1  $njob); do
  mkdir ${work_dir}/out/$k
  cat ${work_dir}/macro/list.$k | sed -e ’s/\//\%/g’ |sed -e
    ’s/\%xrootdfs\%alice/\/data\/01\/\%data\%xrdnamespace/g’ > ${work_dir}/out/$k/list.$k
  rm ${work_dir}/macro/list.$k

  cat <<EOF >> ${work_dir}/macro/MAIN.spl
  Job $k ${work_dir}/macro/run_jds
  VARS $k JOB_NUM="$k" ListFile="${work_dir}/out/$k/list.$k"
  SCRIPT POST $k ${work_dir}/macro/validation.sh ${main_dir} ${work_dir} $k
  EOF

  for (( l=0 ; l<nout_files ; l++ )) ; do
  cat <<EOF >> ${work_dir}/macro/merge$l.C
  m$l.AddFile("${main_dir}/${work_dir}/out/$k/${out_file_array[$l]}");
  EOF 
  done
  done
  
  cat <<EOF > ${work_dir}/macro/condor.dag
  SPLICE MAIN ${work_dir}/macro/MAIN.spl
  Job MERGE ${work_dir}/macro/merge.jdl
  SCRIPT POST MERGE ${work_dir}/macro/validation.sh ${main_dir} ${work_dir}
  PARENT MAIN CHILD MERGE
  EOF
  
  for (( l=0 ; l<nout_files ; l++ )) ; do
  cat <<EOF >> ${work_dir}/macro/merge$l.C
  m${l}.Merge();
  } 
  EOF
  done
  
  if [ $merge -eq 1 ] ; then
        merge_file=multi_merge.sh
  else
        merge_file=nomerge.sh
  fi
  cat <<EOF > ${work_dir}/macro/merge.jdl
  Universe                = vanilla
  Executable              = ${work_dir}/macro/$merge_file
  Log                     = ${work_dir}/logs/merge.log
  Output                  = ${work_dir}/logs/merge.out
  Error                   = ${work_dir}/logs/merge.error

  transfer_input_files    = ${work_dir}/macro/merge\$(process).C
  should_transfer_files   = YES
  transfer_output_files   = ""
  arguments               = "${root_version} \$(process)"
  Queue $nout_files
  EOF
  echo -e "\n$USER_SCRIPT" >> ${work_dir}/macro/$merge_file
  #condor_submit -append "Accounting_Group=group_alice" -append "notify_user=$USER_MAIL"
    ${work_dir}/macro/run_jds
  condor_submit_dag -force -notification Always -append "Accounting_Group=group_alice" -append
    "notify_user=$USER_MAIL" ${work_dir}/macro/condor.dag
