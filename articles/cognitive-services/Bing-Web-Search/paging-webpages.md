---
title: Bing Web Search API 결과를 통해 페이징하는 방법 | Microsoft Docs
description: Bing Web Search API 결과를 통해 페이징하는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 08/20/2018
ms.author: erhopf
ms.openlocfilehash: cd03b3af08746674dd2ba2d4af593e19e066efca
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888244"
---
# <a name="how-to-page-through-bing-web-search-api-results"></a>Bing Web Search API 결과를 통해 페이징하는 방법

Web Search API를 호출하면 Bing이 결과 목록을 반환합니다. 이 목록은 쿼리와 관련된 총 결과 수의 하위 집합입니다. 예상되는 총 사용 가능한 결과 수를 가져오려면 응답 개체의 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#totalestimatedmatches) 필드에 액세스해야 합니다.  
  
다음 예제는 웹 응답에 포함되는 `totalEstimatedMatches` 필드를 보여줍니다.  
  
```
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```
  
사용 가능한 웹 페이지를 페이징하려면 [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#count) 및 [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#offset) 쿼리 매개 변수를 사용합니다.  
  
`count` 매개 변수는 응답에서 반환할 결과의 수를 지정합니다. 응답에서 요청할 수 있는 최대 결과 수는 50개입니다. 기본값은 10입니다. 제공되는 실제 수는 요청된 수보다 작을 수 있습니다.

`offset` 매개 변수는 건너뛸 결과의 수를 지정합니다. `offset`은 0부터 시작하며 (`totalEstimatedMatches` - `count`)보다 작아야 합니다.  
  
페이지당 15개의 이미지를 표시하려면 `count`를 15로, `offset`을 0으로 설정하여 결과의 첫 번째 페이지를 가져오면 됩니다. 페이지가 넘어갈 때마다 `offset`이 15씩 증가합니다(예: 15, 30).  
  
다음은 오프셋 45에서 시작하여 웹 페이지 15개를 요청하는 예제입니다.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

기본 `count` 값이 구현에서 작동하는 경우 `offset` 쿼리 매개 변수를 지정하기만 하면 됩니다.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Web Search API는 웹 페이지를 포함하는 결과를 반환하며 이미지, 비디오 및 뉴스를 포함할 수 있습니다. 검색 결과를 페이징하는 것은 이미지나 뉴스 같은 다른 응답이 아닌 [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) 응답을 페이징하는 것입니다. 예를 들어 `count`를 50으로 설정한 경우 50개 웹 페이지가 표시되지만, 응답에 다른 답변에 대한 결과도 포함될 수 있습니다. 예를 들어 응답에 이미지 15개와 뉴스 기사 4개가 포함될 수 있습니다. 또한 결과의 첫 번째 페이지에는 뉴스가 있지만 두 번째 페이지에는 뉴스가 없는 경우 또는 그 반대의 경우가 발생할 수 있습니다.   
    
`responseFilter` 쿼리 매개 변수를 지정하고 필터 목록에 웹 페이지를 포함하지 않는 경우 `count` 및 `offset` 매개 변수를 사용하지 마세요.  
