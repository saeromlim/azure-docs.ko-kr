---
title: LUIS 미리 빌드된 엔터티 phone number 참조 - Azure | Microsoft Docs
titleSuffix: Azure
description: 이 문서에는 LUIS(Language Understanding)의 phone number 미리 빌드된 엔터티가 포함됩니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: bacb09b50c4b1d82daa6be1ef4fc79c88269cf7a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035456"
---
# <a name="phonenumber-entity"></a>Phonenumber 엔터티
`phonenumber` 엔터티는 국가 코드를 포함하는 다양한 전화 번호를 추출합니다. 이 엔터티를 이미 학습했기 때문에 응용 프로그램에 예제 발언을 추가할 필요가 없습니다. `phonenumber` 엔터티는 `en-us` 문화권에서만 지원됩니다. 

## <a name="types-of-phonenumber"></a>Phonenumber 유형
Phonenumber는 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) Github 리포지토리에서 관리됩니다.

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>미리 빌드된 phonenumber 엔터티의 해결
다음 예제에서는 **builtin.phonenumber** 엔터티의 해결을 보여 줍니다.

```JSON
{
  "query": "my mobile is 00 44 161 1234567",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>다음 단계

[percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) 및 [temperature](luis-reference-prebuilt-temperature.md) 엔터티에 대해 알아봅니다. 