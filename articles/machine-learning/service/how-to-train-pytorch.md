---
title: Azure Machine Learning으로 PyTorch 모델 학습
description: PyTorch Estimator를 사용하여 PyTorch 모델의 단일 노드 및 분산 학습을 실행하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: e569b63f676fb750bcbab88dda6cda39156d41f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977035"
---
# <a name="how-to-train-pytorch-models"></a>PyTorch 모델을 학습하는 방법

Azure Machine Learning에서는 PyTorch를 사용한 DNN(심층 신경망) 학습을 위해 Estimator의 사용자 지정 PyTorch 클래스를 제공합니다. Azure SDK의 PyTorch Estimator를 사용하면 Azure Compute의 단일 노드 및 분산 실행 둘 다에 대해 PyTorch 학습 작업을 쉽게 제출할 수 있습니다.

## <a name="single-node-training"></a>단일 노드 학습
PyTorch Estimator를 사용하는 학습은 [기본 Estimator](how-to-train-ml-models.md)를 사용하는 방식과 비슷하므로 먼저 방법 문서를 확인하고 이 문서에서 소개하는 개념을 이해해야 합니다.
  
PyTorch 작업을 실행하려면 `PyTorch` 개체를 인스턴스화합니다. `compute_target` [계산 대상](how-to-set-up-training-targets.md#batch) 개체와 `ds` [데이터 저장소](how-to-access-data.md) 개체는 이미 만들어져 있어야 합니다.

```Python
from azureml.train.dnn import PyTorch

script_params = {
    '--data_dir': ds
}

pt_est = PyTorch(source_directory='./my-pytorch-proj',
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='train.py',
                 use_gpu=True)
```

여기서는 PyTorch 생성자에 대해 다음 매개 변수를 지정합니다.
* `source_directory`: 교육 작업에 필요한 모든 코드가 포함된 로컬 디렉터리입니다. 이 폴더는 로컬 컴퓨터에서 원격 컴퓨터로 복사됩니다.
* `script_params`: <명령줄 인수, 값> 쌍 형식으로 학습 스크립트 `entry_script`에 대한 명령줄 인수를 지정하는 사전입니다.
* `compute_target`: 학습 스크립트가 실행될 원격 컴퓨터(여기서는 [Batch AI](how-to-set-up-training-targets.md#batch) 클러스터)입니다.
* `entry_script`: 원격 컴퓨터에서 실행할 학습 스크립트의 파일 경로(`source_directory` 기준)입니다. 이 파일 및 이 파일이 의존하는 추가 파일은 이 폴더에 있어야 합니다.
* `conda_packages`: conda를 통해 설치할 학습 스크립트에 필요한 Python 패키지의 목록입니다.
생성자에는 필요한 모든 pip 패키지에 사용할 수 있는 `pip_packages` 매개 변수도 있습니다.
* `use_gpu`: 학습에 GPU를 활용하려면 이 플래그를 `True`로 설정합니다. 기본값은 `False`입니다.

여기서는 PyTorch Estimator를 사용하므로 학습에 사용되는 컨테이너는 PyTorch 패키지 및 CPU와 GPU에서 학습에 필요한 관련 종속성을 기본적으로 포함합니다.

그런 다음 PyTorch 작업을 제출합니다.
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>분산 학습
PyTorch Estimator에서는 Azure VM의 여러 CPU 및 GPU 클러스터에서 전체에서 모델을 대규모로 학습시킬 수 있습니다. API 호출만 몇 번 수행하면 분산 PyTorch 학습을 쉽게 실행할 수 있습니다. 이러한 워크로드를 수행하는 데 필요한 모든 인프라와 오케스트레이션은 Azure Machine Learning이 백그라운드에서 관리합니다.

Azure Machine Learning은 현재 Horovod 프레임 워크를 사용하여 PyTorch의 MPI 기반 분산 교육을 지원합니다.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod)는 Uber에서 개발한 분산 학습용 오픈 소스 ring-allreduce 프레임워크입니다.

Horovod 프레임워크를 사용하여 분산 PyTorch를 실행하려면 다음과 같이 PyTorch 개체를 만듭니다.

```Python
from azureml.train.dnn import PyTorch

pt_est = PyTorch(source_directory='./my-pytorch-project',
                 script_params={},
                 compute_target=compute_target,
                 entry_script='train.py',
                 node_count=2,
                 process_count_per_node=1,
                 distributed_backend='mpi',
                 use_gpu=True)
```

위의 코드는 PyTorch 생성자에 다음과 같은 새 매개 변수를 표시합니다.
* `node_count`: 학습 작업에 사용할 노드의 수입니다. 이 인수의 기본값은 `1`입니다.
* `process_count_per_node`: 각 노드에서 실행할 프로세스("작업자")의 수입니다. 이 인수의 기본값은 `1`입니다.
* `distributed_backend`: 분산 학습 시작을 위한 백 엔드입니다. Estimator는 MPI를 통해 이 백 엔드를 제공합니다. 이 인수의 기본값은 `None`입니다. MPI(와 Horovod)를 사용하여 병렬 또는 분산 학습을 수행하려는 경우(예: `node_count`>1 또는 `process_count_per_node`>1이거나 둘 다 해당됨) `distributed_backend='mpi'`를 설정합니다. Azure Machine Learning에서 사용하는 MPI 구현은 [Open MPI](https://www.open-mpi.org/)입니다.

위의 예제에서는 작업자 2명(노드당 작업자 1명)을 통해 분산 학습을 실행합니다.

Horovod 및 해당 종속성은 자동으로 설치되므로 다음과 같이 학습 스크립트 `train.py`에 가져오기만 하면 됩니다.
```Python
import torch
import horovod
```

마지막으로 분산 PyTorch 작업을 제출합니다.
```Python
run = exp.submit(pt_est)
```

## <a name="examples"></a>예
단일 노드 PyTorch 학습에 대한 자습서는 다음 항목을 참조하세요.
* `training/01.train-tune-deploy-pytorch/01.train-tune-deploy-pytorch.ipynb`

Horovod를 사용하는 분산 PyTorch에 대한 자습서는 다음 항목을 참조하세요.
* `training/02.distributed-pytorch-with-horovod/02.distributed-pytorch-with-horovod.ipynb`

다음 Notebook을 다운로드합니다.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계
* [학습 중에 실행 메트릭 추적](how-to-track-experiments.md)
* [하이퍼 매개 변수 조정](how-to-tune-hyperparameters.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
