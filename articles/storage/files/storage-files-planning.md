---
title: Azure Files 배포에 대한 계획 | Microsoft Docs
description: Azure Files 배포에 대한 계획 시 고려해야 할 사항에 대해 알아봅니다.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 06/12/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 0701049eb1aa86398e90484dbf21ef3781270fba
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831384"
---
# <a name="planning-for-an-azure-files-deployment"></a>Azure Files 배포에 대한 계획
[Azure Files](storage-files-introduction.md)는 산업 표준 SMB 프로토콜을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유를 제공합니다. Azure Files은 완벽하게 관리되기 때문에 프로덕션 시나리오에서 이를 배포하면 파일 서버 또는 NAS 장치를 훨씬 쉽게 배포하고 관리할 수 있습니다. 이 문서에서는 조직 내에서 프로덕션 용도로 Azure 파일 공유를 배포할 때 고려해야 할 항목을 다룹니다.

## <a name="management-concepts"></a>관리 개념
 다음 다이어그램에서는 Azure Files 관리 구조를 보여 줍니다.

![파일 구조](./media/storage-files-introduction/files-concepts.png)

* **Storage 계정**: Azure Storage에 대한 모든 액세스는 Storage 계정을 통해 수행됩니다. 저장소 계정 용량에 대한 자세한 내용은 [확장성 및 성능 목표](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

* **공유**: File Storage 공유는 Azure의 SMB 파일 공유입니다. 모든 디렉터리 및 파일을 부모 공유에 만들어야 합니다. 계정에 포함할 수 있는 공유 수에는 제한이 없으며, 공유에 저장할 수 있는 파일 수에는 파일 공유의 최대 5TiB의 최대 용량까지 제한이 없습니다.

* **디렉터리**: 선택적인 디렉터리 계층 구조입니다.

* **파일**: 공유에 있는 파일입니다. 파일의 크기는 최대 1TiB일 수 있습니다.

* **URL 형식**: 파일 REST 프로토콜을 사용하여 Azure 파일 공유에 적용된 요청의 경우, 다음 URL 형식을 사용하여 파일의 주소를 지정할 수 있습니다.

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/directories>/<file>
    ```

## <a name="data-access-method"></a>데이터 액세스 방법
Azure Files는 두 가지의 편리한 데이터 액세스 방법을 기본 제공하며, 이 두 가지 방법을 따로 또는 서로 조합하여 데이터에 액세스할 수 있습니다.

1. **직접 클라우드 액세스**: Azure 파일 공유는 업계 표준 서버 메시지 블록(SMB) 프로토콜 또는 파일 REST API를 통해 [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) 및/또는 [Linux](storage-how-to-use-files-linux.md)에 의해 탑재될 수 있습니다. SMB를 사용하면 공유되는 파일의 읽기 및 쓰기는 Azure에서 파일 공유에서 직접 수행됩니다. Azure에서 VM으로 탑재하려면 운영 체제의 SMB 클라이언트가 SMB 2.1 이상을 지원해야 합니다. 사용자의 워크스테이션과 같은 온-프레미스를 탑재하려면 워크스테이션이 지원하는 SMB 클라이언트는 SMB 3.0 이상(암호화 사용)을 지원해야 합니다. SMB 외에도 새로운 응용 프로그램 또는 서비스는 소프트웨어 개발을 위해 쉽고 확장 가능한 응용 프로그래밍 인터페이스를 제공하는 파일 REST를 통해 파일 공유에 직접 액세스할 수 있습니다.
2. **Azure 파일 동기화**: Azure 파일 동기화를 사용하면 공유를 Windows 서버 온-프레미스 또는 Azure에 복제할 수 있습니다. 사용자는 Windows Server를 통해(예: SMB 또는 NFS 공유를 통해) 파일 공유에 액세스하게 됩니다. 이는 지사와 같이 Azure 데이터 센터에서 멀리 떨어진 곳에서 데이터에 액세스하고 수정하는 시나리오에 유용합니다. 여러 Windows Server 엔드포인트 간(예: 여러 지사 간)에 데이터가 복제될 수 있습니다. 마지막으로, 모든 데이터를 여전히 서버를 통해 액세스할 수 있지만 서버에는 데이터의 전체 복사본이 없도록 데이터는 Azure Files에 계층화될 수 있습니다. 오히려 사용자가 열 때 데이터 원활하게 호출됩니다.

다음 표는 사용자와 응용 프로그램이 Azure 파일 공유에 액세스할 수 있는 방법을 보여줍니다.

| | 직접 클라우드 액세스 | Azure 파일 동기화 |
|------------------------|------------|-----------------|
| 어떤 프로토콜을 사용해야 합니까? | Azure Files는 SMB 2.1, SMB 3.0 및 파일 REST API를 지원합니다. | Windows Server(SMB, NFS, FTPS, 등)에서 지원되는 프로토콜을 통해 Azure 파일 공유 액세스 |  
| 어디에서 워크로드를 실행합니까? | **Azure에서**: Azure Files는 데이터에 직접 액세스할 수 있습니다. | **느린 네트워크의 온-프레미스**: Windows, Linux 및 macOS 클라이언트는 Azure 파일 공유의 빠른 캐시로서 로컬 온-프레미스 Windows 파일 공유를 탑재할 수 있습니다. |
| ACL의 어떤 수준이 필요합니까? | 공유 및 파일 수준입니다. | 공유, 파일 및 사용자 수준입니다. |

## <a name="data-security"></a>데이터 보안
Azure Files에는 데이터 보안을 위한 몇 가지 기본 제공 옵션이 있습니다.

* 두 네트워크 상 프로토콜에서 암호화 지원: SMB 3.0 암호화 및 HTTPS를 통한 파일 REST입니다. 기본적으로: 
    * SMB 3.0 암호화를 지원하는 클라이언트는 암호화된 채널을 통해 데이터를 송신 및 수신합니다.
    * 암호화가 포함된 SMB 3.0을 지원하지 않는 클라이언트는 SMB 2.1 또는 암호화 기능이 없는 SMB 3.0을 통해 데이터 센터 내에서 통신할 수 있습니다. SMB 클라이언트는 암호화 기능이 없는 SMB 3.0 또는 SMB 2.1을 통해 데이터 센터 내에서 통신할 수 없습니다.
    * 클라이언트는 HTTP 또는 HTTPS를 사용하여 파일 REST를 통해 통신할 수 있습니다.
* 미사용 데이터 암호화([Azure Storage 서비스 암호화](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): SSE(저장소 서비스 암호화)는 모든 저장소 계정에 대해 사용되도록 설정됩니다. 미사용 데이터는 완전히 관리되는 키로 암호화됩니다. 미사용 암호화를 사용할 경우 저장소 비용이 증가하거나 성능이 저하되지 않습니다. 
* 암호화된 데이터 전송 시 선택적 요구 사항: 이를 선택하면 Azure Files는 암호화되지 않은 채널을 통한 데이터 액세스를 거부합니다. 구체적으로 말하면, 암호화 연결을 통한 HTTPS 및 SMB 3.0만 허용됩니다. 

    > [!Important]  
    > 데이터의 보안 전송이 필요한 경우 암호화된 SMB 3.0과 통신할 수 없는 이전 버전의 SMB 클라이언트는 실패합니다. 자세한 내용은 [Windows에 탑재](storage-how-to-use-files-windows.md), [Linux에 탑재](storage-how-to-use-files-linux.md) 및 [macOS에 탑재](storage-how-to-use-files-mac.md)를 참조하세요.

최상의 보안을 위해 최신 클라이언트를 사용하여 데이터에 액세스할 때마다 미사용 시 암호화와 전송 시 데이터 암호화 모두를 항상 사용하는 것이 좋습니다. 예를 들어, SMB 2.1만 지원하는 Windows Server 2008 R2 VM에 공유를 탑재하는 경우 SMB 2.1이 암호화를 지원하지 않기 때문에 저장소 계정에 암호화되지 않은 트래픽을 허용해야 합니다.

Azure 파일 공유에 액세스하기 위해 Azure 파일 동기화를 사용하는 경우, 미사용 시 데이터 암호화 필요 여부에 관계 없이 항상 암호화된 HTTPS 및 SMB 3.0을 사용하여 데이터를 Windows 서버에 동기화합니다.

## <a name="file-share-performance-tiers"></a>파일 공유 성능 계층
Azure Files에서는 표준과 프리미엄의 두 성능 계층을 지원합니다.

* **표준 파일 공유**는 범용 파일 공유, 개발/테스트 환경 등 성능 가변성의 영향을 그다지 받지 않는 IO 워크로드에 대해 안정적인 성능을 제공하는 회전식 HDD(하드 디스크 드라이브)를 통해 지원되며 종량제 청구 모델에서만 제공됩니다.
* **프리미엄 파일 공유(미리 보기)** 는 IO를 가장 많이 사용하는 워크로드에서 수행하는 대다수 IO 작업에 대해 우수한 성능과 짧은 대기 시간(몇 밀리초 단위)을 일관되게 제공하는 SSD(반도체 디스크)를 통해 지원됩니다. 따라서 데이터베이스, 웹 사이트 호스팅, 개발 환경 등의 다양한 워크로드에 적합합니다. 프리미엄 파일 공유는 프로비전된 청구 모델에서만 제공됩니다.

### <a name="provisioned-shares"></a>프로비전된 공유
프리미엄 파일 공유는 고정 GiB/IOPS/처리량 비율을 기준으로 프로비전됩니다. 공유에 프로비전되는 각 GiB당 IOPS 1개, 초당 0.1MiB의 처리량이 공유당 최대 한도까지 지급됩니다. 허용되는 최소 프로비저닝 용량은 100GiB(최소 IOPS/처리량)입니다. 공유 크기는 언제든지 늘리고 줄일 수 있지만, 마지막으로 크기를 늘린 후 24시간이 지나야 줄일 수 있습니다.

최상의 노력 원칙에 따라 모든 공유는 60분 이상의 기간 동안(공유 크기별로 다름) 프로비전된 저장소 용량(GiB)당 IOPS를 3개까지 버스트할 수 있습니다. 새 공유에는 프로비전된 용량을 기준으로 전체 버스트 크레딧이 초기 제공됩니다.

| 프로비전된 용량 | 100GiB | 500GiB | 1TiB | 5TiB | 
|----------------------|---------|---------|-------|-------|
| 기준 IOPS | 100 | 500 | 1,024 | 5,120 | 
| 버스트 한도 | 300 | 1,500 | 3,072 | 15,360 | 
| 처리량 | 110MiB/초 | 150MiB/초 | 202MiB/초 | 612MiB/초 |

## <a name="file-share-redundancy"></a>파일 공유 중복성
Azure Files는 LRS(로컬 중복 저장소), ZRS(영역 중복 저장소) 및 GRS(지역 중복 저장소)라는 세 가지 데이터 중복 옵션을 지원합니다. 다음 섹션에서는 서로 다른 중복 옵션의 차이점에 대해 설명합니다.

### <a name="locally-redundant-storage"></a>로컬 중복 저장소
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>영역 중복 저장소
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>지역 중복 저장소
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="data-growth-pattern"></a>데이터 증가 패턴
현재, Azure 파일 공유의 최대 크기는 5TiB입니다. 현재 이 제한으로 인해 Azure 파일 공유를 배포할 때 예상되는 데이터 증가를 고려해야 합니다. 

Azure 파일 동기화를 사용하여 여러 Azure 파일 공유를 하나의 Windows 파일 서버에 동기화할 수 있습니다. 이를 통해 온-프레미스가 있을 수 있는 이전의 큰 파일 공유를 Azure 파일 동기화로 가져올 수 있습니다. 자세한 내용은 [Azure 파일 동기화 배포 계획](storage-files-planning.md)을 참조하세요.

## <a name="data-transfer-method"></a>데이터 전송 방법
온-프레미스 파일 공유와 같은 기존 파일 공유에서 Azure Files로 데이터를 대량으로 쉽게 전송할 수 있는 여러 옵션이 있습니다. 자주 사용되는 옵션 몇 가지는 다음과 같습니다(비 한정적 목록).

* **Azure 파일 동기화**: Azure 파일 공유("클라우드 엔드포인트")와 Windows 디렉터리 네임스페이스("서버 엔드포인트") 사이의 첫 번째 동기화의 일부로서, Azure 파일 동기화는 기존 파일 공유에서 Azure Files로 모든 데이터를 복제합니다.
* **[Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: Azure Import/Export 서비스를 사용하면 하드 디스크 드라이브를 Azure 데이터 센터에 발송하여 많은 양의 데이터를 안전하게 Azure 파일 공유로 전송할 수 있습니다. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy는 Windows 및 Windows Server와 함께 제공되는 잘 알려진 복사 도구입니다. 파일 공유를 로컬로 탑재하고 탑재된 위치를 Robocopy 명령의 대상으로 사용하는 방식으로 Robocopy를 사용하여 데이터를 Azure Files로 전송할 수 있습니다.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: AzCopy는 간단한 명령과 최적의 성능으로 데이터를 Azure Files 및 Azure Blob Storage에서 복사하도록 디자인된 명령줄 유틸리티입니다. AzCopy는 Windows 및 Linux에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
* [Azure Files 배포](storage-files-deployment-guide.md)
* [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
