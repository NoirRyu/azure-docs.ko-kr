---
title: Azure IoT Edge 연속 통합 및 지속적인 배포 | Microsoft Docs
description: Azure IoT Edge 연속 통합 및 지속적인 배포 개요
author: shizn
manager: ''
ms.author: xshi
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 06dec64a55aaece4cd67ebf0485e34aa206a8936
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633736"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Azure IoT Edge 연속 통합 및 지속적인 배포

[Azure Pipelines에 대한 Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) 또는 [Jenkins용 Azure IoT Edge 플러그 인](https://plugins.jenkins.io/azure-iot-edge)과 함께 Azure IoT Edge 애플리케이션을 사용하여 DevOps를 손쉽게 채택할 수 있습니다. 이 문서에서는 Azure Pipelines 및 Microsoft TFS(Team Foundation Server)의 지속적인 통합 및 지속적인 배포 기능을 사용하여 애플리케이션을 빠르고 효율적으로 빌드, 테스트하여 Azure IoT Edge에 배포하는 방법을 설명합니다. 

이 아티클에서는 다음 방법을 설명합니다.
* 샘플 IoT Edge 솔루션을 만들고 체크 인합니다.
* Azure DevOps에 대한 Azure IoT Edge 확장 프로그램을 설치합니다.
* CI(지속적인 통합)을 구성하여 솔루션을 빌드합니다.
* 지속적인 배포(CD)를 구성하여 솔루션을 배포하고 응답을 봅니다.

이 아티클의 단계를 완료하려면 30분이 걸립니다.

![CI 및 CD](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Visual Studio Code를 사용하여 샘플 Azure IoT Edge 솔루션 만들기

이 섹션에서는 빌드 프로세스의 일부로 실행할 수 있는 단위 테스트를 포함하는 샘플 IoT Edge 솔루션을 만듭니다. 이 섹션의 지침을 수행하기 전에 [Visual Studio Code에서 여러 모듈을 사용하여 IoT Edge 솔루션 개발](tutorial-multiple-modules-in-vscode.md)의 단계를 완료합니다.

1. VS Code 명령 팔레트에서 **Azure IoT Edge: New IoT Edge solution** 명령을 입력하고 실행합니다. 작업 영역 폴더를 선택하고 솔루션 이름(기본 이름은 **EdgeSolution**임)을 지정한 다음, 이 솔루션의 첫 번째 사용자 모듈로 C# 모듈(**FilterModule**)을 만듭니다. 첫 번째 모듈의 Docker 이미지 리포지토리도 지정해야 합니다. 기본 이미지 리포지토리는 로컬 Docker 레지스트리(`localhost:5000/filtermodule`)를 기반으로 합니다. 향후 연속 통합을 위해 이를 Azure Container Registry(`<your container registry address>/filtermodule`) 또는 Docker 허브로 변경해야 합니다.

    ![ACR 설정](./media/how-to-ci-cd/acr.png)

2. VS Code 창에서 IoT Edge 솔루션 작업 영역이 로드됩니다. 필요에 따라 **Azure IoT Edge: Add IoT Edge module**을 입력하고 실행하여 모듈을 추가할 수 있습니다. 루트 폴더에 `modules` 폴더, `.vscode` 폴더 및 배포 매니페스트 템플릿 파일이 있습니다. 모든 사용자 모듈 코드는 `modules` 폴더 아래의 하위 폴더입니다. `deployment.template.json`은 배포 매니페스트 템플릿입니다. 이 파일의 일부 매개 변수는 모든 모듈 폴더에 존재하는 `module.json`에서 구문 분석됩니다.

3. 이제 샘플 IoT Edge 솔루션이 준비되었습니다. 기본 C# 모듈은 파이프 메시지 모듈로 작동합니다. `deployment.template.json`에서 이 솔루션에 두 개의 모듈이 포함된 것을 확인할 수 있습니다. `tempSensor` 모듈에서 메시지가 생성되어 `FilterModule`을 통해 직접 파이프된 다음, IoT 허브로 전송됩니다.

4. 이러한 프로젝트를 저장한 다음, Azure Repos 또는 TFS 리포지토리를 확인합니다.
    
> [!NOTE]
> Azure Repos를 사용하는 방법에 대한 자세한 내용은 [Share your code with Visual Studio and Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)(Visual Studio 및 Azure Repos와 코드 공유)를 참조하세요.


## <a name="configure-azure-pipelines-for-continuous-integration"></a>지속적인 통합을 위한 Azure Pipelines 구성
이 섹션에서는 샘플 IoT Edge 솔루션에 변경 사항을 체크 인하면 자동으로 실행되도록 구성된 빌드 파이프라인을 만들고 Azure Pipelines에 빌드 로그를 표시합니다.

1. Azure DevOps 조직(**https://dev.azure.com/{your organization}/**)에 로그인하고 샘플 앱을 체크 인한 프로젝트를 엽니다.

    ![코드 체크 인](./media/how-to-ci-cd/init-project.png)

1. Azure DevOps Marketplace에서 [Azure Pipelines용 Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy)를 방문하세요. **무료로 받기**를 클릭하고 마법사를 따라 이 확장 프로그램을 Azure DevOps 조직에 설치하거나 TFS에 다운로드합니다.

    ![확장 설치](./media/how-to-ci-cd/install-extension.png)

1. Azure Pipelines에서 **빌드 및 릴리스** 허브를 열고 **빌드** 탭에서 **+ 새 파이프라인**을 선택합니다. 또는 빌드 파이프라인이 이미 있는 경우 **+ 새로 만들기** 단추를 선택합니다.

    ![새 파이프라인](./media/how-to-ci-cd/add-new-build.png)

1. 메시지가 표시되면 **Git** 소스 형식을 선택합니다. 그런 다음, 코드가 있는 프로젝트, 리포지토리 및 분기를 선택합니다. **계속**을 선택합니다.

    ![git 선택](./media/how-to-ci-cd/select-vsts-git.png)

    **템플릿 선택** 창에서 **빈 프로세스로 시작**을 선택합니다.

    ![템플릿 선택](./media/how-to-ci-cd/start-with-empty.png)

1. 파이프라인 편집기에서 에이전트 풀을 선택합니다. 
    
    * Linux 컨테이너용 플랫폼 amd64에서 모듈을 빌드하려는 경우 **호스트된 Ubuntu 1604**를 선택합니다.
    * Windows 컨테이너용 플랫폼 amd64에서 모듈을 빌드하려는 경우 **호스트된 VS2017**을 선택합니다. 
    * Linux 컨테이너용 플랫폼 arm32v7에서 모듈을 빌드하려는 경우 **관리** 단추를 클릭하여 사용자 고유의 빌드 에이전트를 설정해야 합니다.
    
    ![빌드 에이전트 구성](./media/how-to-ci-cd/configure-env.png)

1. 에이전트 작업에서 “+”를 클릭하여 빌드 파이프라인에 두 가지 작업을 추가합니다. 첫 번째는 **Azure IoT Edge**의 작업이고, 두 번째는 **빌드 아티팩트 게시**의 작업입니다.
    
    ![작업 추가](./media/how-to-ci-cd/add-tasks.png)

1. 첫 번째 **Azure IoT Edge** 작업에서 **표시 이름**을 **모듈 빌드 및 푸시**로 업데이트하고 **작업** 드롭다운 목록에서 **빌드 및 푸시**를 선택합니다. **Module.json 파일** 텍스트 상자에서 아래 경로를 추가합니다. 그런 다음, **Container Registry 유형**을 선택하여 코드에서 동일한 레지스트리를 구성하고 선택하도록 합니다(module.json). 이 작업은 솔루션의 모든 모듈을 빌드 및 푸시하고 지정된 컨테이너 레지스트리로 게시합니다. 모듈이 다른 레지스트리로 푸시된 경우 다중 **모듈 빌드 및 푸시** 작업이 있을 수 있습니다. IoT Edge 솔루션이 코드 리포지토리의 루트 아래에 없는 경우 **Edge 솔루션 루트의 경로**를 빌드 정의에 지정할 수 있습니다.
    
    ![빌드 및 푸시](./media/how-to-ci-cd/build-and-push.png)

1. **빌드 아티팩트 게시** 작업에서는 빌드 작업에서 생성된 배포 파일을 지정합니다. **게시할 경로**를 “config/deployment.json”으로 설정합니다. 마지막 작업에서 **Edge 솔루션의 경로**를 설정한 경우 여기에 루트 경로를 조인해야 합니다. 예를 들어 Edge 솔루션 루트의 경로가 “./edgesolution”이면 **게시할 경로**는 “./edgesolution/config/deployment.json”이어야 합니다. `deployment.json` 파일은 빌드 시간 동안에 생성되므로 텍스트 상자의 빨간색 오류 줄은 무시해도 됩니다. 

    ![아티팩트 게시](./media/how-to-ci-cd/publish-build-artifacts.png)

1. **트리거** 탭을 열고 **연속 통합** 트리거를 켭니다. 코드를 포함하는 분기가 포함되어 있는지 확인합니다.

    ![트리거 구성](./media/how-to-ci-cd/configure-trigger.png)

    새 빌드 파이프라인을 저장합니다. **저장** 단추를 클릭합니다.


## <a name="configure-azure-pipelines-for-continuous-deployment"></a>지속적인 배포를 위한 Azure Pipelines 구성
이 섹션에서는 빌드 파이프라인이 아티팩트를 드롭하면 자동으로 실행되도록 구성된 릴리스 파이프라인을 만들고 Azure Pipelines에 배포 로그를 표시합니다.

1. **릴리스** 탭에서 **+ 새 파이프라인**을 선택합니다. 또는 릴리스 파이프라인이 이미 있는 경우 **+ 새로 만들기** 단추를 선택합니다.  

    ![릴리스 파이프라인 추가](./media/how-to-ci-cd/add-release-pipeline.png)

    **템플릿 선택** 창에서 **빈 작업으로 시작**을 선택합니다.

    ![빈 작업 시작](./media/how-to-ci-cd/start-with-empty-job.png)

2. 그런 다음, 릴리스 파이프라인은 **1단계**로 시작됩니다. **1단계**의 이름을 **QA**로 바꾸고 테스트 환경으로 사용합니다. 보통의 지속적인 배포 파이프라인에서는 일반적으로 여러 단계가 존재하기 때문에 DevOps 사례에 따라 더 많이 만들 수 있습니다.

    ![단계 만들기](./media/how-to-ci-cd/QA-env.png)

3. 릴리스를 빌드 아티팩트에 연결합니다. 아티팩트 영역에서 **추가**를 클릭합니다.

    ![아티팩트 추가](./media/how-to-ci-cd/add-artifacts.png)  
    
    **아티팩트 페이지 추가**에서 소스 형식 선택 **빌드**를 선택합니다. 그런 다음, 만든 프로젝트 및 빌드 파이프라인을 선택합니다. 그런 다음, **추가**를 클릭합니다.

    ![아티팩트 추가](./media/how-to-ci-cd/add-an-artifact.png)

    새 빌드를 사용할 때마다 새 릴리스가 생성되도록 지속적인 배포 트리거를 엽니다.

    ![트리거 구성](./media/how-to-ci-cd/add-a-trigger.png)

4. **QA 단계**로 이동하고 이 단계에서 작업을 구성합니다.

    ![QA 작업 구성](./media/how-to-ci-cd/view-stage-tasks.png)

   배포 작업은 플랫폼에 구애 받지 않으므로 **에이전트 풀**(또는 사용자가 관리하는 다른 에이전트에서)의 **호스트된 VS2017** 또는 **호스트된 Ubuntu 1604** 중에서 선택할 수 있습니다. “+”를 클릭하고 작업을 하나 추가합니다.

    ![QA에 대한 작업 추가](./media/how-to-ci-cd/add-task-qa.png)

5. Azure IoT Edge 작업에서 **동작** 드롭다운 목록으로 이동하여 **IoT Edge 디바이스에 배포**를 선택합니다. **Azure 구독**을 선택하고 **IoT Hub 이름**을 입력합니다. IoT Edge **배포 ID**와 배포 **우선 순위**를 지정할 수 있습니다. 또한 단일 또는 여러 장치에 배포하도록 선택할 수 있습니다. **여러 디바이스**에 배포하는 경우 디바이스 **대상 조건**을 지정해야 합니다. 대상 조건은 IoT Hub에서 Edge 디바이스 세트에 일치하는 필터입니다. 디바이스 태그를 조건으로 사용하려는 경우 IoT Hub 디바이스 쌍으로 해당 디바이스 태그를 업데이트해야 합니다. 여러 IoT Edge 디바이스가 ‘qa’로 태그가 지정되었다고 가정하면 작업 구성은 다음 스크린샷과 같아야 합니다. 

    ![QA에 배포](./media/how-to-ci-cd/deploy-to-qa.png)

    새 릴리스 파이프라인을 저장합니다. **저장** 단추를 클릭합니다. 그런 다음, **파이프라인**을 클릭하여 파이프라인으로 다시 돌아갑니다.

6. 두 번째 단계는 프로덕션 환경입니다. 새 단계 “PROD”를 추가하려면 간단히 단계 “QA”를 복제하고 복제된 단계의 이름을 **PROD**로 변경하면 됩니다.

    ![복제 단계](./media/how-to-ci-cd/clone-stage.png)

7. 프로덕션 환경을 위한 작업을 구성합니다. IoT Edge 디바이스가 ‘prod’로 태그가 지정되었다고 가정하고, 작업 구성에서 대상 조건을 “prod”로 업데이트하고, 배포 ID를 “deploy-prod”로 설정합니다. **저장** 단추를 클릭합니다. 그런 다음, **파이프라인**을 클릭하여 파이프라인으로 다시 돌아갑니다.
    
    ![프로덕션에 배포](./media/how-to-ci-cd/deploy-to-prod.png)

7. 현재 빌드 아티팩트는 **QA** 단계, **PROD** 단계에서 지속적으로 트리거됩니다. 그러나 대부분의 경우 QA 디바이스에서 일부 테스트 경우를 통합하고 비트를 수동으로 승인해야 합니다. 나중에 비트는 PROD 환경에 배포됩니다. 다음과 같이 PROD 단계에서 승인을 설정합니다.

    1. **배포 전 조건** 설정 패널을 엽니다.

        ![배포 전 조건 열기](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. **배포 전 승인**에서 **사용**을 설정합니다. 또한 **승인자** 입력을 채웁니다. 그런 다음 **Save**를 클릭합니다.
    
        ![조건 설정](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. 이제 릴리스 파이프라인을 다음과 같이 설정합니다.

    ![릴리스 파이프라인](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>빌드 및 릴리스 파이프라인을 사용하여 IoT Edge CI/CD 확인

이 섹션에서는 CI/CD 파이프라인 작업을 만들도록 빌드를 트리거합니다. 그런 다음, 배포에 성공했는지 확인합니다.

1. 빌드 작업을 트리거하려면 소스 코드 리포지토리에 커밋을 푸시하거나 수동으로 트리거할 수 있습니다. 다음 스크린샷과 같이 **큐** 단추를 클릭하여 빌드 파이프라인에서 빌드 작업을 트리거할 수 있습니다.

    ![수동 트리거](./media/how-to-ci-cd/manual-trigger.png)

2. 빌드 파이프라인이 성공적으로 완료되면 릴리스를 **QA** 단계로 트리거합니다. 빌드 파이프라인 로그로 이동하면 다음을 확인할 수 있습니다.

    ![빌드 로그](./media/how-to-ci-cd/build-logs.png)

3. **QA** 단계에 성공적으로 배포되면 승인자에게 알림이 트리거됩니다. 릴리스 파이프라인으로 이동하면 다음을 확인할 수 있습니다. 

    ![승인 보류 중](./media/how-to-ci-cd/pending-approval.png)


4. 승인자가 이 변경 내용을 승인한 후 **PROD**에 배포할 수 있습니다.

    ![PROD에 배포](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>다음 단계

* [단일 장치 또는 대규모 IoT Edge 배포에 대한 이해](module-deployment-monitoring.md)를 통해 IoT Edge 배포 이해
* [대규모 IoT Edge 모듈 배포 및 모니터링](how-to-deploy-monitor.md)에서 배포를 생성, 업데이트 또는 삭제하는 단계를 연습합니다.
