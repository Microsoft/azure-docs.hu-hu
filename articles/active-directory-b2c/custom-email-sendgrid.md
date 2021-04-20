---
title: Egyéni e-mail-ellenőrzés a SendGriddel
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan integrálhatja a SendGridet, hogy testre szabja az ügyfeleknek a regisztrációkor küldött ellenőrző e-mailt, és használja Azure AD B2C kompatibilis alkalmazásokat.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d63e7916423038e53c375b2be4114582cf4d6152
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725763"
---
# <a name="custom-email-verification-with-sendgrid"></a>Egyéni e-mail-ellenőrzés a SendGriddel

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Egyéni e-mail-Azure Active Directory B2C (Azure AD B2C) egyéni e-maileket küldhet az alkalmazások használatára regisztráló felhasználóknak. A SendGrid külső e-mail-szolgáltató használatával használhatja a saját e-mail-sablonját és a *From:* address and subject (Cím és tárgy) lehetőséget, valamint támogatja a honosítást és az egyéni egyszeri jelszó (OTP) beállításait.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Az egyéni e-mail-ellenőrzéshez külső e-mail-szolgáltatót kell használni, például [a SendGridet,](https://sendgrid.com)a [Mailjetet](https://Mailjet.com)vagy a [SparkPostot,](https://sparkpost.com)egy egyéni REST API-t vagy bármely HTTP-alapú e-mail-szolgáltatót (beleértve a sajátját is). Ez a cikk a SendGridet használó megoldások beállítását ismerteti.

## <a name="create-a-sendgrid-account"></a>SendGrid-fiók létrehozása

Ha még nem rendelkezik ilyen fiókkal, először egy SendGrid-fiókot kell beállításakor (az Azure-ügyfelek havonta 25 000 ingyenes e-mailt oldhat fel). A beállítási utasításokért lásd az E-mailek küldése [a SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) és az Azure használatával című cikk [SendGrid-fiók létrehozása című szakaszát.](../sendgrid-dotnet-how-to-send-email.md)

Mindenképpen töltse ki azt a szakaszt, amelyben [létrehozza a SendGrid API-kulcsot.](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key) Jegyezd fel az API-kulcsot egy későbbi lépésben való használatra.

> [!IMPORTANT]
> A SendGrid lehetővé teszi, hogy az ügyfelek megosztott IP-címekről és dedikált IP-címekről küldjenek [e-maileket.](https://sendgrid.com/docs/ui/account-and-settings/dedicated-ip-addresses/) Dedikált IP-címek használata esetén az IP-címek bemelegítésével, megfelelően kell építenie a jó hírnevét. További információ: [IP-cím bemelegítése.](https://sendgrid.com/docs/ui/sending-email/warming-up-an-ip-address/)

## <a name="create-azure-ad-b2c-policy-key"></a>Szabályzatkulcs Azure AD B2C létrehozása

Ezután tárolja a SendGrid API-kulcsot egy Azure AD B2C szabályzatkulcsban, hogy a szabályzatokra hivatkozni fog.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a saját bérlőt tartalmazó Azure AD B2C használja. Válassza ki **a Címtár és előfizetés** szűrőt a felső menüben, és válassza ki Azure AD B2C címtárat.
1. Válassza **a Bal felső** sarokban található Minden szolgáltatás Azure Portal, majd keresse meg és válassza a **Azure AD B2C.**
1. Az Áttekintés lapon válassza **a** Identity Experience Framework.
1. Válassza **a Házirendkulcsok,** majd a Hozzáadás **lehetőséget.**
1. A **Beállítások beállításhoz** válassza a **Manuális lehetőséget.**
1. Adja meg a **szabályzatkulcs** Nevét. Például: `SendGridSecret`. Az `B2C_1A_` előtag automatikusan hozzáadódik a kulcs nevéhez.
1. A **Titkos kulcs** résznél adja meg a korábban rögzített SendGrid API-kulcsot.
1. A **Kulcshasználat beállításhoz válassza** az Aláírás **lehetőséget.**
1. Válassza a **Létrehozás** lehetőséget.

## <a name="create-sendgrid-template"></a>SendGrid-sablon létrehozása

Ha létrehozott egy SendGrid-fiókot, és egy SendGrid API-kulcsot tárol egy Azure AD B2C szabályzatkulcsban, hozzon létre egy SendGrid dinamikus tranzakciós [sablont.](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)

1. A SendGrid webhelyén nyissa meg a [tranzakciós sablonok oldalát,](https://sendgrid.com/dynamic_templates) és válassza a **Sablon létrehozása lehetőséget.**
1. Adjon meg egy egyedi sablonnevet (például) `Verification email` , majd válassza a Save **(Mentés) lehetőséget.**
1. Az új sablon szerkesztésének megkezdéséhez válassza a **Verzió hozzáadása lehetőséget.**
1. Válassza a **Kódszerkesztő,** majd a **Folytatás lehetőséget.**
1. A HTML-szerkesztőben illessze be a következő HTML-sablont, vagy használja a sajátját. A és a paramétert a rendszer dinamikusan lecseréli az egyszeres jelszóértékre és a felhasználói `{{otp}}` `{{email}}` e-mail-címre.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
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
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
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

1. **Bontsa ki a Beállítások** gombra a bal oldalon, majd az **E-mail tárgya beállításnál adja** meg a következőt: `{{subject}}` .
1. Válassza **a Sablon mentése lehetőséget.**
1. A vissza **nyílra** kattintva térjen vissza a Tranzakciós sablonok lapra.
1. Jegyezd **fel** a későbbi lépésben való használatra létrehozott sablon azonosítóját. Például: `d-989077fbba9746e89f3f6411f596fb96`. Ezt az azonosítót a [jogcím-átalakítás hozzáadásakor adhatja meg.](#add-the-claims-transformation)

## <a name="add-azure-ad-b2c-claim-types"></a>Új Azure AD B2C jogcímtípusok hozzáadása

A szabályzatban adja hozzá a következő jogcímtípusokat `<ClaimsSchema>` a `<BuildingBlocks>` elemhez.

Ezek a jogcímtípusok szükségesek az e-mail-cím egyszeri jelszóval (OTP) való létrehozásához és ellenőrzéséhez.

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="Otp">
      <DisplayName>Secondary One-time password</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="emailRequestBody">
      <DisplayName>SendGrid request body</DisplayName>
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

## <a name="add-the-claims-transformation"></a>A jogcím-átalakítás hozzáadása

Ezután szükség lesz egy jogcím-átalakításra egy JSON-sztring jogcímének kimeneteként, amely a SendGridnek küldött kérelem törzse lesz.

A JSON-objektum struktúráját az InputParameters és az InputClaims TransformationClaimTypes tulajdonságának dot not notation (Az InputParameters és a TransformationClaimTypes) határozzák meg. A pont-kiosztásban a számok tömbökre utalnak. Az értékek az InputClaims értékeiből és az InputParameters "Value" tulajdonságaiból jönnek. További információ a JSON-jogcímek átalakításáról: [JSON-jogcímek átalakítása.](json-transformations.md)

Adja hozzá a következő jogcím-átalakítást `<ClaimsTransformations>` a elemhez a elemen `<BuildingBlocks>` belül. A jogcím-átalakítási XML-fájlon az alábbi frissítéseket kell követnie:

* Frissítse az InputParameter értékét a SendGrid-sablon létrehozása során korábban létrehozott SendGrid tranzakciós sablon `template_id` [azonosítójával.](#create-sendgrid-template)
* Frissítse a `from.email` cím értékét. Érvényes e-mail-cím használatával megakadályozhatja, hogy az ellenőrző e-mail levélszemétként legyen megjelölve.
* Frissítse a tárgysor bemeneti paraméterének értékét a szervezetének megfelelő `personalizations.0.dynamic_template_data.subject` tárgysokkal.

```xml
<!-- 
<BuildingBlocks>
  <ClaimsTransformations> -->
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your SendGrid template. -->
        <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
        <!-- Update with a subject line appropriate for your organization. -->
        <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
  <!--
  </ClaimsTransformations>
</BuildingBlocks> -->
```

## <a name="add-datauri-content-definition"></a>DataUri-tartalomdefiníció hozzáadása

A jogcím-átalakításai alatt adja hozzá a következő `<BuildingBlocks>` [ContentDefinition](contentdefinitions.md) adatokat a 2.1.2-es adat URI-verziójára való hivatkozáshoz:

```xml
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

Az ellenőrző kijelző vezérlője ellenőrzi az e-mail-címet a felhasználónak küldött ellenőrzőkóddal.

Ez a példa megjelenítendő vezérlő a következőre van konfigurálva:

1. Gyűjtse össze `email` a cím jogcímtípusát a felhasználótól.
1. Várjon, amíg a felhasználó meg nem adja `verificationCode` a jogcímtípust a felhasználónak küldött kóddal.
1. Térjen vissza a vissza ahhoz az önfeltételes technikai profilhoz, amely `email` hivatkozik erre a megjelenítési vezérlőre.
1. A művelet használatával hozzon létre egy otP-kódot, és küldjön egy e-mailt az `SendCode` otP-kóddal a felhasználónak.

![Ellenőrző kód küldése e-mail-művelet](media/custom-email-sendgrid/display-control-verification-email-action-01.png)

A tartalomdefiníciók alatt, még mindig a alatt adja hozzá a `<BuildingBlocks>` [következő, VerificationControl](display-controls.md) típusú [DisplayControl](display-control-verification.md) vezérlőt a szabályzathoz.

```xml
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

## <a name="add-otp-technical-profiles"></a>OtP technikai profilok hozzáadása

A `GenerateOtp` technikai profil létrehoz egy kódot az e-mail-címhez. A `VerifyOtp` technikai profil ellenőrzi az e-mail-címhez társított kódot. Módosíthatja a formátum konfigurációját és az egyszeres jelszó lejáratát. Az egyszeri jelszavas technikai profilokkal kapcsolatos további információkért [lásd: Egyszeri jelszó technikai profiljának definiálása.](one-time-password-technical-profile.md)

> [!NOTE]
> A Web.TPEngine.Providers.OneTimePasswordProtocolProvider protokoll által létrehozott egyszeri jelszó-kódok a böngésző-munkamenethez vannak kötve. Ez azt jelenti, hogy a felhasználó egyedi egyszeri jelszókódokat hozhat létre különböző böngésző-munkamenetekben, amelyek a megfelelő munkamenetekhez érvényesek. Ezzel szemben a beépített felhasználói folyamat által generált egyszeri jelszó kódja független a böngésző-munkamenettől, így ha a felhasználó új otP-kódot hoz létre egy új böngésző-munkamenetben, azzal felülírja az előző otP-kódot.

Adja hozzá a következő technikai profilokat a `<ClaimsProviders>` elemhez.

```xml
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

## <a name="add-a-rest-api-technical-profile"></a>Technikai REST API hozzáadása

Ez REST API technikai profil hozza létre az e-mail-tartalmat (SendGrid formátumban). A RESTful műszaki profilokkal kapcsolatos további információkért [lásd: RESTful technikai profil definiálása.](restful-technical-profile.md)

Ahogy az egyszeri egyszeri megoldás technikai profiljai esetében, itt is adja hozzá a következő technikai profilokat a `<ClaimsProviders>` elemhez.

```xml
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendOtp">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
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

## <a name="make-a-reference-to-the-displaycontrol"></a>Hivatkozás a DisplayControl vezérlőre

Az utolsó lépésben adjon hozzá egy hivatkozást a létrehozott DisplayControl vezérlőre. Cserélje le a `LocalAccountSignUpWithLogonEmail` meglévő és `LocalAccountDiscoveryUsingEmailAddress` az önkiszolgáló műszaki profilokat a következőre. Ha a szabályzat egy korábbi verzióját Azure AD B2C használta. Ezek a technikai profilok `DisplayClaims` a DisplayControl-vezérlőre való hivatkozással együtt használhatók.

További információ: Önkiszolgáló technikai [profil és](restful-technical-profile.md) [DisplayControl.](display-controls.md)

```xml
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

## <a name="optional-localize-your-email"></a>[Nem kötelező] Az e-mail honosizálása

Az e-mail honosizáláshoz honosított sztringeket kell küldenie a SendGridnek vagy az e-mail-szolgáltatónak. Honosíthatja például az e-mail tárgyát, törzsét, a kódüzenetét vagy az e-mail aláírását. Erre a [GetLocalizedStringsTransformation](string-transformations.md) jogcím-átalakítással másolhat honosított sztringeket jogcímtípusokba. A JSON hasznos adatokat generáló jogcím-átalakítás a honosított sztringeket tartalmazó bemeneti `GenerateEmailRequestBody` jogcímeket használ.

1. A szabályzatban adja meg a következő sztring jogcímeket: tárgy, üzenet, codeIntro és aláírás.
1. Definiálja a [GetLocalizedStringsTransformation](string-transformations.md) jogcímátalakítást, hogy a honosított sztringértékeket az 1. lépésben megadott jogcímekbe helyettesítse.
1. Módosítsa a `GenerateEmailRequestBody` jogcímek átalakítását úgy, hogy bemeneti jogcímeket használjanak az alábbi XML-kódrészlettel.
1. Frissítse a SendGrid-sablont, hogy dinamikus paramétereket használjon az összes olyan sztring helyére, amelyet a Azure AD B2C.

    ```xml
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
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Adja hozzá a [következő Honosítás elemet.](localization.md)

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
            <!--Email template parameters-->
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
        <LocalizedResources Id="api.custom-email.es">
          <LocalizedStrings>
            <!--Email template parameters-->
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

1. A LocalizedResources elemekre mutató hivatkozásokat a [ContentDefinitions elem frissítésével adhat](contentdefinitions.md) hozzá.

    ```XML
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

1. Végül adja hozzá a következő bemeneti jogcím-átalakítást a `LocalAccountSignUpWithLogonEmail` és `LocalAccountDiscoveryUsingEmailAddress` a technikai profilhoz.

    ```XML
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```
    
## <a name="optional-localize-the-ui"></a>[Nem kötelező] A felhasználói felület honosizálása

A Honosítás elem lehetővé teszi több területi beállítás vagy nyelv támogatását a szabályzatban a felhasználói utakhoz. A szabályzatok honosítási támogatása lehetővé teszi, hogy nyelvspecifikus sztringeket adjon meg az Ellenőrzés megjelenítési vezérlő felhasználói felületének elemeihez [és](localization-string-ids.md#verification-display-control-user-interface-elements)az Egy alkalommal megjelenő jelszó [hibaüzenethez is.](localization-string-ids.md#one-time-password-error-messages) Adja hozzá a következő LocalizedString karakterláncot a LocalizedResources erőforráshoz. 

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

A honosított sztringek hozzáadása után távolítsa el az egyszeri jelszó érvényesítési hibaüzenetei metaadatait a LocalAccountSignUpWithLogonEmail és a LocalAccountDiscoveryUsingEmailAddress technikai profilokból.

## <a name="next-steps"></a>Következő lépések

Az egyéni e-mail-ellenőrzési szabályzatra a GitHubon találhat példát:

- [Egyéni e-mail-ellenőrzés – DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Az egyéni házirendek és a HTTP REST API SMTP e-mail-szolgáltatók használatával kapcsolatos további információkért [lásd: RESTful](restful-technical-profile.md)technikai profil definiálása egyéni házirendben Azure AD B2C házirendben.

::: zone-end
