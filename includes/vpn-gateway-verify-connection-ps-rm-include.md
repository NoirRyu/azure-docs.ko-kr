---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 84bfec375878fe31e085f7de3cd4a41ace645c41
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458651"
---
'-디버그'를 사용하거나 사용하지 않고 'Get-AzureRmVirtualNetworkGatewayConnection' cmdlet을 사용하여 연결이 성공했는지 확인할 수 있습니다. 

1. 일치하는 값을 구성하는 데 다음 cmdlet 예제를 사용합니다. 메시지가 표시되면 '모두' 실행하기 위해 A를 선택합니다. 예제에서 '-Name'은 테스트하려는 연결의 이름을 나타냅니다.

  ```azurepowershell-interactive
  Get-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
  ```
2. cmdlet이 완료되면 값을 봅니다. 아래 예제에서는 연결 상태가 '연결됨'으로 표시되고 송/수신 바이트를 볼 수 있습니다.
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```