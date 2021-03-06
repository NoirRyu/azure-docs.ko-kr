---
title: VS Code에서 여러 Azure IoT Edge 모듈 사용 | Microsoft Docs
description: Visual Studio Code에 대한 IoT 확장을 사용하여 한 번에 Azure IoT Edge에 대한 다중 모듈 개발
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d895e10c6b667a05156b2ce0f83f7776b9ba4f0a
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568763"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Visual Studio Code에서 여러 모듈을 사용하여 IoT Edge 솔루션 개발

Visual Studio Code의 여러 모듈을 사용하여 Azure IoT Edge 솔루션을 개발할 수 있습니다. 이 문서에서는 VS Code의 시뮬레이션된 IoT Edge 장치에서 센서 데이터를 파이핑하는 IoT Edge 솔루션을 만들고, 업데이트하고, 배포하는 방법을 보여줍니다. 

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음 필수 구성 요소가 있어야 합니다.

- [Visual Studio Code](https://code.visualstudio.com/)
- [Visual Studio Code에 대한 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [C# for Visual Studio Code(OmniSharp 제공) 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)
- 하나 이상의 IoT Edge 장치가 있는 활성 IoT Hub

또한 이미지 및 컨테이너를 관리하려면 [Docker for VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)와 Azure IoT Hub Device Explorer를 통합해야 합니다.

## <a name="create-your-iot-edge-solution"></a>IoT Edge 솔루션 만들기

1. Visual Studio code에서 **보기** > **통합된 터미널**을 선택하여 통합 터미널을 엽니다. 

1. VS Code **명령 팔레트**에서 **Azure IoT Edge: 새로운 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 작업 영역 폴더를 선택하고 솔루션 이름을 입력합니다(기본값은 EdgeSolution). 이 솔루션의 첫 번째 사용자 모듈로 C# 모듈을 만듭니다(**PipeModule**이라는 이름 사용). C# 모듈의 기본 템플릿은 업스트림에서 다운스트림으로 메시지를 직접 파이프하는 파이프 모듈입니다. 첫 번째 모듈의 Docker 이미지 리포지토리도 지정해야 합니다. 기본 이미지 리포지토리는 로컬 Docker 레지스트리(**localhost:5000/<first module name>**)를 기반으로 합니다. 이를 Azure Container Registry 또는 Docker 허브로 변경할 수 있습니다. 

2. VS Code 창에서 IoT Edge 솔루션 작업 영역을 로드합니다. 루트 폴더에 **modules** 폴더, **.vscode** 폴더 및 배포 매니페스트 템플릿 파일이 포함되어 있습니다. 디버그 구성은 .vscode 폴더에 있습니다. 모든 사용자 모듈 코드는 모듈 폴더의 하위 폴더입니다. deployment.template.json 파일은 배포 매니페스트 템플릿입니다. 이 파일의 일부 매개 변수는 모든 모듈 폴더에 존재하는 module.json 파일에서 구문 분석됩니다.

3. 이 솔루션 프로젝트에 두 번째 모듈을 추가합니다. 새 모듈을 현재 솔루션에 추가하는 여러 가지 방법이 있습니다. **Azure IoT Edge: IoT Edge 모듈 추가** 명령을 입력하고 실행합니다. 업데이트할 배포 템플릿 파일을 선택합니다. 또는 모듈 폴더나 deployment.template.json 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 모듈 추가**를 선택합니다. 그러면 모듈 형식을 선택하는 드롭다운 목록이 있습니다. **PipeFunction**이라는 **Azure Functions - C#** 및 해당 Docker 이미지 리포지토리를 선택합니다. C# 함수 모듈의 기본 템플릿은 업스트림에서 다운스트림으로 메시지를 직접 파이프하는 파이프 모듈입니다.

4. deployment.template.json 파일을 엽니다. 이 파일에서 3개의 모듈 및 런타임을 선언하는지 확인합니다. 메시지는 tempSensor 모듈에서 생성됩니다. 메시지는 SampleModule 및 SampleFunction 모듈을 통해 직접 파이핑된 후 IoT 허브로 전송됩니다. 

5. 다음 내용으로 이러한 모듈의 경로를 업데이트합니다.

   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/PipeModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/PipeModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/PipeFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/PipeFunction/outputs/output1 INTO $upstream"
        },
   ```

5. 이 파일을 저장합니다.

## <a name="build-and-deploy-your-iot-edge-solution"></a>IoT Edge 솔루션 빌드 및 배포

1. VS Code **명령 팔레트**에서 **Azure IoT Edge: IoT Edge 솔루션 빌드** 명령을 입력하고 실행합니다. 이 명령은 각 모듈 폴더에 있는 module.json 파일에 따라 각 모듈 Docker 이미지를 빌드, 컨테이너화 및 푸시하기 시작합니다. 그런 다음, deployment.template.json 파일에 필요한 값을 전달하고 config 폴더의 정보를 사용하여 deployment.json 파일을 생성합니다. VS Code의 통합 터미널은 빌드 진행률을 보여줍니다. 

2. Azure IoT Hub **Device Explorer**에서 IoT Edge 장치 ID를 마우스 오른쪽 단추로 클릭한 다음, **Edge 장치에 대한 배포 만들기** 명령을 선택합니다. config 폴더에서 deployment.json 파일을 선택합니다. VS Code의 통합 터미널은 배포 ID를 사용하여 성공적으로 생성된 배포를 보여줍니다.

3. 개발 컴퓨터에서 IoT Edge 장치를 시뮬레이션하는 경우 몇 분 안에 모든 모듈 이미지 컨테이너가 시작되는 것을 확인할 수 있습니다.

## <a name="view-the-generated-data"></a>생성된 데이터 보기

1. IoT 허브에 도착하는 데이터를 모니터링하려면 **보기** > **명령 팔레트**를 선택합니다. 그런 다음, **IoT: D2C 메시지 모니터링 시작** 명령을 선택합니다. 
2. 데이터 모니터링을 중지하려면 **명령 팔레트**에서 **IoT: D2C 메시지 모니터링 중지** 명령을 사용합니다. 

## <a name="next-steps"></a>다음 단계

Visual Studio Code에서 Azure IoT Edge를 개발하는 다른 시나리오에 대해 알아봅니다.

* [C#](how-to-develop-csharp-module.md) 또는 [Node.js](how-to-develop-node-module.md)를 사용하여 VS Code에서 모듈을 개발합니다.
* [C#](how-to-develop-csharp-function.md)을 사용하여 VS Code에서 Azure Functions를 개발합니다.

IoT Edge 장치의 모듈을 개발하려면 [Azure IoT Hub SDK를 이해하고 사용](../iot-hub/iot-hub-devguide-sdks.md)합니다.