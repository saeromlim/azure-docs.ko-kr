---
title: 포함 파일
description: 포함 파일
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 08/31/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 71294824bd3dd5215c388cfcd44382c7eee123ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48874053"
---
| 리소스 | 기본 제한 |
| --- | :--- |
| 구독당 최대 클러스터 | 100 |
| 클러스터당 최대 노드 | 100 |
| 노드당 최대 포드: Kubenet과 [기본 네트워킹][basic-networking] | 110 |
| 노드당 최대 포드: Azure CNI와 [고급 네트워킹][advanced-networking] | Azure CLI 배포: 30<sup>1</sup><br />Resource Manager 템플릿: 30<sup>1</sup><br />포털 배포: 30 |

<sup>1</sup> Azure CLI 또는 Resource Manager 템플릿을 사용하여 AKS 클러스터를 배포할 때 클러스터 배포에서 이 값을 구성할 수 있습니다.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
