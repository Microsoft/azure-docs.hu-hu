---
title: Oktatóanyag – ellenőrizhető hitelesítő adatok kiadása és ellenőrzése a Bérlővel (előzetes verzió)
description: A ellenőrizhető hitelesítő adatok kód mintájának módosítása az Azure-Bérlővel való együttműködéshez
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 95d3ab19565ed04d9bf7d59ba7262d40b4971d34
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169997"
---
# <a name="tutorial-issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Oktatóanyag: ellenőrizhető hitelesítő adatok kiadása és ellenőrzése a Bérlővel (előzetes verzió)

> [!IMPORTANT]
> Azure Active Directory ellenőrizhető hitelesítő adatok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Most, hogy az Azure-bérlője beállította a hitelesíthető hitelesítőadat-szolgáltatást, végigvezeti azokat a lépéseket, amelyek lehetővé teszik a Azure Active Directory (Azure AD) számára, hogy ellenőrizhető hitelesítő adatokat bocsásson ki a minta alkalmazás használatával.

Ebben a cikkben a következőket találja:

> [!div class="checklist"]
> * A minta alkalmazás regisztrálása az Azure AD-ben
> * Szabály létrehozása és a fájl megjelenítése
> * Szabályok feltöltése és fájlok megjelenítése
> * A ellenőrizhető hitelesítő adatok kiállítói szolgáltatásának beállítása a Azure Key Vault használatára
> * A mintakód frissítése a bérlő adataival.

A mintakód megköveteli, hogy a felhasználók hitelesítsék magukat egy identitás-szolgáltatónál, különösen Azure AD B2C, mielőtt az ellenőrzött hitelesítő adatokhoz tartozó VC-t ki lehet állítani. A hitelesítő adatok kiadása előtt nem minden ellenőrizhető hitelesítő adat-kiállítók szükségesek a hitelesítéshez.

Az azonosító jogkivonatok hitelesítése lehetővé teszi a felhasználók számára, hogy igazolják, kik, mielőtt megkapják a hitelesítő adatokat. Amikor a felhasználók sikeresen bejelentkeznek, az identitás-szolgáltató egy biztonsági jogkivonatot ad vissza, amely a felhasználóval kapcsolatos jogcímeket tartalmaz. A kiállítói szolgáltatás ezután átalakítja a biztonsági jogkivonatokat és azok jogcímeit egy ellenőrizhető hitelesítő adatba. A ellenőrizhető hitelesítő adatok aláírása a kiállítóval történt.

Az OpenID Connect protokollt támogató bármely identitás-szolgáltató támogatott. A támogatott identitás-szolgáltatók például a következők: [Azure Active Directory](../fundamentals/active-directory-whatis.md)és [Azure ad B2C](../../active-directory-b2c/overview.md). Ebben az oktatóanyagban a HRE-t használjuk.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már végrehajtotta az [előző oktatóanyag](enable-your-tenant-verifiable-credentials.md) lépéseit, és hozzáfér a használt környezethez.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Alkalmazás regisztrálása a felhasználók bejelentkezésének engedélyezéséhez

Egy ellenőrizhető hitelesítő adat kiírásához regisztrálnia kell egy alkalmazást, hogy a hitelesítő vagy bármely más ellenőrizhető hitelesítőadat-tárca engedélyezve legyen a felhasználók bejelentkezni.  

Regisztráljon egy "VC Wallet-alkalmazás" nevű alkalmazást az Azure AD-ben, és szerezzen be egy ügyfél-azonosítót.

1. A regisztráláskor kövesse az alkalmazások [Azure ad](../develop/quickstart-register-app.md) -vel való regisztrálásának utasításait az alábbi értékek használatával.

   - Név: "VC Wallet-alkalmazás"
   - Támogatott fióktípus: csak a szervezeti könyvtárban lévő fiókok
   - Átirányítási URI: vcclient://openid/

   ![alkalmazás regisztrálása](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. Az alkalmazás regisztrálása után jegyezze fel az alkalmazás (ügyfél) AZONOSÍTÓját. Erre az értékre később szüksége lesz.

   ![alkalmazás ügyfél-azonosítója](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Válassza a **végpontok** gombot, és másolja az OpenID Connect metaadat-dokumentum URI-ját. Ezt az információt a következő szakaszban kell megadnia. 

   ![kiállítói végpontok](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-key-vault"></a>A Node-alkalmazás beállítása Key Vaulthoz való hozzáférésre

A felhasználó hitelesítő adatainak kiállítási kérelmének hitelesítéséhez a kiállító webhelye a titkosítási kulcsokat a Azure Key Vaultban használja. Azure Key Vault eléréséhez a webhelynek szüksége van egy ügyfél-AZONOSÍTÓra és egy ügyfél-titkos kulcsra, amelyet a rendszer a Azure Key Vault való hitelesítéshez használhat.

1. A VC Wallet alkalmazás Áttekintés oldalán tekintse meg a **tanúsítványok & Secrets** elemet.
    ![tanúsítványok és titkos kódok](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. Az **ügyfél titkai** szakaszban válassza az **új ügyfél titka** elemet.
    1. Leírás hozzáadása, például "Node VC-ügyfél titka"
    1. Lejárati idő: egy év.
  ![Alkalmazás titka egy éves lejárattal](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. Másolja le a TITKot. Ezekre az információkra szüksége lesz a minta Node-alkalmazás frissítéséhez.

>[!WARNING]
> Van egy lehetősége arra, hogy lemásolja a titkos kulcsot. Ezt követően a titkos kód az egyik módszer. Ne másolja az azonosítót. 

Miután létrehozta az alkalmazást és az ügyfél titkos kulcsát az Azure AD-ben, meg kell adnia az alkalmazás számára a szükséges engedélyeket, hogy végrehajtsa a műveleteket a Key Vault. Az engedélyek módosítása szükséges ahhoz, hogy a webhely hozzáférhessen az ott tárolt titkos kulcsok eléréséhez és használatához.

1. Lépjen Key Vault.
2. Válassza ki az oktatóanyagokhoz használt kulcstartót.
3. **Hozzáférési szabályzatok** kiválasztása a bal oldali navigációs menüben
4. Válassza a **+ hozzáférési házirend hozzáadása** lehetőséget.
5. A **kulcs engedélyei** szakaszban válassza a **beolvasás** lehetőséget, és **Jelentkezzen** be.
6. Válassza ki a **rendszerbiztonsági tag** elemet, és használja az alkalmazás-azonosítót a korábban regisztrált alkalmazás megkereséséhez. Válassza ki.
7. Válassza a **Hozzáadás** lehetőséget.
8. Válassza a **Mentés** lehetőséget.

A Key Vault engedélyekkel és a hozzáférés-vezérléssel kapcsolatos további információkért olvassa el a [Key Vault RBAC útmutatóját](../../key-vault/general/rbac-guide.md)

![Key Vault-engedélyek kiosztása](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>A környezetnek megfelelő módosítások elvégzése

Eddig a minta alkalmazással dolgozunk. Az alkalmazás az [Azure Active Directory B2C](../../active-directory-b2c/overview.md) -t használja, és most áttérünk az Azure ad használatára, ezért néhány módosítást nem csak a környezetnek megfelelően kell módosítania, hanem a korábban nem használt további jogcímeket is támogatnia kell.

1. Másolja a lenti szabályok fájlt, és mentse **modified-expertRules.jsre**. 

    > [!NOTE]
    > **"scope": az "OpenID-profil"** szerepel ebben a szabályban, és nem szerepel a minta alkalmazás szabályait tartalmazó fájlban. A következő szakasz bemutatja, hogyan engedélyezheti a választható jogcímeket a Azure Active Directory bérlőben. 
    
    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
             "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["VerifiedCredentialExpert"]
      }
    }
    ```

2. Nyissa meg a fájlt, és cserélje le a **client_id** és a **konfigurációs** értékeket az előző szakaszban másolt két értékre.

    ![annak a két értéknek a kiemelése, amelyet módosítani kell ennek a lépésnek a részeként](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  Az érték **konfiguráció** az OpenID Connect metaadat-dokumentum URI-ja.

  Mivel a mintakód Azure Active Directory B2C használ, és Azure Active Directoryt használ, a hatókörökön keresztül fel kell vennie a választható jogcímeket, hogy ezeket a jogcímeket fel kell venni az azonosító jogkivonatba a ellenőrizhető hitelesítő adatokba való beíráshoz. 

3. A Azure Portalban nyissa meg a Azure Active Directory.
4. Válassza a **Alkalmazásregisztrációk** lehetőséget.
5. Nyissa meg a korábban létrehozott VC Wallet alkalmazást.
6. Válassza a **jogkivonat-konfiguráció** elemet.
7. Válassza a **+ opcionális jogcím hozzáadása** lehetőséget

     ![a jogkivonat-konfiguráció területen adjon hozzá egy opcionális jogcímet](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. A **token típusa** listából válassza az **azonosító** lehetőséget, és az elérhető jogcímek listájából válassza ki a **given_name** és **family_name**

     ![opcionális jogcímek hozzáadása](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Nyomja meg az **Add** (Hozzáadás) gombot.
10. Ha az alább látható engedélyekkel kapcsolatos figyelmeztetés jelenik meg, jelölje be a jelölőnégyzetet, és válassza a **Hozzáadás** lehetőséget.

     ![választható jogcímek engedélyeinek megadása](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

Ha most megjelenik egy felhasználó a "Bejelentkezés" lehetőséggel, hogy kiadja a hitelesíthető hitelesítő adatait, a VC Wallet alkalmazás a hatókör-paraméterrel beírhatja az adott jogcímeket a ellenőrizhető hitelesítő adatokba.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Hozzon létre egy új virtuális VC-t a szabályokkal és a régi megjelenítési fájllal

1. Töltse fel az új szabályok fájlt a tárolóba
1. A ellenőrizhető hitelesítő adatok lapon hozzon létre egy új, **modifiedCredentialExpert** nevű hitelesítő adatot a régi megjelenítési fájl és az új szabályok fájl (**modified-credentialExpert.json**) használatával.
1. A hitelesítőadat-létrehozási folyamat befejezése után az **Áttekintés** lapon másolja a **probléma hitelesítő adatait tartalmazó URL-címet** , és mentse, mert a következő szakaszban szükség van rá.

## <a name="before-we-continue"></a>A folytatás előtt

A szükséges kód módosításának elvégzése előtt néhány értéket össze kell állítani. Ezeket az értékeket a következő szakaszban fogjuk használni, hogy a minta kód a tárolóban tárolt saját kulcsokat használja. Eddig a következő értékeknek kell szerepelniük.

- Az imént létrehozott hitelesítő adatokból származó **szerződéses URI** (a hitelesítő adatok URL-címének kiadása)
- **Alkalmazás ügyfél-azonosítója** Ezt a Node-alkalmazás regisztrálása során megkaptuk.
- **Ügyfél titka** Ezt korábban hoztuk létre, amikor az alkalmazás hozzáférést kapott a Key vaulthoz.

Néhány további értéket is meg kell kapnia, mielőtt a minta alkalmazásban végre tudnánk változtatni a módosításokat. Most lássuk!

### <a name="verifiable-credentials-settings"></a>Ellenőrizhető hitelesítő adatok beállításai

1. Navigáljon a ellenőrizhető hitelesítő adatok lapra, és válassza a **Beállítások** lehetőséget.  
1. Másolja le a következő értékeket:

    - Bérlő azonosítója 
    - Kiállító azonosítója (a DID)
    - Key Vault (URI)

1. Az aláíró kulcs azonosítója alatt van egy URI, de csak egy részre van szükségünk. Másolja a **issuerSigningKeyION** az alábbi képen látható piros négyszögtel jelölt részből.

   ![bejelentkezési kulcs azonosítója](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>Dokumentum 

1. A különböző [ion Network Explorer](https://identity.foundation/ion/explorer/) megnyitása

2. Illessze be a DID kifejezést a keresősávba.

4. A formázott válaszban keresse meg a **verificationMethod** nevű szakaszt.
5. A "verificationMethod" alatt másolja ki az azonosítót, és címkézze fel kvSigningKeyId
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

Most már mindent meg kell tenni a mintakód változásairól.

- **Kiállító:** app.js a CONST hitelesítő adatok frissítése az új szerződési URI-val
- **Ellenőrző:** app.js frissíti a IssuerDid a kiállító azonosítójával
- A **kiállító és az ellenőrző** a következő értékekkel frissíti a didconfig.jst:


```json=
{
    "azTenantId": "Your tenant ID",
    "azClientId": "Your client ID",
    "azClientSecret": "Your client secret",
    "kvVaultUri": "your keyvault uri",
    "kvSigningKeyId": "The verificationMethod ID from your DID Document",
    "kvRemoteSigningKeyId" : "The snippet of the issuerSigningKeyION we copied ",
    "did": "Your DID"
}
```

>[!IMPORTANT]
>Ez egy bemutató alkalmazás, és általában soha nem szabad közvetlenül megadnia az alkalmazásnak a titkot.


Most már mindent megtesz a saját hitelesítő adatainak a saját kulcsaival való kiválasztásához és ellenőrzéséhez a Azure Active Directory-bérlőben. 

## <a name="issue-and-verify-the-vc"></a>A VC kiadása és ellenőrzése

Kövesse az előző oktatóanyagban ismertetett lépéseket, hogy kiadja az igazolható hitelesítő adatokat, és érvényesítse azt az alkalmazással. Miután sikeresen elvégezte az ellenőrzési folyamatot, most már készen áll a ellenőrizhető hitelesítő adatokkal kapcsolatos ismeretek megismerésére.

1. Nyisson meg egy parancssort, és nyissa meg a kiállító mappát.
2. Futtassa a frissített Node alkalmazást.

    ```terminal
    node app.js
    ```

3. Egy másik parancssor használatával futtassa a ngrok egy URL-cím beállításához a 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Azt is megfigyelheti, hogy ez az alkalmazás vagy webhely kockázatos lehet. Az üzenet a várt időpontban várható, mivel még nem csatoltuk a saját tartományához. A konfigurálásához kövesse a [DNS-kötés](how-to-dnsbind.md) utasításait.

    
4. Nyissa meg a ngrok által generált HTTPS URL-címet.

    ![NGROK továbbítási végpontok](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. A **hitelesítő adatok beolvasása**
6. A hitelesítő ellenőrzi a QR-kódot.
7. **Előfordulhat, hogy az alkalmazás vagy a webhely kockázatos** figyelmeztető üzenetet választ a **speciális** elemre.

  ![Kezdeti figyelmeztetés](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. A kockázatos webhely figyelmeztetése: válassza a **továbblépés egyébként (nem biztonságos) lehetőséget.**

  ![Második figyelmeztetés a kibocsátóról](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. A **hitelesítő adatok hozzáadása** képernyőn figyelje meg, hogy néhány dolog: 
    1. A képernyő felső részén látható egy piros, **nem ellenőrzött** üzenet
    1. A hitelesítő adatok a megjelenítési fájl módosításai alapján testreszabhatók.
    1. A **Bejelentkezés a fiókba** lehetőség az Azure ad bejelentkezési oldalára mutat.
    
   ![hitelesítő adatok hozzáadása képernyő figyelmeztetéssel](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Válassza a **Bejelentkezés a fiókjába** és hitelesítés az Azure ad-bérlő egyik felhasználójának használatával.
11. A **Hozzáadás** gomb sikeres hitelesítése után már nem szürkén jelenik meg. Válassza a **Hozzáadás** lehetőséget.

  ![hitelesítő adatok hozzáadása képernyő a hitelesítés után](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Most kiállítottunk egy ellenőrizhető hitelesítő adatot a bérlőnk használatával, hogy a virtuális áramkört a B2C-bérlőnk használatával is létrehozjuk a hitelesítéshez.

  ![Az Azure AD által kiadott és az Azure B2C-példánnyal hitelesített VC](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>A VC ellenőrzésének tesztelése a minta alkalmazás használatával

Most, hogy kiállítottuk a saját bérlőnk által az Ön Azure AD-beli jogcímeivel ellenőrizhető hitelesítő adatokat, ellenőrizzük, hogy a minta alkalmazásunk használatával.

1. Állítsa le a kiállítói ngrok szolgáltatás futtatását.

    ```cmd
    control-c
    ```

2. Most futtassa a ngrok-t az ellenőrző 8082-as porton.

    ```cmd
    ngrok http 8082
    ```

3. Egy másik terminál ablakban navigáljon az ellenőrző alkalmazáshoz, és futtassa azt hasonlóan a kiállító alkalmazás futtatásához.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Nyissa meg a ngrok URL-címét a böngészőben, és ellenőrizze a QR-kódot a mobileszköz hitelesítő adataival.
5. A mobileszközön válassza az **Engedélyezés lehetőséget** az **új engedély kérése** képernyőn.

   >[!IMPORTANT]
    > Mivel a minta alkalmazás a bejelentési kérelem aláírását is használta, egy figyelmeztetés jelenik meg, hogy ez az alkalmazás vagy webhely kockázatos lehet. Az üzenet a várt időpontban várható, mivel még nem csatoltuk a saját tartományához. A konfigurálásához kövesse a [DNS-kötés](how-to-dnsbind.md) utasításait.
    
   ![új engedély kérése](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Nem ellenőrizte a hitelesítő adatait, és a webhelynek az első és az utolsó nevet kell megjelenítenie az Azure AD felhasználói fiókjából. 

Ezzel befejezte az oktatóanyagot, és hivatalosan ellenőrzött hitelesítő adat-szakértő! A mintául szolgáló alkalmazás mind a kiadási, mind az ellenőrzési, mind a jogcímeket használja, miközben az Azure AD-ből ellenőrizhető hitelesítő adatokba írja a jogcímeket. 

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan hozhat létre [egyéni hitelesítő adatokat](credential-design.md)
- A kiállítói szolgáltatás kommunikációs [példái](issuer-openid.md)