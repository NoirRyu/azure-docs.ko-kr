---
title: Azure Data Factory를 사용하여 Netezza에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Netezza에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: 1b7499990a049f276bf1af9e31b639ea4944d8f7
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167571"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Netezza에서 데이터 복사 

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Netezza에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Netezza에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

Azure Data Factory는 연결을 허용하는 기본 제공 드라이버를 제공합니다. 이 커넥터를 사용하기 위해 드라이버를 수동으로 설치할 필요가 없습니다.

## <a name="get-started"></a>시작하기

.NET SDK, Python SDK, Azure PowerShell, REST API 또는 Azure Resource Manager 템플릿을 통해 복사 작업을 사용하는 파이프라인을 만들 수 있습니다. 복사 작업이 포함된 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](quickstart-create-data-factory-dot-net.md)를 참조하세요.

다음 섹션에서는 Netezza 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음 속성은 Netezza 연결 서비스에 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | **형식** 속성은 **Netezza**로 설정되어야 합니다. | yes |
| connectionString | Netezza에 연결할 ODBC 연결 문자열입니다. 이 필드를 **SecureString** 형식으로 표시하여 Data Factory에서 안전하게 저장합니다. [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)할 수도 있습니다. | yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 선택할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |아니요 |

일반적인 연결 문자열은 `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`입니다. 다음 표에서는 설정 가능한 여러 속성을 설명합니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| SecurityLevel | 드라이버가 데이터 저장소에 연결하는 데 사용하는 보안(SSL/TLS) 수준입니다. 예: `SecurityLevel=preferredSecured`. 지원되는 값은 다음과 같습니다.<br/>- **Only unsecured**(**onlyUnSecured**): 드라이버가 SSL을 사용하지 않습니다.<br/>- **Preferred unsecured(preferredUnSecured)(기본값)**: 서버가 선택 사항을 제공하면 드라이버는 SSL을 사용하지 않습니다. <br/>- **Preferred secured(preferredSecured)**: 서버가 선택 사항을 제공하면 드라이버는 SSL을 사용합니다. <br/>- **Only secured(onlySecured)**: SSL 연결을 사용할 수 있는 경우가 아니면 드라이버가 연결되지 않습니다. | 아니요 |
| CaCertFile | 서버에 사용되는 SSL 인증서의 전체 경로입니다. 예제: `CaCertFile=<cert path>;`| 예(SSL을 사용하는 경우) |

**예제**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

이 섹션에서는 Netezza 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md)를 참조하세요. 

Netezza에서 데이터를 복사하려면 데이터 세트의 **형식** 속성을 **NetezzaTable**로 설정합니다. 이 형식의 데이터 세트에는 추가적인 형식별 속성이 없습니다.

**예제**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

이 섹션에서는 Netezza 원본에서 지원하는 속성의 목록을 제공합니다.

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="netezza-as-source"></a>Netezza를 원본으로

Netezza에서 데이터를 복사하려면 복사 작업의 **원본** 형식을 **NetezzaSource**로 설정합니다. 복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 **형식** 속성을 **NetezzaSource**로 설정해야 합니다. | yes |
| 쿼리 | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예제: `"SELECT * FROM MyTable"` | yes |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
