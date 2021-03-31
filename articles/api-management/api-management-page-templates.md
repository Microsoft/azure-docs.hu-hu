---
title: Lapok az Azure API Managementban | Microsoft Docs
description: Megtudhatja, hogyan szabhatja testre a fejlesztői portál oldal sablonjainak tartalmát az Azure API Managementban.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 24d026785025dba4ae45de404edec67c2cf3871a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91335585"
---
# <a name="page-templates-in-azure-api-management"></a>Lapok az Azure API Management
Az Azure API Management lehetővé teszi a fejlesztői portál oldalai tartalmának testreszabását a tartalmukat konfiguráló sablonok használatával. A [DotLiquid](http://dotliquidmarkup.org/) szintaxisának és a választott szerkesztőnek, például a [tervezők DotLiquid](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), valamint a honosított [karakterlánc-erőforrások](api-management-template-resources.md#strings), a karakterjel- [erőforrások](api-management-template-resources.md#glyphs)és a [lap vezérlőelemeknek](api-management-page-controls.md)a használatával nagyszerű rugalmasságot biztosít a lapok tartalmának konfigurálásához, ahogy az a fenti sablonok használatával illik.  
  
 Az ebben a szakaszban található sablonok segítségével testre szabhatja a bejelentkezés, a regisztráció és az oldal nem található lapjait a fejlesztői portálon.  
  
-   [Bejelentkezés](#SignIn)  
  
-   [Regisztráció](#SignUp)  
  
-   [A lap nem található](#PageNotFound)  
  
> [!NOTE]
>  A minta alapértelmezett sablonjai a következő dokumentációban szerepelnek, de a folyamatos fejlődés miatt változhatnak. Az élő alapértelmezett sablonokat a fejlesztői portálon tekintheti meg, ha a kívánt egyéni sablonokat navigálja. További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="sign-in"></a><a name="SignIn"></a> bejelentkezés  
 A **bejelentkezési** sablon lehetővé teszi a bejelentkezési oldal testreszabását a fejlesztői portálon.  
  
 ![Bejelentkezési oldal](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "A APIM bejelentkezési oldala a fejlesztői portál sablonjai")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>Vezérlők  
 Ez a sablon a következő [lap vezérlőit](api-management-page-controls.md)használhatja.  
  
-   [alapszintű – bejelentkezési](api-management-page-controls.md#basic-signin)  
  
-   [szolgáltatók](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Adatmodell  
 [Felhasználó bejelentkezési](api-management-template-data-model-reference.md#UseSignIn) entitása.  
  
### <a name="sample-template-data"></a>Minta sablonjának adatterülete  
  
```json  
{
    "Email": null,
    "Password": null,
    "ReturnUrl": null,
    "RememberMe": false,
    "RegistrationEnabled": true,
    "DelegationEnabled": false,
    "DelegationUrl": null,
    "SsoSignUpUrl": null,
    "AuxServiceUrl": "https://portal.azure.com/#resource/subscriptions/{subscription ID}/resourceGroups/Api-Default-West-US/providers/Microsoft.ApiManagement/service/contoso5",
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
        ],
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": false
}
```  
  
##  <a name="sign-up"></a><a name="SignUp"></a> feliratkozni  
 A **regisztrációs** sablon segítségével testre szabhatja a regisztrációs oldalt a fejlesztői portálon.  
  
 ![Regisztrációs oldal](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM regisztrációs oldal fejlesztői portál sablonjai")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Vezérlők  
 Ez a sablon a következő [lap vezérlőit](api-management-page-controls.md)használhatja.  
  
-   [regisztráció](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Adatmodell  
 [Felhasználói regisztráció](api-management-template-data-model-reference.md#UserSignUp) entitása.  
  
### <a name="sample-template-data"></a>Minta sablonjának adatterülete  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="page-not-found"></a><a name="PageNotFound"></a> A lap nem található  
 A **lap nem található** sablon lehetővé teszi az oldal nem található oldalának testreszabását a fejlesztői portálon.  
  
 ![Nem található lap](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "A APIM nem található a fejlesztői portál sablonjai")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>Vezérlők  
 A sablon nem használhat [oldal vezérlőelemeket](api-management-page-controls.md).  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|referenceCode|sztring|A kód akkor lett létrehozva, ha a lap egy belső hiba eredményeként jelenik meg.|  
|errorCode|sztring|A kód akkor lett létrehozva, ha a lap egy belső hiba eredményeként jelenik meg.|  
|emailBody|sztring|Az e-mail-szövegtörzs akkor lett létrehozva, ha a lap egy belső hiba eredményeként jelenik meg.|  
|requestedUrl|sztring|A lap nem található a kért URL-cím.|  
|referrerUrl|sztring|A kért URL-címhez tartozó hivatkozó URL-cím.|  
  
### <a name="sample-template-data"></a>Minta sablonjának adatterülete  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>Következő lépések
További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](api-management-developer-portal-templates.md).
