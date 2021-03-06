---
title: Azure Machine Learning FAQ(질문과 대답) | Microsoft Docs
description: 'Azure Machine Learning 소개: 간소화된 예측 모델링에 대한 클라우드 서비스의 요금 청구, 기능 및 제한 사항을 다루는 FAQ.'
keywords: 기계 학습 소개, 예측 모델링, 기계 학습이란 무엇인가요
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
ms.assetid: a4a32a06-dbed-4727-a857-c10da774ce66
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/02/2017
ms.openlocfilehash: 87695e6e7e1f1abce7204ebbbbed2b492297f177
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393301"
---
# <a name="azure-machine-learning-frequently-asked-questions-billing-capabilities-limitations-and-support"></a>Azure Machine Learning 질문과 대답: 대금 청구, 기능, 제한 사항 및 지원
Azure Machine Learning, 예측 모델 개발을 위한 클라우드 서비스 및 웹 서비스를 통한 운용성 솔루션에 대한 질문(FAQ)과 해당하는 대답입니다. 이 FAQ는 청구 모델, 기능, 제한 및 지원을 포함한 서비스 사용 방법에 대한 질문을 제공합니다.

**여기에서 찾을 수 없는 질문이 있나요?**

Azure Machine Learning은 데이터 과학 커뮤니티의 멤버가 Azure Machine Learning에 대한 질문을 할 수 있는 MSDN 포럼을 운영합니다. Azure Machine Learning 팀에서 포럼을 모니터링합니다. 답변을 검색하거나 새로운 질문을 게시하려면 [Azure Machine Learning 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)으로 이동하세요.

## <a name="general-questions"></a>일반적인 질문
**Azure Machine Learning이란 무엇인가요?**

Azure Machine Learning은 완벽하게 관리되는 서비스로, 이 서비스를 통해 클라우드에 예측 분석 솔루션을 만들고, 테스트하고, 운영하고, 관리할 수 있습니다. 브라우저만 있으면 로그인하고 데이터를 업로드하고 즉시 기계 학습 실험을 시작할 수 있습니다. 끌어서 놓기 예측 모델링, 모듈의 대형 팔레트 및 시작 템플릿의 라이브러리를 활용하면 일반적인 기계 학습 작업을 간단히, 빠르게 수행할 수 있습니다. 자세한 내용은 [Azure Machine Learning 서비스 개요](https://azure.microsoft.com/services/machine-learning/)를 참조하세요. 주요 용어 및 개념을 설명하는 기계 학습 소개는 [Azure Machine Learning 소개](what-is-machine-learning.md)를 참조하세요.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

**Machine Learning Studio란 무엇인가요?**

Machine Learning Studio는 웹 브라우저를 사용하여 액세스하는 워크벤치 환경입니다. Machine Learning Studio는 실험 형태의 종단 간 데이터 과학 워크플로를 구성할 수 있는 시각적 컴퍼지션 인터페이스로 모듈 팔레트를 호스트합니다.

Machine Learning Studio에 대한 자세한 내용은 [Machine Learning Studio란 무엇인가요?](what-is-ml-studio.md)

**Azure Machine Learning API 서비스란 무엇인가요?**

Machine Learning API 서비스를 통해 Machine Learning Studio에 기본 제공되는 것과 같은 예측 모델을 확장성 있는 내결함성 웹 서비스로 배포할 수 있습니다. Machine Learning API 서비스로 만든 웹 서비스는, 외부 응용 프로그램과 예측 분석 모델 간의 통신용 인터페이스를 제공하는 REST API입니다.

자세한 내용은 [Azure Machine Learning 웹 서비스 사용 방법](consume-web-services.md)을 참조하세요.

**내 클래식 웹 서비스는 어디에 나열되나요? 내 새 Azure Resource Manager 기반 웹 서비스는 어디에 나열되나요?**

클래식 배포 모델을 사용하여 만든 웹 서비스와 새로운 Azure Resource Manager 배포 모델을 사용하여 만든 웹 서비스가 [Microsoft Azure Machine Learning 웹 서비스](https://services.azureml.net/) 포털에 나열됩니다.

또한 클래식 웹 서비스는 **웹 서비스** 탭의 [Machine Learning Studio](http://studio.azureml.net)에 나열됩니다.

## <a name="azure-machine-learning-questions"></a>Azure Machine Learning 질문
**Azure Machine Learning 웹 서비스란?**

Machine Learning 웹 서비스는 응용 프로그램과 Machine Learning 워크플로 점수 매기기 모델 간의 인터페이스를 제공합니다. 외부 응용 프로그램에서 Azure Machine Learning을 사용하여 Machine Learning 워크플로 점수 매기기 모델과 실시간으로 통신할 수 있습니다. Machine Learning 웹 서비스에 대한 호출은 외부 응용 프로그램에 예측 결과를 반환합니다. 웹 서비스를 호출하려면 웹 서비스를 배포할 때 만들어진 API 키를 전달합니다. Machine Learning 웹 서비스는 웹 프로그래밍 프로젝트에 일반적으로 사용되는 아키텍처인 REST를 기반으로 합니다.

Azure Machine Learning에는 다음 두 가지 유형의 웹 서비스가 있습니다.

* RRS(요청-응답 서비스): 대기 시간이 짧고 확장성 있는 서비스로, Machine Learning Studio를 사용하여 생성 및 배포되는 상태 비저장 모델에 대한 인터페이스를 제공합니다.
* BES(일괄 처리 실행 서비스): 데이터 레코드의 점수를 일괄적으로 매기는 비동기 서비스입니다.

REST API를 사용하고 웹 서비스에 액세스하는 여러 가지 방법이 있습니다. 예를 들어, 웹 서비스를 배포할 때 생성된 샘플 코드를 사용하여 C#, R 또는 Python에서 응용 프로그램을 작성할 수 있습니다.

샘플 코드는 다음에서 제공됩니다.
- Azure Machine Learning 웹 서비스 포털에서 웹 서비스에 대한 사용 페이지
- Machine Learning Studio에서 웹 서비스 대시보드에서 API 도움말 페이지

사용자용으로 만들고 Machine Learning Studio의 웹 서비스 대시보드에서 사용 가능한 샘플 Microsoft Excel 통합 문서도 사용할 수 있습니다.

**Azure Machine Learning에 대한 주요 업데이트는 무엇인가요?**

최신 업데이트는 [Azure Machine Learning의 새로운 기능](../../active-directory/fundamentals/whats-new.md)을 참조하세요.

## <a name="machine-learning-studio-questions"></a>Machine Learning Studio 질문
### <a name="import-and-export-data-for-machine-learning"></a>Machine Learning에 대한 데이터 가져오기 및 내보내기
**Machine Learning에서 지원하는 데이터 원본은 무엇인가요?**

세 가지 방법으로 Machine Learning Studio 실험으로 데이터를 다운로드할 수 있습니다.

- 데이터 집합으로 로컬 파일 업로드
- 모듈을 사용하여 클라우드 데이터 서비스에서 데이터 가져오기
- 다른 실험에서 저장된 데이터 집합 가져오기

지원된 파일 형식에 대한 자세한 내용은 [Machine Learning Studio로 학습 데이터 가져오기](import-data.md)를 참조하세요.

#### <a id="ModuleLimit"></a>모듈에 대해 설정할 수 있는 데이터 집합의 크기는 어느 정도인가요?
Machine Learning Studio의 모듈은 일반적인 사용 사례의 경우 최대 10GB 숫자 데이터의 데이터 집합을 지원합니다. 모듈에서 둘 이상의 입력을 사용하는 경우에는 모든 입력 크기의 합계 값이 10GB입니다. Hive 또는 Azure SQL Database 쿼리를 사용하여 더 큰 데이터 집합을 샘플링하거나 수집하기 전에 Learning by Counts 전처리를 사용할 수 있습니다.  

다음 데이터 형식은 기능 정규화 중에 확장할 수 있으며 10GB 미만으로 제한됩니다.

* 스파스
* 범주
* 문자열
* 이진 데이터

다음 모듈은 10GB 미만의 데이터 집합으로 제한됩니다.

* Recommender 모듈
* SMOTE(Synthetic Minority Oversampling Technique) 모듈
* Scripting 모듈: R, Python, SQL
* 출력 데이터 크기가 입력 데이터 크기보다 클 수 있는 모듈(예: Join 또는 Feature Hashing)
* Cross-validation, Tune Model Hyperparameters, Ordinal Regression 및 One-vs-All Multiclass(반복 횟수가 매우 많은 경우)

#### <a id="UploadLimit"></a>데이터 업로드에 대한 제한 사항은 무엇인가요?
몇 GB보다 큰 데이터 집합의 경우 로컬 파일에서 직접 업로드하지 않고 Azure Storage 또는 Azure SQL Database에 데이터를 업로드하거나 Azure HDInsight를 사용합니다.

**Amazon S3에서 데이터를 읽을 수 있나요?**

소량의 데이터를 HTTP URL을 통해 노출하려는 경우 [데이터 가져오기][import-data] 모듈을 사용할 수 있습니다. 전송할 데이터가 많은 경우에는 먼저 Azure Storage로 전송한 다음 [데이터 가져오기][import-data] 모듈을 사용하여 실험으로 가져옵니다.
<!--

<SEE CLOUD DS PROCESS>
-->

**기본 제공 이미지 입력 기능이 있나요?**

이미지 입력 기능은 [이미지 가져오기][image-reader] 참조에서 확인할 수 있습니다.

### <a name="modules"></a>모듈
**원하는 알고리즘, 데이터 원본, 데이터 형식, 데이터 변환 작업이 Azure Machine Learning Studio에 없습니다. 어떻게 해야 하나요?**

[사용자 피드백 포럼](http://go.microsoft.com/fwlink/?LinkId=404231)으로 이동하여 Microsoft에서 추적 중인 기능 요청을 확인할 수 있습니다. 원하는 기능이 이미 요청된 경우 해당 요청에 투표할 수 있습니다. 원하는 기능이 없는 경우 새로운 요청을 만드세요. 이 포럼에서 요청의 상태를 확인할 수도 있습니다. Microsoft는 이 목록을 긴밀하게 추적하여 기능의 사용 가능성 상태를 자주 업데이트합니다. R 및 Python에 대한 기본적인 지원 외에 필요에 따라 사용자 지정 변환을 만들 수 있습니다.

**기존 코드를 Machine Learning Studio로 가져올 수 있나요?**

예, Machine Learning Studio로 기존 R 또는 Python 코드를 가져와서, Azure Machine Learning 학습자와 동일한 실험에서 실행하고 Azure Machine Learning을 통해 솔루션을 웹 서비스로 배포할 수 있습니다. 자세한 내용은 [R을 사용하여 실험 확장](extend-your-experiment-with-r.md) 및 [Azure Machine Learning Studio에서 Python 기계 학습 스크립트 실행](execute-python-scripts.md)을 참조하세요.

**[PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language)과 유사한 기능을 사용하여 모델을 정의할 수 있나요?**

아니요, PMML(Predictive Model Markup Language)은 지원되지 않습니다. 모듈을 정의하려면 사용자 지정 R 및 Python 코드를 사용할 수 있습니다.

**실험에서 병렬로 실행할 수 있는 모듈은 몇 개인가요?**  

실험에서 동시에 최대 네 개의 모듈을 실행할 수 있습니다.

### <a name="data-processing"></a>데이터 처리
**실험 내에서 대화형으로 데이터를 시각화하는 기능(R 시각화 외)이 있나요?**

모듈의 출력을 클릭하여 데이터를 시각화하고 통계를 가져옵니다.

**브라우저에서 결과 또는 데이터를 미리 볼 때 행 및 열 수가 제한됩니다. 그 이유는 무엇일까요?**

많은 양의 데이터를 브라우저에 전송할 수 있으므로 Machine Learning Studio의 속도 저하를 방지하기 위해 데이터 크기가 제한됩니다. 모든 데이터/결과를 시각화하려면, 데이터를 다운로드하고 Excel 또는 다른 도구를 사용하는 것이 좋습니다.

### <a name="algorithms"></a>알고리즘
**Machine Learning Studio에서 지원되는 기존 알고리즘은 무엇인가요?**

Machine Learning Studio는 Microsoft Research에서 개발된 확장 가능한 고급 의사 결정 트리, Bayesian 권장 시스템, 심층적인 신경망, 의사 결정 정글 등 최신 알고리즘을 제공합니다. Vowpal Wabbit과 같은 확장성 있는 오픈 소스 기계 학습 패키지도 포함되어 있습니다. Machine Learning Studio는 여러 클래스의 이진 분류, 회귀 및 클러스터링을 위한 기계 학습 알고리즘을 지원합니다. 전체 목록은 [Machine Learning 모듈][machine-learning-modules]을 참조하세요.

**데이터에 사용할 올바른 Machine Learning 알고리즘이 자동으로 제안되나요?**

아니요, 그러나 Machine Learning Studio에서 각 알고리즘의 결과를 비교하여 문제에 적합한 알고리즘을 확인할 수 있는 다양한 방법이 있습니다.

**제공된 알고리즘에서 적합한 알고리즘 하나를 선택하는 데 대한 지침이 있나요?**

[알고리즘을 선택하는 방법](algorithm-choice.md)를 참조하세요.

**제공되는 알고리즘은 R 또는 Python으로 작성되었나요?**

아니요, 이러한 알고리즘은 더 나은 성능을 제공하기 위해 주로 컴파일된 언어로 작성됩니다.

**제공되는 알고리즘에 대한 세부 정보가 있나요?**

설명서에는 알고리즘에 대한 일부 정보가 나와 있으며, 용도에 맞게 알고리즘을 최적화할 수 있도록 조정할 매개 변수에 대한 설명이 있습니다.  

**온라인 학습을 지원하나요?**

아니요, 현재는 프로그래밍 방식의 재학습만 지원됩니다.

**기본 제공 모듈을 사용하여 신경망 모델의 계층을 시각화할 수 있나요?**

아니요.

**C# 또는 일부 다른 언어로 모듈을 만들 수 있나요?**

현재는 R을 사용해서만 새 사용자 지정 모듈을 만들 수 있습니다.

### <a name="r-module"></a>R 모듈
**Machine Learning Studio에서 사용 가능한 R 패키지는 무엇인가요?**

Machine Learning Studio는 현재 400개가 넘는 CRAN R 패키지를 지원하며 다음은 모든 포함된 패키지의 [현재 목록](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) 입니다. 이 목록을 직접 검색하는 방법에 대해 알아보려면 [R을 사용하여 실험 확장](extend-your-experiment-with-r.md) 을 참조하세요. 원하는 패키지가 이 목록에 없는 경우 [사용자 피드백 포럼](http://go.microsoft.com/fwlink/?LinkId=404231)에서 패키지 이름을 제공해 주세요.

**사용자 지정 R 모듈을 빌드할 수 있나요?**

예, 자세한 내용은 [Azure Machine Learning에서 사용자 지정 R 모듈 작성](custom-r-modules.md)을 참조하세요.

**R에 대한 REPL 환경이 있나요?**

아니요, R에 대한 REPL(Read-Eval-Print-Loop) 환경은 스튜디오에 없습니다.

### <a name="python-module"></a>Python 모듈
**사용자 지정 Python 모듈을 빌드할 수 있나요?**

현재는 안 되지만, 하나 이상의 [Python 스크립트 실행][python] 모듈을 사용하여 동일한 결과를 낼 수 있습니다.

**Python에 대한 REPL 환경이 있나요?**

Machine Learning Studio에서 Jupyter 노트북을 사용할 수 있습니다. 자세한 내용은 [Azure Machine Learning Studio에 Jupyter 노트북 도입](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)을 참조하세요.

## <a name="web-service"></a>웹 서비스
### <a name="retrain"></a>다시 학습
**Azure Machine Learning 모델 프로그래밍 방식을 다시 학습하려면 어떻게 하나요?**

API 다시 학습을 사용합니다. 자세한 내용은 [프로그래밍 방식으로 Machine Learning 모델 다시 학습](retrain-models-programmatically.md)을 참조하세요. 샘플 코드는 [Microsoft Azure Machine Learning 다시 학습 데모](https://azuremlretrain.codeplex.com/)에서도 제공됩니다.

### <a name="create"></a>생성
**모델을 로컬로 배포하거나 인터넷에 연결하지 않고 응용 프로그램에서 배포할 수 있나요?**

아니요.

**모든 웹 서비스에 예상되는 기준 대기 시간이 있나요?**

[Azure 구독 제한](../../azure-subscription-service-limits.md)을 참조하세요.

### <a name="use"></a>사용
**어떤 경우에 내 예측 모델을 일괄 처리 실행 서비스로 실행하고 어떤 경우에 요청-응답 웹 서비스를 실행하나요?**

RRS(요청-응답 서비스)는 대기 시간이 짧고, 확장성이 높은 웹 서비스로, 실험 환경에서 생성하여 배포하는 상태 비저장 모델에 대한 인터페이스를 제공하는 데 사용됩니다. BES(Batch 실행 서비스)는 데이터 레코드의 배치에 대한 점수를 비동기적으로 계산하는 서비스입니다. BES에 대한 입력은 RRS에서 사용하는 데이터 입력과 유사합니다. 가장 중요한 차이는 BES에서는 Azure Blob Storage 및 Azure Table Storage, Azure SQL Database, HDInsight(Hive 쿼리), HTTP 소스 등의 다양한 소스에서 레코드 블록을 읽는다는 점입니다. 자세한 내용은 [Azure Machine Learning 웹 서비스 사용 방법](consume-web-services.md)을 참조하세요.

**배포된 웹 서비스의 모델을 업데이트하려면 어떻게 해야 하나요?**

이미 배포된 서비스의 예측 모델을 업데이트하려면 학습된 모델을 작성하여 저장하는 데 사용된 실험을 수정하고 다시 실행합니다. 새 버전의 학습된 모델이 확보된 후에는, Machine Learning Studio에서 웹 서비스를 업데이트할지 여부를 묻습니다. 배포된 웹 서비스를 업데이트하는 방법에 대한 자세한 내용은 [Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)를 참조하세요.

다시 학습 API도 사용할 수 있습니다.
자세한 내용은 [프로그래밍 방식으로 Machine Learning 모델 다시 학습](retrain-models-programmatically.md)을 참조하세요. 샘플 코드는 [Microsoft Azure Machine Learning 다시 학습 데모](https://azuremlretrain.codeplex.com/)에서도 제공됩니다.

**프로덕션에 배포된 웹 서비스를 모니터링하려면 어떻게 해야 하나요?**

예측 모델을 배포한 후에는 Azure Machine Learning 웹 서비스 포털에서 모니터링할 수 있습니다. 배포된 각 서비스에는 고유한 대시보드가 있어, 이 대시보드에서 해당 서비스에 대한 모니터링 정보를 볼 수 있습니다. 배포된 웹 서비스를 관리하는 방법에 대한 자세한 내용은 [Azure Machine Learning 웹 서비스 포털을 사용하여 웹 서비스 관리](manage-new-webservice.md) 및 [Azure Machine Learning 작업 영역 관리](manage-workspace.md)를 참조하세요.

**RRS/BES의 출력을 볼 수 있는 곳이 있나요?**

RRS의 경우 웹 서비스 응답은 일반적으로 결과를 보는 위치입니다. Azure Blob Storage에 기록도 가능합니다. BES의 경우 기본적으로 출력은 Blob에 기록됩니다. [데이터 내보내기][export-data] 모듈을 사용하여 출력을 데이터베이스 또는 테이블에 기록할 수도 있습니다.

**Machine Learning Studio에서 만든 모델에서만 웹 서비스를 만들 수 있나요?**

아니요. Jupyter Notebook 및 RStudio를 사용하여 직접 웹 서비스를 만들 수도 있습니다.

**오류 코드에 대한 정보를 어디서 찾을 수 있습니까?**

오류 코드 목록 및 설명은 [Machine Learning 모듈 오류 코드](https://msdn.microsoft.com/library/azure/dn905910.aspx) 를 참조하세요.

## <a name="scalability"></a>확장성
**웹 서비스의 확장성이란 무엇인가요?**

현재 기본 엔드포인트는 엔드포인트당 20개의 동시 RRS 요청으로 프로비전됩니다. [웹 서비스 확장](scaling-webservice.md)에 설명된 것처럼 엔드포인트당 동시 요청 수를 200개까지 확장할 수 있으며 웹 서비스당 10,000개 엔드포인트로 각 웹 서비스를 확장할 수 있습니다. BES의 경우 각 엔드포인트는 한 번에 40개의 요청을 처리할 수 있으며 40개를 초과하는 추가 요청은 큐에 대기됩니다. 이러한 큐에 대기 중인 요청은 큐에서 나옴과 동시에 자동으로 실행됩니다.

**R 작업은 노드 간에 분산되나요?**

아니요.  

**학습에 사용할 수 있는 데이터의 양은 얼마인가요?**

Machine Learning Studio의 모듈은 일반적인 사용 사례의 경우 최대 10GB 숫자 데이터의 데이터 집합을 지원합니다. 모듈에서 둘 이상의 입력을 사용하는 경우에는 모든 입력에 대한 총 크기가 10GB입니다. Hive 쿼리 또는 Azure SQL Database 쿼리를 통하거나 수집 전에 [개수로 알아보기][counts] 모듈로 전처리하여 더 큰 데이터 집합을 샘플링할 수도 있습니다.  

다음 데이터 형식은 기능 정규화 중에 확장할 수 있으며 10GB 미만으로 제한됩니다.

* 스파스
* 범주
* 문자열
* 이진 데이터

다음 모듈은 10GB 미만의 데이터 집합으로 제한됩니다.

* Recommender 모듈
* SMOTE(Synthetic Minority Oversampling Technique) 모듈
* Scripting 모듈: R, Python, SQL
* 출력 데이터 크기가 입력 데이터 크기보다 클 수 있는 모듈(예: Join 또는 Feature Hashing)
* Cross-Validate, Tune Model Hyperparameters, Ordinal Regression 및 One-vs-All Multiclass(반복 횟수가 매우 많은 경우)

몇 GB보다 큰 데이터 집합의 경우 로컬 파일에서 직접 업로드하지 않고 Azure Storage 또는 Azure SQL Database에 데이터를 업로드하거나 HDInsight를 사용합니다.

**벡터 크기 제한이 있나요?**

행 및 열은 각각 .NET 제한인 Max Int: 2,147,483,647로 제한됩니다.

**웹 서비스를 실행하는 가상 머신의 크기를 조정할 수 있나요?**

아니요.  

## <a name="security-and-availability"></a>보안 및 사용 가능성
**웹 서비스에 대한 http 엔드포인트에 기본적으로 액세스할 수 있는 사람은 누구인가요? 엔드포인트에 대한 액세스는 어떻게 제한하나요?**

웹 서비스가 배포된 후 해당 서비스에 대한 기본 엔드포인트가 만들어집니다. 기본 엔드포인트는 API 키를 사용하여 호출할 수 있습니다. 웹 서비스 포털에서 또는 웹 서비스 관리 API를 사용하여 프로그래밍 방식으로 해당 고유 키로 엔드포인트를 더 추가할 수 있습니다. 액세스 키는 웹 서비스를 호출하는 데 필요합니다. 자세한 내용은 [Azure Machine Learning 웹 서비스 사용 방법](consume-web-services.md)을 참조하세요.

**내 Azure Storage 계정을 찾을 수 없는 경우 어떻게 되나요?**

Machine Learning Studio는 워크플로를 실행할 때 사용자가 제공한 Azure Storage 계정을 기반으로 중간 데이터를 저장합니다. 이 저장소 계정은 작업 영역을 만들 때 Machine Learning Studio에 제공됩니다. 작업 영역을 만든 후 저장소 계정이 삭제되고 더 이상 찾을 수 없는 경우에는 해당 작업 영역의 작동이 중지되고 작업 영역의 모든 실험이 실패합니다.

저장소 계정을 실수로 삭제한 경우 삭제한 저장소 계정과 동일한 지역과 동일한 이름으로 저장소 계정을 다시 만듭니다. 그 후 액세스 키를 다시 동기화하세요.

**저장소 계정 액세스 키가 동기화되지 않은 경우 어떻게 되나요?**

Machine Learning Studio는 워크플로를 실행할 때 사용자가 제공한 Azure Storage 계정을 기반으로 중간 데이터를 저장합니다. 이 저장소 계정은 작업 영역을 만들 때 Machine Learning Studio에 제공되고 액세스 키가 해당 작업 영역과 연결됩니다. 액세스 키가 변경되면 작업 영역이 만들어진 후에 작업 영역은 저장소 계정에 더 이상 액세스할 수 없습니다. 작동이 중지되고 해당 작업 영역의 모든 실험이 실패합니다.

저장소 계정 액세스 키를 변경한 경우에는, Azure Portal을 사용하여 작업 영역에서 액세스 키를 다시 동기화합니다.  

## <a name="support-and-training"></a>지원 및 교육
**Azure Machine Learning에 대한 교육은 어디에서 받을 수 있나요?**

[Azure Machine Learning 설명서 센터](https://azure.microsoft.com/services/machine-learning/)에서 비디오 자습서와 방법 가이드를 호스트합니다. 이러한 단계별 가이드에서는 서비스를 소개하고, 데이터 가져오기, 데이터 정리, 예측 모델 구성, Azure Machine Learning을 사용하여 프로덕션에 배포의 데이터 과학 수명 주기 전체를 설명해 줍니다.

Microsoft는 Machine Learning Center에 새로운 자료를 추가하고 있습니다. [사용자 피드백 포럼](https://windowsazure.uservoice.com/forums/257792-machine-learning)에서 Machine Learning 센터의 추가 학습 자료에 대한 요청을 제출할 수 있습니다.

[Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning)에서 교육을 찾을 수도 있습니다.

**Azure Machine Learning에 대한 지원을 받으려면 어떻게 해야 하나요?**

Azure Machine Learning에 대한 기술 지원을 받으려면 [Azure 지원](https://azure.microsoft.com/support/options/) 으로 이동하여 **기계 학습**을 선택합니다.

또한 Azure Machine Learning은 MSDN에 커뮤니티 포럼을 갖고 있으며, 여기에서 Azure 기계 학습 관련 질문을 할 수 있습니다. Azure Machine Learning 팀에서 포럼을 모니터링합니다. [Azure 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)으로 이동하세요.

## <a name="billing-questions"></a>대금 청구 관련 질문
**Machine Learning 결제는 어떤 방식으로 이루어지나요?**

Azure Machine Learning은 Machine Learning Studio 및 Machine Learning 웹 서비스라는 두 구성 요소로 이루어집니다.

Machine Learning Studio를 평가하는 동안 무료 청구 계층을 사용할 수 있습니다. 무료 계층을 사용하면 제한된 용량으로 클래식 웹 서비스를 배포할 수도 있습니다.

Azure Machine Learning이 필요를 충족한다고 결정했으면 표준 계층에 등록할 수 있습니다. 등록하려면 Microsoft Azure 구독이 있어야 합니다.

표준 계층에서는 Machine Learning Studio에서 정의하는 각 작업 영역에 대해 월별로 요금을 청구합니다. 스튜디오에서 실험을 실행하면 실험을 실행할 때 계산 리소스가 청구됩니다. 클래식 웹 서비스를 배포하는 경우 트랜잭션 및 계산 시간은 종량제 기준으로 청구됩니다.

새 Resource Manager 기반 웹 서비스는 비용을 예측할 수 있도록 청구 계획을 적용합니다. 계층화된 가격 책정은 많은 양의 용량을 필요로 하는 고객에게 할인된 요금을 제공합니다.

계획을 만들 때 API 계산 시간 및 API 트랜잭션이 포함된 수량과 함께 제공되는 고정된 비용에 커밋합니다. 포함된 수량이 더 필요한 경우 계획에 인스턴스를 추가할 수 있습니다. 포함된 수량이 더 많이 필요한 경우 포함된 수량을 훨씬 더 많이 제공하고 할인율이 더 나은 높은 계층 계획을 선택할 수 있습니다.

기존 인스턴스에 포함된 수량을 모두 사용한 후에 추가 사용량은 청구 계획 계층과 연결된 초과 요금이 부과됩니다.

> [!NOTE]
포함된 수량은 30일마다 다시 할당되고 사용하지 않은 포함된 수량은 다음 기간에 롤오버되지 않습니다.

추가 대금 청구 및 가격 책정 정보는 [Machine Learning 가격](https://azure.microsoft.com/pricing/details/machine-learning/)을 참조하세요.

**Azure Machine Learning의 무료 평가판이 있나요?**

 Azure Machine Learning에는 무료 구독 옵션이 포함되며 이에 대한 내용은 [Machine Learning 가격 책정](https://azure.microsoft.com/pricing/details/machine-learning/)에 설명됩니다. Machine Learning Studio에는 [Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2)에 로그인할 때 사용 가능한 8시간의 빠른 평가판이 포함됩니다.

 또한 Azure 무료 평가판에 등록하면 한 달 동안 모든 Azure 서비스를 사용해볼 수 있습니다. Azure 무료 평가판에 대한 자세한 내용을 보려면 [Azure 무료 평가판 FAQ](https://azure.microsoft.com/pricing/free-trial-faq/)를 방문하세요.

**트랜잭션이란?**

트랜잭션은 Azure Machine Learning이 응답하는 API 호출을 나타냅니다. RRS(요청-응답 서비스) 및 BES(Batch 실행 서비스) 호출에서 트랜잭션이 집계되고 청구 계획에 대한 요금으로 부과됩니다.

**RRS와 BES 트랜잭션 모두에 대한 계획에 포함된 트랜잭션 수량을 사용할 수 있나요?**

예, RRS와 BES에서 트랜잭션이 집계되고 청구 계획에 대한 요금으로 부과됩니다.

**API 계산 시간이란?**

API 계산 시간은 Machine Learning 계산 리소스를 사용하여 API 호출 실행에 소요되는 시간에 대한 청구 단위입니다. 모든 호출은 청구 목적으로 집계됩니다.

**일반 프로덕션 API 호출은 얼마나 걸리나요?**

일반적으로 프로덕션 API 호출 시간은 수백 밀리초에서 몇 초에 이르기까지 상당히 다양합니다. 일부 API 호출은 데이터 처리 및 기계 학습 모델의 복잡도에 따라 몇 분이 필요할 수도 있습니다. 프로덕션 API 호출 시간을 예측하는 가장 좋은 방법은 Machine Learning 서비스에서 모델을 벤치마킹하는 것입니다.

**스튜디오 계산 시간이란?**

스튜디오 계산 시간은 실험이 스튜디오에서 계산 리소스를 사용한 집계 시간에 대한 청구 단위입니다.

**새 Azure Resource Manager 기반 웹 서비스에서 개발/테스트 계층의 용도는 무엇인가요?**

Resource Manager 기반 웹 서비스는 청구 계획을 프로비전하는 데 사용할 수 있는 여러 계층을 제공합니다. 개발/테스트 가격 책정 계층은 별도의 비용 없이도 실험을 웹 서비스로 테스트할 수 있도록 제한된 만큼의 포함된 수량을 제공하는 계층입니다. 이 계층의 작동 방식을 확인할 수 있습니다.

**저장소 요금은 별도입니까?**

Machine Learning 무료 계층은 별도의 저장소를 필요로 하지 않거나 허용하지 않습니다. Machine Learning 표준 계층을 사용하려면 Azure 저장소 계정이 있어야 합니다. Azure Storage는 [별도로 청구됩니다](https://azure.microsoft.com/pricing/details/storage/).

**Machine Learning에서 고가용성 작업을 지원하나요?**

예. 자세한 내용은 [Machine Learning 가격 책정](https://azure.microsoft.com/pricing/details/machine-learning/)에서 Service Level Agreement(서비스 수준 약정)에 대한 설명을 참조하세요.

**내 프로덕션 API 호출이 실행될 계산 리소스의 구체적인 종류는 무엇인가요?**

Machine Learning 서비스는 다중 테넌트 서비스입니다. 백 엔드에서 사용되는 실제 계산 리소스는 다양하며 성능 및 예측성을 위해 최적화됩니다.

### <a name="management-of-new-resource-manager-based-web-services"></a>새 Resource Manager 기반 웹 서비스 관리
**계획을 삭제하면 어떻게 되나요?**

계획이 구독에서 제거되고 비례 청구된 사용량에 대한 요금이 청구됩니다.

> [!NOTE]
웹 서비스에서 사용하는 계획을 삭제할 수 없습니다. 계획을 삭제하려면 웹 서비스에 새 계획을 할당하거나 웹 서비스를 삭제합니다.

**계획 인스턴스란?**

계획 인스턴스는 청구 계획에 추가할 수 있는 포함된 수량의 단위입니다. 청구 계획에 대한 청구 계층을 선택하면 인스턴스 하나와 함께 제공됩니다. 포함된 수량이 더 필요한 경우 계획에 선택한 청구 계층의 인스턴스를 추가할 수 있습니다.

**계획 인스턴스를 얼마나 추가할 수 있나요?**

구독에는 개발/테스트 가격 책정 계층의 인스턴스 하나를 사용할 수 있습니다.

표준 S1, 표준 S2, 표준 S3 계층의 경우 필요한 만큼을 추가할 수 있습니다.

> [!NOTE]
예상된 사용량에 따라 현재 계층에 인스턴스를 추가하는 대신 더 많이 포함된 수량으로 업그레이드하는 편이 보다 더 비용 효율적일 수 있습니다.

**계획 계층을 변경(업그레이드/다운그레이드)하는 경우 어떻게 되나요?**

이전 계획은 삭제되고 현재 사용량은 비례적으로 청구됩니다. 업그레이드/다운그레이드된 계층의 전체 포함된 수량이 있는 새 계획이 나머지 기간 동안 생성됩니다.

> [!NOTE]
포함된 수량은 기간당 할당되고 사용하지 않은 수량은 롤오버되지 않습니다.

**계획의 인스턴스를 증가시키면 어떻게 되나요?**

수량은 비례적으로 포함되고 적용되는 데 24시간이 걸릴 수 있습니다.

**계획의 인스턴스를 삭제하면 어떻게 되나요?**

인스턴스가 구독에서 제거되고 비례 청구된 사용량에 대한 요금이 청구됩니다.

### <a name="sign-up-for-new-resource-manager-based-web-services-plans"></a>새 Resource Manager 기반 웹 서비스 계획 등록
**계획에 어떻게 등록하나요?**

두 가지 방법으로 청구 계획을 만들 수 있습니다.

Resource Manager 기반 웹 서비스를 처음 배포할 때 기존 계획을 선택하거나 새 계획을 만들 수 있습니다.

이 방식으로 만든 계획은 기본 지역에 있으며 웹 서비스도 해당 지역에 배포됩니다.

기본 지역이 아닌 지역에 서비스를 배포하려는 경우 서비스를 배포하기 전에 청구 계획을 정의하려고 할 수 있습니다.

이 경우에 Azure Machine Learning 웹 서비스 포털에 로그인하여 계획 페이지로 이동할 수 있습니다. 거기에서 계획을 추가하고 삭제할 수 있을 뿐만 아니라 기존 계획을 수정할 수 있습니다.

**처음에 어떤 계획부터 사용하는 게 좋을까요?**

표준 S1 계층으로 시작해보고 서비스의 사용량을 모니터링하는 것이 좋습니다. 포함된 수량을 빠르게 사용할 경우 인스턴스를 추가하거나 더 높은 계층으로 이동하여 할인된 요금을 적용할 수 있습니다. 청구 주기 동안 필요에 따라 청구 계획을 조정할 수 있습니다.

**새 계획을 사용할 수 있는 지역은 어디인가요?**

새 청구 계획은 새 웹 서비스가 지원되는 다음 세 곳의 프로덕션 지역에서 사용할 수 있습니다.

* 미국 중남부
* 서유럽
* 동남아시아

**여러 지역에서 웹 서비스를 사용합니다. 모든 지역에 대한 계획이 필요한가요?**

예. 지역에 따라 계획 가격 책정이 달라집니다. 다른 지역에 웹 서비스를 배포하는 경우 해당 지역에 특정된 계획을 할당해야 합니다. 자세한 내용은 [지역별 사용 가능한 제품]( https://azure.microsoft.com/regions/services/)을 참조하세요.

### <a name="new-web-services-overages"></a>새 웹 서비스 - 초과분
**내 웹 서비스 사용량을 초과했는지 어떻게 확인하나요?**

Azure Machine Learning 웹 서비스 포털의 계획 페이지에서 모든 계획의 사용량을 볼 수 있습니다. 포털에 로그인한 다음 **계획** 메뉴 옵션을 클릭합니다.

테이블의 **트랜잭션** 및 **Compute** 열에서 계획의 포함된 수량 및 사용된 백분율을 볼 수 있습니다.

**개발/테스트 가격 책정 계층에 포함된 수량을 모두 사용하면 어떻게 되나요?**

개발/테스트 가격 책정 계층이 할당된 서비스가 다음 기간 또는 유료 계층으로 전환될 때까지 중지됩니다.

**클래식 웹 서비스 및 새 Resource Manager 기반 웹 서비스 초과분의 경우 RRS(요청 응답) 및 BES(Batch) 워크로드에 대한 가격은 어떻게 계산되나요?**

RRS 워크로드의 경우 요청하신 모든 API 트랜잭션 호출 및 해당 요청과 연관된 계산 시간에 대한 비용이 청구됩니다. RRS 프로덕션 API 트랜잭션 비용은 요청하신 총 API 호출 수에 트랜잭션 1,000개당 가격을 곱하여 계산됩니다(트랜잭션별 일할 계산). RRS API 프로덕션 API 계산 시간 비용은 각 API 호출 실행에 필요한 시간에 총 API 트랜잭션 수를 곱하고 프로덕션 API 계산 시간당 가격을 곱하여 계산됩니다.

예를 들어 표준 S1 초과분의 경우 한 번 실행하는 데 0.72초가 걸리는 API 트랜잭션이 1,000,000개 있다면 프로덕션 API 트랜잭션 비용은 $500(1,000,000 * $0.50/1K API 트랜잭션)이고 프로덕션 API 계산 시간은 $400(1,000,000 * 0.72초 * $2/시간)로 총 $900의 금액이 산정됩니다.

BES 워크로드도 같은 방식으로 청구됩니다. 하지만 API 트랜잭션 비용은 제출한 일괄 작업 수를, 계산 비용은 해당 일괄 작업과 연관된 계산 시간을 나타냅니다. BES 프로덕션 API 트랜잭션 비용은 제출한 총 작업 수에 트랜잭션 1,000개당 가격을 곱하여 계산됩니다(트랜잭션별 일할 계산). BES API 프로덕션 API 계산 시간 비용은 작업의 각 행 실행에 필요한 시간에 작업의 총 행 수, 총 작업 수, 프로덕션 API 계산 시간당 가격을 곱하여 계산됩니다. Machine Learning 계산기를 사용할 때 트랜잭션 미터는 제출하려는 작업 수를, 트랜잭션 필드당 시간은 각 작업의 모든 행을 실행하는 데 필요한 총 시간을 나타냅니다.

예를 들어 표준 S1 초과분에 대해 각 0.72초가 필요한 행 500개로 구성된 각 작업을 하루에 100개 제출한다고 가정해 보겠습니다. 월간 초과 비용은 프로덕션 API 트랜잭션 비용 $1.55(하루 100개 작업 = 3,100개 작업/1개월 * $0.50/1K API 트랜잭션)이며 프로덕션 API 계산 시간은 $620(500행 * 0.72초 * 3,100개 작업 * $2/시간)로 총 $621.55의 금액이 산정됩니다.

### <a name="azure-machine-learning-classic-web-services"></a>Azure Machine Learning 클래식 웹 서비스
**종량제를 계속 사용할 수 있나요?**

예, 기존 웹 서비스는 Azure Machine Learning에서 계속 사용할 수 있습니다.  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Azure Machine Learning 무료 및 표준 계층
**Azure Machine Learning 무료 계층에는 무엇이 포함되나요?**

Azure Machine Learning 무료 계층은 Azure Machine Learning Studio를 자세히 소개하기 위한 것입니다. Microsoft 계정을 등록하기만 하면 됩니다. 무료 계층에는 [Microsoft 계정](https://account.microsoft.com/account)당 하나의 Azure Machine Learning Studio 작업 영역에 대한 무료 액세스 권한이 포함됩니다. 이 계층에서 최대 10GB의 저장소를 사용하고 스테이징 API로서 모델을 운영할 수 있습니다. 무료 계층 작업은 SLA의 적용을 받지 않으며 개발 및 개인 용도로만 사용할 수 있습니다. 

무료 계층 작업 영역의 제한 사항:

* 워크로드가 SQL Server가 실행되는 온-프레미스 서버에 연결하여 데이터에 액세스할 수 없습니다.
* 새 리소스 관리자 기반 웹 서비스를 배포할 수 없습니다.


**Azure Machine Learning 표준 계층 및 계획에는 무엇이 포함됩니까?**

Azure Machine Learning 표준 계층은 Azure Machine Learning Studio의 유료 프로덕션 버전입니다. Azure Machine Learning Studio는 작업 영역별 및 월별 기준으로 요금이 청구되고 한 달이 안되는 경우 일할 계산됩니다. Azure Machine Learning 스튜디오 실험 시간은 실제 실험의 계산 시간을 기준으로 요금이 청구됩니다. 요금은 부분 시간별로 일할 계산됩니다.  

Azure Machine Learning API 서비스는 클래식 웹 서비스 또는 새로운 Resource Manager 기반 웹 서비스인지에 따라 요금이 청구됩니다.

다음 요금은 구독에 대해 작업 영역별로 집계됩니다.

* Machine Learning 작업 영역 구독 - Machine Learning 작업 영역 구독은 Machine Learning Studio 작업 영역에 대한 액세스를 제공하는 월별 요금입니다. 이 구독은 스튜디오에서 실행하고 프로덕션 API를 활용하는 실험에 필요합니다.
* 스튜디오 실험 시간 - 이 미터는 Machine Learning Studio에서 실행 중인 실험과 스테이징 환경에서 실행 중인 프로덕션 API 호출로 발생하는 모든 계산 요금을 집계합니다.
* 교육 및 평가를 위해 모델에서 SQL Server를 실행하는 온-프레미스 서버에 연결하여 데이터에 액세스합니다.
* 클래식 웹 서비스의 경우:
  * 프로덕션 API Compute 시간: 이 미터에는 프로덕션에서 실행 중인 웹 서비스로 발생하는 계산 요금이 포함됩니다.
  * 프로덕션 API 트랜잭션(단위: 1000): 이 미터에는 프로덕션 웹 서비스 호출당 발생하는 요금이 포함됩니다.

Resource Manager 기반 웹 서비스의 경우 앞의 요금 외에도 선택한 계획에 대한 요금이 집계됩니다.

* 표준 S1/S2/S3 API 계획(단위): 이 미터는 Resource Manager 기반 웹 서비스에 대해 선택한 인스턴스의 유형을 나타냅니다.
* 표준 S1/S2/S3 초과 API Compute 시간: 이 미터는 기존 인스턴스의 포함된 수량을 모두 사용한 후에 프로덕션에서 실행되는 Resource Manager 기반 웹 서비스에서 발생한 계산 요금을 포함합니다. 추가 사용량은 S1/S2/S3 계획 계층과 관련한 초과 요금으로 부과됩니다.
* 표준 S1/S2/S3 초과 API 트랜잭션(단위: 1,000): 이 미터는 기존 인스턴스의 포함된 수량을 모두 사용한 후에 프로덕션 Resource Manager 기반 웹 서비스에 대한 호출당 발생하는 요금을 포함합니다. 추가 사용량은 S1/S2/S3 계획 계층과 관련한 초과 요금으로 부과됩니다.
* 포함된 수량 API Compute 시간: 이 미터는 Resource Manager 기반 웹 서비스를 사용하여 API 계산 시간의 포함된 수량을 나타냅니다.
* 포함된 수량 API 트랜잭션(단위: 1,000): 이 미터는 Resource Manager 기반 웹 서비스를 사용하여 API 트랜잭션의 포함된 수량을 나타냅니다.

**Azure Machine Learning 무료 계층에 등록하려면 어떻게 하나요?**

Microsoft 계정만 있으면 됩니다. [Azure Machine Learning 홈](https://azure.microsoft.com/services/machine-learning/)으로 이동하고 **지금 시작**을 클릭하세요. Microsoft 계정으로 로그인하면 무료 계층에 작업 영역이 생성됩니다. Machine Learning 실험을 바로 탐색하고 만들 수 있습니다.

**Azure Machine Learning 표준 계층에 등록하려면 어떻게 하나요?**

표준 Machine Learning 작업 영역을 만들려면 먼저 Azure 구독에 대한 액세스 권한이 있어야 합니다. 30일 무료 평가판 Azure 구독에 등록하고 나중에 유료 Azure 구독으로 업그레이드하거나 유료 Azure 구독을 바로 구입할 수 있습니다. 그런 다음 구독에 대한 액세스 권한을 받은 후 Microsoft Azure Portal에서 Machine Learning 작업 영역을 만들 수 있습니다. [단계별 지침](https://azure.microsoft.com/trial/get-started-machine-learning-b/)을 참조하세요.

또는 표준 Machine Learning 작업 영역 소유자에게 초대를 받아 소유자의 작업 영역에 액세스할 수 있습니다.

**무료 계층에서 사용하기 위해 고유한 Azure Blob Storage 계정을 지정할 수 있나요?**

아니요, 표준 계층은 표준 계층이 도입되기 전에 이용 가능했던 Machine Learning 서비스 버전과 같습니다.

**무료 계층에서 내 기계 학습 모델을 API로 배포할 수 있나요?**

예, 무료 계층의 일부로서 스테이징 API 서비스에 기계 학습 모델을 운영할 수 있습니다. 스테이징 API 서비스를 프로덕션하고 운영 서비스에 대한 프로덕션 엔드포인트를 가져오려면 표준 계층을 사용해야 합니다.

**Azure 무료 평가판과 Azure Machine Learning 무료 계층의 차이점은 무엇인가요?**

[Microsoft Azure 평가판](https://azure.microsoft.com/free/)은 한 달 동안 모든 Azure 서비스에 적용할 수 있는 크레딧을 제공합니다. Azure Machine Learning 무료 계층은 프로덕션 워크로드가 아닌 경우 Azure Machine Learning에 특별히 지속적인 액세스를 제공합니다.

**무료 계층에서 표준 계층으로 실험을 이동하려면 어떻게 하나요?**

무료 계층에서 표준 계층으로 실험을 복사하려면 다음을 수행합니다.

1. Azure Machine Learning Studio에 로그인하고 맨 위 탐색 모음의 작업 영역 선택기에 무료 작업 영역과 표준 작업 영역이 모두 표시되는지 확인합니다.
2. 표준 작업 영역에 있는 경우 무료 작업 영역으로 전환합니다.
3. 실험 목록 보기에서 복사하려는 실험을 선택하고 **복사** 명령 단추를 클릭합니다.
4. 팝업 대화 상자에서 표준 작업 영역을 선택하고 **복사** 단추를 클릭합니다.
   연결된 데이터 집합, 학습한 모델 등이 모두 실험과 함께 표준 작업 영역에 복사됩니다.
5. 실험을 다시 실행하고 표준 작업 영역에 웹 서비스를 다시 게시해야 합니다.

### <a name="studio-workspace"></a>스튜디오 작업 영역
**작업 영역마다 별도의 청구 내역을 볼 수 있습니까?**

작업 영역 요금은 단일 청구서에 해당하는 각 미터로 별도 청구됩니다.

**내 실험을 실행할 구체적인 계산 리소스 종류는 무엇입니까?**

Machine Learning 서비스는 다중 테넌트 서비스입니다. 백 엔드에서 사용되는 실제 계산 리소스는 다양하며 성능 및 예측성을 위해 최적화됩니다.

### <a name="guest-access"></a>게스트 액세스
**Azure Machine Learning Studio의 게스트 액세스란?**

게스트 액세스는 제한적인 체험 서비스입니다. Azure Machine Learning Studio에서 인증 없이 무료로 실험을 만들어 실행할 수 있습니다. 게스트 세션은 저장할 수 없으며 임시로 유지되고, 8시간으로 제한됩니다. 또한 R 및 Python이 제대로 지원되지 않고 스테이징 API가 충분하지 않으며 데이터 집합 크기와 저장소 용량도 제한됩니다. 하지만 Microsoft 계정으로 로그인한 사용자는 저장이 가능한 작업 영역과 보다 포괄적인 기능이 포함되어 있는 이전에 설명된 Machine Learning Studio의 무료 계층에 모두 액세스할 수 있습니다. 무료 Machine Learning 환경을 선택하려면 [https://studio.azureml.net](https://studio.azureml.net)에서 **시작하기**를 클릭하고 **게스트 액세스**를 선택하거나 Microsoft 계정으로 로그인합니다.

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx
