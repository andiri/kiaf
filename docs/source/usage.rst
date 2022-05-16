이용 안내
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
계정이 생성되면 임시 비밀번호와 함께 안내메일이 신청서 상의 사용자 Email로 전송 됩니다.
작성한 신청서는 KISTI KIAF support팀으로 제출합니다. 
kiaf-support@kisti.re.kr

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

