---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e80033d696de1b83da43fc27e5be9eca3b3f8757
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44169083"
---
## <a name="create-a-device-identity"></a>장치 ID 만들기

이 섹션에서는 Azure CLI를 사용하여 이 자습서의 장치 ID를 만듭니다. Azure CLI는 [Azure Cloud Shell](https://docs.microsoft.com/zure/cloud-shell/overview)에 사전 설치되어 있거나 [로컬로 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)할 수 있습니다. 장치 ID는 대/소문자를 구분합니다.

1. Azure CLI를 사용하여 IoT 확장을 설치하는 명령줄 환경에서 다음 명령을 실행합니다.

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. Azure CLI를 로컬로 실행하는 경우 다음 명령을 사용하여 Azure 계정에 로그인합니다(Cloud Shell을 사용하는 경우 자동으로 로그인되므로 이 명령을 실행할 필요가 없음).

    ```cmd/sh
    az login
    ```

1. 마지막으로 `myDeviceId`라는 새 장치 ID를 만들고 다음 명령으로 장치 연결 문자열을 검색합니다.

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

결과에서 장치 연결 문자열을 기록해 둡니다. 이 장치 연결 문자열은 장치 앱이 장치로 IoT Hub에 연결하는 데 사용됩니다.

<!-- images and links -->
