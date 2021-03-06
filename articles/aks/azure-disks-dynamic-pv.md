---
title: Azure Kubernetes Service를 사용하여 영구 볼륨 만들기
description: Azure 디스크를 사용하여 AKS(Azure Kubernetes Service)에서 Pod에 대한 영구 볼륨을 만드는 방법 알아보기
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/20/2018
ms.author: iainfou
ms.openlocfilehash: 7048ab4e08d25fd5181857a4e7592d0bcb7d3b5f
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885597"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)용 Azure 디스크를 사용하여 영구 볼륨 만들기

영구적 볼륨은 Kubernetes Pod와 함께 사용하기 위해 프로비전된 저장소 부분을 나타냅니다. 하나 이상의 Pod에서 영구적 볼륨을 사용할 수 있으며 동적 또는 정적으로 프로비전할 수 있습니다. Kubernetes 영구적 볼륨에 대한 자세한 내용은 [Kubernetes 영구적 볼륨][kubernetes-volumes]을 참조하세요. 이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에서 Azure 디스크와 함께 영구 볼륨을 사용하는 방법을 설명합니다.

> [!NOTE]
> Azure 디스크는 *액세스 모드* 형식 *ReadWriteOnce*만 사용하여 탑재할 수 있으며, 이 모드는 단일 AKS 노드에서만 사용할 수 있습니다. 여러 노드에서 영구 볼륨을 공유해야 하는 경우, [Azure 파일][azure-files-pvc]을 사용하는 것이 좋습니다.

## <a name="built-in-storage-classes"></a>기본 제공 저장소 클래스

저장소 클래스를 사용하여 영구적 볼륨에서 저장소 단위를 동적으로 생성되는 방법을 정의합니다. Kubernetes 저장소 클래스에 대한 자세한 내용은 [Kubernetes 저장소 클래스][kubernetes-storage-classes]를 참조하세요.

모든 AKS 클러스터에는 Azure 디스크에서 작동하도록 구성된 2개의 미리 만들어진 저장소 클래스가 포함되어 있습니다.

* *default* 저장소 클래스는 표준 Azure 디스크를 프로비전합니다.
    * 표준 저장소는 HDD에 의해 지원되며 성능은 그대로이면서 비용 효율적인 저장소를 제공합니다. 표준 디스크는 비용 효율적인 개발 및 테스트 워크로드에 적합합니다.
* *managed-premium* 저장소 클래스는 프리미엄 Azure 디스크를 프로비전합니다.
    * 프리미엄 디스크는 SSD 기반 고성능의 대기 시간이 짧은 디스크에서 지원합니다. 프로덕션 워크로드를 실행하는 VM에 완벽한 디스크입니다. 클러스터의 AKS 노드가 프리미엄 저장소를 사용하는 경우 *managed-premium* 클래스를 선택합니다.

[kubectl get sc][kubectl-get] 명령을 사용하여 미리 생성된 저장소 클래스를 확인합니다. 다음 예제에서는 AKS 클러스터 내에서 사용할 수 있는 미리 생성된 저장소 클래스를 보여 줍니다.

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> 영구 볼륨 클레임은 GiB로 지정되지만 Azure 관리 디스크는 특정 크기에 대한 SKU로 청구됩니다. 이러한 SKU의 범위는 S4 또는 P4 디스크에 대한 32GiB에서 S50 또는 P50 디스크에 대한 4TiB입니다. 프리미엄 관리 디스크의 처리량 및 IOPS 성능은 SKU 및 AKS 클러스터에서 노드의 인스턴스 크기에 따라 달라집니다. 자세한 내용은 [Managed Disks의 가격 책정 및 성능][managed-disk-pricing-performance]을 참조하세요.

## <a name="create-a-persistent-volume-claim"></a>영구적 볼륨 클레임 만들기

PVC(영구적 볼륨 클레임)을 사용하여 저장소 클래스를 기반으로 하는 저장소를 자동으로 프로비전합니다. 이 경우에 PVC는 미리 생성된 저장소 클래스 중 하나를 사용하여 표준 또는 프리미엄 Azure 관리 디스크를 만들 수 있습니다.

파일 `azure-premium.yaml`을 만들고 다음 매니페스트에 복사합니다. 클레임은 *ReadWriteOnce* 액세스 권한이 *5GB* 크기의 `azure-managed-disk`이라는 디스크를 요구합니다. *managed-premium* 저장소 클래스를 저장소 클래스로 지정합니다.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> 표준 저장소를 사용하는 디스크를 만들려면 *managed-premium* 대신 `storageClassName: default`를 사용합니다.

[kubectl create][kubectl-create] 명령을 사용하여 영구적 볼륨 클레임을 만들고 *azure-premium.yaml* 파일을 지정합니다.

```
$ kubectl create -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>영구적 볼륨 사용

영구적 볼륨 클레임이 생성되고 디스크가 성공적으로 프로비전되면 디스크에 액세스하여 Pod를 만들 수 있습니다. 다음 매니페스트는 *azure-managed-disk*라는 영구적 볼륨 클레임을 사용하여 `/mnt/azure` 경로에 Azure 디스크를 탑재하는 기본 NGINX Pod를 만듭니다.

파일 `azure-pvc-disk.yaml`을 만들고 다음 매니페스트에 복사합니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

다음 예에 표시된 대로 [kubectl create][kubectl-create] 명령을 사용하여 Pod를 만듭니다.

```
$ kubectl create -f azure-pvc-disk.yaml

pod/mypod created
```

이제 Azure 디스크가 `/mnt/azure` 디렉터리에 탑재된 Pod가 실행되고 있습니다. 다음 압축된 예제에 표시된 것처럼 `kubectl describe pod mypod`를 통해 Pod를 검사할 때 이 구성을 볼 수 있습니다.

```
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false

Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>영구적 볼륨 백업

영구적 볼륨에 데이터를 백업하려면 볼륨에 대한 관리 디스크의 스냅숏을 만듭니다. 그런 다음, 이 스냅숏을 사용하여 복원된 디스크를 만들고 데이터를 복원하는 수단으로 Pod에 연결할 수 있습니다.

먼저 *azure-managed-disk*라는 PVC의 경우처럼 `kubectl get pvc` 명령을 사용하여 볼륨 이름을 가져옵니다.

```
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

이 볼륨 이름은 기본 Azure 디스크 이름을 형성합니다. [az disk list][az-disk-list]를 사용하여 디스크 ID를 쿼리하고 다음 예에 표시된 것처럼 PVC 볼륨 이름을 제공합니다.

```
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

디스크 ID를 사용하여 [az snapshot create][az-snapshot-create]를 통해 스냅숏 디스크를 만듭니다. 다음 예에서는 AKS 클러스터와 동일한 리소스 그룹에서 *pvcSnapshot*이라는 스냅숏을 만듭니다(*MC_myResourceGroup_myAKSCluster_eastus*). AKS 클러스터가 액세스 권한이 없는 리소스 그룹에서 스냅숏을 만들고 디스크를 복원하는 경우 사용 권한 문제가 발생할 수 있습니다.

```azurecli
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

디스크의 데이터 양에 따라 스냅숏을 만드는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="restore-and-use-a-snapshot"></a>스냅숏 복원 및 사용

디스크를 복원하고 Kubernetes Pod와 함께 사용하려면 [az disk create][az-disk-create]로 디스크를 만들 경우 스냅숏을 원본으로 사용합니다. 원래 데이터 스냅숏에 액세스해야 할 경우 이 작업은 원래 리소스를 보존합니다. 다음 예제에서는 *pvcSnapshot*이라는 스냅숏에서 *pvcRestored*라는 디스크를 만듭니다.

```azurecli
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Pod를 사용하여 복원된 디스크를 사용하려면 매니페스트에 디스크의 ID를 지정합니다. [az disk show][az-disk-show] 명령을 사용하여 디스크 ID를 가져옵니다. 다음 예제에서는 이전 단계에서 만든 *pvcRestored*에 대한 디스크 ID를 가져옵니다.

```azurecli
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

`azure-restored.yaml`이라는 Pod 매니페스트를 만들고 이전 단계에서 가져온 디스크 URI를 지정합니다. 다음 예제에서는 */mnt/azure*에 복원된 디스크를 볼륨으로 탑재하여 기본 NGINX 웹 서버를 만듭니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
    - name: myfrontendrestored
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

다음 예에 표시된 대로 [kubectl create][kubectl-create] 명령을 사용하여 Pod를 만듭니다.

```
$ kubectl create -f azure-restored.yaml

pod/mypodrestored created
```

볼륨 정보를 보여주는 압축된 다음 예제와 같이 `kubectl describe pod mypodrestored`를 사용하여 Pod의 세부 정보를 볼 수 있습니다.

```
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>다음 단계

Azure 디스크를 사용하는 Kubernetes 영구적 볼륨에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure 디스크용 Kubernetes 플러그 인][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
