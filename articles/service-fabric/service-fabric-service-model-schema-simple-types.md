---
title: Azure Service Fabric 서비스 모델 XML 스키마 단순 형식 | Microsoft Docs
description: Service Fabric 서비스 모델 XML 스키마의 단순 형식을 설명합니다.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/06/2018
ms.author: ryanwi
ms.openlocfilehash: 385d950498730af6c665270111f0206cda1c7315
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809219"
---
<!-- This article was generated by the Python script found in the service-fabric-service-model-schema.md file -->

# <a name="service-model-xml-schema-simple-types"></a>서비스 모델 XML 스키마 단순 형식

## <a name="fabricuri-simpletype"></a>FabricUri simpleType
Microsoft Azure Service Fabric 명명 시스템에서 서비스의 안정적 ID로 사용하는 URI입니다. 

### <a name="xml-source"></a>XML 원본
```xml
<xs:simpleType xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="FabricUri">
    <xs:annotation>
      <xs:documentation>A URI that is used as a stable identifier of services by Microsoft Azure Service Fabric Naming system. </xs:documentation>
    </xs:annotation>
    <xs:restriction base="xs:anyURI"/>
  </xs:simpleType>
  
```
