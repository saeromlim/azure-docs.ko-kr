---
title: Azure IoT Edge 모듈 구성 | Microsoft Docs
description: 배포 매니페스트에서 배포할 모듈을 선언하는 방법, 배포하는 방법 및 이들 간에 메시지 경로를 만드는 방법을 알아봅니다.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a65eb029dbf10b194bd28bf7ad82f5aa839338a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990623"
---
# <a name="learn-how-to-use-deployment-manifests-to-deploy-modules-and-establish-routes"></a>배포 매니페스트를 사용하여 모듈을 배포하고 경로를 설정하는 방법 알아보기

각 IoT Edge 장치는 $edgeAgent 및 $edgeHub라는 두 개 이상의 모듈을 실행합니다. 그러면 IoT Edge 런타임을 구성합니다. 해당하는 두 개의 표준 외에도 IoT Edge 장치는 여러 모듈을 실행하여 개수에 관계 없이 프로세스를 수행할 수 있습니다. 장치에 이러한 모든 모듈을 한 번에 배포하는 경우 포함되는 모듈 및 서로 상호 작용하는 방법을 선언하는 방법이 필요합니다. 

*배포 매니페스트*는 다음 항목을 설명하는 JSON 문서입니다.

* 각 모듈에 대한 컨테이너 이미지, 개인 컨테이너 레지스트리에 액세스하는 자격 증명 및 각 모듈이 생성 및 관리되어야 하는 방법에 대한 지침을 포함하는 Edge 에이전트의 구성입니다.
* 모듈 간 및 결국 IoT Hub에 대한 메시지 흐름 방법을 포함하는 Edge 허브의 구성
* 경우에 따라 모듈 쌍의 desired 속성입니다.

모든 IoT Edge 장치는 배포 매니페스트로 구성해야 합니다. 새로 설치된 IoT Edge 런타임은 유효한 매니페스트로 구성될 때까지 오류 코드를 보고합니다. 

Azure IoT Edge 자습서에서는 Azure IoT Edge 포털의 마법사를 통해 배포 매니페스트를 빌드합니다. REST 또는 IoT Hub 서비스 SDK를 사용하여 프로그래밍 방식으로 배포 매니페스트를 적용할 수도 있습니다. 자세한 내용은 [IoT Edge 배포에 대한 이해][lnk-deploy]를 참조하세요.

## <a name="create-a-deployment-manifest"></a>배포 매니페스트 만들기

상위 수준에서 배포 매니페스트는 IoT Edge 장치에 배포된 IoT Edge 모듈에 대해 모듈 쌍의 원하는 속성을 구성합니다. 이러한 모듈 중 두 개, `$edgeAgent` 및 `$edgeHub`는 항상 존재합니다.

IoT Edge 런타임(에이전트 및 허브)만 포함하는 배포 매니페스트가 유효합니다.

매니페스트는 다음과 같은 구조를 따릅니다.

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": {
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "{module1}": {  // optional
            "properties.desired": {
                // desired properties of module with id {module1}
            }
        },
        "{module2}": {  // optional
            ...
        },
        ...
    }
}
```

## <a name="configure-modules"></a>모듈 구성

IoT Edge 런타임에 배포에서 모듈을 설치하는 방법을 알려야 합니다. 모든 모듈에 대한 구성 및 관리 정보는 **$edgeAgent** 원하는 속성 내에 포함됩니다. 이 정보에는 Edge 에이전트 자체에 대한 구성 매개 변수가 포함됩니다. 

포함될 수 있거나 포함되어야 하는 속성의 전체 목록은 [Edge 에이전트 및 Edge 허브의 속성](module-edgeagent-edgehub.md)을 참조하세요.

$edgeAgent 속성은 다음과 같은 구조를 따릅니다.

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "{module1}": { // optional
                // configuration and management details
            },
            "{module2}": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>경로 선언

Edge 허브는 모듈 간 및 모듈과 IoT Hub 간에 메시지를 선언적으로 라우팅하는 방법을 제공합니다. Edge 허브는 모든 통신을 관리합니다. 따라서 경로 정보는 **$edgeHub** 원하는 속성 내에 포함됩니다. 동일한 배포 내에 여러 경로가 있을 수 있습니다.

경로는 다음과 같은 구문을 사용하여 **$edgeHub** 원하는 속성에 선언됩니다.

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "{route1}": "FROM <source> WHERE <condition> INTO <sink>",
            "{route2}": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

모든 경로에는 원본 및 싱크가 필요하지만 조건은 메시지를 필터링하는 데 사용할 수 있는 선택적 요소입니다. 


### <a name="source"></a>원본
원본은 메시지가 발생한 위치를 지정합니다. 다음 값 중 하나일 수 있습니다.

| 원본 | 설명 |
| ------ | ----------- |
| `/*` | 모든 장치 또는 모듈의 모든 장치-클라우드 메시지 |
| `/messages/*` | 일부 출력을 통하거나 어떠한 출력도 없이 장치 또는 모듈에서 보낸 모든 장치-클라우드 메시지 |
| `/messages/modules/*` | 일부 출력을 통하거나 어떠한 출력도 없이 모듈에서 보낸 모든 장치-클라우드 메시지 |
| `/messages/modules/{moduleId}/*` | 어떠한 출력도 없이 {moduleId}에서 보낸 모든 장치-클라우드 메시지 |
| `/messages/modules/{moduleId}/outputs/*` | 일부 출력에서 {moduleId}에서 보낸 모든 장치-클라우드 메시지 |
| `/messages/modules/{moduleId}/outputs/{output}` | {output}을 사용하여 {moduleId}에서 보낸 모든 장치-클라우드 메시지 |

### <a name="condition"></a>조건
조건은 경로 선언의 선택 사항입니다. 싱크에서 원본으로 모든 메시지를 전달하려는 경우 전체 **WHERE** 절을 그대로 둡니다. 또는 [IoT Hub 쿼리 언어][lnk-iothub-query]를 사용하여 조건을 만족하는 특정 메시지 또는 메시지 유형에 대해 필터링할 수 있습니다.

IoT Edge의 모듈 간에 전달되는 메시지는 장치와 Azure IoT Hub 간에 전달되는 메시지와 동일한 서식이 지정됩니다. 모든 메시지는 JSON으로 서식이 지정되고 **systemProperties**, **appProperties** 및 **body** 매개 변수를 포함합니다. 

다음 구문을 사용하여 세 가지 매개 변수에 쿼리를 빌드할 수 있습니다. 

* 시스템 속성: `$<propertyName>` 또는 `{$<propertyName>}`
* 응용 프로그램 속성: `<propertyName>`
* 본문 속성: `$body.<propertyName>` 

메시지 속성에 대한 쿼리를 만드는 방법에 대한 예제는 [장치-클라우드 메시지 경로에 대한 쿼리 식](../iot-hub/iot-hub-devguide-routing-query-syntax.md)을 참조하세요.

IoT Edge에 특정되는 예제는 리프 장치에서 게이트웨이 장치에 도착하는 메시지를 필터링하려는 경우입니다. 모듈에서 발생한 메시지에는 **connectionModuleId**라는 시스템 속성이 포함되어 있습니다. 따라서 리프 장치에서 IoT Hub로 직접 메시지를 라우팅하려는 경우 다음 경로를 사용하여 모듈 메시지를 제외합니다.

```sql
FROM /messages/\* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>sink
싱크는 메시지를 보낼 위치를 정의합니다. 다음 값 중 하나일 수 있습니다.

| sink | 설명 |
| ---- | ----------- |
| `$upstream` | 메시지를 IoT Hub로 보냅니다. |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | 메시지를 `{moduleId}` 모듈의 `{input}` 입력으로 보냅니다. |

IoT Edge는 최소 한 번의 보장을 제공합니다. Edge 허브는 경로에서 해당 싱크로 메시지를 전달할 수 없는 경우 로컬로 메시지를 저장합니다. 예를 들어 Edge 허브에서 IoT Hub에 연결할 수 없거나 대상 모듈이 연결되지 않은 경우입니다.

Edge 허브는 [Edge 허브 원하는 속성](module-edgeagent-edgehub.md)의 `storeAndForwardConfiguration.timeToLiveSecs` 속성에서 지정된 시간까지 메시지를 저장합니다.

## <a name="define-or-update-desired-properties"></a>원하는 속성 정의 또는 업데이트 

배포 매니페스트는 IoT Edge 장치에 배포된 각 모듈의 모듈 쌍에 원하는 속성을 지정할 수 있습니다. desired 속성이 배포 매니페스트에 지정되면 현재 모듈 쌍에 있는 모든 desired 속성을 덮어씁니다.

배포 매니페스트에서 모듈 쌍의 desired 속성을 지정하지 않으면, IoT Hub는 어떤 방식으로든 모듈 쌍을 수정하지 않고 desired 속성을 프로그래밍 방식으로 설정할 수 있습니다.

장치 쌍을 수정할 수 있게 해주는 동일한 메커니즘이 모듈 쌍을 수정하는 데 사용됩니다. 자세한 내용은 [장치 쌍 개발자 가이드](../iot-hub/iot-hub-devguide-device-twins.md)를 참조하세요.   

## <a name="deployment-manifest-example"></a>배포 매니페스트 예제

다음은 배포 매니페스트 JSON 문서의 예제입니다.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "{password}",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": ""
            }
          }
        },
        "modules": {
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

* $edgeAgent 및 $edgeHub에 포함될 수 있거나 포함되어야 하는 속성의 전체 목록은 [Edge 에이전트 및 Edge 허브의 속성](module-edgeagent-edgehub.md)을 참조하세요.

* 이제 IoT Edge 모듈을 사용하는 방법을 알았으므로 [IoT Edge 모듈 개발을 위한 요구 사항 및 도구에 대해 알아봅니다][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-routing-query-syntax.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
