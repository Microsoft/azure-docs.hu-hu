---
title: SAML-jogkivonat titkosítása Azure Active Directory
description: Ismerje meg, hogyan konfigurálhatja Azure Active Directory SAML-jogkivonat titkosítását.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32a6034f407bd81e5d2d2ab8227d08a67f034d4c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258642"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Útmutató: az Azure AD SAML-jogkivonat titkosításának konfigurálása

> [!NOTE]
> A jogkivonat-titkosítás egy Azure Active Directory (Azure AD) prémium szintű szolgáltatás. Az Azure AD-kiadásokkal,-funkciókkal és-díjszabással kapcsolatos további információkért lásd: az [Azure ad díjszabása](https://azure.microsoft.com/pricing/details/active-directory/).

Az SAML-jogkivonat titkosítása lehetővé teszi a titkosított SAML-kijelentések használatát egy olyan alkalmazással, amely támogatja azt. Ha egy alkalmazáshoz van konfigurálva, az Azure AD az Azure AD-ben tárolt tanúsítványból beszerzett nyilvános kulccsal titkosítja az alkalmazás számára kibocsátott SAML-kijelentéseket. Az alkalmazásnak a megfelelő titkos kulcsot kell használnia, hogy visszafejtse a tokent, mielőtt a bejelentkezett felhasználó hitelesítésének bizonyítékként használható lenne.

Az Azure AD és az alkalmazás közötti SAML-kikötések titkosítása további garanciát nyújt arra, hogy a jogkivonat tartalma nem oldható fel, és a személyes vagy vállalati adatokat is feltörték.

Az Azure AD SAML-tokeneket még a jogkivonat titkosítása nélkül sem adja át a rendszer a hálózaton. Az Azure AD-ben jogkivonat-kérelmek/-válaszok cseréjére van szükség a titkosított HTTPS/TLS-csatornákon keresztül, így a IDENTITÁSSZOLGÁLTATÓ, a böngésző és az alkalmazás közötti kommunikáció titkosított hivatkozások használatával történik. Vegye figyelembe a helyzet token-titkosításának értékét a további tanúsítványok kezelésével szemben.   

A jogkivonat-titkosítás konfigurálásához fel kell töltenie egy X. 509 nevű tanúsítványfájl, amely a nyilvános kulcsot tartalmazza az alkalmazást képviselő Azure AD Application objektumhoz. Az X beszerzése. 509 tanúsítvány, letöltheti azt az alkalmazásból, vagy beolvashatja az alkalmazás gyártójától olyan esetekben, amikor az alkalmazás gyártója titkosítási kulcsokat biztosít, vagy olyan esetekben, amikor az alkalmazás a titkos kulcs megadását kéri, kriptográfiai eszközökkel, az alkalmazás kulcstárolóba feltöltött titkos kulcs részével, valamint az Azure AD-ba feltöltött, megfelelő nyilvános kulcsú tanúsítvány használatával.

Az Azure AD AES-256 használatával titkosítja az SAML-kikötések adataival.

## <a name="configure-saml-token-encryption"></a>SAML-jogkivonat titkosításának konfigurálása

Az SAML-jogkivonat titkosításának konfigurálásához kövesse az alábbi lépéseket:

1. Szerezzen be egy olyan nyilvános kulcsú tanúsítványt, amely megfelel az alkalmazásban konfigurált titkos kulcsnak.

    Hozzon létre egy, a titkosításhoz használandó aszimmetrikus kulcspárt. Ha az alkalmazás egy nyilvános kulcsot biztosít a titkosításhoz, kövesse az alkalmazás utasításait az X. 509 tanúsítvány letöltéséhez.

    A nyilvános kulcsot. cer formátumban kell tárolni egy X. 509 tanúsítványfájl-fájlban.

    Ha az alkalmazás egy, a példányhoz létrehozott kulcsot használ, kövesse az alkalmazás által az Azure AD-bérlőből származó tokenek visszafejtéséhez használt titkos kulcs telepítésére vonatkozó utasításokat.

1. Adja hozzá a tanúsítványt az alkalmazás konfigurációjához az Azure AD-ben.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>A jogkivonat-titkosítás konfigurálása a Azure Portalban

A nyilvános tanúsítványt a Azure Portalon belül adhatja hozzá az alkalmazás konfigurációjához.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

1. Lépjen a **Azure Active Directory > vállalati alkalmazások** panelre, majd válassza ki azt az alkalmazást, amelyre a jogkivonat-titkosítást konfigurálni kívánja.

1. Az alkalmazás lapján válassza a jogkivonat- **titkosítás** lehetőséget.

    ![Jogkivonat-titkosítási beállítás a Azure Portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > A **jogkivonat-titkosítási** beállítás csak olyan SAML-alkalmazásokhoz érhető el, amelyek a Azure Portal **vállalati alkalmazások** paneljéről lettek beállítva, vagy az alkalmazás-katalógusból vagy egy nem katalógusból származó alkalmazásból. Más alkalmazások esetén ez a menüpont le van tiltva. A Azure Portal **Alkalmazásregisztrációk** felületén keresztül regisztrált alkalmazások esetében az alkalmazás jegyzékfájljának használatával konfigurálhatja a titkosítást az SAML-tokenekhez az Microsoft Graph vagy a PowerShell segítségével.

1. A **jogkivonat-titkosítás** lapon válassza a **tanúsítvány importálása** elemet a nyilvános X. 509 tanúsítványt tartalmazó. cer fájl importálásához.

    ![Importálja az X. 509 tanúsítványt tartalmazó. cer fájlt.](./media/howto-saml-token-encryption/import-certificate-small.png)

1. A tanúsítvány importálása után, a titkos kulcs pedig az alkalmazás oldalán való használatra van konfigurálva, aktiválja a titkosítást az ujjlenyomat állapota melletti **...** elemre kattintva, majd válassza a **jogkivonat-titkosítás aktiválása** lehetőséget a legördülő menüből.

1. Válassza az **Igen** lehetőséget a jogkivonat-titkosítási tanúsítvány aktiválásának megerősítéséhez.

1. Ellenőrizze, hogy az alkalmazáshoz kibocsátott SAML-kijelentések titkosítva vannak-e.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Jogkivonat-titkosítás inaktiválása a Azure Portalban

1. A Azure Portal lépjen a **Azure Active Directory > vállalati alkalmazások** elemre, majd válassza ki azt az alkalmazást, amelyen engedélyezve van az SAML-jogkivonat titkosítása.

1. Az alkalmazás lapján válassza a jogkivonat- **titkosítás** elemet, keresse meg a tanúsítványt, majd válassza a **...** lehetőséget a legördülő menü megjelenítéséhez.

1. Jelölje be a **jogkivonat-titkosítás inaktiválása** jelölőnégyzetet.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>SAML-jogkivonat titkosításának konfigurálása a Graph API, a PowerShell vagy az alkalmazás jegyzékfájljának használatával

A titkosítási tanúsítványokat az Azure AD-ban található Application objektum tárolja `encrypt` használati címkével. Több titkosítási tanúsítványt is konfigurálhat, amelyek esetében a tokenek titkosítása aktív, az `tokenEncryptionKeyID` attribútum azonosítja.

A jogkivonat-titkosítás Microsoft Graph API vagy a PowerShell használatával történő konfigurálásához szüksége lesz az alkalmazás objektumazonosító-azonosítójával. Ezt az értéket programozott módon megkeresheti, vagy megtekintheti az alkalmazás **Tulajdonságok** lapját a Azure Portal, és megállapíthatja az **objektumazonosító** értékét.

Amikor a Graph, a PowerShell vagy az Application manifest használatával konfigurálja a hitelesítő adatokat, akkor a keyId használandó GUID azonosítót kell megadnia.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Jogkivonat-titkosítás konfigurálása Microsoft Graph használatával

1. Frissítse az alkalmazás `keyCredentials` egy X. 509 tanúsítvánnyal a titkosításhoz. Az alábbi példa azt szemlélteti, hogyan teheti ezt meg.

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

1. Azonosítsa a tokenek titkosításához aktív titkosítási tanúsítványt. Az alábbi példa azt szemlélteti, hogyan teheti ezt meg.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Jogkivonat-titkosítás konfigurálása a PowerShell használatával

1. A bérlőhöz való kapcsolódáshoz használja a legújabb Azure AD PowerShell-modult.

1. Állítsa be a jogkivonat titkosítási beállításait a **[set-AzureApplication](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** parancs használatával.

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Olvassa el a jogkivonat titkosítási beállításait a következő parancsokkal.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Jogkivonat-titkosítás konfigurálása az alkalmazás jegyzékfájljának használatával

1. A Azure Portal lépjen a **Azure Active Directory > Alkalmazásregisztrációk**.

1. Válassza a **minden alkalmazás** lehetőséget a legördülő listából az összes alkalmazás megjelenítéséhez, majd válassza ki a konfigurálni kívánt vállalati alkalmazást.

1. Az alkalmazás oldalán válassza a **jegyzékfájl** lehetőséget az [alkalmazás jegyzékfájljának](../develop/reference-app-manifest.md)szerkesztéséhez.

1. Állítsa be az attribútum értékét `tokenEncryptionKeyId` .

    A következő példa egy olyan alkalmazás-jegyzékfájlt mutat be, amely két titkosítási tanúsítvánnyal van konfigurálva, a második pedig a tokenEnryptionKeyId használatával.

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

* Ismerje meg, [hogyan használja az Azure ad az SAML protokollt](../develop/active-directory-saml-protocol-reference.md)
* A SAML-tokenek formátumának, biztonsági jellemzőinek és tartalmának megismerése az [Azure ad-ben](../develop/reference-saml-tokens.md)