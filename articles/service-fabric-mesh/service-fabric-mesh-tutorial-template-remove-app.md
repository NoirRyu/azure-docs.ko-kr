---
title: 자습서 - Azure Service Fabric Mesh에서 실행 중인 앱 제거 | Microsoft Docs
description: 이 자습서에서는 Service Fabric Mesh에서 실행 중인 응용 프로그램을 제거하고 리소스를 삭제하는 방법을 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: f366413ae5f758601dfebc2a29ff848feb756083
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960433"
---
# <a name="tutorial-remove-an-application-and-resources"></a>자습서: 응용 프로그램 및 리소스 제거

이 자습서는 시리즈의 4부입니다. [이전에 Service Fabric Mesh에 배포된](service-fabric-mesh-tutorial-template-deploy-app.md) 실행 중인 응용 프로그램을 제거하는 방법을 알아보겠습니다. 

시리즈 4부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Service Fabric Mesh에서 실행 중인 앱 삭제
> * 응용 프로그램 리소스 삭제

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [템플릿을 사용하여 Service Fabric Mesh에 응용 프로그램 배포](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Service Fabric Mesh에서 실행 중인 응용 프로그램 확장](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Service Fabric Mesh에서 실행 중인 응용 프로그램 업그레이드](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * 응용 프로그램 제거

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에:

* Azure 구독이 아직 없으면 시작하기 전에 [무료 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

* [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md)을 열거나 [Azure CLI 및 Service Fabric Mesh CLI를 로컬에 설치](service-fabric-mesh-howto-setup-cli.md#install-the-service-fabric-mesh-cli-locally)합니다.

## <a name="delete-the-resource-group-and-all-the-resources"></a>리소스 그룹 및 모든 리소스 삭제

더 이상 필요하지 않은 경우 만든 리소스를 모두 삭제합니다. 이전에는 Azure Container Registry 인스턴스 및 Service Fabric Mesh 응용 프로그램 리소스를 호스트하는 [새로운 리소스 그룹을 생성](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry)했습니다.  이 리소스 그룹은 삭제할 수 있으며, 그러면 연결된 모든 리소스도 삭제됩니다.

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>리소스 개별 삭제
또한 ACR 인스턴스, Service Fabric Mesh 응용 프로그램 및 네트워크 리소스를 개별적으로 삭제할 수도 있습니다.

ACR 인스턴스를 삭제하려면:

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Service Fabric Mesh 응용 프로그램을 삭제하려면:

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

네트워크를 삭제하려면:
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Service Fabric Mesh에서 실행 중인 앱 삭제
> * 응용 프로그램 리소스 삭제