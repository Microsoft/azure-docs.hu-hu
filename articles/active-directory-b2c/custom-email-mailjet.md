---
title: Egyéni e-mail-ellenőrzés a Mailjetben
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan integrálhatja a Mailjetet, hogy testre szabja az ügyfeleknek a regisztrációkor küldött ellenőrző e-maileket a Azure AD B2C alkalmazások használatára.
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
ms.openlocfilehash: b4bb58f106f3255ec6cd80b14b175ff413bc0dc6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725799"
---
# <a name="custom-email-verification-with-mailjet"></a>Egyéni e-mail-ellenőrzés a Mailjetben

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Egyéni e-mail-Azure Active Directory B2C (Azure AD B2C) egyéni e-maileket küldhet az alkalmazások használatára regisztráló felhasználóknak. A harmadik féltől származó Mailjet e-mail-szolgáltató használatával használhatja a saját e-mail-sablonját és a *Cím:* cím és tárgy lehetőséget, valamint támogatja a honosítást és az egyéni egyszeri jelszó (OTP) beállításait.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Az egyéni e-mail-ellenőrzéshez külső e-mail-szolgáltató, például a [Mailjet,](https://Mailjet.com)a [SendGrid](./custom-email-sendgrid.md)vagy a [SparkPost,](https://sparkpost.com)egy egyéni REST API vagy bármely HTTP-alapú e-mail-szolgáltató (beleértve a sajátját is) kell használnia. Ez a cikk a Mailjetet használó megoldások beállítását ismerteti.

## <a name="create-a-mailjet-account"></a>Mailjet-fiók létrehozása

Ha még nem rendelkezik ilyen fiókkal, először egy Mailjet-fiókot kell beállításakor (az Azure-ügyfelek 6000 e-mail zárolását oldják fel naponta legfeljebb 200 e-mail-cím használatával). 

1. Kövesse a [Mailjet-fiók létrehozása beállítási utasításait.](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/)
1. Ahhoz, hogy e-maileket küldhető legyen, regisztrálja és [érvényesítse](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/#how-to-configure-mailjet-for-use) a Küldő e-mail-címét vagy tartományát.
2. Lépjen az [API Kulcskezelés oldalára.](https://app.mailjet.com/account/api_keys) Jegyezd fel **az API-kulcsot** **és** a titkos kulcsot egy későbbi lépésben való használatra. A rendszer mindkét kulcsot automatikusan generálja a fiók létrehozásakor.  

> [!IMPORTANT]
> A Mailjet lehetővé teszi, hogy az ügyfelek megosztott IP-címekről és dedikált IP-címekről küldjenek [e-maileket.](https://documentation.mailjet.com/hc/articles/360043101973-What-is-a-dedicated-IP) Dedikált IP-címek használata esetén az IP-címek bemelegítésével, megfelelően kell építenie a jó hírnevét. További információ: Hogyan [bemelegíti az IP-címemet?](https://documentation.mailjet.com/hc/articles/1260803352789-How-do-I-warm-up-my-IP-).


## <a name="create-azure-ad-b2c-policy-key"></a>Szabályzatkulcs Azure AD B2C létrehozása

Ezután tárolja a Mailjet API-kulcsot egy Azure AD B2C házirendkulcsban, hogy a szabályzatokra hivatkozni fog.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Győződjön meg arról, hogy a saját bérlőt tartalmazó könyvtárat Azure AD B2C használja. A felső **menüben válassza** a Címtár és előfizetés szűrőt, majd válassza ki Azure AD B2C címtárat.
1. Válassza **a bal felső** sarokban található Minden szolgáltatás Azure Portal, majd keresse meg és válassza a **Azure AD B2C.**
1. Az Áttekintés **lapon** válassza **a** Identity Experience Framework.
1. Válassza **a Házirendkulcsok,** majd a Hozzáadás **lehetőséget.**
1. A **Beállítások beállításhoz** válassza a **Manuális lehetőséget.**
1. Adja meg a **szabályzatkulcs** Nevét. Például: `MailjetApiKey`. Az `B2C_1A_` előtag automatikusan hozzáadódik a kulcs nevéhez.
1. A **Titkos kulcsban** adja meg a korábban feljegyzett Mailjet **API-kulcsot.**
1. A **Kulcshasználat beállításhoz válassza** az Aláírás **lehetőséget.**
1. Válassza a **Létrehozás** lehetőséget.
1. Válassza **a Házirendkulcsok,** majd a Hozzáadás **lehetőséget.**
1. A **Beállítások beállításhoz** válassza a **Manuális lehetőséget.**
1. Adja meg a **szabályzatkulcs** Nevét. Például: `MailjetSecretKey`. Az `B2C_1A_` előtag automatikusan hozzáadódik a kulcs nevéhez.
1. A **Titkos kulcsban** adja meg a korábban rögzített Mailjet **Secret Key** (Mailjet titkos kulcs) kulcsát.
1. A **Kulcshasználat beállításhoz válassza** az Aláírás **lehetőséget.**
1. Válassza a **Létrehozás** lehetőséget.

## <a name="create-a-mailjet-template"></a>Mailjet-sablon létrehozása

Hozzon létre egy Mailjet-fiókot és a Mailjet API-kulcsot egy Azure AD B2C szabályzatkulcsban, hozzon létre egy dinamikus tranzakciós [Mailjet-sablont.](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)

1. A Mailjet webhelyen nyissa meg a [tranzakciósablonok](https://app.mailjet.com/templates/transactional) oldalát, és válassza **az Új sablon létrehozása lehetőséget.**
1. Válassza **a Kódolás HTML-ben** lehetőséget, majd válassza a **Kód az nulláról lehetőséget.**
1. Adjon meg egy egyedi sablonnevet, például `Verification email` : , majd válassza a Létrehozás **lehetőséget.**
1. A HTML-szerkesztőben illessze be a következő HTML-sablont, vagy használja a sajátját. A és a paramétert a rendszer dinamikusan lecseréli az egyszeres jelszóértékre és a felhasználói `{{var:otp:""}}` `{{var:email:""}}` e-mail-címre.

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

1. **Bontsa ki a** Tulajdonos szerkesztése gombra a bal felső sarokban
    1. A **Tárgy mezőben** adjon meg egy alapértelmezett értéket a tárgyhoz. A Mailjet akkor használja ezt az értéket, ha az API nem tartalmaz tárgyparamétert.
    1. A Név **mezőbe** írja be a vállalata nevét.
    1. A Cím **mezőben** válassza ki az e-mail-címét
    1. Kattintson a **Mentés** gombra.
1. A jobb felső sarokban válassza a **Save & Publish**(Közzététel) lehetőséget, majd az **Igen, publish changes (Módosítások közzététele) lehetőséget.**
1. Jegyezd fel **a** későbbi lépésben való használatra létrehozott sablon sablonazonosítóját. Ezt az azonosítót a [jogcím-átalakítás hozzáadásakor adhatja meg.](#add-the-claims-transformation)


## <a name="add-azure-ad-b2c-claim-types"></a>Új Azure AD B2C jogcímtípusok hozzáadása

A szabályzatban adja hozzá a következő jogcímtípusokat `<ClaimsSchema>` a `<BuildingBlocks>` elemhez.

Ezek a jogcímtípusok szükségesek az e-mail-cím egyszeri jelszóval (OTP) való létrehozásához és ellenőrzéséhez.

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

## <a name="add-the-claims-transformation"></a>A jogcím-átalakítás hozzáadása

Ezután szükség lesz egy jogcím-átalakításra egy JSON-sztring jogcímének kimeneteként, amely a Mailjetnek küldött kérelem törzse lesz.

A JSON-objektum struktúráját az InputParameters és az InputClaims TransformationClaimTypes tulajdonságának dot not notation (Az InputParameters és a TransformationClaimTypes) határozzák meg. A pont-kiosztásban a számok tömbökre utalnak. Az értékek az InputClaims értékeiből és az InputParameters "Value" tulajdonságaiból jönnek. További információ a JSON-jogcímek átalakításáról: [JSON-jogcímek átalakítása.](json-transformations.md)

Adja hozzá a következő jogcím-átalakítást `<ClaimsTransformations>` a elemhez a elemen `<BuildingBlocks>` belül. A jogcím-átalakítási XML-fájlban az alábbi frissítéseket kell követnie:

* Frissítse az InputParameter értékét a Mailjet-sablon létrehozása során korábban létrehozott Mailjet tranzakciós `Messages.0.TemplateID` [sablon azonosítójával.](#create-a-mailjet-template)
* Frissítse a `Messages.0.From.Email` cím értékét. Érvényes e-mail-cím használatával megakadályozhatja, hogy az ellenőrző e-mail levélszemétként legyen megjelölve.
* Frissítse a tárgysor bemeneti paraméterének értékét a szervezetének megfelelő `Messages.0.Subject` tárgysokkal.

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

## <a name="add-datauri-content-definition"></a>DataUri-tartalomdefiníció hozzáadása

A jogcím-átalakításai alatt adja hozzá a következő `<BuildingBlocks>` [ContentDefinition](contentdefinitions.md) adatokat a 2.1.2-es verziójú adat URI-azonosítóra való hivatkozáshoz:

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

Az ellenőrző kijelző vezérlője ellenőrzi az e-mail-címet a felhasználónak küldött ellenőrzőkóddal.

Ez a példa megjelenítendő vezérlő a következőre van konfigurálva:

1. Gyűjtse össze `email` a cím jogcímtípusát a felhasználótól.
1. Várjon, amíg a felhasználó meg nem adja `verificationCode` a jogcímtípust a felhasználónak küldött kóddal.
1. Adja vissza a et annak az önfeltételes technikai profilnak, amely `email` hivatkozik erre a megjelenítési vezérlőre.
1. A művelet használatával hozzon létre egy otP-kódot, és küldjön egy e-mailt az `SendCode` otP-kóddal a felhasználónak.

   ![Ellenőrző kód küldése e-mail-művelet](media/custom-email-mailjet/display-control-verification-email-action-01.png)

A tartalomdefiníciók alatt, még a alatt adja hozzá a `<BuildingBlocks>` [következő, VerificationControl](display-controls.md) típusú [DisplayControl](display-control-verification.md) vezérlőt a szabályzathoz.

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

## <a name="add-otp-technical-profiles"></a>OtP technikai profilok hozzáadása

A `GenerateOtp` technikai profil létrehoz egy kódot az e-mail-címhez. A `VerifyOtp` technikai profil ellenőrzi az e-mail-címhez társított kódot. Módosíthatja a formátum konfigurációját és az egyszeres jelszó lejáratát. Az egyszeri jelszavas technikai profilokkal kapcsolatos további információkért [lásd: Egyszeri jelszó műszaki profil definiálása.](one-time-password-technical-profile.md)

> [!NOTE]
> A Web.TPEngine.Providers.OneTimePasswordProtocolProvider protokoll által létrehozott egyszeri jelszó-kódok a böngésző-munkamenethez vannak kötve. Ez azt jelenti, hogy a felhasználó egyedi egyszeri jelszó-kódokat hozhat létre különböző böngésző-munkamenetekben, amelyek a megfelelő munkamenetekhez érvényesek. Ezzel szemben a beépített felhasználói folyamat által generált egyszeri jelszókód független a böngésző-munkamenettől, így ha a felhasználó új otP-kódot hoz létre egy új böngésző-munkamenetben, azzal felülírja az előző OTP-kódot.

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

## <a name="add-a-rest-api-technical-profile"></a>Technikai REST API hozzáadása

Ez REST API technikai profil hozza létre az e-mail-tartalmat (Mailjet formátumban). A RESTful technikai profilokkal kapcsolatos további információkért [lásd: RESTful technikai profil definiálása.](restful-technical-profile.md)

Akár csak az egyszeri egyszeri megoldás technikai profiljaihoz, adja hozzá a következő technikai profilokat a `<ClaimsProviders>` elemhez.

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

## <a name="make-a-reference-to-the-displaycontrol"></a>Hivatkozás a DisplayControl vezérlőre

Az utolsó lépésben adjon hozzá egy hivatkozást a létrehozott DisplayControl vezérlőre. Cserélje le a `LocalAccountSignUpWithLogonEmail` meglévő és `LocalAccountDiscoveryUsingEmailAddress` az önkiszolgáló műszaki profilokat a következőre. Ha a szabályzat korábbi verzióját Azure AD B2C használta. Ezek a technikai profilok `DisplayClaims` a DisplayControl vezérlőre való hivatkozással használhatók.

További információ: Önkiszolgáló technikai [profil és](restful-technical-profile.md) [DisplayControl.](display-controls.md)

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

## <a name="optional-localize-your-email"></a>[Nem kötelező] Az e-mail honosizálása

Az e-mail honosítania kell a honosított sztringeket a Mailjetnek vagy az e-mail-szolgáltatónak. Honosíthatja például az e-mail tárgyát, törzsét, a kódüzenetét vagy az e-mail aláírását. Erre a [GetLocalizedStringsTransformation](string-transformations.md) jogcím-átalakítással másolhat honosított sztringeket jogcímtípusokba. A JSON hasznos adatokat generáló jogcím-átalakítás a honosított sztringeket tartalmazó bemeneti `GenerateEmailRequestBody` jogcímeket használ.

1. A szabályzatban adja meg a következő sztring jogcímeket: tárgy, üzenet, codeIntro és aláírás.
1. Definiálhat [egy GetLocalizedStringsTransformation](string-transformations.md) jogcím-átalakítást, amely honosított sztringértékeket helyettesít az 1. lépésben megadott jogcímek között.
1. Módosítsa a `GenerateEmailRequestBody` jogcímek átalakítását úgy, hogy bemeneti jogcímeket használjanak a következő XML-kódrészlettel.
1. Frissítse a Mailjet-sablont, hogy dinamikus paramétereket használjon az összes olyan sztring helyett, amelyet a Azure AD B2C.

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

1. A LocalizedResources elemekre mutató hivatkozásokat a [ContentDefinitions elem frissítésével adhat](contentdefinitions.md) hozzá.

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

1. Végül adja hozzá a következő bemeneti jogcímek átalakítását a `LocalAccountSignUpWithLogonEmail` és `LocalAccountDiscoveryUsingEmailAddress` a technikai profilhoz.

    ```xml
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```
    
## <a name="optional-localize-the-ui"></a>[Nem kötelező] A felhasználói felület honosizálása

A Honosítás elem lehetővé teszi több területi beállítás vagy nyelv támogatását a szabályzatban a felhasználói utakhoz. A házirendek honosítási támogatása lehetővé teszi, hogy [](localization-string-ids.md#verification-display-control-user-interface-elements)nyelvspecifikus sztringeket adjon meg mind az Ellenőrzés megjelenítési vezérlő felhasználói felületének elemeihez, mind pedig az Egyszer megjelenő jelszó [hibaüzenethez.](localization-string-ids.md#one-time-password-error-messages) Adja hozzá a következő LocalizedString karakterláncot a LocalizedResources erőforráshoz. 

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

A honosított sztringek hozzáadása után távolítsa el az egyszeri jelszóval kapcsolatos érvényesítési hibaüzenetek metaadatait a LocalAccountSignUpWithLogonEmail és a LocalAccountDiscoveryUsingEmailAddress technikai profilokból.

## <a name="next-steps"></a>Következő lépések

Az egyéni e-mail-ellenőrzési szabályzatra a GitHubon találhat példát:

- [Egyéni e-mail-ellenőrzés – DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Az egyéni házirendek és a HTTP REST API SMTP e-mail-szolgáltatók használatával kapcsolatos információkért [lásd: RESTful](restful-technical-profile.md)technikai profil definiálása egyéni házirendben Azure AD B2C házirendben.

::: zone-end
