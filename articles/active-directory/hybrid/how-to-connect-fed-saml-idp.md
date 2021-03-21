---
title: 'Azure AD Connect: az egyszeri bejelentkezéshez SAML 2,0 identitás-szolgáltató használata – Azure'
description: Ez a dokumentum egy SAML 2,0-kompatibilis identitásszolgáltató használatát ismerteti az egyszeri bejelentkezéshez.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b26c24149d422021dcb86f75c915ade89cbccdec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97589875"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>SAML 2.0 identitásszolgáltató használata egyszeri bejelentkezéshez

Ez a dokumentum olyan SAML 2,0-kompatibilis SP-Lite profil-alapú identitás-szolgáltató használatával kapcsolatos információkat tartalmaz, mint az előnyben részesített biztonsági jogkivonat szolgáltatás (STS)/Identity Provider. Ez a forgatókönyv akkor lehet hasznos, ha már rendelkezik egy olyan helyszíni felhasználói címtárral és jelszó-tárolóval, amely az SAML 2,0 használatával érhető el. Ezt a meglévő felhasználói könyvtárat használhatja a Microsoft 365 és más Azure AD-védelemmel ellátott erőforrásokhoz való bejelentkezéshez. Az SAML 2,0 SP-Lite profil a széles körben használt Security Assertion Markup Language (SAML) összevont identitási szabványon alapul, amely lehetővé teszi a bejelentkezést és az attribútumok Exchange-keretrendszerét.

>[!NOTE]
>Az Azure AD-vel való használatra tesztelt, harmadik féltől származó IDP megtekintéséhez tekintse meg az [Azure ad összevonási kompatibilitási listáját](how-to-connect-fed-compatibility.md)

A Microsoft a megfelelő módon konfigurált SAML 2,0-profil alapú identitásszolgáltató támogatja a Microsoft Cloud Service (például Microsoft 365) integrációját. Az SAML 2,0 Identity Providers harmadik féltől származó termékek, ezért a Microsoft nem nyújt támogatást az üzembe helyezés, a konfigurálás és a velük kapcsolatos ajánlott eljárások során. A megfelelő konfigurálást követően az SAML 2,0 identitás-szolgáltatóval való integráció a Microsoft connectivity Analyzer eszköz használatával tesztelhető a megfelelő konfigurációhoz, amely az alábbiakban olvasható részletesebben. Ha többet szeretne megtudni az SAML 2,0 SP-Lite profil alapú identitás-szolgáltatóról, kérje meg az azt szolgáltató szervezetet.

> [!IMPORTANT]
> Ebben a bejelentkezési forgatókönyvben csak korlátozott számú ügyfél érhető el az SAML 2,0 Identity Providers használatával, ez a következőket foglalja magában:
> 
> - Web-alapú ügyfelek, például az Outlook Web Access és a SharePoint Online
> - Az egyszerű hitelesítést és egy támogatott Exchange-hozzáférési módszert használó e-mail-alapú ügyfelek, például az IMAP, a POP, az Active Sync, a MAPI stb. (a bővített ügyféloldali protokoll végpontjának központi telepítése szükséges), beleértve a következőket:
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (különböző iOS-verziók)
>     - Különböző Google Android-eszközök
>     - Windows Phone-telefon 7, Windows Phone-telefon 7,8 és Windows Phone-telefon 8,0
>     - Windows 8 mail Client és Windows 8,1 mail Client
>     - Windows 10 levelező ügyfélprogram

Az SAML 2,0-es identitás-szolgáltató az összes többi ügyfél nem érhető el ebben a bejelentkezési forgatókönyvben. Például a Lync 2010 asztali ügyfél nem tud bejelentkezni a szolgáltatásba az egyszeri bejelentkezéshez konfigurált SAML 2,0 Identity Provider használatával.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Az Azure AD SAML 2,0 protokoll követelményei
Ez a dokumentum a protokoll és az üzenet formázásának részletes követelményeit tartalmazza, amelyeket az SAML 2,0-identitás szolgáltatójának kell végrehajtania ahhoz, hogy összevonása az Azure AD-vel, hogy engedélyezze a bejelentkezést egy vagy több Microsoft Cloud Services-szolgáltatásba (például Microsoft 365). Az ebben a forgatókönyvben használt Microsoft Cloud Service SAML 2,0 függő entitása (SP-STS) az Azure AD.

Azt javasoljuk, hogy az SAML 2,0-azonosító kimeneti üzenetei a lehető leghasonlóak legyenek a megadott mintavételi nyomkövetésekhez. Ha lehetséges, használjon adott attribútum-értékeket a megadott Azure AD-metaadatokból. Ha elégedett a kimeneti üzenetekkel, tesztelheti a Microsoft connectivity Analyzert az alább leírtak szerint.

Az Azure AD metaadatait a következő URL-címről töltheti le: [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml) .
A Microsoft 365 kínai-specifikus példányát használó Kínában lévő ügyfelek esetében a következő összevonási végpontot kell használni: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml) .

## <a name="saml-protocol-requirements"></a>SAML protokollra vonatkozó követelmények
Ez a szakasz részletesen ismerteti, hogy a kérelem és a válaszüzenet párok hogyan hozhatók össze az üzenetek megfelelő formázásának elősegítése érdekében.

Az Azure AD konfigurálható úgy, hogy az SAML 2,0 SP Lite-profilt használó identitás-szolgáltatókkal működjön együtt az alább felsorolt konkrét követelményekkel. Az SAML-kérelem és a válaszüzenetek, valamint az automatikus és manuális tesztelés együttes használatával dolgozhat az Azure AD-vel való együttműködéssel.

## <a name="signature-block-requirements"></a>Aláírási blokkolási követelmények
Az SAML-válaszüzenetben az aláírási csomópont az üzenet digitális aláírásával kapcsolatos információkat tartalmaz. Az aláírási blokk követelményei a következők:

1. Magát az állítási csomópontot kell aláírni
2. Az RSA-SHA1 algoritmust DigestMethod kell használni. Más digitális aláírási algoritmusok nem fogadhatók el.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3. Az XML-dokumentumot is aláírhatja. 
4. Az átalakítási algoritmusnak meg kell egyeznie a következő mintában szereplő értékekkel:     `<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9. A SignatureMethod algoritmusnak meg kell egyeznie a következő mintával:    `<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Támogatott kötések
A kötések az átvitelsel kapcsolatos kommunikációs paraméterek, amelyekre szükség van. Az alábbi követelmények vonatkoznak a kötésekre

1. A HTTPS a szükséges átvitel.
2. Az Azure AD-ben a bejelentkezés során a rendszer HTTP-BEJEGYZÉST kér a jogkivonat-küldéshez.
3. Az Azure AD HTTP-BEJEGYZÉST fog használni a hitelesítési kérelemhez az Identitáskezelő számára, és átirányítja a kijelentkezési üzenetet az identitás-szolgáltatónak.

## <a name="required-attributes"></a>Szükséges attribútumok
Ez a táblázat az SAML 2,0 üzenetben megadott attribútumok követelményeit mutatja be.
 
|Attribútum|Leírás|
| ----- | ----- |
|NameID|Az állítás értékének meg kell egyeznie az Azure AD-felhasználó ImmutableID. Legfeljebb 64 alfanumerikus karakterből állhat. A nem HTML-alapú biztonságos karaktereket kódolni kell, például a "+" karaktert ". 2B"-ként.|
|IDPEmail|Az egyszerű felhasználónév (UPN) az SAML-válaszban szerepel, mint a felhasználó UserPrincipalName (UPN) az Azure AD-ben vagy Microsoft 365-ben IDPEmail nevű elem. Az egyszerű felhasználónév e-mail-cím formátumú. UPN-érték a Windows Microsoft 365ban (Azure Active Directory).|
|Kiállító|Az identitás-szolgáltató URI azonosítójának kell lennie. Ne használja újra a kiállítót a mintául szolgáló üzenetekből. Ha az Azure AD-bérlő több legfelső szintű tartománnyal rendelkezik, a kiállítónak meg kell egyeznie a megadott URI-beállításokkal.|

>[!IMPORTANT]
>Az Azure AD jelenleg a következő NameID formátumot támogatja az SAML 2.0 esetében: urn: Oasis: Names: TC: SAML: 2.0: NameID-Format: perzisztens.

## <a name="sample-saml-request-and-response-messages"></a>SAML-kérelem és válaszüzenetek mintája
Megjelenik egy kérelem és válaszüzenet pár a bejelentkezési üzenet Exchange-hez.
Az alábbi példa az Azure AD-ből egy példa SAML 2,0-identitás-szolgáltatóra küldött kérelem-üzenet. Az SAML 2,0-es identitás-szolgáltató az SAML-P protokoll használatára konfigurált Active Directory összevonási szolgáltatások (AD FS) (AD FS). Az együttműködési képesség tesztelése más SAML 2,0 identitás-szolgáltatókkal is befejeződött.

```xml
  <samlp:AuthnRequest 
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" 
    xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" 
    ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" 
    IssueInstant="2014-01-30T16:18:35Z" 
    Version="2.0" 
    AssertionConsumerServiceIndex="0" >
        <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
        <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
  </samlp:AuthnRequest>
```

A következő egy példaként szolgáló válaszüzenet, amelyet a rendszer az SAML 2,0-kompatibilis identitás-szolgáltatótól az Azure AD-be/Microsoft 365-re küld.

```xml
    <samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>
```

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Az SAML 2,0-kompatibilis identitás-szolgáltató konfigurálása
Ez a szakasz azt ismerteti, hogyan konfigurálhatja az SAML 2,0 Identity providert úgy, hogy az Azure AD-vel való összevonása engedélyezze az egyszeri bejelentkezéses hozzáférést egy vagy több Microsoft Cloud Services-szolgáltatáshoz (például Microsoft 365) az SAML 2,0 protokoll használatával. A jelen forgatókönyvben használt Microsoft Cloud Service SAML 2,0 függő entitása az Azure AD.

## <a name="add-azure-ad-metadata"></a>Azure AD-metaadatok hozzáadása
Az SAML 2,0-identitás szolgáltatójának meg kell felelnie az Azure AD függő entitásra vonatkozó információknak. Az Azure AD metaadatokat tesz közzé a következő helyen: https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml .

Javasoljuk, hogy mindig importálja a legújabb Azure AD-metaadatokat az SAML 2,0-identitás szolgáltatójának konfigurálásakor.

>[!NOTE]
>Az Azure AD nem olvassa be a metaadatokat az identitás-szolgáltatótól.

## <a name="add-azure-ad-as-a-relying-party"></a>Azure AD hozzáadása függő entitásként
Engedélyeznie kell az SAML 2,0-identitás szolgáltatója és az Azure AD közötti kommunikációt. Ez a konfiguráció a megadott identitás-szolgáltatótól függ, és a dokumentációjában kell megjelennie. Általában úgy állítja be a függő entitás AZONOSÍTÓját, hogy az az Azure AD metaadatainak entityID megegyezzen.

>[!NOTE]
>Ellenőrizze, hogy az SAML 2,0 azonosító kiszolgálójának órája szinkronizálva van-e a pontos időforrással. A pontatlan idő miatt az összevont bejelentkezések sikertelenek lehetnek.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>A Windows PowerShell telepítése SAML 2,0 identitás-szolgáltatóval való bejelentkezéshez
Miután konfigurálta az SAML 2,0 Identity providert az Azure AD-bejelentkezéssel való használatra, a következő lépés a Windows PowerShell Azure Active Directory moduljának letöltése és telepítése. A telepítés után ezeket a parancsmagokat fogja használni az Azure AD-tartományok összevont tartományként való konfigurálásához.

A Windows PowerShellhez készült Azure Active Directory modul egy letöltés a szervezetek adatai Azure AD-ben való kezeléséhez. Ez a modul parancsmagokat telepít a Windows PowerShell-be; ezeket a parancsmagokat futtatva egyszeri bejelentkezéses hozzáférést állíthat be az Azure AD-hez, és az összes előfizetett felhőalapú szolgáltatáshoz. A parancsmagok letöltésére és telepítésére vonatkozó utasításokért lásd: [/Previous-Versions/Azure/jj151815 (v = Azure. 100)](/previous-versions/azure/jj151815(v=azure.100))

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Megbízhatóság beállítása az SAML-identitás szolgáltatója és az Azure AD között
Az összevonás Azure AD-tartományhoz való konfigurálása előtt konfigurálnia kell egy egyéni tartományt. Nem összevonása a Microsoft által biztosított alapértelmezett tartományt. A Microsoft alapértelmezett tartománya a "onmicrosoft.com" karakterlánccal végződik.
A Windows PowerShell parancssori felületén számos parancsmagot fog futtatni az egyszeri bejelentkezéshez szükséges tartományok hozzáadásához vagy átalakításához.

Az SAML 2,0-összevonása használni kívánt összes Azure Active Directory tartományt egyszeri bejelentkezési tartományként kell hozzáadni, vagy egy normál tartományból egyszeri bejelentkezési tartományba kell alakítani. Egy tartomány hozzáadása vagy konvertálása megbízhatósági kapcsolatot létesít az SAML 2,0-identitás szolgáltatója és az Azure AD között.

Az alábbi eljárás végigvezeti egy meglévő standard tartomány egy összevont tartományba történő átalakításán az SAML 2,0 SP-Lite használatával. 

>[!NOTE]
>A tartomány a lépés elvégzése után akár 2 órával is hatással lehet a felhasználókra.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Tartomány konfigurálása az Azure AD-címtárban összevonás esetén


1. Kapcsolódjon az Azure AD-címtárhoz bérlői rendszergazdaként:

  ```powershell
  Connect-MsolService
  ```
  
2. Konfigurálja a kívánt Microsoft 365 tartományt az SAML 2,0-alapú összevonás használatára:

  ```powershell
  $dom = "contoso.com" 
  $BrandName - "Sample SAML 2.0 IDP" 
  $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" 
  $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" 
  $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" 
  $MyURI = "urn:uri:MySamlp2IDP" 
  $MySigningCert = "MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" 
  $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" 
  $Protocol = "SAMLP" 
  Set-MsolDomainAuthentication `
    -DomainName $dom `
    -FederationBrandName $BrandName `
    -Authentication Federated `
    -PassiveLogOnUri $LogOnUrl `
    -ActiveLogOnUri $ecpUrl `
    -SigningCertificate $MySigningCert `
    -IssuerUri $MyURI `
    -LogOffUri $LogOffUrl `
    -PreferredAuthenticationProtocol $Protocol
  ``` 

3.  Az aláíró tanúsítvány Base64 kódolású karakterláncát a IDENTITÁSSZOLGÁLTATÓ metaadat-fájljából kérheti le. Erre a helyre példa van megadva, de a megvalósítástól függően némileg eltérőek lehetnek.

  ```xml
  <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <KeyDescriptor use="signing">
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
       <X509Data>
         <X509Certificate> MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate>
        </X509Data>
      </KeyInfo>
    </KeyDescriptor>
  </IDPSSODescriptor>
  ``` 

További információ a "set-MsolDomainAuthentication"-ról: [/Previous-Versions/Azure/dn194112 (v = Azure. 100)](/previous-versions/azure/dn194112(v=azure.100)).

>[!NOTE]
>Csak akkor kell használni, `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` Ha beállít egy ECP-bővítményt az identitás-szolgáltatóhoz. Az Exchange Online-ügyfelek az Outlook Web Application (OWA) kizárása után a POST-alapú aktív végpontra támaszkodnak. Ha az SAML 2,0 STS egy aktív végponthoz hasonló aktív végpontot valósít meg, amely ahhoz hasonlít, hogy az Shibboleth az aktív végpontok ECP-implementációja, lehetséges, hogy ezek a gazdag ügyfelek kommunikálhatnak az Exchange Online szolgáltatással.

Az összevonás konfigurálása után visszaválthat a "nem összevont" (vagy "felügyelt") értékre, azonban ez a változás akár két órát is igénybe vehet, és új véletlenszerű jelszavakat kell kiosztania a felhőalapú bejelentkezéshez az egyes felhasználók számára. Bizonyos helyzetekben szükség lehet a "felügyelt" állapotra váltásra, ha a beállításokban hibát kíván visszaállítani. További információ a tartomány-átalakításról: [/Previous-Versions/Azure/dn194122 (v = Azure. 100)](/previous-versions/azure/dn194122(v=azure.100)).

## <a name="provision-user-principals-to-azure-ad--microsoft-365"></a>Felhasználói rendszerbiztonsági tag kiépítése az Azure AD-be/Microsoft 365
Ahhoz, hogy a felhasználók hitelesítése Microsoft 365, az Azure AD-t olyan felhasználói rendszerbiztonsági tagekkel kell kiépítenie, amelyek megfelelnek az SAML 2,0 jogcímben szereplő előállításnak. Ha ezeket a felhasználói rendszerbiztonsági tagokat nem ismeri előre az Azure AD-ben, akkor nem használhatók összevont bejelentkezéshez. A Azure AD Connect vagy a Windows PowerShell használatával is kiépíthető a felhasználói rendszerbiztonsági tag.

A Azure AD Connect segítségével az Azure AD-címtárban szereplő tartományokhoz rendszerbiztonsági tag is kiépíthető a helyszíni Active Directory használatával. További információ: a [helyszíni címtárak integrálása a Azure Active Directorysal](whatis-hybrid-identity.md).

A Windows PowerShell használatával automatizálható az új felhasználók hozzáadása az Azure AD-hez, valamint a változások szinkronizálása a helyszíni címtárból. A Windows PowerShell-parancsmagok használatához le kell töltenie a [Azure Active Directory modulokat](/powershell/azure/active-directory/install-adv2).

Ez az eljárás bemutatja, hogyan adhat hozzá egyetlen felhasználót az Azure AD-hez.


1. Kapcsolódjon az Azure AD-címtárhoz bérlői rendszergazdaként: Kapcsolódás-MsolService.
2. Új felhasználói tag létrehozása:

    ```powershell
    New-MsolUser `
      -UserPrincipalName elwoodf1@contoso.com `
      -ImmutableId ABCDEFG1234567890 `
      -DisplayName "Elwood Folk" `
      -FirstName Elwood `
      -LastName Folk `
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" `
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" `
      -UsageLocation "US" 
    ```

További információ a "New-MsolUser" pénztárról: [/Previous-Versions/Azure/dn194096 (v = Azure. 100)](/previous-versions/azure/dn194096(v=azure.100))

>[!NOTE]
>A "UserPrincipalName" értéknek egyeznie kell azzal az értékkel, amelyet az SAML 2,0-jogcím "IDPEmail" küld, és a "ImmutableID" értéknek meg kell egyeznie a "NameID" állításban elküldött értékkel.

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Az egyszeri bejelentkezés ellenőrzése az SAML 2,0-IDENTITÁSSZOLGÁLTATÓ
Rendszergazdaként az egyszeri bejelentkezés (más néven identitás-összevonás) ellenőrzése és kezelése előtt tekintse át az adatokat, és hajtsa végre az alábbi cikkekben ismertetett lépéseket az egyszeri bejelentkezés beállításához az SAML 2,0 SP-Lite-alapú identitás-szolgáltatóval:

1. Áttekintette az Azure AD SAML 2,0 protokoll követelményeit
2. Az SAML 2,0-identitás szolgáltatóját konfigurálta
3. A Windows PowerShell telepítése az SAML 2,0 Identity Provider használatával történő egyszeri bejelentkezéshez
4. Megbízhatóság beállítása az SAML 2,0 Identity Provider és az Azure AD között
5. A Windows PowerShell vagy a Azure AD Connect használatával kiépített egy ismert teszt felhasználói rendszerbiztonsági tag Azure Active Directory (Microsoft 365).
6. Konfigurálja a címtár-szinkronizálást [Azure ad Connect](whatis-hybrid-identity.md)használatával.

Miután beállította az egyszeri bejelentkezést az SAML 2,0 SP-Lite-alapú identitás-szolgáltatóval, ellenőrizze, hogy megfelelően működik-e.

>[!NOTE]
>Ha egy tartományt úgy alakított át, hogy nem ad hozzá egyet, az egyszeri bejelentkezés beállítása akár 24 óráig is eltarthat.
Az egyszeri bejelentkezés ellenőrzése előtt be kell fejeznie Active Directory szinkronizálás beállítását, szinkronizálnia kell a címtárakat, és aktiválnia kell a szinkronizált felhasználókat.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Az eszköz használatával ellenőrizze, hogy az egyszeri bejelentkezés helyesen van-e beállítva.
Annak ellenőrzéséhez, hogy az egyszeri bejelentkezés helyesen van-e beállítva, a következő eljárással ellenőrizheti, hogy be tud-e jelentkezni a Cloud Service-be a vállalati hitelesítő adataival.

A Microsoft olyan eszközt biztosított, amellyel tesztelheti az SAML 2,0-alapú identitás-szolgáltatót. A tesztelési eszköz futtatása előtt konfigurálnia kell egy Azure AD-bérlőt, hogy összevonása az identitás-szolgáltatóval.

>[!NOTE]
>A connectivity Analyzer használatához Internet Explorer 10-es vagy újabb verzió szükséges.



1. Töltse le a [connectivity Analyzert](https://testconnectivity.microsoft.com/?tabid=Client).
2. A telepítés gombra kattintva megkezdheti az eszköz letöltését és telepítését.
3. Válassza a "nem tudom beállítani az Office 365, az Azure vagy más, Azure Active Directory használó szolgáltatásokkal való összevonást" lehetőséget.
4. Az eszköz letöltése és futtatása után megjelenik a kapcsolati diagnosztika ablak. Az eszköz végigvezeti az összevonási kapcsolatok tesztelésének lépésein.
5. A connectivity Analyzer megnyitja az SAML 2,0 IDENTITÁSSZOLGÁLTATÓ a bejelentkezéshez, adja meg a tesztelni kívánt felhasználói tag hitelesítő adatait:

    ![Az SAML 2,0-IDENTITÁSSZOLGÁLTATÓ bejelentkezési ablakát bemutató képernyőkép.](./media/how-to-connect-fed-saml-idp/saml1.png)

6.  Az összevonási teszt bejelentkezési ablakában meg kell adnia egy fióknevet és egy jelszót ahhoz az Azure AD-bérlőhöz, amely az SAML 2,0-identitás-szolgáltatóval való összevonásra van konfigurálva. Az eszköz megkísérli a bejelentkezést a hitelesítő adatok használatával, és a bejelentkezési kísérlet során végrehajtott tesztek részletes eredményeit kimenetként adja meg.

    ![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)

7. Ez az ablak a tesztelés sikertelen eredményét jeleníti meg. A részletes eredmények megtekintése elemre kattintva megtekintheti az egyes végrehajtott tesztek eredményeire vonatkozó információkat. Az eredményeket lemezre is mentheti, hogy megossza őket.
 
> [!NOTE]
> A connectivity Analyzer a WS *-based és az ECP/PAOS protokollok használatával is teszteli az aktív összevonást. Ha nem használja ezeket, figyelmen kívül hagyhatja a következő hibát: az aktív bejelentkezési folyamat tesztelése az identitás-szolgáltató aktív összevonási végpontjának használatával.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Manuálisan ellenőrizze, hogy az egyszeri bejelentkezés helyesen van-e beállítva.

A manuális ellenőrzés további lépéseket tesz elérhetővé, amelyekkel biztosíthatja, hogy az SAML 2,0-identitás szolgáltatója megfelelően működjön.
Annak ellenőrzéséhez, hogy az egyszeri bejelentkezés helyesen van-e beállítva, hajtsa végre a következő lépéseket:

1. Egy tartományhoz csatlakoztatott számítógépen jelentkezzen be a felhőalapú szolgáltatásba ugyanazzal a bejelentkezési névvel, amelyet a céges hitelesítő adataihoz is használ.
2. Kattintson a jelszó mezőbe. Ha az egyszeri bejelentkezés be van állítva, a jelszó mező árnyékolva lesz, és a következő üzenet jelenik meg: "mostantól a vállalatnál be kell jelentkeznie &lt; &gt; ."
3. Kattintson a bejelentkezés a &lt; céges &gt; hivatkozásra. Ha be tud jelentkezni, az egyszeri bejelentkezés beállítása megtörtént.

## <a name="next-steps"></a>Következő lépések

- [Active Directory összevonási szolgáltatások (AD FS) felügyelet és testreszabás Azure AD Connect](how-to-connect-fed-management.md)
- [Az Azure AD összevonás kompatibilitási listája](how-to-connect-fed-compatibility.md)
- [Egyéni telepítés Azure AD Connect](how-to-connect-install-custom.md)
