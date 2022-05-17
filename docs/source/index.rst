KIAF 사용자 가이드
===================================

**KIAF** is KISTI Analysis Facility for Korean ALICE research groups.

KISTI에서 제공하는 KIAF는 KoALICE 연구자 그룹의 고에너지물리 연구를 위해 구축 되었습니다. 
각 분석머신은 연구자 분들이 요청하신 분석툴을 UI와 동일하게 이용 할수 있도 록 구성되어 있으며, 시스템은 HTCondor를 통해 배치 작업을 수행합니다. 
연구자의 원활한 분석을 위해 KIAF 담당자는 시스템의 기술지원과 함께 배치작업을 수행하기 위해 필요한 예제 매크로 및 스크립트를 작성하여 제공하고 있습니다.

본 가이드는 KIAF 사용자 가이드로 KIAF 접속, HTCondor 사용법, 분석 예제 수행과 사용자 분석 환경에 따른 추가사항까지 5개의 장으로 나뉘어 있습니다. 
본 가이드의 목표는 KIAF 계정을 발급 받아 KIAF 서버에 접속하고 분석 예제를 수행하는 일련의 과정을 수행하여 사용자가 직접 분석 코드를 수행하여 결과를 얻도록 도움을 주는 데에 있습니다. 
가이드에서 이해가 되지 않는 부분이나 오탈자와 관련해서는 작성자(kiaf-support<NO_SPAM>@kisti.re.kr)에게 연락 주시기 바랍니다.

부가적으로 GSDC에서 서비스중인 HTCondor 통합팜에 통합되어 있으며, 여유 분석머 신을 공유하여 할당된 자원량 이상 사용이 가능한 환경이 구축되어 있습니다.

Contents
--------

.. toctree::

   usage
   dataset
   htcondor_usage
   htcondor_jdl_ex
   miscellaneous
   references
   appendix_a
   appendix_c
   appendix_d
   appendix_e
   account_manage_bot
