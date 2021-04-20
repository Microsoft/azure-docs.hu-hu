---
title: Oktatóanyag – Ellenőrizhető hitelesítő adatok kiállítása és ellenőrzése az Azure-bérlő használatával (előzetes verzió)
description: Módosítsa az Ellenőrizhető hitelesítőadat-kódmintát, hogy működjön az Azure-bérlővel
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 310c821bf102d267d0b5f77dbf206b896ab2f1c7
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739223"
---
# <a name="tutorial---issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Oktatóanyag – Ellenőrizhető hitelesítő adatok kiállítása és ellenőrzése a bérlő használatával (előzetes verzió)

> [!IMPORTANT]
> Azure Active Directory hitelesítő adatok ellenőrizhetővé tehetők jelenleg nyilvános előzetes verzióban.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Most, hogy beállította az Azure-bérlőt az Ellenőrizhető hitelesítő adatok szolgáltatással, végigveszünk minden olyan lépést, amely ahhoz szükséges, hogy az Azure Active Directory (Azure AD) ellenőrizhető hitelesítő adatokat állítson ki a mintaalkalmazással.

Ebben a cikkben a következőt olvashatja:

> [!div class="checklist"]
> * A mintaalkalmazás regisztrálása az Azure AD-ban
> * Szabályok létrehozása és fájl megjelenítése
> * Szabályok feltöltése és fájlok megjelenítése
> * Állítsa be az Ellenőrizhető hitelesítő adatok kiállítója szolgáltatást az Azure Key Vault
> * Frissítse a mintakódot a bérlő adataival.

Mintakódunk megköveteli, hogy a felhasználók hitelesítést hitelesítsen egy identitásszolgáltatónál, különösen Azure AD B2C, mielőtt kibocsátható lenne az Ellenőrzött hitelesítőadat-szakértő VC. Nem minden ellenőrizhető hitelesítőadat-kiállító igényel hitelesítést a hitelesítő adatok kiállítása előtt.

Az azonosító jogkivonatok hitelesítése lehetővé teszi a felhasználóknak, hogy igazolják, ki is ő, mielőtt megkapják a hitelesítő adataikat. Amikor a felhasználók sikeresen bejelentkeznek, az identitásszolgáltató egy biztonsági jogkivonatot ad vissza, amely a felhasználóval kapcsolatos jogcímeket tartalmazza. A kiállító szolgáltatás ezután ellenőrizhető hitelesítő adatokkal alakítja át ezeket a biztonsági jogkivonatokat és azok jogcímeit. Az ellenőrizhető hitelesítő adat a kiállító DID-jával van aláírva.

Minden olyan identitásszolgáltató támogatott, OpenID Connect protokollt. A támogatott identitásszolgáltatók közé tartozik például a [Azure Active Directory](../fundamentals/active-directory-whatis.md)és a [Azure AD B2C.](../../active-directory-b2c/overview.md) Ebben az oktatóanyagban az AAD-t használjuk.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már [](enable-your-tenant-verifiable-credentials.md) befejezte az előző oktatóanyag lépéseit, és hozzáfér a használt környezethez.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Alkalmazás regisztrálása a felhasználók bejelentkezésének engedélyezéséhez DID-tárcak számára

Az ellenőrizhető hitelesítő adatok kiállításához regisztrálnia kell egy alkalmazást, hogy az Authenticator vagy bármely más ellenőrizhető hitelesítőadat-pénztárca bejelentkeztethet-e felhasználókat.  

Regisztráljon egy "VC-tárcaalkalmazás" nevű alkalmazást az Azure AD-ben, és szerezze be az ügyfél-azonosítót.

1. A regisztráció során kövesse az alkalmazások [Azure AD-ban](../develop/quickstart-register-app.md) való regisztrálásával és az alábbi értékekkel való regisztrálásra vonatkozó utasításokat.

   - Név: "VC-tárcaalkalmazás"
   - Támogatott fióktípusok: Csak az ebben a szervezeti címtárban található fiókok
   - Átirányítási URI: vcclient://openid/

   ![alkalmazás regisztrálása](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. Az alkalmazás regisztrálása után írja fel az alkalmazás (ügyfél) azonosítóját. Erre az értékre később szüksége lesz.

   ![alkalmazás ügyfél-azonosítója](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Válassza **a Végpontok gombot,** és másolja OpenID Connect metaadat-dokumentum URI-ját. Erre az információra szüksége lesz a következő szakaszban. 

   ![kiállító végpontjai](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-azure-key-vault"></a>A Node-alkalmazás beállítása hozzáféréssel Azure Key Vault

A felhasználó hitelesítőadat-kiállítási kérelmének hitelesítéséhez a kiállító webhelye a titkosítási kulcsokat használja a Azure Key Vault. A Azure Key Vault eléréséhez a webhelyének szüksége van egy ügyfél-azonosítóra és titkos ügyfélk titkos kódra, amely felhasználható a Azure Key Vault.

1. A VC-tárca alkalmazás áttekintő oldalának megtekintésekor válassza a **Tanúsítványok és & lehetőséget.**
    ![tanúsítványok és titkos kulcsok](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. Az Ügyfél **titkos kulcsok szakaszában** válassza az Új titkos **ügyfélk titkos ügyfélkomóta lehetőséget.**
    1. Adjon hozzá egy leírást, például: "Node VC titkos ügyféltitk"
    1. Lejárat: egy év alatt.
  ![Alkalmazás titkos kulcsának lejárata egy év után](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. Másolja le a TITKOS GOMBRA. Erre az információra szüksége lesz a node-mintaalkalmazás frissítéséhez.

>[!WARNING]
> Csak egy lehetősége van a titkos adatok másolásra. Ezt követően a titkos ok egyike kivonatolva lesz. Ne másolja az azonosítót. 

Miután létrehozta az alkalmazást és a titkos ügyféloldali titkos ügyfélszámítógépet az Azure AD-ban, meg kell adnunk az alkalmazásnak a műveletek végrehajtásához szükséges engedélyeket a Key Vault. Ezeknek az engedélyeknek a módosításai szükségesek ahhoz, hogy a webhely hozzáférjen az ott tárolt titkos kulcsokhoz, és használva azt használni.

1. Ugrás a Key Vault.
2. Válassza ki az oktatóanyagokhoz használt kulcstartót.
3. A **bal oldali navigációs sávon válassza** a Hozzáférési szabályzatok lehetőséget
4. Válassza **a +Hozzáférési szabályzat hozzáadása lehetőséget.**
5. A **Kulcsengedélyek szakaszban** válassza a **Get**, és **Sign lehetőséget.**
6. Válassza **a Principal** (Rendszerbiztonsági tag) lehetőséget, és az alkalmazásazonosítóval keresse meg a korábban regisztrált alkalmazást. Válassza ki.
7. Válassza a **Hozzáadás** lehetőséget.
8. Válassza a **SAVE (MENTÉS) lehetőséget.**

Az engedélyek és hozzáférés-vezérlés Key Vault további információért olvassa el a [Key Vault RBAC-útmutatóját](../../key-vault/general/rbac-guide.md)

![kulcstartó-engedélyek hozzárendelése](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>A környezetnek megfelelő módosítások

Eddig a mintaalkalmazással dolgoztunk. Az alkalmazás [Azure Active Directory B2C,](../../active-directory-b2c/overview.md) és most átváltunk az Azure AD használatára, ezért végre kell hoznunk néhány módosítást, hogy ne csak a környezetének feleltsünk meg, hanem további jogcímeket is támogatnunk kell, amelyek korábban nem voltak használatban.

1. Másolja ki az alábbi szabályfájlt, és mentse **modified-expertRules.jsa következőn:**. 

    > [!NOTE]
    > **"scope": az "openid profile" (openid profil)** szerepel ebben a szabályfájlban, és nem szerepel a mintaalkalmazás Rules (Szabályok) fájljában. A következő szakasz azt ismerteti, hogyan engedélyezheti a választható jogcímeket a Azure Active Directory bérlőben. 
    
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

2. Nyissa meg a fájlt, és cserélje **client_id** és **konfigurációs** értékeit az előző szakaszban kimásott két értékre.

    ![a lépés részeként módosítható két érték kiemelése](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  A **Konfiguráció érték** az OpenID Connect metaadat-dokumentum URI-jának értéke.

  Mivel a mintakód az Azure Active Directory B2C-t használja, és Azure Active Directory-t használ, nem kötelező jogcímeket kell hozzáadnunk hatókörök segítségével, hogy ezeket a jogcímeket az azonosító jogkivonatba írják az Ellenőrizhető hitelesítő adatokba. 

3. A Azure Portal nyissa meg Azure Active Directory.
4. Válassza a **Alkalmazásregisztrációk** lehetőséget.
5. Nyissa meg a korábban létrehozott VC-tárcaalkalmazást.
6. Válassza a **Jogkivonat-konfiguráció lehetőséget.**
7. Válassza **a + Választható jogcím hozzáadása lehetőséget**

     ![a tokenkonfiguráció alatt adjon hozzá egy opcionális jogcímet](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. A **Token type (Jogkivonat típusa)** listából válassza az **ID** (Azonosító) lehetőséget, majd az elérhető jogcímek listájából válassza a **given_name** és **family_name**

     ![választható jogcímek hozzáadása](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Nyomja meg az **Add** (Hozzáadás) gombot.
10. Ha az engedélyekkel kapcsolatos figyelmeztetés jelenik meg az alább látható módon, jelölje be a jelölőnégyzetet, és válassza a **Hozzáadás lehetőséget.**

     ![engedélyek hozzáadása választható jogcímek számára](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

Mostantól, ha egy felhasználónál jelennek meg az "bejelentkezés" az ellenőrizhető hitelesítő adatok kibocsátása előtt, a VC-tárcaalkalmazás tudni fogja, hogy a hatókör paraméterén keresztül megadott jogcímeket az Ellenőrizhető hitelesítő adatokba kell írni.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Új VC létrehozása ezzel a szabályfájllal és a régi megjelenítési fájllal

1. Az új szabályfájl feltöltése a tárolóba
1. Az ellenőrizhető hitelesítő adatok oldalon hozzon létre egy **új, modifiedCredentialExpert** nevű hitelesítő adatokat a régi megjelenítési fájl és az új szabályfájl (modified-credentialExpert.js **található).**
1. Miután a hitelesítő adatok létrehozásának  folyamata befejeződött az Áttekintés lapon, másolja ki a Probléma hitelesítő adatainak **URL-címét,** és mentse, mert a következő szakaszban szükség lesz rá.

## <a name="before-we-continue"></a>A folytatás előtt

Össze kell hoznunk néhány értéket a szükséges kódváltozások előtt. Ezeket az értékeket a következő szakaszban arra használjuk, hogy a mintakód a tárolóban tárolt saját kulcsait használja. Eddig az alábbi értékeknek kell készen állnunk.

- **Szerződés URI-je** az előbb létrehozott hitelesítő adatból (Hitelesítő adatok url-címének kiállítása)
- **Alkalmazás ügyfél-azonosítója** Ezt a Node-alkalmazás regisztrálva lett.
- **Titkos ügyfélk titkos ügyfél** Ezt korábban akkor hoztuk létre, amikor hozzáférést adtunk az alkalmazásnak a Key Vaulthoz.

Még néhány értéket be kell szereznünk ahhoz, hogy a módosításokat még egyszer el tudjuk végrehozni a mintaalkalmazásban. Most pedig ássunk neki!

### <a name="verifiable-credentials-settings"></a>Hitelesítő adatok ellenőrizhető beállításai

1. Lépjen az Ellenőrizhető hitelesítő adatok oldalra, és válassza a Beállítások **lehetőséget.**  
1. Másolja le a következő értékeket:

    - Bérlőazonosító 
    - Kiállító azonosítója (a saját DID-azonosítója)
    - Key Vault (URI)

1. Az Aláírókulcs-azonosító alatt van egy URI, de csak egy részre van szükségünk. Másolja ki az alábbi képen látható piros téglalap által kiemelt **issuerSigningKeyION** részt.

   ![bejelentkezési kulcs azonosítója](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>DID dokumentum

1. Nyissa meg a [DIF ION-Hálózattallózó](https://identity.foundation/ion/explorer/)

2. Illessze be a DID et a keresősávba.

4. A formázott válaszban keresse meg az **verificationMethod nevű szakaszt**
5. Az "verificationMethod" alatt másolja ki az azonosítót, és címkézze fel kvSigningKeyId-ként
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

Most már minden megvan, amire szükségünk van a mintakódban való módosításhoz.

- **Kiállító:** app.js hitelesítő adatainak frissítése az új szerződés URI-jával
- **Ellenőrző:** app.js issuerDid frissítése a kiállító azonosítójával
- **A Issuer és a Verifier** didconfig.jsa következő értékekkel frissítik a tanúsítványt:


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
>Ez egy bemutató alkalmazás, és általában soha ne adja meg közvetlenül az alkalmazásnak a titkos secret-et.


Most már minden megvan, ami kiadható és ellenőrizhető a saját ellenőrizhető hitelesítő Azure Active Directory a saját kulcsokkal. 

## <a name="issue-and-verify-the-vc"></a>A VC kiállítása és ellenőrzése

Kövesse az előző oktatóanyagban is követett lépéseket az ellenőrizhető hitelesítő adatok kiállításához és az alkalmazással való ellenőrzéséhez. Miután sikeresen befejezte az ellenőrzési folyamatot, folytathatja az ellenőrizhető hitelesítő adatokkal kapcsolatos információkkal.

1. Nyisson meg egy parancssort, és nyissa meg az issuer mappát.
2. Futtassa a frissített Node-alkalmazást.

    ```terminal
    node app.js
    ```

3. Egy másik parancssor használatával futtassa az ngrok parancsot egy URL-cím beállítására a 8081-es címen

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Azt is észreveheti, hogy az alkalmazás vagy webhely kockázatos lehet. Az üzenet jelenleg várható, mivel a DID-t még nem kapcsoltuk össze a tartománnyal. A [konfiguráláshoz kövesse a](how-to-dnsbind.md) DNS-kötésre vonatkozó utasításokat.

    
4. Nyissa meg az ngrok által létrehozott HTTPS URL-címet.

    ![NGROK-továbbítási végpontok](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. Válassza a **GET CREDENTIAL (HITELESÍTŐ ADATOK LEKÉRTE**
6. Az Authenticatorban vizsgálja meg a QR-kódot.
7. Az Ez az alkalmazás vagy webhely kockázatos figyelmeztető üzenet **lehet,** válassza a **Speciális lehetőséget.**

  ![Kezdeti figyelmeztetés](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. A kockázatos webhely figyelmeztetésnél válassza a **Folytatás mindenképp (nem biztonságos) lehetőséget**

  ![Második figyelmeztetés a kiállítóról](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. A Hitelesítő **adatok hozzáadása képernyőn** figyelje meg a következő néhány dolgot: 
    1. A képernyő tetején egy piros Nem ellenőrzött üzenet **jelenik** meg
    1. A hitelesítő adatok a megjelenítési fájlban végrehajtott módosítások alapján szabhatók testre.
    1. A **Bejelentkezés a fiókba** lehetőség az Azure AD bejelentkezési oldalára mutat.
    
   ![hitelesítő adatok hozzáadása képernyő figyelmeztetéssel](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Válassza **a Bejelentkezés a fiókba lehetőséget,** és hitelesítsen egy Azure AD-bérlőben egy felhasználóval.
11. A sikeres hitelesítés után a **Hozzáadás** gomb már nem szürkül. Válassza a **Hozzáadás lehetőséget.**

  ![hitelesítő adatok hozzáadása képernyő a hitelesítés után](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Most kiadtunk egy ellenőrizhető hitelesítő adatokat a bérlő használatával a vc létrehozásához, miközben továbbra is az eredeti B2C-bérlőt használjuk hitelesítésre.

  ![Az Azure AD által kiadott és az Azure B2C-példány által hitelesített vc](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>A VC tesztelése a mintaalkalmazással

Most, hogy kiadtunk egy, az Azure AD-tól származó jogcímeket tartalmazó, ellenőrizhető hitelesítő adatokat a saját bérlőjéhez, ellenőrizzük a mintaalkalmazással.

1. Állítsa le a kiállító ngrok szolgáltatás futtatását.

    ```cmd
    control-c
    ```

2. Most futtassa az ngrok-t a 8082-es ellenőrzőporttal.

    ```cmd
    ngrok http 8082
    ```

3. Egy másik terminálablakban keresse meg a hitelesítő alkalmazást, és futtassa a kiállító alkalmazás futtatásához hasonlóan.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Nyissa meg az ngrok URL-címet a böngészőben, és vizsgálja meg a QR-kódot a mobileszköz Authenticator eszközén.
5. A mobileszközön válassza **az** Új engedélykérés képernyőn az Allow (Engedélyezése) lehetőséget. 

   >[!IMPORTANT]
    > Mivel a mintaalkalmazás is a DID-t használja a bemutató kérés aláírásához, egy figyelmeztetést fog látni, amely szerint ez az alkalmazás vagy webhely kockázatos lehet. Az üzenet várhatóan most jelenik meg, mivel a DID-t még nem kapcsoltuk össze a tartománnyal. A [konfiguráláshoz kövesse a](how-to-dnsbind.md) DNS-kötésre vonatkozó utasításokat.
    
   ![új engedélykérés](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Sikeresen ellenőrizte hitelesítő adatait, és a webhelyen meg kell jelenni az Azure AD felhasználói fiókjából származó vezeték- és vezetéknév. 

Befejezte az oktatóanyagot, és hivatalosan is hitelesítőadat-szakértőként működik. A mintaalkalmazás a DID-t használja a kiállításhoz és ellenőrzéshez, miközben jogcímeket ír egy ellenőrizhető hitelesítő adatba az Azure AD-ból. 

## <a name="next-steps"></a>Következő lépések

- Útmutató egyéni hitelesítő [adatok létrehozásához](credential-design.md)
- Példák a kiállító szolgáltatás kommunikációs [példáira](issuer-openid.md)
