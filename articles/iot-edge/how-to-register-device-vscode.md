---
title: 새 Azure IoT Edge 장치 등록(VS Code) | Microsoft Docs
description: Visual Studio Code를 사용하여 Azure IoT Hub에 새 IoT Edge 장치 만들기
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/14/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cf9603c65454f076a494789e784c9352fb7bef33
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578708"
---
# <a name="register-a-new-azure-iot-edge-device-from-visual-studio-code"></a>Visual Studio Code에서 새 Azure IoT Edge 장치 등록

Azure IoT Edge에서 IoT 장치를 사용하려면 먼저 IoT Hub에 등록해야 합니다. 장치를 등록하면 Edge 워크로드에 대해 장치를 설정하는 데 사용할 수 있는 연결 문자열을 받게 됩니다. 

이 문서에서는 VS Code(Visual Studio Code)를 사용하여 새 IoT Edge 장치를 등록하는 방법을 보여 줍니다. VS Code에서 대부분의 작업을 수행하는 방법에는 여러 가지가 있습니다. 이 문서에서는 탐색기를 사용하지만, 명령 팔레트를 사용하여 대부분의 단계를 실행할 수도 있습니다. 

## <a name="prerequisites"></a>필수 조건

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/) 
* Visual Studio Code에 대한 [Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)

## <a name="sign-in-to-access-your-iot-hub"></a>로그인하여 IoT Hub에 액세스

Visual Studio Code용 Azure IoT 확장을 사용하여 IoT Hub에 대한 작업을 수행할 수 있습니다. 이러한 작업이 수행되려면 Azure 계정에 로그인하고 작업 중인 IoT Hub를 선택해야 합니다.

1. Visual Studio Code에서 **탐색기** 보기를 엽니다.

2. Explorer의 맨 아래에서 **Azure IoT Hub 장치** 섹션을 확장합니다. 

   ![Azure IoT Hub 장치 확장](./media/how-to-register-device-vscode/azure-iot-hub-devices.png)

3. **Azure IoT Hub 장치** 섹션 헤더에서 **...** 를 클릭합니다. 줄임표가 표시되지 않으면 헤더를 클릭하거나 마우스로 가리킵니다. 

4. **IoT Hub 선택**을 선택합니다.

5. Azure 계정에 로그인하지 않은 경우, 프롬프트를 따라 로그인합니다. 

6. Azure 구독을 선택합니다. 

7. IoT Hub를 선택합니다. 

## <a name="create-a-device"></a>장치 만들기

1. VS Code Explorer에서 **Azure IoT Hub 장치** 섹션을 확장합니다. 

2. **Azure IoT Hub 장치** 섹션 헤더에서 **...** 를 클릭합니다. 줄임표가 표시되지 않으면 헤더를 클릭하거나 마우스로 가리킵니다. 

3. **IoT Edge 장치 만들기**를 선택합니다. 

4. 열리는 텍스트 상자에 장치 ID를 입력합니다. 

출력 화면에 명령의 결과가 표시됩니다. 장치 정보가 인쇄되며, 여기에는 입력한 **deviceId**와 물리적 장치를 IoT Hub에 연결하는 데 사용할 수 있는 **connectionString**이 포함됩니다. 

## <a name="view-all-devices"></a>모든 장치 보기

IoT Hub에 연결하는 모든 장치는 Visual Studio Code Explorer의 **Azure IoT Hub 장치** 섹션에 나열됩니다. IoT Edge 장치는 다른 아이콘 및 확장하여 각 장치에 배포된 모듈을 표시할 수 있다는 점에서 비 Edge 장치와 구분됩니다. 

   ![VS Code에서 장치 보기](./media/how-to-register-device-vscode/view-devices.png)

## <a name="retrieve-the-connection-string"></a>연결 문자열 검색

장치를 설정할 준비가 되면, 물리적 장치를 IoT Hub에 있는 해당 ID와 연결하는 연결 문자열이 필요합니다.

1. **Azure IoT Hub 장치** 섹션에서 장치 ID를 마우스 오른쪽 단추로 클릭합니다. 
2. **장치 연결 문자열 복사**를 선택합니다.

   연결 문자열이 클립보드에 복사됩니다. 

오른쪽 클릭 메뉴에서 **장치 정보 가져오기**를 선택하여 출력 창에서 연결 문자열을 비롯한 모든 장치 정보를 볼 수도 있습니다. 


## <a name="next-steps"></a>다음 단계

[Visual Studio Code를 사용하여 장치에 모듈을 배포](how-to-deploy-modules-vscode.md)하는 방법을 알아봅니다.
