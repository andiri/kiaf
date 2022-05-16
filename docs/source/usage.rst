KIAF 이용 안내
==================

사용자 계정 생성
------------------

우선 KISTI에서 제공하는 KIAF를 이용하기 위해서는 KIAF 접속을 위한 계정을 발급받아야 합니다. 
이 절에서는 KIAF 계정을 발급받기 위해 필요한 과정을 안내합니다.

계정 신청서
^^^^^^^^^^^^^^^^^

계정 신청서는 마지막 페이지의 계정 신청서 양식을 사용하거나 KIAF 담당자에게 양식 을 요청하여 작성합니다. 
항목별 자세한 작성방법은 48페이지의 부록A을 참고하여 주시기 바랍니다.
개인 정보보호법과 관련하여 개인정보 제공 및 이용 동의서를 제출받고 있습니다. 
해당 양식은 마지막 페이지의 개인정보 제공 및 이용 동의서 양식을 이용해주시기 바랍니다.

Account Name은 사용자 계정명이 될부분으로 알아보기 쉽게 작성해주시기 바랍니다. 

.. note::

   ID를 정확하게 만들기 위해서 신청서를 담당자에게 제출하는 메일에 ID를 기입하여 보내주시기를 권장 합니다.
   
계정이 생성되면 임시 비밀번호와 함께 안내메일이 신청서 상의 사용자 Email로 전송 됩니다.
작성한 신청서는 KISTI KIAF 담당자에게 제출합니다. 
계정 요청 담당자 : kong91@kisti.re.kr


KIAF 서버 사용법
-------------------

1.1절을 따라 계정을 생성 한 이후 전달 받은 계정 정보를 통해 분석팜에 접속하고 비밀
번호 설정, 데이터 복사등 기본적인 서버 사용법에 대해서 알아 보겠습니다.

KIAF 서버 접속
^^^^^^^^^^^^^^^^^^^

사용자 계정 신청 절차를 따라 계정을 요청 하여 관리자로 부터 임시 비밀번호를 받으 면, 발급 받은 계정과 임시 비밀번호를 이용하여 KIAF 서버에 접속이 가능합니다. 
서버의 주소는 <kiaf_url> 입니다. 
서버의 보안을 위해 ssh 접속 포트를 ####번으로 설정되어 있습니다. (기본 접속 포트인 ##번 포트로는 접속이 불가능 합니다.) 
windows 환경에서는 putty나 ssh secure shell client 등 ssh 접속 유틸리티를 이용하여 KIAF 서버에 접속이 가능 합니다.

.. note::

   putty는 홈페이지(https://www.putty.org)를 통해서 무료로 다운로드가 가능합니다.

   
리눅스 환경의 터미널에서 아래의 명령어와 옵션을 이용하면 KIAF 서버에 접속이 가능합니다.

.. code-block:: console
   ssh -p #### 계정명@<kiaf_url>

비밀번호 설정
^^^^^^^^^^^^^^^^^^^

계정을 발급받고 임시 비밀번호를 이용하여 KIAF에 처음 접속하면 비밀번호가 만료되 었다는 안내와 함께 비밀번호를 변경하라는 문구가 나타납니다. 
먼저 물어보는 것은 현재의 암호입니다. 
발급 받은 임시 비밀번호를 입력합니다. 
이후에 비밀번호 정책에 따른 새로운 비밀번호를 설정 합니다. 
새로운 비밀번호는 영문 대소문자, 숫자, 특수문자 등 4가지 모두를 혼용하여야 하며, 최소 8자 이상이여야 합니다.

.. note::
   
   위 규칙을 준수하더라도 보안상 취약하다고 판단되면 암호 설정 불가합니다. 사유와 함께 다시 설정하라는 문구가 나타니 다른 암호로 설정하시기 바랍니다.
   
이때 비밀번호의 설정이 완료 되면 연결이 종료됩니다. 
다시 접속을 시도하여 새롭게 설정한 비밀번호를 입력하면 접속에 성공 합니다.

.. note::

   접속이 되면 첫 위치는 사용자의 홈 디렉토리 입니다. 사용자의 홈 디렉토리는 경로는 /alice/home/계정명 입니다.
   
이후 패스워드 만료등의 이유로 비밀번호를 다시 변경하고자할때는 passwd 명령을 통해 비밀번호 변경이 가능합니다.

.. code-block:: console

   계정명: ̃/> ssh -p #### 계정명@<kiaf_url>
   계정명@<kiaf_url>’s password:
   Password expired. Change your password now.
   Last login: Wed Jul 10 16:38:45 1970
   WARNING: Your password has expired.
   You must change your password now and login again!
   Changing password for user kiaf_tester.
   A valid password should be a mix of upper and lower case letters,
   digits, and other characters. You can use a 8 character long
   password with characters from these 4 classes.
   Maximum of Consecutive Repeating Characters from Same Character Class is 3.
   Current Password:
   New password:
   Retype new password:
   passwd: all authentication tokens updated successfully.
   Connection to <kiaf_url> closed.

데이터 전송
^^^^^^^^^^^^^^^^^

일반적으로 사용하는 GUI환경이 아닌 KIAF과 본인의 워크스테이션간에 데이터를 전송 하려면 전송 툴이나 명령어를 사용하여야합니다. 
windows는 putty나 ssh secure shell client 등에서 제공하는 파일전송 툴을 이용하면 됩니다. 
리눅스 환경에서는 scp나 sftp 명령어를 이용합니다.

.. code-block:: console
   scp -P #### /path/to/src/file 계정명@<kiaf_url>:/path/to/dst/file
  
ALICE 환경 로딩
------------------------

이 절에서는 지원이 가능한 패키지를 확인하고 패키지의 버전을 지정하여 지정한 버전의 ALICE 패키지 환경을 로드하는 방법까지 알아보겠습니다.

.. _alicepackage:

ALICE 패키지
^^^^^^^^^^^^^^^^^^^^^^^

ALICE 패키지는 CERN에서 배포하는 CVMFS 를 통해 제공 됩니다. 
따라서 KIAF에서 사 용가능한 AliRoot 및 AliPhysics의 버젼은 http://alimonitor.cern.ch/packages 에서 확인이 가능합니다. 
위 사이트를 통해 사용하고자 하는 버젼의 CVMFS status가 Avail- able인지 확인 하시기 바랍니다. 
사용이 불가능한 버젼을 지정한 경우 AliRoot를 실행하는 과정에서 에러가 발생하며 정상적으로 실행 되지 않습니다.
사용하고자하는 버전을 결정한 뒤에는 KIAF의 개별 설정파일에서 해당 버전을 지정해 야 합니다. 
$HOME/.alice env/alice env.conf 파일을 vi, emacs 등의 텍스트 편집기를 통해 수정하면 사용하고자하는 버전이 지정됩니다.
아래는 AliPhysics 설정 예시입니다.

.. code-block:: console
   export ALIPHYSICS_VERSION="vAN-20160328-1"

다음은 AliRoot 설정 예시입니다.

.. code-block:: console
   export ALICE_ROOT_VERSION="v5-07-20-4"
   
ALICE 환경 로드 명령어
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ALICE 환경을 로드하는 과정은 kiafenv 명령어로 정의 되어 있습니다. 
이 명령어는 `alicepackage`_절에서 설정한 버전을 기반으로 ALICE 환경을 로드합니다.
    
.. code-block:: console    
   [계정명@<kiaf_url>  ̃]$ kiafenv
   Load the ALICE environment
   Set the AliPhysics::vAN-20160328-1 environment

이때 로드되는 환경의 AliRoot 혹은 AliPhysics의 버젼은 $HOME/.alice env/alice env.conf 에서 정의됩니다.


.. _installation:

Installation
------------

To use Lumache, first install it using pip:

.. code-block:: console

   (.venv) $ pip install lumache

Creating recipes
----------------

To retrieve a list of random ingredients,
you can use the ``lumache.get_random_ingredients()`` function:

.. autofunction:: lumache.get_random_ingredients

The ``kind`` parameter should be either ``"meat"``, ``"fish"``,
or ``"veggies"``. Otherwise, :py:func:`lumache.get_random_ingredients`
will raise an exception.

.. autoexception:: lumache.InvalidKindError

For example:

>>> import lumache
>>> lumache.get_random_ingredients()
['shells', 'gorgonzola', 'parsley']

