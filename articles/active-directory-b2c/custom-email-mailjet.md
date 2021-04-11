---
title: Egyéni e-mail-ellenőrzés a mailjet
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálható a mailjet az ügyfeleknek küldött ellenőrző e-mailek testreszabásához, amikor a Azure AD B2C-kompatibilis alkalmazások használatára jelentkeznek.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/09/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a40f3286b4e832f5c73e650859fa9a1d4fe4b6cb
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256956"
---
# <a name="custom-email-verification-with-mailjet"></a>Egyéni e-mail-ellenőrzés a mailjet

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Az egyéni e-mailek használata Azure Active Directory B2C (Azure AD B2C) a testreszabott e-mailek küldése az alkalmazásait használó felhasználók számára. A külső gyártótól származó e-mail-szolgáltató mailjet használhatja a saját e-mail-sablonját, a cím és *a* tárgy lehetőséggel, valamint a honosítási és egyéni egyszeri jelszó (OTP) beállítások támogatásával.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Az egyéni e-mail-ellenőrzéshez egy külső gyártótól származó e-mail-szolgáltató, például [mailjet](https://Mailjet.com), [SendGrid](./custom-email-sendgrid.md)vagy [SparkPost](https://sparkpost.com), egyéni REST API vagy bármilyen HTTP-alapú e-mail-szolgáltató használata szükséges (beleértve a sajátját is). Ez a cikk a mailjet-t használó megoldások beállítását ismerteti.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-mailjet-account"></a>Mailjet-fiók létrehozása

Ha még nem rendelkezik ilyennel, kezdje egy mailjet-fiók beállításával (az Azure-ügyfelek legfeljebb 200 e-maileket állíthatnak fel 6 000 e-mailben). 

1. Kövesse a telepítési utasításokat a [mailjet-fiók létrehozása](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/)című részben.
1. E-mailek küldéséhez [regisztráljon és érvényesítse](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/#how-to-configure-mailjet-for-use) a küldő e-mail-címét vagy tartományát.
2. Navigáljon az [API-kulcs kezelése lapra](https://app.mailjet.com/account/api_keys). Jegyezze fel az **API-kulcsot** és a **titkos kulcsot** egy későbbi lépésben való használatra. A fiók létrehozásakor mindkét kulcs automatikusan létrejön.  

## <a name="create-azure-ad-b2c-policy-key"></a>Azure AD B2C házirend-kulcs létrehozása

Ezután tárolja a mailjet API-kulcsot egy Azure AD B2C házirend-kulcsban a szabályzatok hivatkozásához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a Azure ad B2C könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Az **Áttekintés** lapon válassza az **identitási élmény keretrendszert**.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a **manuális** lehetőséget.
1. Adja meg a szabályzat kulcsának **nevét** . Például: `MailjetApiKey`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
1. A **Secret (titkos** kulcs) mezőben adja meg a korábban rögzített mailjet **API-kulcsot** .
1. A **kulcshasználat** beállításnál válassza az **aláírás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.
1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a **manuális** lehetőséget.
1. Adja meg a szabályzat kulcsának **nevét** . Például: `MailjetSecretKey`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
1. A **Secret (titkos** kulcs) mezőben adja meg a korábban rögzített **titkos** mailjet.
1. A **kulcshasználat** beállításnál válassza az **aláírás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

## <a name="create-a-mailjet-template"></a>Mailjet-sablon létrehozása

Hozzon létre egy mailjet [dinamikus tranzakciós sablont](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)a létrehozott mailjet-fiókkal és egy Azure ad B2Ci mailjet API-kulccsal.

1. A mailjet webhelyen nyissa meg a [tranzakciós sablonok](https://app.mailjet.com/templates/transactional) lapot, és válassza az **új sablon létrehozása** lehetőséget.
1. Válassza **a HTML**-kódban való kódolással lehetőséget, majd válassza **a teljesen új kód** lehetőséget.
1. Adjon meg egy egyedi nevet, például: `Verification email` , majd válassza a **Létrehozás** lehetőséget.
1. A HTML-szerkesztőben illessze be a következő HTML-sablont, vagy használja a sajátját. A `{{var:otp:""}}` és a `{{var:email:""}}` Paraméterek dinamikusan lesznek lecserélve az egyszeri jelszóval és a felhasználói e-mail-címmel.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{var:email:""}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{var:otp:""}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. A bal felső rész **szerkesztési tárgyának** kibontása
    1. A **Tárgy** mezőben adja meg a tárgy alapértelmezett értékét. A mailjet ezt az értéket használja, ha az API nem tartalmaz tárgy paramétert.
    1. A **név** mezőbe írja be a vállalat nevét.
    1. A **cím** mezőben válassza ki az e-mail-címét
    1. Kattintson a **Mentés** gombra.
1. A jobb felső sarokban válassza a **mentés & közzététel**, majd az **Igen, a módosítások közzététele** lehetőséget.
1. Jegyezze fel a létrehozott sablon **azonosítóját** egy későbbi lépésben való használatra. Ezt az azonosítót kell megadnia [a jogcím-átalakítás hozzáadásakor](#add-the-claims-transformation).


## <a name="add-azure-ad-b2c-claim-types"></a>Azure AD B2C jogcím-típusok hozzáadása

A szabályzatban adja hozzá a következő típusú jogcímeket a `<ClaimsSchema>` elemhez: `<BuildingBlocks>` .

Ezek a jogcím-típusok szükségesek az e-mail-cím egyszeri jelszavas (OTP) kóddal történő létrehozásához és ellenőrzéséhez.

```XML
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="Otp">
      <DisplayName>Secondary One-time password</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="emailRequestBody">
      <DisplayName>Mailjet request body</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="VerificationCode">
      <DisplayName>Secondary Verification Code</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your email verification code</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

## <a name="add-the-claims-transformation"></a>Jogcím-átalakítás hozzáadása

Ezután szükség van egy jogcím-átalakításra, amely egy JSON-karakterlánc-jogcím kimenetét adja meg, amely a kérelem törzse lesz, amelyet a mailjet küld.

A JSON-objektum struktúráját az azonosítók a InputParameters és a Szabályzattípushoz TransformationClaimTypes határozza meg. A pont jelölésében szereplő számok tömböket jelentenek. Az értékek a Szabályzattípushoz "Values" és a InputParameters "" érték "tulajdonságaiból származnak. A JSON-jogcímek átalakításával kapcsolatos további információkért lásd a [JSON-jogcímek átalakítását](json-transformations.md)ismertető témakört.

Adja hozzá a következő jogcím-átalakítást a `<ClaimsTransformations>` elemen belül `<BuildingBlocks>` . Hajtsa végre a következő frissítéseket a jogcím-átalakítási XML-ben:

* Frissítse a `Messages.0.TemplateID` InputParameter értéket a [mailjet-sablon létrehozása](#create-a-mailjet-template)során korábban létrehozott mailjet tranzakciós sablon azonosítójával.
* Módosítsa a `Messages.0.From.Email` címtartomány értékét. Érvényes e-mail-cím használatával megakadályozhatja, hogy az ellenőrző e-mailek levélszemétként legyenek megjelölve.
* Frissítse a `Messages.0.Subject` Tárgy sor bemeneti paraméter értékét a szervezete számára megfelelő tárgyi vonallal.

```XML
<!-- 
<BuildingBlocks>
  <ClaimsTransformations> -->
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your Mailjet template. -->
        <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
        <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

        <!-- Update with an email appropriate for your organization. -->
        <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>

        <!-- Update with a subject line appropriate for your organization. -->
        <InputParameter Id="Messages.0.Subject" DataType="string" Value="Contoso account email verification code"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
  <!--
  </ClaimsTransformations>
</BuildingBlocks> -->
```

## <a name="add-datauri-content-definition"></a>DataUri-definíció hozzáadása

A jogcím-átalakítások alatt `<BuildingBlocks>` adja hozzá a következő [ContentDefinition](contentdefinitions.md) a 2.1.2 adaturi-verzióra való hivatkozáshoz:

```XML
<!--
<BuildingBlocks> -->
  <ContentDefinitions>
   <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!--
</BuildingBlocks> -->
```

## <a name="create-a-displaycontrol"></a>DisplayControl létrehozása

A rendszer ellenőrzi, hogy az e-mail-cím a felhasználónak küldött ellenőrző kóddal van-e meghatározva.

Ez a példa megjelenítési vezérlő a következőre van konfigurálva:

1. Gyűjtse össze a `email` jogcím típusát a felhasználótól.
1. Várjon, amíg a felhasználó meg nem adja a `verificationCode` jogcím típusát a felhasználónak továbbított kóddal.
1. Küldje vissza a `email` -t az önérvényesített technikai profilba, amely erre a megjelenítési vezérlőelemre hivatkozik.
1. A `SendCode` művelettel egy egyszeri jelszavas kódot kell előállítania, és e-mailt kell küldenie az OTP-kóddal a felhasználónak.

   ![Ellenőrző kód e-mail-műveletének küldése](media/custom-email-mailjet/display-control-verification-email-action-01.png)

A tartalmi definíciók területen még `<BuildingBlocks>` a-ban is adja hozzá a következő [DisplayControl](display-controls.md) típusú [VerificationControl](display-control-verification.md) a szabályzathoz.

```XML
<!--
<BuildingBlocks> -->
  <DisplayControls>
    <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
      <DisplayClaims>
        <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" />
      </OutputClaims>
      <Actions>
        <Action Id="SendCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
          </ValidationClaimsExchange>
        </Action>
        <Action Id="VerifyCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
          </ValidationClaimsExchange>
        </Action>
      </Actions>
    </DisplayControl>
  </DisplayControls>
<!--
</BuildingBlocks> -->
```

## <a name="add-otp-technical-profiles"></a>OTP technikai profilok hozzáadása

A `GenerateOtp` technikai profil létrehoz egy kódot az e-mail-cím számára. A `VerifyOtp` technikai profil ellenőrzi az e-mail-címmel társított kódot. Módosíthatja a formátum konfigurációját és az egyszeri jelszó lejárati idejét. Az OTP technikai profiljaival kapcsolatos további információkért lásd: [egyszeri jelszóval kapcsolatos technikai profil meghatározása](one-time-password-technical-profile.md).

Adja hozzá a következő technikai profilokat a `<ClaimsProviders>` elemhez.

```XML
<!--
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>One time password technical profiles</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="GenerateOtp">
        <DisplayName>Generate one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">GenerateCode</Item>
          <Item Key="CodeExpirationInSeconds">1200</Item>
          <Item Key="CodeLength">6</Item>
          <Item Key="CharacterSet">0-9</Item>
          <Item Key="ReuseSameCode">true</Item>
          <Item Key="NumRetryAttempts">5</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
        </OutputClaims>
      </TechnicalProfile>

      <TechnicalProfile Id="VerifyOtp">
        <DisplayName>Verify one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">VerifyCode</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
          <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
        </InputClaims>
      </TechnicalProfile>
     </TechnicalProfiles>
  </ClaimsProvider>
<!--
</ClaimsProviders> -->
```

## <a name="add-a-rest-api-technical-profile"></a>REST API technikai profil hozzáadása

Ez a REST API technikai profil az e-mail-tartalmat hozza létre (a mailjet formátum használatával). További információ a REST-technikai profilokról: [Rest-technikai profil meghatározása](restful-technical-profile.md).

Az OTP technikai profiljaihoz hasonlóan adja hozzá a következő technikai profilokat a `<ClaimsProviders>` elemhez.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="sendOtp">
      <DisplayName>Use email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.mailjet.com/v3.1/send</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_MailjetApiKey" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_MailjetSecretKey" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Hivatkozás készítése a DisplayControl

Az utolsó lépésben adjon hozzá egy hivatkozást a létrehozott DisplayControl. Cserélje le a meglévő `LocalAccountSignUpWithLogonEmail` és `LocalAccountDiscoveryUsingEmailAddress` saját maga által vezérelt technikai profilokat az alábbira. Ha a Azure AD B2C szabályzat korábbi verzióját használta. Ezek a műszaki profilok a `DisplayClaims` DisplayControl mutató hivatkozást használják.

További információ: [önérvényesített technikai profil](restful-technical-profile.md) és [DisplayControl](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>Választható E-mail honosítása

Az e-mail honosítása érdekében honosított karakterláncokat kell küldenie a mailjet vagy az e-mail-szolgáltatónak. Honosíthatja például az e-mail-tárgyat, a törzset, az Ön kódjának üzenetét vagy az e-mail aláírását. Ehhez használhatja a [GetLocalizedStringsTransformation](string-transformations.md) jogcím-átalakítást a honosított karakterláncok jogcím-típusokra való másolásához. A `GenerateEmailRequestBody` JSON-adattartalmat generáló jogcím-átalakítás a honosított karakterláncokat tartalmazó bemeneti jogcímeket használja.

1. A szabályzatban adja meg a következő karakterlánc-jogcímeket: tulajdonos, üzenet, codeIntro és aláírás.
1. Definiáljon egy [GetLocalizedStringsTransformation](string-transformations.md) jogcím-átalakítást a honosított karakterlánc-értékek helyettesítéséhez az 1. lépésben szereplő jogcímekbe.
1. Módosítsa a `GenerateEmailRequestBody` jogcím-átalakítást a bemeneti jogcímek használatára a következő XML-kódrészlettel.
1. Frissítse a mailjet-sablont a dinamikus paraméterek használatára a Azure AD B2C által honosított összes karakterlánc helyett.

    ```XML
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="Messages.0.Subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="Messages.0.Variables.otpmessage" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="Messages.0.Variables.otpcodeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="Messages.0.Variables.otpsignature" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your Mailjet template. -->
        <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
        <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

        <!-- Update with an email appropriate for your organization. -->
        <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Adja hozzá a következő [honosítási](localization.md) elemet.

    ```xml
    <!--
    <BuildingBlocks> -->
      <Localization Enabled="true">
        <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
          <SupportedLanguage>en</SupportedLanguage>
          <SupportedLanguage>es</SupportedLanguage>
        </SupportedLanguages>
        <LocalizedResources Id="api.custom-email.en">
          <LocalizedStrings>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
          </LocalizedStrings>
          </LocalizedStrings>
        </LocalizedResources>
        <LocalizedResources Id="api.custom-email.es">
          <LocalizedStrings>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
      </Localization>
    <!--
    </BuildingBlocks> -->
    ```

1. A [ContentDefinitions](contentdefinitions.md) elem frissítésével adjon hozzá hivatkozásokat a LocalizedResources elemekhez.

    ```xml
    <!--
    <BuildingBlocks> -->
      <ContentDefinitions>
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
        <ContentDefinition Id="api.localaccountpasswordreset">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
      </ContentDefinitions>
    <!--
    </BuildingBlocks> -->
    ```

1. Végül adja hozzá a következő bemeneti jogcímek átalakítását a `LocalAccountSignUpWithLogonEmail` és a `LocalAccountDiscoveryUsingEmailAddress` technikai profilokhoz.

    ```xml
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```
    
## <a name="optional-localize-the-ui"></a>Választható A felhasználói felület honosítása

A honosítási elem lehetővé teszi, hogy a felhasználói útvonalakhoz tartozó szabályzatban több területi beállítást vagy nyelvet támogasson. A házirendek honosítási támogatása lehetővé teszi nyelvspecifikus karakterláncok megadását a [felhasználói felület elemeihez](localization-string-ids.md#verification-display-control-user-interface-elements)és a [jelszó egyszeri jelszavához](localization-string-ids.md#one-time-password-error-messages). Adja hozzá a következő LocalizedString a LocalizedResources. 

```XML
<LocalizedResources Id="api.custom-email.en">
  <LocalizedStrings>
    ...
    <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
    <!-- Claims-->
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="DisplayName">Verification Code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="UserHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="AdminHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Eamil</LocalizedString>
    <!-- Email validation error messages-->
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">The verification has failed, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

A honosított karakterláncok hozzáadása után távolítsa el az egyszeri jelszavas érvényesítési hibaüzenetek metaadatait a LocalAccountSignUpWithLogonEmail és a LocalAccountDiscoveryUsingEmailAddress technikai profiljaiból.

## <a name="next-steps"></a>Következő lépések

A GitHubon megtalálhatja például az egyéni e-mail-ellenőrzési szabályzatot:

- [Egyéni e-mail-ellenőrzés – DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Az egyéni REST API vagy bármely HTTP-alapú SMTP e-mail-szolgáltató használatáról további információt a [Rest műszaki profil definiálása egy Azure ad B2C egyéni szabályzatban](restful-technical-profile.md)című témakörben talál.

::: zone-end