---
title: Azure Log Analytics에서 데이터를 분석하는 보기 만들기 | Microsoft Docs
description: Log Analytics에서 뷰 디자이너를 사용하면 Azure Portal에 표시되고 Log Analytics 작업 영역에 다양한 데이터 시각화를 포함하는 사용자 지정 보기를 만들 수 있습니다. 이 문서에는 뷰 디자이너 개요 및 사용자 지정 보기를 만들고 편집하는 절차가 포함되어 있습니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: bbf38d17f2f411fde240a67f6666953b275fb788
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637248"
---
# <a name="create-custom-views-by-using-view-designer-in-log-analytics"></a>Log Analytics에서 뷰 디자이너를 사용하여 사용자 지정 보기 만들기
[Azure Log Analytics](../../log-analytics/log-analytics-queries.md)에서 뷰 디자이너를 사용하면 Azure Portal에서 Log Analytics 작업 영역의 데이터를 시각화하는 데 도움이 되는 다양한 사용자 지정 보기를 만들 수 있습니다. 이 문서에는 뷰 디자이너 개요 및 사용자 지정 보기를 만들고 편집하는 절차가 제공됩니다.

뷰 디자이너에 대한 자세한 내용은 다음을 참조하세요.

* [타일 참조](view-designer-tiles.md): 사용자 지정 보기에서 사용 가능한 각 타일의 설정에 대한 참조 가이드를 제공합니다.
* [시각화 요소 참조](view-designer-parts.md): 사용자 지정 보기에서 사용 가능한 시각화 요소의 설정에 대한 참조 가이드를 제공합니다.


## <a name="concepts"></a>개념
보기는 Azure Portal에서 Log Analytics 작업 영역의 **개요** 페이지에 표시됩니다. 각 사용자 지정 보기의 타일은 사전 순으로 표시되며 솔루션에 대한 타일은 동일한 작업 영역에 설치됩니다.

![개요 페이지](media/view-designer/overview-page.png)

뷰 디자이너로 만든 보기에는 다음 표에 설명된 요소가 포함됩니다.

| 부 | 설명 |
|:--- |:--- |
| 타일 | Log Analytics 작업 영역 **개요** 페이지에 표시됩니다. 각 타일에는 해당 타일이 나타내는 사용자 지정 보기의 시각적 요약이 표시됩니다. 각 타일 유형은 레코드의 다른 시각화를 제공합니다. 사용자 지정 보기를 표시하려면 타일을 선택합니다. |
| 사용자 지정 보기 | 타일을 선택하면 표시됩니다. 각 보기는 시각화 요소를 하나 이상 포함합니다. |
| 시각화 요소 | 하나 이상의 [로그 검색](../../log-analytics/log-analytics-queries.md)을 기반으로 Log Analytics 작업 영역에 데이터의 시각화를 나타냅니다. 대부분의 요소는 대략적인 시각화를 제공하는 헤더와 상위 결과를 표시하는 목록을 포함합니다. 각 요소 유형은 Log Analytics 작업 영역에 레코드의 다른 시각화를 제공합니다. 요소의 항목을 선택하면 자세한 레코드를 제공하는 로그 검색이 수행됩니다. |


## <a name="work-with-an-existing-view"></a>기존 보기로 작업
뷰 디자이너로 만든 보기에는 다음 옵션이 표시됩니다.

![개요 메뉴](media/view-designer/overview-menu.png)

옵션은 다음 표에 설명되어 있습니다.

| 옵션 | 설명 |
|:--|:--|
| 새로 고침   | 최신 데이터로 보기를 새로 고칩니다. | 
| 분석 | [Advanced Analytics 포털](../../log-analytics/log-analytics-log-search-portals.md)을 열어서 로그 쿼리를 통해 데이터를 분석합니다. |
| 편집       | 뷰 디자이너에서 보기를 열어서 내용과 구성을 편집합니다.  |
| 복제      | 새 보기를 만든 후 뷰 디자이너에서 엽니다. 새 보기의 이름은 원래 이름과 동일하지만 복사본이 추가됩니다. |
| 날짜 범위 | 보기에 포함된 데이터의 날짜와 시간 범위 필터를 설정합니다. 이 날짜 범위는 보기의 쿼리에서 데이터 범위가 설정되기 전에 적용됩니다.  |
| +          | 보기에 대해 정의된 사용자 지정 필터를 정의합니다. |


## <a name="create-a-new-view"></a>새 보기 만들기
Log Analytics 작업 영역의 메뉴에서 **뷰 디자이너**를 선택하여 뷰 디자이너에서 새 보기를 만들 수 있습니다.

![뷰 디자이너 타일](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>뷰 디자이너 작업
뷰 디자이너를 사용하여 새 보기를 만들거나 기존 보기를 편집합니다. 

뷰 디자이너에는 세 개의 창이 있습니다. 
* **디자인**: 만들거나 편집하는 사용자 지정 보기가 포함됩니다. 
* **컨트롤**: **디자인** 창에 추가하는 타일과 요소가 포함됩니다. 
* **속성**: 타일 또는 선택한 요소의 속성이 표시됩니다.

![뷰 디자이너](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>보기 타일 구성
사용자 지정 보기에는 타일 하나만 있을 수 있습니다. 현재 타일을 보거나 다른 타일을 선택하려면 **컨트롤** 창에서 **타일** 탭을 선택합니다. **속성** 창에 현재 타일의 속성이 표시됩니다. 

[타일 참조](view-designer-tiles.md)의 정보에 따라 타일 속성을 구성한 다음, **적용**을 클릭하여 변경 내용을 저장합니다.

### <a name="configure-the-visualization-parts"></a>시각화 요소 구성
보기에는 시각화 요소가 얼마든지 포함될 수 있습니다. 보기에 요소를 추가하려면 **보기** 탭을 선택한 다음, 시각화 요소를 선택합니다. **속성** 창에 선택한 요소의 속성이 표시됩니다. 

[시각화 요소 참조](view-designer-parts.md)의 정보에 따라 보기 속성을 구성한 다음, **적용**을 클릭하여 변경 내용을 저장합니다.

보기에는 시각화 요소의 행 하나만 있습니다. 기존 요소를 새 위치로 끌어서 다시 정렬할 수 있습니다.

요소의 오른쪽 위에 있는 **X**를 클릭하면 보기에서 시각화 요소를 제거할 수 있습니다.


### <a name="menu-options"></a>메뉴 옵션
편집 모드에서 보기 작업을 수행하는 옵션이 다음 표에 설명되어 있습니다.

![편집 메뉴](media/view-designer/edit-menu.png)

| 옵션 | 설명 |
|:--|:--|
| 저장        | 변경 내용을 저장하고 보기를 닫습니다. |
| 취소      | 변경 내용을 무시하고 보기를 닫습니다. |
| 보기 삭제 | 보기를 삭제합니다. |
| 내보내기      | 다른 작업 영역으로 가져올 수 있는 [Azure Resource Manager 템플릿](../../azure-resource-manager/resource-group-authoring-templates.md)으로 보기를 내보냅니다. 파일 이름은 보기 이름이며 확장명은 *omsview*입니다. |
| 가져오기      | 다른 작업 영역에서 내보낸 *omsview* 파일을 가져옵니다. 이렇게 하면 기존 보기의 구성을 덮어씁니다. |
| 복제       | 새 보기를 만든 후 뷰 디자이너에서 엽니다. 새 보기의 이름은 원래 이름과 동일하지만 복사본이 추가됩니다. |

## <a name="next-steps"></a>다음 단계
* 사용자 지정 보기에 [타일](view-designer-tiles.md) 추가
* 사용자 지정 보기에 [시각화 요소](view-designer-parts.md) 추가
