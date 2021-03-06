---
title: '자습서: Azure Active Directory와 GlassFrog 통합 | Microsoft Docs'
description: Azure Active Directory 및 GlassFrog 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7cf5dae6-32d6-418e-8ef2-b2041e686086
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: jeedes
ms.openlocfilehash: 1fdd1e8acf446ba3e409e2ff0bf9ca18f581c299
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49433183"
---
# <a name="tutorial-azure-active-directory-integration-with-glassfrog"></a>자습서: GlassFrog와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 GlassFrog를 통합하는 방법에 대해 알아봅니다.

GlassFrog를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- GlassFrog에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 GlassFrog에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

GlassFrog와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- GlassFrog Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 GlassFrog 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-glassfrog-from-the-gallery"></a>갤러리에서 GlassFrog 추가
GlassFrog의 Azure AD 통합을 구성하려면 갤러리의 GlassFrog를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 GlassFrog를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![이미지](./media/glassfrog-tutorial/selectazuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![이미지](./media/glassfrog-tutorial/a_select_app.png)
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![이미지](./media/glassfrog-tutorial/a_new_app.png)

4. 검색 상자에 **GlassFrog**를 입력하고 결과 패널에서 **GlassFrog**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

     ![이미지](./media/glassfrog-tutorial/tutorial_glassfrog_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 GlassFrog에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD 사용자에 해당하는 GlassFrog 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 GlassFrog의 관련 사용자 간에 연결이 설정되어야 합니다.

GlassFrog에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[GlassFrog 테스트 사용자 만들기](#create-a-glassfrog-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 GlassFrog에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 GlassFrog 응용 프로그램에서 Single Sign-On을 구성합니다.

**GlassFrog에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com/)의 **GlassFrog** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![이미지](./media/glassfrog-tutorial/b1_b2_select_sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![이미지](./media/glassfrog-tutorial/b1_b2_saml_sso.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![이미지](./media/glassfrog-tutorial/b1-domains_and_urlsedit.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://app.glassfrog.com/people/sso?org_id=<ORGANIZATIONID>` 패턴을 사용하여 URL을 입력합니다.

    ![이미지](./media/glassfrog-tutorial/tutorial_glassfrog_url.png)

    > [!NOTE] 
    > 로그온 URL 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [GlassFrog 지원 팀](https://support.glassfrog.com/support/solutions/9000107654)에 문의하세요.
 
5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 **페더레이션 메타데이터 XML**을 컴퓨터에 다운로드하고 저장합니다.

    ![이미지](./media/glassfrog-tutorial/tutorial_glassfrog_certificate.png) 

6. **GlassFrog** 쪽에서 Single Sign-On을 구성하려면 다운로드한 **페더레이션 메타데이터 XML**을 [GlassFrog 지원 팀](mailto:support@alchemy.fr)에 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![이미지](./media/glassfrog-tutorial/d_users_and_groups.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![이미지](./media/glassfrog-tutorial/d_adduser.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![이미지](./media/glassfrog-tutorial/d_userproperties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-glassfrog-test-user"></a>GlassFrog 테스트 사용자 만들기

이 섹션에서는 GlassFrog에서 Britta Simon이라는 사용자를 만듭니다. GlassFrog 플랫폼에서 사용자를 추가하려면 [GlassFrog 지원 팀](https://support.glassfrog.com/support/solutions/9000107654)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 GlassFrog에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 응용 프로그램**을 선택한 다음, **모든 응용 프로그램**을 선택합니다.

    ![이미지](./media/glassfrog-tutorial/d_all_applications.png)

2. 응용 프로그램 목록에서 **GlassFrog**를 선택합니다.

    ![이미지](./media/glassfrog-tutorial/tutorial_glassfrog_app.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/glassfrog-tutorial/d_leftpaneusers.png)

4. **추가** 단추를 선택하고 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/glassfrog-tutorial/d_assign_user.png)

4. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

5. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 GlassFrog 타일을 클릭하면 GlassFrog 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)


