---
title: AKS(Azure Kubernetes Service)에 내부 네트워크용 수신 컨트롤러 만들기
description: AKS(Azure Kubernetes Service) 클러스터에서 내부 개인 네트워크용 NGINX 수신 컨트롤러를 설치하고 구성하는 방법에 대해 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 76ad9d21f7b328e7f201d227cdd9ace51c62a3fd
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356036"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 내부 가상 네트워크에 대한 수신 컨트롤러 만들기

수신 컨트롤러는 역방향 프록시, 구성 가능한 트래픽 라우팅, Kubernetes 서비스에 대한 TLS 종료를 제공하는 소프트웨어입니다. Kubernetes 수신 리소스는 개별 Kubernetes 서비스에 대한 수신 규칙 및 라우팅을 구성하는 데 사용됩니다. 수신 컨트롤러 및 수신 규칙을 사용하면 단일 IP 주소를 사용하여 Kubernetes 클러스터의 여러 서비스에 트래픽을 라우팅할 수 있습니다.

이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에 [NGINX 수신 컨트롤러][nginx-ingress]를 배포하는 방법을 보여 줍니다. 수신 컨트롤러는 내부 개인 가상 네트워크 및 IP 주소에 구성됩니다. 외부 액세스가 허용되지 않습니다. 두 응용 프로그램이 AKS 클러스터에서 실행되며 단일 IP 주소를 통해 각 응용 프로그램에 액세스할 수 있습니다.

또한 다음을 수행할 수 있습니다.

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 응용 프로그램 라우팅 추가 기능 사용][aks-http-app-routing]
- [동적 공용 IP를 사용하여 수신 컨트롤러를 만들고 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt 구성][aks-ingress-tls]
- [고정 공용 IP 주소를 사용하여 수신 컨트롤러를 만들고 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt 구성][aks-ingress-static-tls]

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Helm을 사용하여 NGINX 수신 컨트롤러, cert-manager 및 샘플 웹앱을 설치합니다. AKS 클러스터 내에서, Tiller의 서비스 계정을 사용하여 Helm이 초기화되어 있어야 합니다. Helm을 구성하고 사용하는 방법에 대한 자세한 내용은 [Helm을 사용하여 AKS(Azure Kubernetes Service)에 응용 프로그램 설치][use-helm]를 참조하세요.

또한 이 문서에서는 Azure CLI 버전 2.0.41 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="create-an-ingress-controller"></a>수신 컨트롤러 만들기

기본적으로는 동적 공용 IP 주소 할당을 통해 NGINX 수신 컨트롤러를 만듭니다. 일반적인 구성 요구 사항은 내부 개인 네트워크 및 IP 주소를 사용하는 것입니다. 이 접근 방식을 사용하면 외부 액세스 없이 서비스 액세스를 내부 사용자로 제한할 수 있습니다.

다음 예제 매니페스트 파일을 사용하여 *internal-ingress.yaml* 파일을 만듭니다. 이 예제에서는 *loadBalancerIP* 리소스에 *10.240.0.42*를 할당합니다. 수신 컨트롤러와 함께 사용할 고유한 내부 IP 주소를 제공합니다. 가상 네트워크 내에서 이 IP 주소가 이미 사용되고 있지 않은지 확인합니다.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

이제 Helm을 사용하여 *nginx-ingress* 차트를 배포합니다. 이전 단계에서 만든 매니페스트 파일을 사용하려면 `-f internal-ingress.yaml` 매개 변수를 추가합니다.

> [!TIP]
> 다음 예제에서는 `kube-system` 네임스페이스에 수신 컨트롤러를 설치합니다. 원하는 경우 환경용으로 다른 네임스페이스를 지정할 수 있습니다. AKS 클러스터가 RBAC를 사용할 수 없는 경우 명령에 `--set rbac.create=false`를 추가합니다.

```console
helm install stable/nginx-ingress --namespace kube-system -f internal-ingress.yaml
```

NGINX 수신 컨트롤러에 대해 Kubernetes 부하 분산 장치 서비스를 만든 경우 다음 예제 출력에 표시된 대로 내부 IP 주소를 할당합니다.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

아직 수신 규칙이 만들어지지 않았으므로 내부 IP 주소를 검색하면 NGINX 수신 컨트롤러의 기본 404 페이지가 표시됩니다. 수신 규칙은 다음 단계에서 구성됩니다.

## <a name="run-demo-applications"></a>데모 응용 프로그램 실행

작동 중인 수신 컨트롤러를 확인하기 위해 AKS 클러스터에서 두 개의 데모 응용 프로그램을 실행하겠습니다. 이 예제에서는 Helm을 사용하여 간단한 ‘Hello world’ 응용 프로그램의 두 인스턴스를 배포합니다.

샘플 Helm 차트를 설치하려면, 먼저 다음과 같이 Azure 샘플 리포지토리를 Helm 환경에 추가합니다.

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

다음 명령을 사용하여 Helm 차트에서 첫 번째 데모 응용 프로그램을 만듭니다.

```console
helm install azure-samples/aks-helloworld
```

이제 데모 응용 프로그램의 두 번째 인스턴스를 설치합니다. 두 번째 인스턴스에서, 두 응용 프로그램을 시각적으로 구분할 수 있도록 새 제목을 지정합니다. 고유한 서비스 이름도 지정합니다.

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>수신 경로 만들기

이제 두 응용 프로그램이 모두 Kubernetes 클러스터에서 실행됩니다. 트래픽을 각 응용 프로그램으로 라우팅하려면 Kubernetes 수신 리소스를 만듭니다. 수신 리소스는 두 응용 프로그램 중 하나로 트래픽을 라우팅하는 규칙을 구성합니다.

다음 예제에서 주소 `http://10.240.0.42/`으로 향하는 트래픽은 `aks-helloworld`라는 서비스로 라우트됩니다. 주소 `http://10.240.0.42/hello-world-two`로 향하는 트래픽은 `ingress-demo` 서비스로 라우팅됩니다.

`hello-world-ingress.yaml` 파일을 만들고 다음 예제 YAML을 복사합니다.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

`kubectl apply -f hello-world-ingress.yaml` 명령을 사용하여 수신 리소스를 만듭니다.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>수신 컨트롤러 테스트

수신 컨트롤러의 경로를 테스트하려면 웹 클라이언트를 통해 두 개의 응용 프로그램으로 이동합니다. 필요한 경우 AKS 클러스터의 Pod에서 내부 전용 기능을 신속하게 테스트할 수 있습니다. 테스트 Pod를 만들고 여기에 터미널 세션을 연결합니다.

```console
kubectl run -it --rm aks-ingress-test --image=debian
```

`apt-get`을 사용하여 Pod에 `curl`을 설치합니다.

```console
apt-get update && apt-get install -y curl
```

이제 *http://10.240.0.42*와 같은 `curl`을 사용하여 Kubernetes 수신 컨트롤러의 주소에 액세스합니다. 이 문서의 첫 번째 단계에서 수신 컨트롤러를 배포할 때 지정한 고유한 내부 IP 주소를 제공합니다.

```console
curl -L http://10.240.0.42
```

주소와 함께 추가 경로가 제공되지 않았으므로 수신 컨트롤러는 기본값인 */* 경로로 설정됩니다. 첫 번째 데모 응용 프로그램은 다음 축소된 예제 출력에 표시된 대로 반환됩니다.

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

이제 *http://10.240.0.42/hello-world-two*와 같은 주소에 */hello-world-two* 경로를 추가합니다. 사용자 지정 제목이 있는 두 번째 데모 응용 프로그램은 다음 축소된 예제 출력에 표시된 대로 반환됩니다.

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="next-steps"></a>다음 단계

이 문서에는 AKS의 몇 가지 외부 구성 요소가 포함되었습니다. 이러한 구성 요소에 대한 자세한 내용은 다음 프로젝트 페이지를 참조하세요.

- [Helm CLI][helm-cli]
- [NGINX 수신 컨트롤러][nginx-ingress]

또한 다음을 수행할 수 있습니다.

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 응용 프로그램 라우팅 추가 기능 사용][aks-http-app-routing]
- [동적 공용 IP를 사용하여 수신 컨트롤러를 만들고 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt 구성][aks-ingress-tls]
- [고정 공용 IP 주소를 사용하여 수신 컨트롤러를 만들고 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt 구성][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
