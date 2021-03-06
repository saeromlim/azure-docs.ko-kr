---
title: Azure Alerts의 로그 경고에 대한 웹후크 작업
description: 이 문서에서는 Log Analytics 또는 Application Insights를 사용하는 로그 경고 규칙이 HTTP 웹후크 및 다른 가능한 사용자 지정의 세부 정보로 데이터를 푸시하는 방법을 설명합니다.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: f5f8ed885791a648f30790434be56d966bbf2e47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989297"
---
# <a name="webhook-actions-for-log-alert-rules"></a>로그 경고 규칙에 대한 웹후크 작업
[Azure에서 로그 경고를 생성](alert-log.md)할 때 하나 이상의 작업을 수행하도록 선택적으로 [작업 그룹 사용을 구성](monitoring-action-groups.md)할 수 있습니다.  이 문서에서는 사용 가능한 다양한 웹후크 작업 및 사용자 지정 JSON 기반 웹후크 구성에 대해 설명합니다.


## <a name="webhook-actions"></a>웹후크 작업

웹후크 작업을 사용하여 단일 HTTP POST 요청을 통해 외부 프로세스를 호출할 수 있습니다.  호출 중인 서비스는 웹후크를 지원하고 수신하는 페이로드를 사용하는 방법을 결정해야 합니다.    

웹후크 작업에는 다음 표의 속성이 필요합니다.

| 자산 | 설명 |
|:--- |:--- |
| Webhook URL |웹후크의 URL입니다. |
| 사용자 지정 JSON 페이로드 |경고를 만드는 동안 이 옵션을 선택한 경우 웹후크와 함께 전송할 사용자 지정 페이로드입니다. [로그 경고 관리](alert-log.md)에서 사용 가능한 세부사항 |

> [!NOTE]
> 로그 경고의 *웹후크에 대한 사용자 정의 JSON 페이로드 포함* 옵션과 함께 웹 후크 보기 단추를 누르면 제공된 사용자 정의용 샘플 웹후크 페이로드가 표시됩니다. 로그 경고에 사용된 JSON 스키마의 실제 데이터 및 표현은 포함되지 않습니다. 

웹후크는 URL 및 외부 서비스에 보낸 데이터인 JSON 형식의 페이로드를 포함하고 있습니다.  기본적으로 페이로드에는 다음 표의 값이 포함됩니다. 이 페이로드를 자체적인 사용자 지정 페이로드로 바꾸도록 선택할 수 있습니다.  그러한 경우 각 매개 변수에 대한 표의 변수를 사용하여 해당 값을 사용자 지정 페이로드에 포함할 수 있습니다.


| 매개 변수 | 변수 | 설명 |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |경고 규칙의 이름입니다. |
| 심각도 |#severity |실행된 로그 경고에 대해 설정된 심각도입니다. |
| AlertThresholdOperator |#thresholdoperator |경고 규칙에 대한 임계값 연산자입니다.  *보다 큼* 또는 *보다 작음*. |
| AlertThresholdValue |#thresholdvalue |경고 규칙에 대한 임계값입니다. |
| LinkToSearchResults |#linktosearchresults |경고를 생성한 쿼리에서 레코드를 반환하는 Analytics 포털의 링크입니다. |
| ResultCount |#searchresultcount |검색 결과의 레코드 수입니다. |
| 검색 간격 종료 시간 |#searchintervalendtimeutc |쿼리의 종료 시간(UTC)으로, mm/dd/yyyy HH:mm:ss AM/PM 형식입니다. |
| 검색 간격 |#searchinterval |경고 규칙의 시간으로, HH:mm:ss 형식입니다. |
| 검색 간격 시작 시간 |#searchintervalstarttimeutc |쿼리의 시작 시간(UTC)으로, mm/dd/yyyy HH:mm:ss AM/PM 형식입니다. 
| SearchQuery |#searchquery |경고 규칙에서 사용하는 로그 검색 쿼리입니다. |
| SearchResults |"IncludeSearchResults": true|“IncludeSearchResults”:true를 사용자 지정 JSON 웹후크 정의에 최상위 속성으로 추가할 경우 쿼리에서 JSON 테이블로 반환되는 레코드이며, 처음 1,000개 레코드로 제한됩니다. |
| WorkspaceID |#workspaceid |Log Analytics 작업 영역의 ID입니다. |
| 응용 프로그램 UI |#applicationid |Application Insight 앱의 ID입니다. |
| 구독 ID |#subscriptionid |Application Insights에서 사용되는 Azure 구독의 ID입니다. 

> [!NOTE]
> LinkToSearchResults는 분석 섹션에서 보기 위해 URL의 SearchQuery, 검색 간격 시작 시간 및 검색 간격 종료 시간 같은 매개 변수를 Azure Portal에 전달합니다. Azure Portal에는 약 2,000자라는 URI 크기 제한이 있으며 매개 변수 값이 해당 제한을 초과하는 경우 경고에 제공된 링크를 열지 *않습니다*. 사용자는 세부 정보를 수동으로 입력하여 Analytics 포털에서 결과를 보거나, [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) 또는 [Log Analytics REST API](https://dev.loganalytics.io/reference)를 사용하여 결과를 프로그래밍 방식으로 검색할 수 있습니다. 

예를 들어 *text*라는 단일 매개변수를 포함하는 다음과 같은 사용자 지정 페이로드를 지정할 수 있습니다.  이 웹후크가 호출하는 서비스는 이 매개 변수를 예상합니다.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
이 예제 페이로드는 웹후크에 보낼 때 다음과 같은 내용으로 확인될 수 있습니다.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
사용자 지정 웹후크의 모든 변수는 "#searchinterval"처럼 JSON 엔클로저 내에 지정되어야 하므로 그 결과로 생기는 웹후크 역시 "00:05:00"처럼 엔클로저 내의 변수 데이터를 갖습니다.

사용자 지정 페이로드에 검색 결과를 포함하려면 **IncudeSearchResults**가 json 페이로드에 최상위 속성으로 설정되어 있는지 확인합니다. 

## <a name="sample-payloads"></a>샘플 페이로드
이 섹션에서는 페이로드가 표준인 경우와 사용자 지정인 경우를 포함하여 로그 경고에 대한 웹후크의 샘플 페이로드를 보여 줍니다.

> [!NOTE]
> 이전 버전과의 호환성을 보장하기 위해 Azure Log Analytics를 사용하는 경고에 대한 표준 웹후크 페이로드는 [Log Analytics 경고 관리](../log-analytics/log-analytics-alerts-creating.md)와 같습니다. 그러나 [Application Insights](../application-insights/app-insights-analytics.md)를 사용하는 로그 경고에 대한 표준 웹후크 페이로드는 작업 그룹 스키마를 기반으로 합니다.

### <a name="standard-webhook-for-log-alerts"></a>로그 경고에 대한 표준 웹후크 
이 두 가지 예제에서 모두, 열과 행이 각각 두 개만 포함된 더미 페이로드를 언급했습니다.

#### <a name="log-alert-for-azure-log-analytics"></a>Azure Log Analytics에 대한 로그 경고
다음은 Log Analytics 기반 경고에 사용할 경우 *사용자 지정 JSON 옵션이 없는* 표준 웹후크 작업의 샘플 페이로드입니다.

```json
{
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
 }
 ```   

#### <a name="log-alert-for-azure-application-insights"></a>Azure Application Insights에 대한 로그 경고
다음은 Application Insights 기반 로그 경고에 사용할 경우 *사용자 지정 JSON 옵션이 없는* 표준 웹후크의 샘플 페이로드입니다.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Error",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>사용자 지정 JSON 페이로드가 있는 로그 경고
예를 들어 경고 이름과 검색 결과만 포함하는 사용자 지정 페이로드를 만들려면 다음을 사용할 수 있습니다. 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

다음은 로그 경고에 대한 사용자 지정 웹후크 작업의 샘플 페이로드입니다.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>다음 단계
- [Azure Alerts의 로그 경고](monitor-alerts-unified-log.md)에 대해 알아봅니다.
- [Azure에서 로그 경고 관리](alert-log.md)를 이해합니다.
- [Azure에서 작업 그룹](monitoring-action-groups.md) 만들기 및 관리
- [Application Insights](../application-insights/app-insights-analytics.md)에 대해 자세히 알아보기
- [Log Analytics](../log-analytics/log-analytics-overview.md)에 대해 자세히 알아보기 