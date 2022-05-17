HTCondor 사용법 안내
=================================


작업 제출에 관한 설명
-------------------------------

사용자가 작업을 제출하기 위해서는 작업명세파일과 실제 작업 스크립트가 필요합니다. 
본 절에서는 HTCondor에서 사용자의 작업을 수행할 환경을 지정하는 universe 등 작업명세 파일 작성방법에 대해서 설명합니다.

.. code-block:: console
  :caption: HTCondor 작업명세파일 샘플

  Universe                = vanilla
  Executable              = run.script
  Log                     = path/log
  Output                  = path/out
  Error                   = path/err
  Accounting_Group        = group_alice
  should_transfer_files   = YES
  when_to_transfer_output = ON_EXIT
  arguments               = "$1 $2 $3"
  output_destination      = file:///my/path/to/save/file
  Request_cpus            = 1
  Request_memory          = 5GB
  Request_disk            = 10GB
  Notification           = Always | Complete | Error | Never
  Notify_user             = user@email.com
  Queue


작업명세파일에 대한 설명
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* **universe**는 실행 파일을 수행할 환경을 지정. 현재 HTCondor에서는 **standard**, **vanilla**, **grid**, **java**, **local**, **scheduler**, **docker** 등 다양한 환경을 제공
  * **vanila** : 가장 일반적인 경우에 많이 쓰이는 환경으로, ``condor_compile`` 로 링 크될 수 없는 경우로 쉘스크립트가 대표적인 예
  * **standard** : 리모트에서 수행중인 job의 상태를 호스트에 저장할 수 있는 환경으로 checking-point와 remote system call 을 사용. standard 환경에서 실행되는 파일은 반드시 ``condor_compile`` 과 링크
  * **grid** :그리드 작업을 수행할 때 사용
  * **Java** : 자바 프로그램을 실행하는 환경을 제공
  * **docker** : 워커노드에서 docker 컨테이너를 실행
* **executable** : 워커노드에서 실제로 수행될 명령어나 스크립트
* **log** : 로그를 저장할 곳을 지정 (HTCondor 작업의 상태에 대한 로그)
* **output** : 실행 결과를 저장할 곳을 지정 (**executable** 의 표준출력값)
* **error** : 에러 로그 파일을 지정 (**executable** 의 표준에러값)
* **Accounting_Group** : 제출할 작업의 그룹을 지정 
* **should_transfer_files** 은 파일 전송을 가능 여부를 설정하는 옵션이며, 이는 ``YES``, ``IF_NEEDED`` 그리고 ``NO`` 의 옵션값 존재
  * ``YES``  : HTCondor의 파일 전송 기능을 활성화하고, SN 머신에서 실행 파일을 WN 으로로 전송. 또한 워크노드에서 생성된 output 파일은 SN으로 전송
  * ``IF_NEEDED`` : SN과 WN이 다른 File System Domain에 있을경우 YES 옵션과 같 이 파일 전송. 그러나 SN과 WN이 같은 File System Domain에 있을경우 파일을 전송하지 않고 공유된 파일 시스템을 사용
  * ``NO`` : HTCondor의 파일 전송 기능을 비활성화
* **when_to_transfer_output** 은 output 파일을 SN으로 전송하는 시점을 선택가능. 이는 ``ON_EXIT`` 와 ``ON_EXIT_OR_EVICT`` 의 값을 가짐
  * ``ON_EXIT`` : 작업이 종료되었을 때에만 전송
  * ``ON_EXIT_OR_EVICT`` : 작업이 종료되었거나 취소되었을 때 전송
* **arguments** : **executable**의 스크립트에 사용할 변수(따옴표 안에서 스페이스로 구별)
* **output_destination** : 샌드 박스의 전체 출력물을 전송하기위한 위치를 지정
* **Request_cpus** : 기본값은 1 이며, 사용자가 필요로 하는 ``CPU`` 의 갯수
* **Request_memory** : 작업에 필요로하는 ``RAM`` 의 크기이며, KB, MB, GB 등으로 지정
* **Request_disk** : 작업에 필요로하는 디스크의 크기이며, KB, MB, GB 등으로 지정
* **Notification** 은 작업 상태에 대한 메일을 발송해야 하는 상황을 정의
  * ``Always`` : 어떤 상황이던 항상 메일 발송
  * ``Complete`` : 작업이 정상적으로 종료되면 메일 발송
  * ``Error`` : 작업이 비정상적으로 종료되거나 작업이 홀드되었을때 메일 발송 
  * ``Never`` : 기본값으로 이메일을 받지 않음
* **Notify_user** : HTCondor가 발송하는 메일을 받을 주소

작업 제출을 위한 HTCondor 명령어
------------------------------------------

condor_submit
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* HTCondor에서 작업을 제출하는 가장 기본적인 명령어
* 제출할 작업명세파일을 변수로 사용 

.. code_block:: console

  condor_submit condor.script
  
condor_submit 자주 사용되는 옵션
"""""""""""""""""""""""""""""""""""""""""""

(옵션에 사용되는 값은 보통 작업명세파일에 기입하여 사용)
* ``-append`` : 추가적으로 명세파일의 값을 추가하거나 일시적으로 변경할때 사용 

condor_submit_dag 소개
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* DAGMan 작업을 제출하기 위해 사용하는 명령어
* 작업명세파일이 아닌 ``dag`` 파일을 변수로 사용
* DAGMan에 대한 작업명세파일이 없어 필요한 설정은 명령어로 대체

.. code-block:: console

  condor_submit_dag condor.dag
  
condor_submit_dag 자주 사용되는 옵션
""""""""""""""""""""""""""""""""""""""""""""""

* ``-force`` : 이미 존재하는 파일은 덮어씌우고 작업을 제출
* ``-maxjobs`` : 동시에 최대로 제출되는 작업 갯수를 제한(``Proc`` 가 아닌 ``Cluster`` 기준) 
* ``-notification`` : 안내 메일 옵션 설정 (``condor_submit`` 설명 참고)
* ``-append`` : 기본옵션으로 설정이 불가능한 값을 입력하기 위한 옵션
  * ``Accounting_Group`` , ``Notify_User`` 등의 값
  
작업 모니터링을 위한 HTCondor 명령어
-------------------------------------------------

condor_q 소개
^^^^^^^^^^^^^^^^^^^^^^^^^^^

* HTCondor에 제출된 작업을 살펴보려면 ``condor_q`` 명령어를 사용
* ``condor_q`` 는 작업 제출데몬(``SCHEDD``)에서 정보를 수집하여 사용자에게 제공
* 사용자는 확인하고 싶은 작업의 ``<cluster>`` 나 ``<cluster>.<proc>`` 또는 ``<owner>`` 정보를 이용하여 특정 작업을 확인 가능, 기본값은 본인의 작업

.. code-block:: console

  [계정명@<kiaf_url>  ̃]$ condor_q

  -- Schedd: <kiaf_url> : <ip_address:port?... @ 01/01/70 11:00:33
  OWNER  BATCH_NAME    SUBMITTED   DONE   RUN    IDLE  TOTAL JOB_IDS

  Total for query: 5 jobs; 0 completed, 0 removed, 0 idle, 5 running, 0 held, 0 suspended
  Total for kong91: 5 jobs; 0 completed, 0 removed, 0 idle, 5 running, 0 held, 0 suspended
  Total for all users: 13 jobs; 0 completed, 0 removed, 0 idle, 12 running, 1 held, 0 suspended

condor_q 출력 내용 설명
"""""""""""""""""""""""""""""""""""""""

* ``Schedd`` : 작업제출서버정보
* ``OWNER`` : 작업을 제출한 유저의 아이디
* ``BATCHNAME`` : 제출된 실행 파일의 이름(같은 이름을 가진 작업은 묶여서 보이고 개별작업 확인은 ``-nobatch`` 옵션으로 확인합니다)
* ``SUBMITTED`` : 작업이 제출된 시간
* ``DONE`` : 완료된 작업의 개수
* ``RUN`` :현재처리중인작업의개수
* ``IDLE`` : 제출되었으나 아직까지 시작되지 않은 작업의 개수
* ``HOLD`` : 치명적인 오류가 발생하여 더이상 작업을 진행을 할 수 없는 작업의 개수
* ``TOTAL`` : 총 작업수
* ``JOB_IDS`` : 제출된 작업중 끝나지 않은 작업의 ``<Cluster>``와 ``<Process>`` 정보
  
condor_q -nobatch 출력 내용 설명
""""""""""""""""""""""""""""""""""""""""""""""""""

.. code-block:: console

  [계정명@<kiaf_url>  ̃]$ condor_q -nobatch

  -- Schedd: <kiaf_url> : <ip_address:port?... @ 01/01/70 11:00:33
   ID      OWNER            SUBMITTED     RUN_TIME ST PRI SIZE CMD
   726.0   계정명            7/17 10:59   0+00:00:38 R  0    0.0 job_alice.sh
   726.1   계정명            7/17 10:59   0+00:00:38 R  0    0.0 job_alice.sh
   726.2   계정명            7/17 10:59   0+00:00:38 R  0    0.0 job_alice.sh
   726.3   계정명            7/17 10:59   0+00:00:38 R  0    0.0 job_alice.sh
   726.4   계정명            7/17 10:59   0+00:00:38 R  0    0.0 job_alice.sh
   
  Total for query: 5 jobs; 0 completed, 0 removed, 0 idle, 5 running, 0 held, 0 suspended
  Total for kong91: 5 jobs; 0 completed, 0 removed, 0 idle, 5 running, 0 held, 0 suspended
  Total for all users: 13 jobs; 0 completed, 0 removed, 0 idle, 12 running, 1 held, 0 suspended
  
``nobatch`` 옵션을 사용한 경우 작업 기준으로 출력됩니다. 
위의 기본옵션과 비교할 때 출 력 결과 중 일부는 다른 정보가 보이고 기본 옵션에서 보이지 않던 결과의 의미는 다음과 같습니다.

* ``ID`` : 작업의 ``cluster`` , ``process`` 번호 
* ``RUN_TIME`` : 작업이 수행된 시간 
* ``ST`` : 작업의 상태정보
  * ``H`` : 작업 정지(Hold) 상태
  * ``R`` : 작업 진행(Running) 상태
  * ``I`` : IDLE상태
  * ``C`` : 종료 상태, 단, 작업이 완전히 끝나면 DONE에서도 제거되므로 실제 DONE 상태인 작업들은 결과파일 전송 등 사후 작업 중인 작업을 의미
  * ``X`` : 작업 취소 상태
  * ``S`` : 작업 정지 상태
  * ``<`` : 전송 수신 상태(WN→SN)
  * ``>`` : 전송 송신 상태(SN→WN)
* ``PRI`` : 작업의 ``priority`` 값
* ``SIZE`` : 작업이 최대 사용중인 ``RAM`` 의 크기 (MB 단위) 
* ``CMD`` : 작업이 수행하는 명령 (executable + arguments)

condor_q 자주 사용되는 옵션 
""""""""""""""""""""""""""""""""""""""

* ``-global``
  * ``--global`` 옵션은 작업 제출 데몬 서버가 2개 이상일 때 사용
  * ``condor_q`` 는 기본적으로 해당 작업 제출 데몬 서버로부터 제출된 작업들만을 확인
  * 다른 작업 제출 데몬으로부터 제출된 작업들을 확인하려면 ``--global`` 옵션을 사용
* ``-allusers`` / ``-all``
  * ``condor_q`` 는 해당 사용자가 제출한 작업만 확인 가능
  * 다른 사용자가 제출한 작업 목록을 확인하고 싶다면 ``-allusers`` 또는 ``-all`` 옵션을 사용 
* ``-nobatch``
  * 최신 ``condor_q`` 명령어는 기본적으로 batch형식의 작업 목록을 보임
  * 묶음이 아닌 개별 작업들의 정보를 확인하고 싶을 때는 ``-nobatch`` 옵션을 사용 
  * ``-run`` 옵션과 함께 사용하여 실행되고 있는 작업과 머신의 정보를 확인 가능
  * ``-dag`` 옵션과 함께 사용하여 DAGMan 작업을 구별하여 확인 가능
* ``-run``
  * 제출된 작업 중 현재 실행되고 있는 작업들의 목록을 확인 (``IDLE`` , ``HOLD`` 상태의 작업 제외)
  * 이 옵션은 ``-nobatch`` 와 함께 사용하지 않는 한 효과가 없음 
* ``-dag``
  * DAGMan 인스턴스 아래에 DAG 노드 작업을 표시
  * 자식 노드는 들여 쓰기를 사용하여 DAG의 구조를 표시
  * 이 옵션은 ``-nobatch`` 와 함께 사용하지 않는 한 효과가 없음
* ``-version``
  * 해당 HTCondor의 버전을 확인
* ``-autoformat`` / ``-af``
  * 기존 ``-format`` 옵션을 완전히 대체 가능
  * 기존 포맷은 해당 ``ClassAd`` 의 데이터 형태를 알아야 확인 가능
  * ``-autoformat`` 옵션은 해당 포맷의 데이터를 자동으로 확인
  * 예) ``ClusterID`` , ``RequestCpus`` , ``RemoteHost`` 정보를 확인할 경우 

.. code-block:: console

  [계정명@<kiaf_url>  ̃]$ condor_q -af:h ClusterId RequestCpus RemoteHost
  55526 0 1 2930

    ∗ ``-af:h`` 나 ``-af:lrng`` 옵션을 통해 출력 메시지를 변경 가능 
    
.. code-block:: condole
  :caption: -af:h
  
  [계정명@<kiaf_url>  ̃]$ condor_q -af:h ClusterId RequestCpus RemoteHost
  ClusterId ProcId RequestCpus RequestMemory
  55526 0 1 2930

.. code-block:: condole
  :caption: -af:lrng

  [계정명@<kiaf_url>  ̃]$ condor_q -af:lrng ClusterId RequestCpus RemoteHost
  ClusterId = 55526
  ProcId = 0
  RequestCpus = 1
  RequestMemory = 2930
  
* ``-long``
  * 해당 작업의 작업 ``ClassAd`` 정보를 확인
  * 하나의 작업을 살펴볼 때 유용 
* ``-wide[:<width>]``
  * 한 줄에서 보여줄 메시지의 크기를 조절
  * 머신의 슬롯 이름이 너무 길어 한 줄에 보이지 않을 때 유용
* ``-analyze`` / ``-better-analyze``
  * 작업이 정상적으로 실행되지 않을 때 해당 작업에 대한 정보를 출력
  
condor_tail 소개
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* 실행 중인 작업(Running)의 표준 출력과 표준 에러를 보여주는 명령어
* 실행 상태이지만 실제로 동작하고 있지 않은 작업들을 구별
* 단, 표준에러 메시지들은 ``-stderr -no-stdout`` 옵션을 추가로 설정하여 확인 가능

condor_tail 자주 사용되는 옵션
""""""""""""""""""""""""""""""""""""""""

* ``-follow`` / ``-f``
  * 작업이 중단될때까지 계속해서 출력
* ``-maxbytes <num>``
  * 메시지의 크기를 결정. 기본은 1024바이트이며 해당 바이트 수만큼 출력
* ``-no-stdout``
  * 작업의 표준출력을 제외
* ``-stderr``
  * 표준 에러를 포함하여 출력 (stdout+stderr)
  
condor_qedit 소개
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* 이미 제출된 작업의 ``ClassAd`` 값을 변경
* ``Requirement`` 등 요구조건 변경을 위해 사용
* 문자열 형식의 ``ClassAd`` 값을 수정할 때에는 따옴표(”)로 표기

.. code-block:: console
  :caption: 예) 메모리 요구량이 잘못된 작업의 요구량을 수정할 때 

  [계정명@<kiaf_url>  ̃]$ condor_qedit 55.0 RequestMemory 1024
  Set attribute "RequestMemory" for 1 matching jobs.
  
.. code-block:: console
  :caption: 예) 문제가 있는 서버를 제외하고 작업을 돌리고 싶을 때 

  [계정명@<kiaf_url>  ̃]$ condor_qedit 55.0Requirements ’Machine =!= <kiaf_wn_url>’
  Set attribute "Requirements" for 1 matching jobs.
  
작업 관리를 위한 HTCondor 명령어 
-----------------------------------------

condor_rm 소개
^^^^^^^^^^^^^^^^^^^^^^^^^^

* 작업을 삭제하기 위해 사용하는 명령어 

condor_rm 자주 사용되는 옵션
""""""""""""""""""""""""""""""""""""""

* ``<cluster>``
  * 특정 클러스터의 모든 작업을 삭제시키기 위한 옵션
* ``<cluster.process>``
  * 특정 작업을 삭제시키기 위한 옵션
* ``<user>``
  * 특정 유저의 모든 작업을 삭제시키기 위한 옵션
* ``-all``
  * 모든 작업을 삭제시키기 위한 옵션
  
condor_hold 소개
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* 작업을 홀드하기 위해 사용하는 명령어

condor_hold 자주 사용되는 옵션
""""""""""""""""""""""""""""""""""""""

* ``<cluster>``
  * 특정 클러스터의 모든 작업을 홀드시키기 위한 옵션
* ``<cluster.process>``
  * 특정 작업을 홀드시키기 위한 옵션
* ``<user>``
  * 특정 유저의 모든 작업을 홀드시키기 위한 옵션
* ``-all``
  * 모든 작업을 홀드시키기 위한 옵션
  
condor_release 소개
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* 홀드되어 있는 작업을 재개하기 위해 사용하는 명령어

condor_release 자주 사용되는 옵션
"""""""""""""""""""""""""""""""""""""""

* ``<cluster>``
  * 특정 클러스터의 모든 작업을 재개시키기 위한 옵션
* ``<cluster.process>``
  * 특정 작업을 재개시키기 위한 옵션
* ``<user>``
  * 특정 유저의 모든 작업을 재개시키기 위한 옵션
* ``-all``
  * 모든 작업을 재개시키기 위한 옵션
  
Cluseter 상태 확인을 위한 HTCondor 명령어 
------------------------------------------------

condor_status 소개
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* HTCondor에서 머신의 정보를 확인하는 명령어

.. code-block:: console

  [kong91@kiaf-ui  ̃]$ condor_status
  Name  OpSys      Arch   State     Activity LoadAv Mem ActvtyTime
  slot1@kiaf-wn1000.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 96580 8+00:46:01
  slot2@kiaf-wn1000.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 48290 8+00:46:14
  slot3@kiaf-wn1000.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 48290 8+00:46:14
  slot1@kiaf-wn1001.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 96580 8+00:20:49
  slot2@kiaf-wn1001.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 48290 8+00:21:02
  slot3@kiaf-wn1001.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 48290 8+00:21:02
  slot1@kiaf-wn1002.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 96580 7+02:26:00
  slot2@kiaf-wn1002.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 48290 7+02:26:14
  slot3@kiaf-wn1002.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 48290 7+02:26:14
  slot1@kiaf-wn1003.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 96580 7+02:31:01
  slot2@kiaf-wn1003.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 48290 7+02:31:14
  slot3@kiaf-wn1003.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 48290 7+02:31:14
  slot1@kiaf-wn1004.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 96580 8+01:26:05
  slot2@kiaf-wn1004.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 48290 8+01:26:18
  slot3@kiaf-wn1004.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 48290 8+01:26:18
  slot1@kiaf-wn1005.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 96580 8+00:40:41
  slot2@kiaf-wn1005.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 48290 8+00:40:56
  slot3@kiaf-wn1005.sdfarm.kr   LINUX      X86_64 Unclaimed Idle      0.000 48290 8+00:40:56

               Machines Owner Claimed Unclaimed Matched Preempting  Drain
  X86_64/LINUX       18     0       0        18       0          0      0
         Total       18     0       0        18       0          0      0

condor_status 표시 정보
"""""""""""""""""""""""""""""""""

* ``Name`` : 해당 머신의 슬롯 이름과 머신정보 
* ``OpSys`` : 해당 머신의 운영체제 정보
* ``Arch`` : 해당 머신의 아키텍처 정보 
* ``State`` : 머신의 점유 상태
  * ``Unclaimed`` :현재 HTCondor에 의해 사용되고 있지 않은 머신 
  * ``Claimed`` : HTCondor에 의해 사용 중인 머신
* ``Activity`` : 머신의 상태
  * ``Idle`` : 머신이 작업을 수행하고 있지 않음
  * ``Busy`` : 해당 머신이 HTCondor나 다른 프로그램에 의해 사용 중
* ``LoadAv`` : 해당 머신의 부하 평균값
* ``Mem`` : 해당 머신이 사용가능한 메모리크기 
* ``ActvtyTime`` : 해당 머신이 동작한 시간

condor_status 자주 사용되는 옵션
"""""""""""""""""""""""""""""""""""""""

* ``-avail`` : 현재 사용이 가능한 자원의 정보를 출력
* ``-run`` : 현재 사용중인 머신의 정보를 출력
* ``-server`` : HTCondor 풀에 속해 있는 머신들의 자원 정보를 확인
* ``-state`` : 머신의 자원현황 정보를 출력
* ``-startd`` : 워커노드의 정보를 출력
* ``-long`` : 자세한 머신에 대한 정보를 획득. 머신 관련 ``ClassAd`` 확인 가능
* ``-total`` : 종합 정보만 출력
* ``-wide`` : 한 줄에 출력한 문자 개수를 지정
* ``-autoformat`` : ``condor_q``의 ``-autoformat``과 사용법이 동일. 해당 머신의 ``ClassAd`` 내용을 확인 가능
* ``-constraint`` : 특정 조건에 맞는 머신만을 확인. 많은 경우 특정 이름을 가지거나 특정 State인 머신을 검색할 때 사용 

.. code-block:: console
  :caption: 예시

  condor_status -constraint "Memory <= 4000"
  condor_status -constraint "LoadAvg >= 0.2"
  condor_status -constraint State==\"Unclaimed\"
  condor_status -constraint Activity==\"Idle\"

어려 옵션 혼합 예시
"""""""""""""""""""""""""""""""

.. code-block:: console
  :caption: 사용가능한 자원량을 확인하고 싶을 때
  
  [계정명@<kiaf_url>  ̃]$ condor_status -avail -state
  Name                                     Cpu  Mem  LoadAv   KbdIdle    State    StateTime  Activ   ActvtyTime
  slot1@<kiaf_wn_url>                28 96580  0.000   7+01:15:04 Unclaimed 8+08:21:19 Idle    8+08:21:19
  slot2@<kiaf_wn_url>                14 48290  0.000   7+01:15:04 Unclaimed 8+08:21:19 Idle    8+08:21:19
  slot3@<kiaf_wn_url>                14 48290  0.000   7+01:15:04 Unclaimed    8+08:21:19 Idle    8+08:21:19
  slot1@<kiaf_wn_url>                28 96580  0.000   7+01:15:04 Unclaimed    8+07:56:04 Idle    8+07:55:51
  slot2@<kiaf_wn_url>                14 48290  0.000   7+01:15:04 Unclaimed    8+07:56:04 Idle    8+07:56:04
  slot3@<kiaf_wn_url>                14 48290  0.000   7+01:15:04 Unclaimed    8+07:56:04 Idle    8+07:56:04
  slot1@<kiaf_wn_url>                28 96580  0.000   7+01:15:32 Unclaimed    7+10:01:18 Idle    7+10:01:04
  slot2@<kiaf_wn_url>                14 48290  0.000   7+01:15:32 Unclaimed    7+10:01:18 Idle    7+10:01:18
  slot3@<kiaf_wn_url>                14 48290  0.000   7+01:15:32 Unclaimed    7+10:01:18 Idle    7+10:01:18
  slot1@<kiaf_wn_url>                28 96580  0.000   7+01:15:02 Unclaimed    7+10:06:22 Idle    7+10:06:09
  slot2@<kiaf_wn_url>                14 48290  0.000   7+01:15:02 Unclaimed    7+10:06:22 Idle    7+10:06:22
  slot3@<kiaf_wn_url>                14 48290  0.000   7+01:15:02 Unclaimed    7+10:06:22 Idle    7+10:06:22
  slot1@<kiaf_wn_url>                28 96580  0.000   7+01:15:14 Unclaimed    8+09:01:19 Idle    8+09:01:06
  slot2@<kiaf_wn_url>                14 48290  0.000   7+01:15:14 Unclaimed    8+09:01:19 Idle    8+09:01:19
  slot3@<kiaf_wn_url>                14 48290  0.000   7+01:15:14 Unclaimed     8+09:01:19 Idle    8+09:01:19
  slot1@<kiaf_wn_url>                28 96580  0.000   7+01:15:35 Unclaimed    8+08:16:01 Idle    8+08:15:46
  slot2@<kiaf_wn_url>                14 48290  0.000   7+01:15:35 Unclaimed    8+08:16:01 Idle    8+08:16:01
  slot3@<kiaf_wn_url>                14 48290  0.000   7+01:15:35 Unclaimed    8+08:16:01 Idle    8+08:16:01

           Machines Owner Unclaimed Claimed Preempting Matched  Drain
           
     Idle         0     0        18       0          0       0      0
     
    Total         0     0        18       0          0       0      0
    
.. code-block:: console
  :caption: 현재 작업이 어느 머신에서 작업중인지 확인하고 싶을때
  
  [계정명@<kiaf_url>  ̃]$ condor_status -startd -run
  Name                                     OpSys       Arch   LoadAv RemoteUser ClientMachine
  slot3_1@<kiaf_wn_url>            LINUX       X86_64  0.010 계정명@<kiaf_url>    <kiaf_url>
  slot3_2@<kiaf_wn_url>            LINUX       X86_64  0.010 계정명@<kiaf_url>    <kiaf_url>
  slot3_3@<kiaf_wn_url>            LINUX       X86_64  0.010 계정명@<kiaf_url>    <kiaf_url>
  slot3_4@<kiaf_wn_url>            LINUX       X86_64  0.010 계정명@<kiaf_url>    <kiaf_url>
  slot3_5@<kiaf_wn_url>            LINUX       X86_64  0.010 계정명@<kiaf_url>    <kiaf_url>
  slot3_6@<kiaf_wn_url>            LINUX       X86_64  0.010 계정명@<kiaf_url>    <kiaf_url>
  slot3_7@<kiaf_wn_url>            LINUX       X86_64  0.010 계정명@<kiaf_url>    <kiaf_url>
  slot3_8@<kiaf_wn_url>            LINUX       X86_64  0.010 계정명@<kiaf_url>    <kiaf_url>
  slot3_9@<kiaf_wn_url>            LINUX       X86_64  0.010 계정명@<kiaf_url>    <kiaf_url>
  slot3_10@<kiaf_wn_url>            LINUX       X86_64  0.010 계정명@<kiaf_url>    <kiaf_url>
  
                  Machines         MIPS       KFLOPS   AvgLoadAvg
                  
     X86_64/LINUX       10       531183     59249201   0.010
     
            Total       10       531183     59249201   0.010
