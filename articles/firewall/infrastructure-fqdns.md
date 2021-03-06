---
title: Azure Firewall의 인프라 FQDN
description: Azure Firewall의 인프라 FQDN에 대해 자세히 알아보기
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 1369a82829b2c80768d746ba92daf2482c1fd7f8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994142"
---
# <a name="infrastructure-fqdns"></a>인프라 FQDN

Azure Firewall은 기본적으로 허용되는 인프라 FQDN에 대한 기본 제공 규칙 컬렉션을 포함합니다. 이러한 FQDN은 플랫폼에 대해 특정적이며 다른 용도로 사용할 수 없습니다. 

다음 서비스는 기본 제공 규칙 컬렉션에 포함됩니다.

- 저장소 PIR(플랫폼 이미지 리포지토리)에 대한 액세스를 계산합니다.
- 관리 디스크 상태 저장소 액세스.
- Azure 진단 및 로깅(MDS)
- Azure Active Directory

## <a name="overriding"></a>대체 

마지막으로 처리되는 모두 거부 응용 프로그램 규칙 컬렉션을 만들어 이 기본 제공 인프라 규칙 컬렉션을 재정의할 수 있습니다. 항상 인프라 규칙 컬렉션 전에 처리됩니다. 인프라 규칙 컬렉션에 없는 항목은 기본적으로 거부됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Azure Firewall을 배포 및 구성](tutorial-firewall-deploy-portal.md)하는 방법에 대해 알아봅니다.