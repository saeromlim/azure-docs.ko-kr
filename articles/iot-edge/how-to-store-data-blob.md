---
title: Azure IoT Edge 장치의 Azure Blob Storage | Microsoft Docs
description: 에지에 데이터를 저장하도록 IoT Edge 장치에 Azure Blob Storage 모듈을 배포합니다.
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 09/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b9e48eba4b46f024b056fe53b3b3df24feec802e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995672"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>IoT Edge(미리 보기)에서 Azure Blob Storage를 사용하여 에지에 데이터 저장

IoT Edge의 Azure Blog Storage는 에지에 블록 Blob 저장소 솔루션을 제공합니다. IoT Edge 장치의 Blob 저장소 모듈은 Azure Blob 서비스처럼 동작하지만, 블록 Blob은 IoT Edge 장치에 로컬로 저장됩니다. 동일한 Azure 저장소 SDK 메서드 또는 이미 익숙한 블록 Blob API 호출을 사용하여 Blob에 액세스할 수 있습니다. 

이 문서에서는 IoT Edge 장치에서 Blob service를 실행하는 Azure Blob Storage를 IoT Edge 컨테이너에 배포하기 위한 지침을 제공합니다. 

>[!NOTE]
>IoT Edge의 Azure Blob Storage는 현재 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)입니다. 

## <a name="prerequisites"></a>필수 조건

Azure IoT Edge 장치:

* [Linux](quickstart-linux.md) 또는 [Windows 장치](quickstart.md)의 빠른 시작에 설명된 단계에 따라 개발 머신 또는 가상 머신을 Edge 장치로 사용할 수 있습니다.
* IoT Edge 모듈의 Azure Blob Storage는 다음 장치 구성을 지원합니다.

   | 운영 체제 | 아키텍처 |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Core(10월 업데이트) | AMD64 |
   | Windows 10 IoT Enterprise(10월 업데이트) | AMD64 |
   | Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

클라우드 리소스:

* Azure의 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md). 


## <a name="deploy-blob-storage-to-your-device"></a>장치에 Blob 저장소 배포

IoT Edge의 Azure Blob Storage는 세 개의 표준 컨테이너 이미지를 제공하는데, 그 중 두 개는 Linux 컨테이너(AMD64 및 ARM32 아키텍처)용이고 하나는 Windows 컨테이너(AMD64)용입니다. 이러한 모듈 이미지 중 하나를 사용하여 IoT Edge 장치에 Blob 저장소를 배포할 때 장치의 모듈 인스턴스를 구성하기 위한 다음 세 가지 정보를 제공해야 합니다.

* **계정 이름** 및 **계정 키**. Azure Storage와의 일관성을 유지하기 위해, Blob 저장소 모듈은 계정 이름 및 계정 키를 사용하여 액세스를 관리합니다. 계정 이름은 3~24자 사이여야 하고 소문자와 숫자만 사용해야 합니다. 계정 키는 base64로 인코딩된 64바이트여야 합니다. [GeneratePlus](https://generate.plus/en/base64) 같은 도구를 사용하여 키를 생성할 수 있습니다.
* **로컬 저장소 옵션**. Blob 저장소 모듈은 Blob을 IoT Edge 장치에 로컬로 저장하므로, 모듈이 중지되거나 다시 시작되더라도 Blob이 유지됩니다. 장치에서 Blob을 저장할 기존 [볼륨](https://docs.docker.com/storage/volumes
) 또는 로컬 폴더 경로를 선언하세요. 

IoT Edge 장치에 모듈을 배포하는 여러 가지 방법이 있는데, 모든 방법이 IoT Edge 모듈의 Azure Blob Storage에서 작동합니다. 가장 간단한 두 가지 방법은 Azure Portal 또는 Visual Studio Code 템플릿을 사용하는 것입니다. 

### <a name="azure-portal"></a>Azure portal

Azure Portal 통해 Blob 저장소를 배포하려면 [Azure Portal에서 Azure IoT Edge 모듈 배포](how-to-deploy-modules-portal.md)의 단계를 따릅니다. 모듈을 배포하기 전에, 컨테이너 운영 체제에 따라 이미지 URI를 복사하고 컨테이너 만들기 옵션을 준비합니다. 배포 문서의 **배포 매니페스트 구성** 섹션에 제공된 값을 사용합니다. 

Blob 저장소 모듈에 대한 이미지 URI **mcr.microsoft.com/azure-blob-storage**를 제공합니다. 
   
**컨테이너 만들기 옵션** 필드에 다음 JSON 템플릿을 사용합니다. 저장소 계정 이름, 저장소 계정 키 및 저장소 디렉터리 바인딩을 사용하여 JSON을 구성합니다.  
   
   ```json
   {
       "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
       ],
       "HostConfig":[
           "Binds":[
               "<storage directory bind>"
           ],
           "PortBindings":{
               "11002/tcp":[{"HostPort":"11002"}]
           }
       ]
   }
   ```   
   
만들기 옵션 JSON에서 `\<your storage account name\>`을 원하는 이름으로 업데이트합니다. `\<your storage account key\>`를 64바이트 base64 키로 업데이트합니다. 바이트 길이를 선택할 수 있는 [GeneratePlus](https://generate.plus/en/base64) 같은 도구를 사용하여 키를 생성할 수 있습니다. 다른 모듈에서 Blob 저장소에 액세스하려면 이러한 자격 증명을 사용합니다.

만들기 옵션 JSON에서 컨테이너 운영 체제에 따라 `<storage directory bind>`를 업데이트합니다. IoT Edge 장치에서 Blob 모듈이 데이터를 저장할 [볼륨](https://docs.docker.com/storage/volumes/) 이름 또는 디렉터리 절대 경로를 제공합니다.  

   * Linux 컨테이너: **\<storage path>:/blobroot**. 예: /srv/containerdata:/blobroot. 또는 my-volume:/blobroot. 
   * Windows 컨테이너: **\<storage path>:C:/BlobRoot**. 예: C:/ContainerData:C:/BlobRoot. 또는 my-volume:C:/blobroot. 


IoT Edge의 Azure Blob Storage에 액세스하기 위해 레지스트리 자격 증명을 제공할 필요가 없으며, 배포에 대한 경로를 선언할 필요가 없습니다. 

### <a name="visual-studio-code-templates"></a>Visual Studio Code 템플릿

Azure IoT Edge는 Visual Studio Code에 에지 솔루션 개발을 도와주는 템플릿을 제공합니다. 다음 단계를 수행하려면 개발 머신에 [Visual Studio Code](https://code.visualstudio.com/)가 설치되어 있고 [Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)이 구성되어 있어야 합니다.

다음 단계에 따라 Blob 저장소 모듈을 사용하여 새 IoT Edge 솔루션을 만들고, 배포 매니페스트를 구성합니다. 

1. **보기** > **명령 팔레트**를 선택합니다. 

2. 명령 팔레트에서 **Azure IoT Edge: 새로운 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 

3. 프롬프트에 따라 새 솔루션을 만듭니다. 

   1. **폴더 선택** - 새 솔루션을 만들 폴더로 이동합니다.  
   
   2. **솔루션 이름 제공** - 솔루션 이름을 제공하거나 기본값을 그대로 적용합니다.
   
   3. **모듈 템플릿 선택** - **기존 모듈**을 선택합니다(전체 이미지 URL 입력).
   
   4. **모듈 이름 제공** - 인식 가능한 모듈 이름을 입력합니다(예: **azureBlobStorage**).
   
   5. **모듈의 Docker 이미지 제공** - 이미지 URI **mcr.microsoft.com/azure-blob-storage**를 제공합니다.

VS Code는 입력한 정보를 사용하여 IoT Edge 솔루션을 만든 다음, 새 창에서 로드합니다. 

솔루션 템플릿은 Blob 저장소 모듈 이미지를 포함하는 배포 매니페스트 템플릿을 만들지만, 모듈의 만들기 옵션을 구성해야 합니다. 

1. 새 솔루션 작업 영역에서 **deployment.template.json**을 열고 **모듈** 섹션을 찾습니다. 

2. 이 배포에 필요 없는 **tempSensor** 모듈을 삭제합니다. 

3. 다음 코드를 복사하여 Blob 저장소 모듈의 **createOptions** 필드에 붙여넣습니다. 

   ```json
   {\"Env\": [\"LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME\",\" LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY\"],\"HostConfig\": {\"Binds\": [\"<storage directory bind>\"],\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"11002\"}]}}}
   ```

   ![모듈 만들기 옵션 업데이트](./media/how-to-store-data-blob/create-options.png)

4. 만들기 옵션 JSON에서 컨테이너 운영 체제에 따라 `<storage directory bind>`를 업데이트합니다. IoT Edge 장치에서 Blob 모듈이 데이터를 저장할 [볼륨](https://docs.docker.com/storage/volumes/) 이름 또는 디렉터리 절대 경로를 제공합니다.  

   * Linux 컨테이너: **\<storage path>:/blobroot**. 예: /srv/containerdata:/blobroot. 또는 my-volume:/blobroot.
   * Windows 컨테이너: **\<storage path>:C:/BlobRoot**. 예: C:/ContainerData:C:/BlobRoot. 또는 my-volume:C:/blobroot.

5. **deployment.template.json**을 저장합니다.

6. 솔루션 작업 영역에서 **.env**를 엽니다. 

7. Blob 저장소 이미지의 컨테이너 레지스트리 값은 공개적으로 제공되므로 입력할 필요가 없습니다. 대신, 다음 두 환경 변수를 새로 추가합니다. 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. 저장소 계정 이름을 제공하고, 저장소 계정 키의 64바이트 base64 키를 제공합니다. [GeneratePlus](https://generate.plus/en/base64) 같은 도구를 사용하여 키를 생성할 수 있습니다. 다른 모듈에서 Blob 저장소에 액세스하려면 이러한 자격 증명을 사용합니다. 

9. **.env**를 저장합니다. 

10. **deployment.template.json**을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 배포 매니페스트 생성**을 선택합니다. 

Visual Studio Code는 deployment.template.json 및 .env에 제공된 정보를 사용하여 새로운 배포 매니페스트 파일을 만듭니다. 솔루션 작업 영역의 새 **config** 폴더에 배포 매니페스트가 만들어집니다. 이 파일이 생겼으면 [Visual Studio Code에서 Azure IoT Edge 모듈 배포](how-to-deploy-modules-vscode.md) 또는 [Azure CLI 2.0을 사용하여 Azure IoT Edge 모듈 배포](how-to-deploy-modules-cli.md)의 단계를 수행할 수 있습니다.

## <a name="connect-to-your-blob-storage-module"></a>Blob 저장소 모듈에 연결

모듈에 대해 구성한 계정 이름 및 계정 키를 사용하여 IoT Edge 장치의 Blob 저장소에 액세스할 수 있습니다. 

만드는 저장소 요청의 Blob 엔드포인트로 IoT Edge 장치를 지정합니다. 구성한 IoT Edge 장치 정보 및 계정 이름을 사용하여 [명시적 저장소 엔드포인트에 대한 연결 문자열을 만들 수 있습니다](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint). 

IoT Edge의 Azure Blob Storage에 대한 Blob 엔드포인트는 `http://<IoT Edge device hostname>:11002/<account name>`입니다. 

## <a name="deploy-multiple-instances"></a>여러 인스턴스 배포

IoT Edge에 Azure Blob Storage 인스턴스를 여러 개 배포하려면 모듈이 바인딩되는 HostPort만 변경하면 됩니다. Blob 저장소 모듈은 항상 컨테이너에서 11002 포트를 공개하지만, 호스트에서 모듈이 바인딩될 포트를 선언할 수도 있습니다. 

모듈 만들기 옵션을 편집하여 HostPort 값을 변경할 수 있습니다.

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

추가 Blob 저장소 모듈에 연결할 때 업데이트된 호스트 포트를 가리키도록 엔드포인트를 변경합니다. 

### <a name="try-it-out"></a>체험

Azure Blob Storage 설명서에는 여러 언어로 샘플 코드를 제공하는 빠른 시작이 포함되어 있습니다. 이러한 샘플을 실행하여 Blob 저장소 모듈을 가리키도록 Blob 엔드포인트를 변경하는 방법으로 IoT Edge의 Azure Blob Storage를 테스트할 수 있습니다.

다음 빠른 시작에서는 IoT Edge에서도 지원되는 언어를 사용하므로, Blob 저장소 모듈과 함께 IoT Edge 모듈로 배포할 수 있습니다.

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="supported-storage-operations"></a>지원되는 저장소 작업

IoT Edge의 Blob 저장소 모듈은 동일한 Azure Storage SDK를 사용하며, 블록 Blob 엔드포인트용 Azure Storage API의 2018-03-28 버전과 일치합니다. 이후 릴리스는 고객의 요구에 따라 다릅니다. 

일부 Azure Blob Storage 작업은 IoT Edge의 Azure Blob Storage에서 지원되지 않습니다. 다음 섹션에서는 지원되지 않는 작업에 대해 설명합니다. 

### <a name="account"></a>계좌

지원됨: 
* 컨테이너 나열

지원되지 않음: 
* Blob 서비스 속성을 가져오고 설정
* 실행 전 Blob 요청
* Blob 서비스 통계 가져오기
* 계정 정보 가져오기

### <a name="containers"></a>컨테이너

지원됨: 
* 컨테이너 만들기 및 삭제
* 컨테이너 속성 및 메타데이터 가져오기
* Blob 나열

지원되지 않음: 
* 컨테이너 ACL 가져오기 및 설정
* 컨테이너 임대
* 컨테이너 메타데이터 설정

### <a name="blobs"></a>Blob

지원됨: 
* Blob 배치, 가져오기 및 삭제
* Blob 속성 가져오기 및 설정
* Blob 메타데이터 가져오기 및 설정

지원되지 않음: 
* Blob 임대
* Blob 스냅숏
* Blob 복사 및 중단
* Blob 삭제 취소
* Blob 계층 설정

### <a name="block-blobs"></a>블록 Blob

지원됨: 
* 블록 배치
* 블록 목록 배치 및 가져오기

지원되지 않음:
* URL에서 블록 배치

## <a name="next-steps"></a>다음 단계

[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)에 대해 자세히 알아보기

