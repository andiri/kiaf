HTCondor 작업명세파일 예제
=======================================

.. _condor_script:

Condor 수행 스크립트
----------------------------

KIAF는 사용자의 편의성을 위해 일부 스크립트를 작성하여 제공하고 있습니다. 
몇가지 기본설정을 통해 Grid와 유사하게 작업을 제출하는 스크립트에 대해 알아보겠습니다. 
Local job을 Condor job으로 수행 시키는 스크립트는 */tutorial* 디렉토리 아래에 *runCondor.sh* 라는 파일명으로 위치 합니다. 
해당 스크립트는 분석에 필요한 사용자 변수만을 설정 하고 설정된 변수 값을 통해 */pool/macro/runScript.sh* 을 수행 합니다. 
*runScript.sh* 파일의 내용은 :ref:`appendix_e` 에 나와 있습니다. 

.. note::

  해당 파일의 내용은 버그 등의 문제가 있을시 바로 수정 되기 때문에 실제 파일과 일부 다를 수 있습니다.  

*runCondor.sh* 파일의 내용은 아래와 같습니다.

.. code-block:: console

  #!/bin/bash
  condor_slot=40
  nfile=2
  merge=1 #0=nomerge, 1=merge
  
  work_dir=‘date +%y%m%d%H%M%S‘
  run_macro="runAnalysis.C"
  data_file="sim_LHC13d3_195675_AOD159.txt"
  input_files="AddMyTask.C,AliAnalysisTaskMyTask.cxx,AliAnalysisTaskMyTask.h"
  out_file="AnalysisResults.root"
  USER_MAIL=""
  
  main_dir=‘pwd‘
  
  source /pool/macro/runScript.sh
  
  
해당 스크립트의 내용은 분석 예제에 동일하게 적용 되어 있으며, 사용자 분석 작업에도 사용됩니다. 
스크립트의 각 변수의 의미와 값 수정을 통해 사용자 작업을 수행 하는 방법에 대해서는 :ref:`user_analysis` 에서 알아보겠습니다.

분석 예제
------------------

분석 예제 파일은 */pool/tutorial* 아래에 있습니다. 
*batch_test* 폴더를 복사하거나, *batch_test.tar* 파일을 복사 한뒤 압축을 풀어 사용하면 됩니다.

.. code-block:: console

  cp -r /tutorial/batch_test $HOME/batch_test
  
또는

.. code-block:: console

  cp -r /tutorial/batch_test.tar $HOME/batch_test.tar
  tar -xvf batch_test.tar

*batch_test* 폴더 안의 파일 리스트는 아래와 같습니다.

.. code-block:: console

  [계정명@<kiafi_url> batch_test]$ ls -l 
   합계 28
  -rw-r--r--. 1 계정명 그룹명  776 1월 16 13:37 AddMyTask.C
  -rw-r--r--. 1 계정명 그룹명 2036 1월 16 13:37 AliAnalysisTaskMyTask.cxx
  -rw-r--r--. 1 계정명 그룹명  895 1월 16 13:37 AliAnalysisTaskMyTask.h 
  -rw-r--r--. 1 계정명 그룹명 1120 1월 16 13:37 runAnalysis.C
  -rwxr--r--. 1 계정명 그룹명  378 1월 16 13:37 runCondor.sh
  -rw-r--r--. 1 계정명 그룹명 5325 1월 16 13:37 sim_LHC13d3_195675_AOD159.txt

위 리스트에서 확인 하였듯이 *batch_test* 폴더 안에는 분석에 사용되는 여러 코드와 배치 잡을 실행 시켜주기 위한 매크로 파일이 있습니다. 
이 리스트 중에 AliRoot에서 실행되는 매크로는 *runAnalysis.C* 파일이며, *AddMyTask.C* , *AliAnalysisTaskMyTask.cxx* , *AliAnalysisTaskMyTask.h* 은 *runAnalysis.C* 와 함께 분석에 사용 되는 파일입니다. 
*sim_LHC13d3_195675_AOD159.txt* 은 분석할 데이터의 위치 리스트를 가지고 있는 파일입니다. :ref:`condor_script` 에서 살펴 보았듯이 *runCondor.sh* 은 HTCondor 잡을 수행하기 위한 스크립트 이며, 실제 잡을 실행 시키기 위해서는 *runCondor.sh* 파일을 실행 시키면 됩니다. 
이 분석 예제는 특별한 수정 없이 바로 실행이 가능하며 특별한 에러없이 실행됩니다.
**이때 *runCondor.sh* 는 *runAnalysis.C* 의 인풋 파일을 작게 나누어 병렬 계산을 도와주는 스크립트 입니다. 즉, *runAnalysis.C* 매크로 자체의 에러가 발생한다면 *runCondor.sh* 를 실행할 때에도 에러가 발생하니 local test를 먼저 수행하시길 권장합니다.**
``source runCondor.sh`` 또는 ``./runCondor.sh`` 명령을 통해 스크립트를 실행하면 아래와 같은 결과를 터미널을 통해 볼 수 있습니다.

.. code-block:: console

  [계정명@<kiaf_url> batch_test]$ ./runCondor.sh
  
  Renaming rescue DAGs newer than number 0
  -----------------------------------------------------------------------
  File for submitting this DAG to HTCondor         : 200116135123/macro/condor.dag.condor.sub
  Log of DAGMan debugging messages                 : 200116135123/macro/condor.dag.dagman.out
  Log of HTCondor library output                   : 200116135123/macro/condor.dag.lib.out
  Log of HTCondor library error messages           : 200116135123/macro/condor.dag.lib.err
  Log of the life of condor_dagman itself          : 200116135123/macro/condor.dag.dagman.log
  
  Submitting job(s).
  1 job(s) submitted to cluster 878014.
  -----------------------------------------------------------------------

이때 만들어진 *200116135123* 라는 이름의 폴더는 잡을 수행시킨 년월일시분초의 이름로 만들어진 폴더이며, 워킹 디렉토리로 사용됩니다. 
워킹디렉토리 아래에는 숫자로 이루어진 세부 작업 디렉토리가 있습니다. 
나눠진 세부 작업의 갯수대로 생성된 디렉토리에는 각 작업의 인풋 리스트와 결과 파일 등이 남아 있습니다. 
각 잡의 로그는 워킹디렉토리 아래 *logs* 폴더에 위치합니다. 또한 *macro* 폴더에는 dagman의 로그, 각 잡에 사용된 macro및 스크립트 등이 있습니다. 
최종 결과 파일은 *batch_test* 폴더 안에 *merge0_200116135123_AnalysisResults.root* 라는 이름으로 생성 됩니다.

.. node::

  아웃풋 갯수에 따라 merge 다음의 숫자가 1부터 차례로 증가하며, 결과파일이 덮어쓰워지는 것을 방지하기 위해 워킹디렉토리 이름이 사용 됩니다.

수행중인 잡의 큐는 ``condor_q`` 라는 명령어를 통해 확인이 가능 합니다. 
전체 작업을 관리하는 ``condor_dagman`` 이 가장 상위에 있으며 이 작업은 모든 하위 작업이 종료될때까지 queue에서 조회됩니다. *job_wrap.sh* 와 같이 dagman이 관리하는 하위 작업이 dagman아래로 조회됩니다.

.. code-block:: console

  [계정명@<kiaf_url> batch_test]$ condor_q
  
  -- Schedd: <kiaf_url> : <ip_address:port?... @ 01/01/70 13:51:32
  OWNER  BATCH_NAME           SUBMITTED   DONE   RUN    IDLE  TOTAL JOB_IDS
  계정명   condor.dag+878014   1/16 13:51     _      3       1      1 878015.0 ... 878018.0
  
  Total for query: 4 jobs; 0 completed, 0 removed, 1 idle, 3 running, 0 held, 0 suspended
  Total for kong91: 4 jobs; 0 completed, 0 removed, 1 idle, 3 running, 0 held, 0 suspended
  Total for all users: 5 jobs; 0 completed, 0 removed, 1 idle, 4 running, 0 held, 0 suspended
 


.. _user_analysis:

사용자 분석 코드 입력 방법
------------------------------------
 
이번에는 위에서 수행 한 예제 파일을 기반으로 사용자 분석코드를 수행하는 방법에 대해 알아보도록 하겠습니다. 
진행하기에 전에 앞의 예제 부분을 숙지하길 권장합니다. 
우선 사용자가 로컬모드로 테스트가 완료된 매크로를 준비합니다. 
이때 사용 되는 C, cxx ,h 파일 등 모든파일이 필요합니다. 
그리고 예제 파일 중에 *runCondor.sh* 파일을 복사해 옵니다. 
스크립트에서 설정이 필요한 각 항목별 의미와 수정 방법에 대하여 알아보도록 하겠습니다.

* **condor_slot** condor에 동시에 할당될 슬롯의 최대값 입니다. 다른 사용자들의 원할한 사용을 위하여 사용되었었으나, 통합팜에서는 사용되지 않습니다.
* **nfile** 슬롯당 분배 되는 인풋 파일의 갯수 입니다. Grid잡에서 *CreateAlienHandler.C* 의 **SetSplitMaxInputFileNumber** 의 값과 동일한 역할을 합니다. ``cat data_file | wc -l`` 명령어를 통해 전체 인풋 파일의 갯수를 알 수 있으니, 이를 토대로 적당한 값을 사용자가 기입합니다. 너무 큰 값을 기입하면 잡당 소요 시간이 길어지고, 너무 작은 값을 기입하면 작업 갯수가 늘어나 작업 대기시간이 길어 질수 있습니다.

.. note:: 

  |는 엔터키 위의 ₩(또는 ⁄)키를 shift와 같이 누르면 입력이 가능 합니다.
  
* **merge** 결과 파일을 한개의 아웃풋파일로 합칠것인지에 대한 옵션 입니다. 0을 기입할 시 merge를 수행하지 않고, 1을 기입할 시 merge를 수행합니다.
* **work_dir** 생성 되는 워킹 디렉토리의 이름으로 사용자가 원하는 이름으로 변경하여도 무방합니다.
* **run_macro** ``aliroot`` 를 통해 실행 되는 분석 매크로 입니다.
* **data_file** 실제 입력되는 데이터가 아닌 데이터의 경로 리스트 파일입니다. 요청하셨던 데이터 파일의 리스트는 */pool/datalist* 아래에 런별로 위치하며, 복사하여 사용하시거나 따로 만들어 사용하시면 됩니다. 다만 데이터 리스트 파일은 *runCondor.sh* 와 같은 경로에 위치하여야 합니다. 그렇지 않은 경우 여기에는 절대경로의 파일명을 적어 주셔야 합니다.
 

HTCondor 작업명세파일 활용법
-------------------------------------

기본적인 HTCondor 작업명세파일 작성법은 :doc:`htcondor_usage` 에서 확인 하였습니다. 
이 절에서는 하 나의 명령어를 여러 변수에 대해 반복적으로 수행할때 이를 간단히 작성하는 방법에 대해 알아 보겠습니다.

matching 활용하기
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

maching의 결과를 변수로 이용하여 단순 반복적인 작업을 간단히 작성하도록 도와줍니다. 
기본적인 구조는 아래와 같습니다.

 queue [<int>] [<varname>] matching [files|dirs] [slice] <list of items>
  
위 방식을 활용하여 dat 형식의 파일을 변수로 받아 analysis로 분석하는 샘플을 만들어 보도록 하겠습니다.

.. code-block:: console

  universe                = vanilla
  executable              = analysis
  arguments               = $(filename)
  queue filename matching files *.dat

from 활용하기
^^^^^^^^^^^^^^^^^^^^^^^^^^

파일에 입력된 값을 라인별로 읽어 변수로 이용하여 단순 반복적인 작업을 간단히 작성하도록 도와줍니다. 
기본적인 구조는 아래와 같습니다.

.. code-block:: console

  queue [<int>] [<list of varnames>] from [slice] <file name>
  
위 방식을 활용하여 datalist 파일의 값을 변수로 받아 analysis로 분석하는 샘플을 만들어 보도록 하겠습니다.

.. code-block:: console

  universe                = vanilla
  executable              = analysis
  arguments               = $(filename)
  queue filename from datalist
