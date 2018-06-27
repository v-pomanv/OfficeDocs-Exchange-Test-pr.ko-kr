﻿---
title: 'Active Manager: Exchange 2013 Help'
TOCTitle: Active Manager
ms:assetid: f4be27b7-1d7c-47b4-87ac-bfdfcc046f00
ms:mtpsurl: https://technet.microsoft.com/ko-kr/library/Dd776123(v=EXCHG.150)
ms:contentKeyID: 50484544
ms.date: 05/22/2018
mtps_version: v=EXCHG.150
ms.translationtype: MT
---

# Active Manager

 

_**적용 대상:**Exchange Server 2013_

_**마지막으로 수정된 항목:**2015-04-07_

Microsoft Exchange Server 2013에는 DAG(데이터베이스 사용 가능 그룹) 및 사서함 데이터베이스 복사본이 들어 있는 고가용성 플랫폼을 관리하는 *Active Manager*라는 구성 요소가 들어 있습니다. Active Manager는 모든 사서함 서버의 Microsoft Exchange Replication Service(MSExchangeRepl.exe) 내에서 실행됩니다. DAG의 구성원이 아닌 사서함 서버에는 다음과 같은 단일 Active Manager 역할이 있습니다. *독립 실행형 Active Manager*. DAG의 구성원인 서버에는 다음과 같은 두 가지 Active Manager 역할이 있습니다. PAM(*Primary Active Manager*)과 SAM(*Standby Active Manager*), 두 가지입니다. PAM은 활성 및 수동 복사본을 결정하는 DAG의 Active Manager 역할입니다. PAM은 토폴로지 변경 알림을 가져오고 서버 오류에 대처하는 역할을 담당합니다. PAM 역할을 맡은 DAG 구성원은 항상 현재 클러스터 쿼럼 리소스(기본 클러스터 그룹)를 소유한 구성원입니다. 클러스터 쿼럼 리소스를 소유한 서버에 오류가 발생하면 PAM 역할은 클러스터 쿼럼 리소스 소유권을 취하는 남은 서버로 자동으로 이동됩니다. 또한 유지 관리 또는 업그레이드를 위해 클러스터 쿼럼 리소스를 호스팅하는 서버를 오프라인으로 전환해야 하는 경우 먼저 PAM을 DAG의 다른 서버로 옮겨야 합니다. PAM은 데이터베이스 복사본 간 활성 지정의 모든 움직임을 제어합니다. ((지정된 시간에 오직 하나의 복사본만 활성화 상태일 수 있으며, 해당 복사본은 탑재 또는 분리될 수 있습니다.) PAM은 로컬 시스템에서 SAM 역할의 기능도 수행합니다(로컬 데이터베이스 및 로컬 정보 저장소 오류 검색).

SAM은 사서함 데이터베이스의 활성 복사본을 Active Manager 클라이언트 구성 요소를 실행하는 다른 Exchange 구성 요소에 호스팅하는 서버에 대한 정보를 제공합니다(예: 클라이언트 액세스 또는 전송 서비스). SAM은 로컬 정보 저장소 및 로컬 데이터베이스의 오류를 검색합니다. SAM은 오류에 대처하기 위해 PAM에 장애 조치(failover)를 시작하도록 요청합니다(데이터베이스가 복제되는 경우). SAM은 장애 조치(failover)의 대상을 결정하지 않고, PAM의 데이터베이스 위치 상태도 업데이트하지 않습니다. SAM은 수신하는 활성 데이터베이스 복사본에 대한 쿼리에 응답하기 위해 활성 데이터베이스 복사본 위치 상태에 액세스합니다.


> [!NOTE]
> Exchange 2013은 클러스터된 응용 프로그램이 아닙니다. 대신 클러스터, 그룹, 클러스터 네트워크(하트비트), 노드 관리, 클러스터 레지스트리를 위해 clusapi.dll에 구현된 클러스터 라이브러리 기능과 몇 가지 제어 코드 기능을 사용합니다. 또한 Active Manager는 클러스터 데이터베이스(클러스터 레지스트리라고도 함)에 현재 사서함 데이터베이스 정보를 저장합니다(예: 활성 및 수동 데이터, 탑재된 데이터). 이 정보는 클러스터 데이터베이스에 직접 저장되지만 다른 구성 요소에 의해 직접 액세스되지는 않습니다.



Exchange 2013에서 Microsoft Exchange Replication Service는 탑재된 모든 데이터베이스의 상태를 주기적으로 모니터링합니다. 또한 ESE(Extensible Storage Engine)에서 I/O 오류 또는 장애도 모니터링합니다. 오류가 발견되면 Active Manager에 이를 알립니다. 그러면 Active Manager는 탑재해야 할 데이터베이스 복사본과 이 데이터베이스를 탑재하기 위해 필요한 요소를 결정합니다. 또한 마지막으로 탑재된 데이터베이스 복사본을 바탕으로 사서함 데이터베이스의 활성 복사본을 추적하고, 클라이언트가 연결되는 클라이언트 액세스 서버에 추적 결과 정보를 제공합니다.

## 최적의 복사본 선택

오류가 발생하여 복제된 사서함 데이터베이스의 활성 복사본에 액세스할 수 없으면 Active Manager는 영향을 받을 수 있는 수동 복사본 중에서 활성화하기 가장 적합한 복사본을 선택하여 오류로부터 복구하기 위한 단계를 수행합니다. 이 프로세스를 Exchange 2010에서는 BCS(최적의 복사본 선택)라고 하고 Exchange 2013에서는 BCSS(최적의 복사본 및 서버 선택)라고 합니다. 일반 프로세스는 다음 순서로 발생합니다.

1.  관리되는 가용성 또는 Active Manager가 오류를 감지하거나 관리자가 대상 없는 전환을 시작합니다.

2.  PAM은 BCSS 내부 알고리즘을 실행합니다.

3.  ACLL(*마지막 로그 복사 시도*)이라는 프로세스가 발생하며, 장애 조치 또는 전환 전에 활성 데이터베이스 복사본을 호스트한 서버에서 누락된 로그 파일을 복사하려고 시도합니다.

4.  ACLL 프로세스가 완료된 후에는 데이터베이스 복사본을 호스트하는 사서함 서버의 *AutoDatabaseMountDial* 값과 활성화하려는 데이터베이스의 복사 큐 길이가 비교됩니다. 이 시점에서는 다음 중 하나가 수행됩니다.
    
      - 누락된 로그 파일의 수가 *AutoDatabaseMountDial* 값보다 작거나 같은 경우 5단계가 발생합니다.
    
      - 누락된 로그 파일의 수가 *AutoDatabaseMountDial* 값보다 큰 경우 Active Manager가 사용 가능한 다음 최적의 복사본(있는 경우)을 활성화합니다.

5.  PAM은 RPC(원격 프로시저 호출)를 통해 Microsoft Exchange 정보 저장소에 대한 탑재 요청을 실행합니다. 이 시점에서는 다음 중 하나가 수행됩니다.
    
      - 데이터베이스가 탑재되고 클라이언트가 사용할 수 있습니다.
    
      - 데이터베이스가 탑재되지 않고 PAM은 다음 최상의 복사본(사용 가능한 경우)에서 3단계와 4단계를 수행합니다.

Exchange 2010 BCS 프로세스를 활성화 하려면 최상의 복사본을 확인 하려면 각 데이터베이스 복사본의 여러 측면 들을 계산 합니다. 이러한 포함 합니다.

  - 복사 큐 길이

  - 재생 큐 길이

  - 데이터베이스 상태

  - 콘텐츠 인덱스 상태

Exchange 2013에서 Active Manager는 동일한 BCS 확인 및 단계를 거치지만 상태 순서가 내림차순으로 적용되는 제약 조건을 사용합니다. 특히 BCSS에는 Exchange 2013에서 기본 제공된 관리되는 가용성 모니터링 구성 요소에 속하는 몇 가지 새로운 상태 확인이 포함되어 있습니다. Active Manager가 새로 수행하는 추가 확인은 네 가지이며 아래에 수행되는 순서대로 나열되어 있습니다.

1.  **모두 정상**   모든 모니터링 구성 요소가 정상 상태인 영향을 받은 데이터베이스의 복사본을 호스팅하는 서버가 있는지 확인합니다.

2.  **보통 정상**   보통 우선 순위의 모든 모니터링 구성 요소가 정상 상태인 영향을 받은 데이터베이스의 복사본을 호스팅하는 서버가 있는지 확인합니다.

3.  **모두 원본보다 양호**   모니터링 구성 요소가 영향을 받은 복사본을 호스팅하는 현재 서버보다 양호한 상태인 영향을 받은 데이터베이스의 복사본을 호스팅하는 서버가 있는지 확인합니다.

4.  **원본과 동일**   모니터링 구성 요소가 영향을 받은 복사본을 호스팅하는 현재 서버와 동일한 상태인 영향을 받은 데이터베이스의 복사본을 호스팅하는 서버가 있는지 확인합니다.

모니터링 구성 요소(예: 장애 조치(failover) 응답기)에 의해 트리거된 장애 조치의 결과로 BCSS가 호출되면 대상 서버의 구성 요소 상태가 장애 조치(failover)가 발생한 서버보다 나아야 한다는 추가적인 필수 제약 조건이 적용됩니다. 예를 들어 Microsoft Office Outlook Web App이 장애 조치(failover) 응답기를 통해 장애 조치(failover)를 트리거하면 BCSS는 영향 받은 데이터베이스를 호스트하는 서버 중에서 Outlook Web App이 정상 상태인 서버를 선택해야 합니다.

## 최적의 복사본 선택 프로세스

데이터베이스 장애(프로토콜 장애가 아님)와 관련해서 Exchange 2013의 Active Manager는 Exchange 2010의 경우와 동일한 검사를 수행합니다. Active Manager는 활성화의 잠재적인 후보자인 데이터베이스 복사본의 목록을 만들어 최상의 복사본 선택 프로세스를 시작합니다. 읽을 수 없거나 관리자가 활성화를 차단한 데이터베이스 복사본은 선택 프로세스 중에 무시되며 사용되지 않습니다. 목록의 순서는 *AutoDatabaseMountDial* 값에 따라 다릅니다.

  - *AutoDatabaseMountDial*이 데이터베이스 복사본을 호스트하는 모든 서버에서 `Lossless` 이외의 값으로 구성되면 Active Manager는 복사 큐 길이를 기본 키로 사용하여 결과 목록을 정렬합니다. 이 계산은 LastLogInspected(복사본의 관점에서)를 기반으로 하므로 잠재적인 복사본 목록은 LastLogInspected의 최고값(복사 큐 길이가 가장 짧은 복사본)을 기준으로 정렬됩니다. 필요한 경우 Active Manager는 목록을 한 번 더 정렬합니다. 이때는 둘 이상의 수동 복사본이 동일한 복사 큐 길이를 갖는 결합 상태를 깨뜨리는 보조 키로 활성화 기본 설정 값이 사용됩니다. 가장 낮은 활성화 기본 설정 값을 갖는 복사본이 목록에서 가장 높은 우선 순위를 갖습니다.

  - *AutoDatabaseMountDial*을 데이터베이스 복사본을 호스팅하는 모든 서버에서 `Lossless` 값으로 구성하면 Active Manager는 활성화 기본 설정 값을 기본 키로 사용하여 결과 목록을 오름차순으로 정렬합니다. 또한 관리자가 대상을 지정하지 않고 무손실 서버 또는 데이터베이스 전환을 수행할 경우에도 Active Manager는 활성화 기본 설정 값을 기본 키로 사용하여 결과 목록을 오름차순으로 정렬합니다.

다음으로, Active Manager는 상태가 Healthy, DisconnectedAndHealthy, DisconnectedAndResynchronizing 또는 SeedingSource인 사서함 데이터베이스 복사본을 목록에서 찾은 다음, 10개의 조건 순서 집합을 사용하여 목록에서 각 복사본의 활성화 가능성을 평가합니다. Active Manager는 활성화 후보자가 조건의 첫 번째 집합을 충족하는지 확인합니다.

  - 상태가 Healthy이며 콘텐츠 인덱스가 있음

  - 복사 큐 길이가 로그 파일 10개 미만임

  - 재생 큐 길이가 로그 파일 50개 미만임

조건의 첫 번째 집합을 충족하는 데이터베이스 복사본이 없는 경우 Active Manager는 조건의 두 번째 집합을 충족하는 데이터베이스 복사본을 찾습니다.

  - 상태가 Crawling이며 콘텐츠 인덱스가 있음

  - 복사 큐 길이가 로그 파일 10개 미만임

  - 재생 큐 길이가 로그 파일 50개 미만임

조건의 두 번째 집합을 충족하는 데이터베이스 복사본이 없는 경우 Active Manager는 조건의 세 번째 집합을 충족하는 데이터베이스 복사본을 찾습니다.

  - 상태가 Healthy이며 콘텐츠 인덱스가 있음

  - 재생 큐 길이가 로그 파일 50개 미만임

조건의 세 번째 집합을 충족하는 데이터베이스 복사본이 없는 경우 Active Manager는 조건의 네 번째 집합을 충족하는 데이터베이스 복사본을 찾습니다.

  - 상태가 Crawling이며 콘텐츠 인덱스가 있음

  - 재생 큐 길이가 로그 파일 50개 미만임

조건의 네 번째 집합을 충족하는 데이터베이스 복사본이 없는 경우 Active Manager는 조건의 다섯 번째 집합을 충족하는 데이터베이스 복사본을 찾습니다.

  - 재생 큐 길이가 로그 파일 50개 미만임

조건의 다섯 번째 집합을 충족하는 데이터베이스 복사본이 없는 경우 Active Manager는 조건의 여섯 번째 집합을 충족하는 데이터베이스 복사본을 찾습니다.

  - 상태가 Healthy이며 콘텐츠 인덱스가 있음

  - 복사 큐 길이가 로그 파일 10개 미만임

여섯 번째 조건을 충족하는 데이터베이스 복사본이 없는 경우 Active Manager는 조건의 일곱 번째 집합을 충족하는 데이터베이스 복사본을 찾습니다.

  - 상태가 Crawling이며 콘텐츠 인덱스가 있음

  - 복사 큐 길이가 로그 파일 10개 미만임

조건의 일곱 번째 집합을 충족하는 데이터베이스 복사본이 없는 경우 Active Manager는 조건의 여덟 번째 집합을 충족하는 데이터베이스 복사본을 찾습니다.

  - 상태가 Healthy이며 콘텐츠 인덱스가 있음

조건의 여덟 번째 집합을 충족하는 데이터베이스 복사본이 없는 경우 Active Manager는 조건의 아홉 번째 집합을 충족하는 데이터베이스 복사본을 찾습니다.

  - 상태가 Crawling이며 콘텐츠 인덱스가 있음

조건의 아홉 번째 집합을 충족하는 데이터베이스 복사본이 없는 경우 Active Manager는 상태가 Healthy, DisconnectedAndHealthy, DisconnectedAndResynchronizing 또는 SeedingSource(조건의 열 번째 집합)인 아무 데이터베이스 복사본이나 활성화합니다. 조건의 열 번째 집합을 충족하는 데이터베이스 복사본을 찾을 수 없는 경우 데이터베이스 복사본을 자동으로 활성화할 수 없습니다.

하나 이상의 조건 집합을 충족하는 하나 이상의 복사본을 찾은 후에는 ACLL 프로세스가 실행되어 로그 파일을 원래 원본에서 잠재적인 새 활성 복사본으로 복사합니다. ACLL 프로세스가 완료된 후에는 PAM은 탑재 요청을 발행하여 데이터베이스가 탑재되고 클라이언트가 사용할 수 있거나 데이터베이스가 탑재되지 않고 PAM은 다음 최상의 복사본(사용 가능한 경우)을 검색합니다.
