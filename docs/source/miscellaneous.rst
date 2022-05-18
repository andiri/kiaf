사용자 분석 환경에 따른 추가사항
==========================================


.. _alientoken:

Alien token
-----------------------

Alien접속 등에 필요한 토큰을 발급 받기 위해서는 CERN에서 발급한 개인 인증서를 먼저 취득 해야 합니다. 
해당 과정에 대해서는 `CERN Grid CA <https://ca.cern.ch/ca/>`_ 을 참고하시기 바랍니다. 
아래의 내용은 로컬머신에 유저 PEM keypair를 만든 이후 과정을 설명 합니다.

인증서 복사
^^^^^^^^^^^^^^^^^^^^^

*$HOME/.globus* 디렉토리에 개인 인증서 파일인 *usercert.pem* , *userkey.pem* 을 복사합니다.
개인 머신에서 ``scp`` 명령어를 사용하여 복사 하면됩니다.

.. code-block:: console

  계정명: ̃/> scp -r -P ### .globus 계정명@<kiaf_url>:/alice/home/계정명/.
  계정명@<kiaf_url>’s password:
  usercert.pem                   100% 3334     3.3KB/s   00:00
  userkey.pem                    100% 1907     1.9KB/s   00:00
  
Alien Token 발급
^^^^^^^^^^^^^^^^^^^^^^^^^^

토큰은 ``alien-token-init`` 명령어를 통해 발급이 가능합니다. 
다만 KIAF 계정명 과 CERN 계정명이 일치하지 않는 경우, ``alien-token-init`` 뒤에 CERN 계정명을 적어 줘야 합니다. 
이 경우 ``kiafenv -t`` 명령을 통해 정상적으로 토큰을 발급 받기 위해서는 *$HOME/.alice_env/alice_env.conf* 에 한줄을 추가하여, ``export alien_API_USER=<CERN_계정명>`` 을 적어주시기 바랍니다.

Condor 워커노드에 Alien Token 발급
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

분석 코드중에 Alien에 접근 하는 부분이 있어 토큰이 필요한 경우에는 워커노드에도 alien token이 필요합니다. 
이때는 ``alien-token-init`` 명령이 아닌 ``kiafenv -t`` 명령을 이용 하여 토큰을 발급 받아야만 합니다. 
해당 명령을 수행 하지 않을 경우 접속한 KIAF 서버에는 토큰이 있지만 워커노드는 토큰이 없기 때문에 작업에 에러가 발생 할 수 있습니다.

.. _proofusage:

PROOF 사용법
------------------------

PROOF-Lite 사용법
^^^^^^^^^^^^^^^^^^^^^^^^^

PROOF-Lite는 특별한 설정 없이 AliROOT의 명령을 이용해 실행이 가능 합니다.

.. caution:: 

  PROOF-Lite는 팜전체가 아닌 UI머신의 모든 CPU를 사용합니다. 
  
AliROOT를 실행한 후 Proof 세션을 오픈합니다.

.. code-block:: console

  [계정명@<kiaf_url>  ̃]$ aliroot -l
  Warning in <TUnixSystem::SetDisplay>: DISPLAY not set, setting it to <arbitrary_url>:0.0
  root [0] TProof::Open("")
   +++ Starting PROOF-Lite with # workers +++
  Opening connections to workers: OK (# workers)
  Setting up worker servers: OK (# workers)
  PROOF set to parallel mode (# workers)
  (class TProof*)0x1c87750
  root [1]

UI의 CPU \#개를 모두 사용하여 \#개의 워커가 준비되었음을 확인 할 수 있습니다. 

PoD 사용법
^^^^^^^^^^^^^^^^^^^^^^

PoD는 Proof를 사용자가 원하는 대로 설정하여 사용하도록 도와주는 어플리케이션입니다. 
PoD를 사용하면 HTCondor 팜 전체의 CPU자원을 활용 할 수 있습니다.

PoD 환경 설정
"""""""""""""""""""""

PoD 환경 설정은 ``kiafenv -p`` 명령을 이용합니다. 
마지막 두줄은 PoD를 처음 실행할 때에만 나타나며 이후에는 보이지 않습니다.

.. code-block:: console

  [계정명@<kiaf_url>  ̃]$ kiafenv -p
  Load the ALICE environment
  Set the AliPhysics::vAN-20180810-1 environment
  Load PoD environment
  Generating a default PoD configuration file...
  Generating a default PoD configuration file - DONE.
  
위 명령을 수행하면 사용자 디렉토리 아래에 *.PoD* 의 폴더가 생성 됩니다. 
폴더 내의 *PoD.cfg* 파일을 수정하여 세부 조정이 가능 합니다. 
파일을 수정한 뒤에는 pod환경을 다시 불러와야 합니다. 
이때는 ``source /pool/PoD/3.16/PoD env.sh`` 명령을 사용합니다.

PoD 실행 방법
"""""""""""""""""""""""

설정을 마친 뒤 PoD를 사용하기 위해서는 몇가지 절차가 필요합니다. 
우선 PoD 서버를 시작해야 합니다. 
``pod-server start`` 명령어를 이용합니다.

.. code-block:: console

  [계정명@<kiaf_url>  ̃]$ pod-server start
  Starting PoD server...
  updating xproofd configuration file...
  starting xproofd...
  starting PoD agent...
  preparing PoD worker package...
  select user defined environment script to be added to worker package...
  selecting pre-compiled bins to be added to worker package...
  PoD worker package: /계정명/.PoD/wrk/PoDWorker.sh
  ------------------------
  XPROOFD [4188529] port: ####
  PoD agent [4188660] port: ####
  PROOF connection string: 계정명@<kiaf_url>:####
  ------------------------
  
위 처럼 PoD 서버가 정상적으로 시작이 되면 원하는 만큼 PROOF 워커를 불러 옵니다. 
여기에 사용 되는 명령어는 ``pod-submit -r condor -n 10`` 입니다.

.. code-block:: console

  [계정명@<kiaf_url>  ̃]$ pod-submit -r condor -n 10
  Job ID: 878019

``-r`` 옵션은 PROOF가 사용할 배치시스템을 지정하는 것입니다. KIAF에서는 condor만 지원하기 때문에 이 값은 변경할 수 없습니다. 
``-n`` 옵션은 실행할 PROOF 워커의 갯수입니다. 
condor를 사용하기 때문에 최대 갯수는 ###개 입니다. 
또한 PROOF를 condor를 통해 실행하기 때문에 condor의 잡 ID를 할당 받습니다.

PoD가 준비가 되었는지는 ``pod-info -n`` 으로 확인합니다. 
준비가 완료된 PROOF의 갯수를 표기합니다. 
condor를 실행하는데 약간의 시간이 필요함으로 지정한 갯수와 맞지 않는 경우 잠시 기다립니다.

.. code-block:: console

  [계정명@<kiaf_url>  ̃]$ pod-info -n
  10

이렇게 모든 준비를 마치면 AliROOT에서 PoD에 접속하여 PROOF를 이용하는 방법과 동일하게 사용하면 됩니다.

.. code-block:: console

  [계정명@<kiaf_url>  ̃]$ aliroot -l
  Warning in <TUnixSystem::SetDisplay>: DISPLAY not set, setting it to <arbitrary_url>:0.0
  root [0] TProof::Open("pod://")
  Starting master: opening connection ...
  Starting master: OK
  Opening connections to workers: OK (10 workers)
  Setting up worker servers: OK (10 workers)
  Note: File "iostream" already loaded
  PROOF set to parallel mode (10 workers)
  (class TProof*)0x2b86c20
  root [1]
  
PoD 사용이 종료되면 HTCondor의 작업을 삭제하여 사용하지 않는 슬롯을 점유하지 않도록 해주시길 부탁드립니다.

Docker Universe 사용법(추후 추가 예정)
------------------------------------------------

HTCondor의 Docker Universe를 사용하여 Docker를 이용할 수 있습니다. 
HTCondor 작업명세파일의 **universe** 를 기본값인 **vanilla** 가 아닌 **docker** 로 입력합니다. 
그리고 사용해야 할 도커 이미지의 이름을 **docker_image** 에 입력합니다. 
이를 제외한 나머지 부분은 vanilla universe와 동일합니다. 
아래는 docker를 이용하는 HTCondor 작업명세파일의 간단한 예시 입니다.

.. code-block:: console

  universe          = docker
  docker_image      = centos:6
  executable        = /bin/cat
  arguments         = /etc/redhat-release
  output            = output_docker
  should_transfer_files = YES
  
  queue
  
위 작업명세파일을 제출하면 도커의 redhat 버전인 CentOS release 6.10 (Final)이 output 인 *output_docker* 파일에 저장됩니다.
