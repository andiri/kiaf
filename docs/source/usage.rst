KIAF 이용 안내
==================

사용자 계정 생성
------------------

우선 KISTI에서 제공하는 KIAF를 이용하기 위해서는 KIAF 접속을 위한 계정을 발급받아야 합니다. 
이 절에서는 KIAF 계정을 발급받기 위해 필요한 과정을 안내합니다.

.. 계정 신청서

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

### KIAF 서버 접속 ###

사용자 계정 신청 절차를 따라 계정을 요청 하여 관리자로 부터 임시 비밀번호를 받으 면, 발급 받은 계정과 임시 비밀번호를 이용하여 KIAF 서버에 접속이 가능합니다. 
서버의 주소는 <url> 입니다. 
서버의 보안을 위해 ssh 접속 포트를 ####번으로 설정되어 있습니다. (기본 접속 포트인 ##번 포트로는 접속이 불가능 합니다.) 
windows 환경에서는 putty2나 ssh secure shell client 등 ssh 접속 유틸리티를 이용하여 KIAF 서버에 접속이 가능 합니다.
리눅스 환경의 터미널에서 아래의 명령어와 옵션을 이용하면 KIAF 서버에 접속이 가능합니다.

``ssh -p #### 계정명@<url>``

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

