---
title: '자습서: 인증서와 함께 Azure AD Reporting API를 사용하여 데이터 가져오기 | Microsoft Docs'
description: 이 자습서에서는 인증서 자격 증명과 함께 Azure AD Reporting API를 사용하여 사용자 작업 없이 디렉터리에서 데이터를 가져오는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0ee756828a50cdf62471923614afbe88e238b9ef
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624560"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>자습서: 인증서와 함께 Azure Active Directory Reporting API를 사용하여 데이터 가져오기

[Azure AD(Azure Active Directory) Reporting API](concept-reporting-api.md)는 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스합니다. 다양한 프로그래밍 언어 및 도구에서 이러한 API를 호출할 수 있습니다. 사용자 작업 없이 Azure AD Reporting API에 액세스하려면 인증서를 사용하도록 액세스를 구성해야 합니다.

이 자습서에서는 테스트 인증서를 사용하여 보고용 MS Graph API에 액세스하는 방법을 알아봅니다. 프로덕션 환경에서는 테스트 인증서를 사용하지 않는 것이 좋습니다. 

## <a name="prerequisites"></a>필수 조건

1. 먼저 [Azure Active Directory Reporting API에 액세스하기 위한 필수 구성 요소](howto-configure-prerequisites-for-reporting-api.md)를 모두 준비합니다. 

2. [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md)를 다운로드하여 설치합니다.

3. [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/)를 설치합니다. 이 모듈에서는 다음을 비롯한 몇 가지 유틸리티 cmdlet을 제공합니다.
    - 인증에 필요한 ADAL 라이브러리
    - ADAL을 사용하는 사용자, 응용 프로그램 키 및 인증서의 액세스 토큰
    - Graph API를 처리하는 페이지 단위의 결과

4. 모듈을 처음 사용하는 경우 **Install-MSCloudIdUtilsModule**을 실행하고, 그렇지 않으면 **Import-Module** Powershell 명령을 사용하여 모듈을 가져옵니다. 세션은 다음 화면과 유사하게 표시됩니다.

        ![Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)
  
5. **New-SelfSignedCertificate** Powershell commandlet을 사용하여 테스트 인증서를 만듭니다.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

6. **Export-Certificate** commandlet을 사용하여 테스트 인증서를 인증서 파일로 내보냅니다.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>인증서와 함께 Azure Active Directory Reporting API를 사용하여 데이터 가져오기

1. [Azure Portal](https://portal.azure.com)로 이동하여 **Azure Active Directory**, **앱 등록**을 차례로 선택하고 목록에서 응용 프로그램을 선택합니다. 

2. **설정** > **키**를 선택하고 **공개 키 업로드**를 선택합니다.

3. 이전 단계의 인증서 파일을 선택하고 **저장**을 선택합니다. 

4. 방금 응용 프로그램에 등록한 인증서의 지문 및 응용 프로그램 ID를 적어 둡니다. 지문을 찾으려면 포털의 응용 프로그램 페이지에서 **설정**으로 이동하고 **키**를 클릭합니다. **공개 키** 목록 아래에 지문이 표시됩니다.

5. 인라인 매니페스트 편집기에서 응용 프로그램 매니페스트를 열고 다음 스키마를 사용하여 *keyCredentials* 속성을 새 인증서 정보로 바꿉니다. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. 매니페스트를 저장합니다. 
  
7. 이제 이 인증서를 사용하여 MS Graph API에 대한 액세스 토큰을 가져올 수 있습니다. MSCloudIdUtils PowerShell 모듈에서 **Get-MSCloudIdMSGraphAccessTokenFromCert** cmdlet을 사용하여 이전 단계에서 가져온 응용 프로그램 ID와 지문을 전달합니다. 

 ![Azure portal](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Powershell 스크립트에서 액세스 토큰을 사용하여 Graph API를 쿼리합니다. MSCloudIDUtils에서 **Invoke-MSCloudIdMSGraphQuery** cmdlet을 사용하여 signins 및 directoryAudits 엔드포인트를 열거합니다. 이 cmdlet은 여러 페이지 단위의 결과를 처리한 다음 PowerShell 파이프라인에 해당 결과를 보냅니다.

9. directoryAudits 엔드포인트를 쿼리하여 감사 로그를 검색합니다. 
 ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. signins 엔드포인트를 쿼리하여 로그인 로그를 검색합니다.
 ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

11. 이제 이 데이터를 CSV로 내보내고 SIEM 시스템에 저장하도록 선택할 수 있습니다. 예약된 태스크에서 스크립트를 래핑하여 원본 코드에서 응용 프로그램 키를 저장하지 않고 주기적으로 테넌트에서 Azure AD 데이터를 가져올 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

* [reporting API의 첫 인상 살펴보기](concept-reporting-api.md)
* [감사 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [로그인 활동 보고서 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
