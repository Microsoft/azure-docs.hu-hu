---
title: SAML-jogkivonat titkosítása a Azure Active Directory
description: Megtudhatja, hogyan konfigurálhatja az SAML Azure Active Directory titkosítást.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: iangithinji
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c06a499cccb03e6726ee19542d7eb79e0c99b43
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375729"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>How to: Configure Azure AD SAML token encryption (Az Azure AD SAML-jogkivonat titkosításának konfigurálása)

> [!NOTE]
> A jogkivonat-titkosítás egy Azure Active Directory (Azure AD) prémium szintű szolgáltatás. További információ az Azure AD kiadásairól, funkcióiról és díjszabásról: [Az Azure AD díjszabása.](https://azure.microsoft.com/pricing/details/active-directory/)

Az SAML-jogkivonat titkosítása lehetővé teszi a titkosított SAML-helyességi feltétel használatát az azt támogató alkalmazással. Ha egy alkalmazáshoz van konfigurálva, az Azure AD az Azure AD-ban tárolt tanúsítványból származó nyilvános kulccsal titkosítja az alkalmazás számára kibocsátott SAML-helyességi adatokat. Az alkalmazásnak a megfelelő titkos kulcsot kell használnia a jogkivonat visszafejtéséhez, mielőtt használható lenne a bejelentkezett felhasználó hitelesítésének bizonyítékaként.

Az Azure AD és az alkalmazás KÖZÖTTI SAML-helyességi feltétel titkosítása további garanciát nyújt arra, hogy a jogkivonat tartalma nem fogható el, és a személyes vagy vállalati adatok biztonsága sérül.

Az Azure AD SAML-jogkivonatok még jogkivonattitkosítás nélkül sem adnak át titkosítva a hálózaton. Az Azure AD megköveteli, hogy a jogkivonat-kérések és -válaszok cseréje titkosított HTTPS/TLS-csatornákon keresztül történik, hogy az internetszolgáltató, a böngésző és az alkalmazás közötti kommunikáció titkosított hivatkozásokon keresztül zajlik. Vegye figyelembe az Ön helyzetére vonatkozó jogkivonat-titkosítás értékét a további tanúsítványok kezelésével kapcsolatos többletterheléshez képest.   

A jogkivonat titkosításának konfigurálához fel kell töltenie egy X.509-tanúsítványfájlt, amely tartalmazza a nyilvános kulcsot az alkalmazást képviselő Azure AD-alkalmazásobjektumba. Az X.509-tanúsítvány beszerzéséhez letöltheti az alkalmazást magáról az alkalmazásból, vagy beszerezheti az alkalmazás gyártójától olyan esetekben, amikor az alkalmazás szállítója titkosítási kulcsokat biztosít, vagy ha az alkalmazás azt várja, hogy titkos kulcsot adjon meg, kriptográfiai eszközökkel, az alkalmazás kulcstárolóba feltöltött titkoskulcs-részével és az Azure AD-be feltöltött megfelelő nyilvánoskulcs-tanúsítvánnyal.

Az Azure AD az AES-256 használatával titkosítja az SAML helyességi feltétel adatait.

## <a name="configure-saml-token-encryption"></a>SAML-jogkivonat titkosításának konfigurálása

Az SAML-jogkivonat titkosításának konfiguráláshoz kövesse az alábbi lépéseket:

1. Szerezzen be egy nyilvános kulcsú tanúsítványt, amely megegyezik az alkalmazásban konfigurált titkos kulccsal.

    Hozzon létre egy aszimmetrikus kulcspárt a titkosításhoz. Vagy ha az alkalmazás nyilvános kulcsot ad meg a titkosításhoz, kövesse az alkalmazás utasításait az X.509-tanúsítvány letöltéséhez.

    A nyilvános kulcsot egy X.509 tanúsítványfájlban kell tárolni .cer formátumban.

    Ha az alkalmazás a példányhoz létrehozott kulcsot használ, kövesse az alkalmazás által az Azure AD-bérlőtől származó jogkivonatok visszafejtéséhez használt titkos kulcs telepítésére vonatkozó utasításokat.

1. Adja hozzá a tanúsítványt az Alkalmazáskonfigurációhoz az Azure AD-ban.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Jogkivonat titkosításának konfigurálása a Azure Portal

A nyilvános tanúsítványt hozzáadhatja az alkalmazáskonfigurációhoz a Azure Portal.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

1. A Vállalati **Azure Active Directory > panelen** válassza ki azt az alkalmazást, amely számára jogkivonat-titkosítást kíván konfigurálni.

1. Az alkalmazás oldalán válassza a Jogkivonat-titkosítás **lehetőséget.**

    ![Jogkivonat-titkosítási beállítás a Azure Portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > A **Jogkivonat-titkosítás** lehetőség csak olyan SAML-alkalmazásokhoz  érhető el, amelyek az Azure Portal Vállalati alkalmazások paneljére vannak beállítva az Alkalmazásgyűjteményből vagy egy nem katalógusból származó alkalmazásból. Más alkalmazások esetében ez a menüpont le van tiltva. Az Alkalmazásregisztrációk felhasználói  élményében regisztrált alkalmazások esetében az SAML Azure Portal token titkosítását az alkalmazásjegyzék használatával, a Microsoft Graph vagy a PowerShell használatával konfigurálhatja.

1. A **Jogkivonat-titkosítás lapon** válassza a Tanúsítvány **importálása** lehetőséget a nyilvános X.509-tanúsítványt tartalmazó .cer fájl importáláshoz.

    ![Importálja az X.509-tanúsítványt tartalmazó .cer fájlt](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Miután importálta a tanúsítványt, és konfigurálta a titkos kulcsot az alkalmazásoldalon való használatra, aktiválja  a titkosítást az ujjlenyomat állapota melletti **...** kiválasztásával, majd válassza a Tokentitkosítás aktiválása lehetőséget a legördülő menüben.

1. Válassza **az Igen** lehetőséget a jogkivonat-titkosítási tanúsítvány aktiválásának megerősítéséhez.

1. Győződjön meg arról, hogy az alkalmazáshoz kibocsátott SAML helyességi feltétel titkosítva van.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Tokentitkosítás inaktiválása a Azure Portal

1. A Azure Portal válassza a Vállalati **alkalmazások Azure Active Directory > lehetőséget,** majd válassza ki azt az alkalmazást, amely esetében engedélyezve van az SAML-jogkivonat titkosítása.

1. Az alkalmazás oldalán válassza a **Jogkivonat-titkosítás** lehetőséget, keresse meg a tanúsítványt, majd válassza a **...** lehetőséget a legördülő menüt.

1. Válassza **a Token Encryption inaktiválása lehetőséget.**

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>SAML-jogkivonat titkosításának konfigurálása Graph API, PowerShell vagy alkalmazásjegyzék használatával

A titkosítási tanúsítványokat az alkalmazásobjektum tárolja az Azure AD-ban egy `encrypt` használati címkével. Több titkosítási tanúsítványt is konfigurálhat, és a tokenek titkosításához aktív tanúsítványt a attribútum `tokenEncryptionKeyID` azonosítja.

Az alkalmazás objektumazonosítójára szüksége lesz a jogkivonatok titkosításának Microsoft Graph API vagy a PowerShell használatával. Ezt az értéket programozott módon, vagy az alkalmazás  Tulajdonságok lapján találhatja meg a Azure Portal és az **Objektumazonosító** értékét.

Amikor a KeyCredentialt a Graph, a PowerShell vagy az alkalmazásjegyzék használatával konfigurálja, létre kell hoznia egy GUID-t a keyId azonosítóhoz.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Jogkivonat titkosításának konfigurálása Microsoft Graph

1. Frissítse az alkalmazásokat egy `keyCredentials` X.509-tanúsítvánnyal a titkosításhoz. Az alábbi példa ezt mutatja be.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Azonosítsa a jogkivonatok titkosításához aktív titkosítási tanúsítványt. Az alábbi példa ezt mutatja be.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Jogkivonat-titkosítás konfigurálása a PowerShell használatával

1. A legújabb Azure AD PowerShell-modullal csatlakozhat a bérlőhöz.

1. Állítsa be a jogkivonat titkosítási beállításait a **[Set-AzureApplication paranccsal.](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview&preserve-view=true)**

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Olvassa el a jogkivonat titkosítási beállításait az alábbi parancsokkal.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Jogkivonat-titkosítás konfigurálása az alkalmazásjegyzék használatával

1. A Azure Portal a **Azure Active Directory > Alkalmazásregisztrációk.**

1. Válassza **Minden alkalmazás** az összes alkalmazást a legördülő menüből, majd válassza ki a konfigurálni kívánt vállalati alkalmazást.

1. Az alkalmazás oldalán válassza a **Jegyzékfájl** lehetőséget az [alkalmazásjegyzék szerkesztéséhez.](../develop/reference-app-manifest.md)

1. Állítsa be az attribútum `tokenEncryptionKeyId` értékét.

    Az alábbi példa egy két titkosítási tanúsítványsal konfigurált alkalmazásjegyzéket mutat be, a másodikat pedig aktívként a tokenEnryptionKeyId használatával.

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>Következő lépések

* Ismerje [meg, hogyan használja az Azure AD az SAML protokollt](../develop/active-directory-saml-protocol-reference.md)
* Az SAML-jogkivonatok formátumának, biztonsági jellemzőinek és tartalmának megismerése [az Azure AD-ban](../develop/reference-saml-tokens.md)