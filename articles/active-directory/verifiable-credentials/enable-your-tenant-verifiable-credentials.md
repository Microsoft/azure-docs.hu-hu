---
title: Oktatóanyag – a Azure Active Directory konfigurálása ellenőrizhető hitelesítő adatok kibocsátására (előzetes verzió)
description: Ebben az oktatóanyagban felépíti a szükséges környezetet a bérlőben ellenőrizhető hitelesítő adatok üzembe helyezéséhez
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: cd39f6c484ebe116918611bb1d543c1919a3cb0a
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222945"
---
# <a name="tutorial---configure-your-azure-active-directory-to-issue-verifiable-credentials-preview"></a>Oktatóanyag – a Azure Active Directory konfigurálása ellenőrizhető hitelesítő adatok kibocsátására (előzetes verzió)

Ebben az oktatóanyagban az első [lépéseket](get-started-verifiable-credentials.md) ismertető cikk részeként dolgozunk, és saját [decentralizált azonosítóval](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (DID) állíthatja be a Azure Active Directoryt (Azure ad). A decentralizált azonosító használatával ellenőrizhető hitelesítő adatokat adhat ki a minta alkalmazás és a kiállító használatával; Ebben az oktatóanyagban azonban továbbra is a minta Azure B2C-bérlőt használjuk a hitelesítéshez.  A következő oktatóanyagban további lépéseket teszünk az Azure AD-vel való együttműködésre konfigurált alkalmazás beszerzéséhez.

> [!IMPORTANT]
> Azure Active Directory ellenőrizhető hitelesítő adatok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A cikk tartalma:

> [!div class="checklist"]
> * Hozza létre a szükséges szolgáltatásokat az Azure AD-ben ellenőrizhető hitelesítő adatok bevezetéséhez 
> * Létrehozta a
> * Testre szabjuk a szabályokat és a megjelenített fájlokat
> * Ellenőrizhető hitelesítő adatok konfigurálása az Azure AD-ben.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt sikeresen elvégezte az oktatóanyagot, először a következőket kell tennie:

- Fejezze be az első [lépéseket](get-started-verifiable-credentials.md).
- Rendelkeznie kell aktív előfizetéssel rendelkező Azure-fiókkal. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure AD P2 [licenccel](https://azure.microsoft.com/pricing/details/active-directory/). Ha nem rendelkezik ilyennel, kövesse az [ingyenes fejlesztői fiók létrehozása című témakört](how-to-create-a-free-developer-account.md) .
- [Azure Key Vault](../../key-vault/general/overview.md) egy példánya, ahol a kulcsok és a titkos kódok létrehozásához szükséges jogosultságokkal rendelkezik.

## <a name="azure-active-directory"></a>Azure Active Directory

Mielőtt elkezdenénk, szükségünk van egy Azure AD-bérlőre. Ha a bérlő engedélyezve van a ellenőrizhető hitelesítő adatokhoz, akkor a rendszer egy decentralizált azonosítót (DID) rendel hozzá, és engedélyezte a kiállítói szolgáltatásnak a ellenőrizhető hitelesítő adatok kiadását. Az Ön által kiállított ellenőrizhető hitelesítő adatokat a bérlő és a hozzá tartozó tanúsítvány adja ki. A DID a ellenőrizhető hitelesítő adatok ellenőrzésekor is használatos.
Ha most létrehozott egy Azure-előfizetést, a bérlőt nem kell a felhasználói fiókokkal feltöltenie, de a későbbi oktatóanyagok végrehajtásához legalább egy felhasználói teszt fiókra van szükség.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A ellenőrizhető hitelesítő adatokkal végzett munka során a bérlő által a ellenőrizhető hitelesítő adatok digitális aláírására használt titkosítási kulcsok felügyeletét és felügyeletét végezheti el. A hitelesítő adatok kibocsátásához és ellenőrzéséhez meg kell adnia az Azure AD számára a Azure Key Vault saját példányának elérését.

1. A Azure Portal menüben vagy a **Kezdőlap** lapon válassza az **erőforrás létrehozása** lehetőséget.
2. A keresőmezőbe írja be a **Key Vault** kifejezést.
3. Az eredmények listájában válassza a **Key Vault** lehetőséget.
4. A Key Vault szakaszban kattintson a **Létrehozás** gombra.
5. A **Kulcstartó létrehozása** szakaszban adja meg a következő információkat:
    - **Előfizetés**: Válassza ki az előfizetést.
    - Az **erőforráscsoport** területen válassza az **új létrehozása** elemet, és adjon meg egy erőforráscsoport-nevet (például **VC-Resource-Group**). Ugyanazt az erőforráscsoport-nevet használjuk több cikk között.
    - **Név**: Egy egyedi nevet kell megadnia. A **woodgroup-VC-kV-** ot használjuk, így a saját egyedi nevére cseréljük ezt az értéket.
    - A **Hely** legördülő menüből válassza ki a helyet.
    - A többi beállítást hagyja az alapértelmezett értéken.
6. A fenti információk megadása után válassza a **hozzáférési házirend** elemet.

    ![Key Vault-oldal létrehozása](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

7. A hozzáférési szabályzat képernyőn válassza a **hozzáférési házirend hozzáadása** lehetőséget.

    >[!NOTE]
    > Alapértelmezés szerint a Key vaultot létrehozó fiók az egyetlen, amely hozzáféréssel rendelkezik. A ellenőrizhető hitelesítőadat-szolgáltatásnak hozzáféréssel kell rendelkeznie a Key vaulthoz. A Key vaultnak rendelkeznie kell egy hozzáférési házirenddel, amely lehetővé teszi, hogy a rendszergazda **kulcsokat hozzon létre**, a kulcsokat **törölheti** , ha letiltják, majd **aláírva** hozza létre a tartomány-kötést a ellenőrizhető hitelesítő adatokhoz. Ha ugyanazt a fiókot használja, a tesztelés során ügyeljen arra, hogy az alapértelmezett házirendet úgy módosítsa, hogy a fiók **aláírását** is megadja a tár létrehozói számára biztosított alapértelmezett engedélyek mellett.

8. A felhasználói rendszergazda számára győződjön meg arról, hogy a kulcs engedélyei szakaszban engedélyezve van a **Létrehozás**, a **Törlés** és az **aláírás** . Alapértelmezés szerint a létrehozás és törlés lehetőség már engedélyezve van, és a bejelentkezés csak az egyetlen kulcsfontosságú engedély, amelyet frissíteni kell. 

    ![Engedélyek Key Vault](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

9. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
10. Válassza a **Létrehozás** lehetőséget.
11. Nyissa meg a tárolót, és jegyezze fel a tár nevét és az URI-t.

Jegyezze fel az alábbi két tulajdonságot:

- Tár **neve**: a példában az érték neve **Woodgrove-VC-kV**. Ezt a nevet más lépésekhez használhatja.
- Tároló **URI-ja**: a példában ez az érték https://woodgrove-vc-kv.vault.azure.net/ . A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

>[!NOTE]
> Minden Key Vault-tranzakció további Azure-előfizetési költségeket eredményez. További részletekért tekintse át a [Key Vault díjszabási oldalát](https://azure.microsoft.com/pricing/details/key-vault/) .

>[!IMPORTANT]
> A Azure Active Directory ellenőrizhető hitelesítő adatok előzetes verziójában a tárolóban létrehozott kulcsok és titkok nem módosíthatók a létrehozásuk után. A kulcsok és titkos kódok törlése, letiltása vagy frissítése érvényteleníti a kiállított hitelesítő adatokat. Az előzetes verzióban ne módosítsa a kulcsokat és a titkos kódokat.

## <a name="create-a-modified-rules-and-display-file"></a>Módosított szabályok létrehozása és fájl megjelenítése

Ebben a szakaszban a minta kiállítói alkalmazás szabályait és a fájlok megjelenítését használjuk, és kis mértékben módosítjuk őket a bérlő első ellenőrizhető hitelesítő adatainak létrehozásához.

1. Másolja a szabályokat, és jelenítse meg a JSON-fájlokat egy ideiglenes mappába, és nevezze át őket **MyFirstVC-display.jsbe** -és **MyFirstVC-rules.js** . Mindkét fájl megtalálható a **kiállító \ issuer_config**

   ![a fájlok megjelenítése a minta alkalmazás könyvtárának részeként](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![ideiglenes mappában lévő fájlok megjelenítése és szabályozása](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

2. Nyissa meg a MyFirstVC-rules.jsfájlt a Kódszerkesztő alkalmazásban. 

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

Most változtassa meg a Type (típus) mezőt "MyFirstVC" értékre. 

  ```json
   "type": ["MyFirstVC"]
  
  ```

Mentse ezt a módosítást.

 >[!NOTE]
   > Az oktatóanyag ezen pontján nem módosítjuk a **"Configuration"** vagy a **"client_id"** változót. Továbbra is az első [lépések](get-started-verifiable-credentials.md)során használt Microsoft B2C-bérlőt használjuk. Az Azure AD-t a következő oktatóanyagban fogjuk használni.

3. Nyissa meg a MyFirstVC-display.jsfájlt a Kódszerkesztő alkalmazásban.

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

Lehetővé teszi, hogy néhány módosítást hajtson végre, így ez a ellenőrizhető hitelesítő adat láthatóan eltér a mintakód verziójától. 
    
```json
     "card": {
        "title": "My First VC",
        "issuedBy": "Your Issuer Name",
        "backgroundColor": "#ffffff",
        "textColor": "#000000",
```

Mentse ezeket a módosításokat.
## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az első ellenőrizhető hitelesítő adatok létrehozása előtt létre kell hozni egy Blob Storage tárolót, amely képes tárolni a konfigurációs és a szabályok fájljait.

1. Hozzon létre egy Storage-fiókot az alább látható beállítások használatával. A részletes lépésekért tekintse meg a [Storage-fiók létrehozása](../../storage/common/storage-account-create.md?tabs=azure-portal) című cikket.

   - **Előfizetés:** Válassza ki az oktatóanyaghoz használt előfizetést.
   - **Erőforráscsoport:** Válassza ki ugyanazt az erőforráscsoportot, amelyet a korábbi oktatóanyagokban használt (**VC-Resource-Group**).
   - **Név:**  Egy egyedi név.
   - **Hely:** (USA) USA keleti régiója.
   - **Teljesítmény:** Standard.
   - **Fiók típusa:** Storage v2.
   - **Replikáció:** Helyileg redundáns.
 
   ![Új tárfiók létrehozása](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

2. A Storage-fiók létrehozása után létre kell hoznia egy tárolót. Válassza ki a **tárolókat** a **blob Storage** területen, és hozzon létre egy tárolót az alább megadott értékek alapján:

    - **Név:** VC – tároló
    - **Nyilvános hozzáférési szint:** Privát (nincs névtelen hozzáférés)

   ![Tároló létrehozása](media/enable-your-tenant-verifiable-credentials/new-container.png)

3. Most válassza ki az új tárolót, és töltse fel az új szabályokat, és jelenítse meg a korábban létrehozott és **MyFirstVC-rules.js** **MyFirstVC-display.js** fájlokat.

   ![szabályok feltöltése fájl](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-blob-role"></a>BLOB-szerepkör kiosztása

A hitelesítő adatok létrehozása előtt először meg kell adni a bejelentkezett felhasználót a megfelelő szerepkör-hozzárendeléssel, hogy hozzáférhessenek a fájlokhoz a Storage blobban.

1. Navigáljon a **Storage**  >  **tárolóhoz**.
2. A bal oldali menüben válassza a **Access Control (iam)** lehetőséget.
3. Válassza a **szerepkör-hozzárendelések** lehetőséget.
4. Válassza a **Hozzáadás** lehetőséget.
5. A **szerepkör** szakaszban válassza a **Storage blob-Adatolvasó** elemet.
6. **A hozzáférés társítása** elemnél válassza a **felhasználó, csoport vagy szolgáltatás elv** lehetőséget.
7. A **Select (kiválasztás**) elemnél válassza ki azt a fiókot, amelyet a lépések végrehajtásához használ.
8. A szerepkör-hozzárendelés befejezéséhez válassza a **Mentés** lehetőséget.


   ![Szerepkör-hozzárendelés hozzáadása](media/enable-your-tenant-verifiable-credentials/role-assignment.png)

  >[!IMPORTANT]
  >Alapértelmezés szerint a Container Creators beolvassa a **tulajdonosi** szerepkört. A **tulajdonosi** szerepkör önmagában nem elegendő. A fióknak szüksége van a **Storage blob Adatolvasó** szerepkörre. További információ: [a Azure Portal használata Azure-szerepkörök hozzárendeléséhez a blob-és üzenetsor-adatokhoz való hozzáféréshez](../../storage/common/storage-auth-aad-rbac-portal.md)

## <a name="set-up-verifiable-credentials-preview"></a>Ellenőrizhető hitelesítő adatok beállítása (előzetes verzió)

Most végre kell hajtania az utolsó lépést, hogy beállítsa a bérlőt a ellenőrizhető hitelesítő adatokhoz.

1. A Azure Portal keresse meg a **ellenőrizhető hitelesítő adatokat**.
2. Válassza a **ellenőrizhető hitelesítő adatok (előzetes verzió)** lehetőséget.
3. Az első **lépések** kiválasztása
4. Be kell állítania a szervezetét, és meg kell adnia a szervezet nevét, tartományát és kulcstartóját. Nézzük meg az egyes értékeket.

      - **szervezet neve**: Ez a név a hitelesíthető hitelesítőadat-szolgáltatáson belüli üzleti hivatkozás. Ez az érték nem az ügyfél felé irányul.
      - **Tartomány:** A megadott tartomány hozzá van adva egy szolgáltatási végponthoz a DID-dokumentumban. [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) és más pénztárcák ezeket az információkat használják annak ellenőrzéséhez, hogy a DID a [tartományhoz van-e kapcsolva](how-to-dnsbind.md). Ha a mobiltárca igazolni tudja a DID-t, egy ellenőrzött szimbólum jelenik meg. Ha a mobiltárca nem tudja ellenőrizni a DID-t, arról tájékoztatja a felhasználót, hogy a hitelesítő adatokat egy olyan szervezet adta ki, amelyet nem sikerült érvényesíteni. A tartomány azt köti össze, hogy a felhasználó a vállalata tudomására jutott.
      - **Key Vault:** Adja meg a korábban létrehozott Key Vault nevét.
 
   >[!IMPORTANT]
   > A tartomány nem lehet átirányítás, ellenkező esetben a DID és a tartomány nem csatolható. Ügyeljen arra, hogy a https://www.domain.com formátumot használja.
    
5. Válassza **a Mentés és a hitelesítő adatok létrehozása** lehetőséget.

    ![a szervezeti identitás beállítása](media/enable-your-tenant-verifiable-credentials/save-create.png)

Gratulálunk, a bérlője már engedélyezve van a ellenőrizhető hitelesítő adatok előzetes verziójában!

## <a name="create-your-vc-in-the-portal"></a>A virtuális merevlemez létrehozása a portálon

Az előző lépés az **új hitelesítő adatok létrehozása** oldalon marad. 

   ![ellenőrizhető hitelesítő adatok – első lépések](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. A hitelesítő adatok neve területen adja meg a **MyFirstVC** nevet. A rendszer ezt a nevet használja a portálon a ellenőrizhető hitelesítő adatok azonosítására, és része a ellenőrizhető hitelesítő adatokra vonatkozó szerződésnek.
2. A fájl megjelenítése szakaszban válassza a **megjelenítési fájl konfigurálása** lehetőséget.
3. A **Storage-fiókok** szakaszban válassza a **woodgrovevcstorage** lehetőséget.
4. Az elérhető tárolók listájából válassza a **VC-Container** elemet.
5. Válassza ki a korábban létrehozott fájl **MyFirstVC-display.js** .
6. Az **új hitelesítő adatok létrehozása** a **szabályok fájl** szakaszban válassza a **szabályok konfigurálása** elemet.
7. A **Storage-fiókok** szakaszban válassza a **woodgrovecstorage** lehetőséget.
8. Válassza a **VC-Container** elemet.
9. **MyFirstVC-rules.js** kiválasztása
10. Az **új hitelesítő adatok létrehozása** képernyőn válassza a **Létrehozás** lehetőséget.

   ![új hitelesítő adat létrehozása](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>Hitelesítő adatok URL-címe

Most, hogy új hitelesítő adatokkal rendelkezik, másolja a hitelesítő adatok URL-címét.

   ![A probléma hitelesítő adatának URL-címe](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>A hitelesítő adatok URL-címe a szabályok és a megjelenítendő fájlok kombinációja. Ez az URL-cím, amelyet a hitelesítő kiértékel, mielőtt megjeleníti a felhasználó által ellenőrizhető hitelesítő adatok kiállítási követelményeit.  

## <a name="update-the-sample-app"></a>A minta alkalmazás frissítése

Most végezzük el a minta-alkalmazás kiállítói kódjának módosítását az igazolható hitelesítő adatok URL-címével való frissítéshez. Ez lehetővé teszi, hogy a saját bérlője használatával hitelesítő adatokat bocsásson ki.

1. Lépjen arra a mappára, ahová elhelyezte a minta alkalmazás fájljait.
2. Nyissa meg a kiállítói mappát, majd nyissa meg app.js a Visual Studio Code-ban.
3. Frissítse az állandó hitelesítő adatokat az új hitelesítő adatok URL-címével, és állítsa a credentialType állandót a "MyFirstVC" értékre, és mentse a fájlt.

    ![az érintett területeket kiemelő Visual Studio Code képe](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

4. Nyisson meg egy parancssort, és nyissa meg a kiállító mappát.
5. Futtassa a frissített Node alkalmazást.

    ```terminal
    node app.js
    ```

6. Egy másik parancssor használatával futtassa a ngrok egy URL-cím beállításához a 8081

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Azt is megfigyelheti, hogy ez az alkalmazás vagy webhely kockázatos lehet. Az üzenet a várt időpontban várható, mivel még nem csatoltuk a saját tartományához. A konfigurálásához kövesse a [DNS-kötés](how-to-dnsbind.md) utasításait.

    
7. Nyissa meg a ngrok által generált HTTPS URL-címet.

    ![NGROK továbbítási végpontok](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

8. A **hitelesítő adatok beolvasása**
9. A hitelesítő ellenőrzi a QR-kódot.
10. **Előfordulhat, hogy az alkalmazás vagy a webhely kockázatos** figyelmeztető üzenetet választ a **speciális** elemre.

  ![Kezdeti figyelmeztetés](media/enable-your-tenant-verifiable-credentials/site-warning.png)

11. A kockázatos webhely figyelmeztetése: válassza a **továbblépés egyébként (nem biztonságos) lehetőséget.**

  ![Második figyelmeztetés a kibocsátóról](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


12. A **hitelesítő adatok hozzáadása** képernyőn figyelje meg, hogy néhány dolog: 
    1. A képernyő felső részén látható egy piros, **nem ellenőrzött** üzenet
    1. A hitelesítő adatok a megjelenítési fájl módosításai alapján testreszabhatók.
    1. A **Bejelentkezés a fiókba** lehetőség a **didplayground.b2clogin.com** mutat.
    
   ![hitelesítő adatok hozzáadása képernyő figyelmeztetéssel](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

13. Válassza a **Bejelentkezés a fiókba** lehetőséget, és hitelesítse magát az [első lépések oktatóanyagban](get-started-verifiable-credentials.md)megadott hitelesítő adatok használatával.
14. A **Hozzáadás** gomb sikeres hitelesítése után már nem szürkén jelenik meg. Válassza a **Hozzáadás** lehetőséget.

  ![hitelesítő adatok hozzáadása képernyő a hitelesítés után](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Most kiállítottunk egy ellenőrizhető hitelesítő adatot a bérlőnk használatával, hogy a virtuális áramkört a B2C-bérlőnk használatával is létrehozjuk a hitelesítéshez.

  ![Az Azure AD által kiadott és az Azure B2C-példánnyal hitelesített VC](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>A VC ellenőrzésének tesztelése a minta alkalmazás használatával

Most, hogy kiállítottuk az igazolható hitelesítő adatokat a saját bérlőről, ellenőrizze, hogy a minta alkalmazás használatával.

>[!IMPORTANT]
> Teszteléskor használja ugyanazt az e-mail-címet és jelszót, amelyet az [első lépések](get-started-verifiable-credentials.md) című cikkben használt. Amíg a bérlő kiadja a VC-t, a bemenet a Microsoft B2C-bérlő által kiadott azonosító jogkivonatból származik. A második oktatóanyagban a jogkivonat-kiadást a bérlőre váltjuk át

1. Nyissa meg a **beállításokat** a Azure Portal ellenőrizhető hitelesítő adatok paneljén. Másolja a decentralizált azonosítót (DID).

   ![a DID másolása](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

2. Most nyissa meg az ellenőrző mappa részét a minta alkalmazás fájljai közül. Az ellenőrző mintakód app.js fájlját frissíteni kell, és a következő módosításokat kell végeznie:

    - **hitelesítő adat**: váltás a hitelesítő adatok URL-címére
    - **credentialType**: "MyFirstVC"
    - **issuerDid**: másolja ezt az értéket Azure Portal>ellenőrizhető hitelesítő adatok (előzetes verzió) >beállítások>decentralizált azonosító (DID)
    
   ![az állandó issuerDid frissítése a kiállító azonosítójának megfelelően](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

3. Állítsa le a kiállítói ngrok szolgáltatás futtatását.

    ```cmd
    control-c
    ```

4. Most futtassa a ngrok-t az ellenőrző 8082-as porton.

    ```cmd
    ngrok http 8082
    ```

5. Egy másik terminál ablakban navigáljon az ellenőrző alkalmazáshoz, és futtassa azt hasonlóan a kiállító alkalmazás futtatásához.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

6. Nyissa meg a ngrok URL-címét a böngészőben, és ellenőrizze a QR-kódot a mobileszköz hitelesítő adataival.
7. A mobileszközön válassza az **Engedélyezés lehetőséget** az **új engedély kérése** képernyőn.

    >[!NOTE]
    > A virtuális VC aláírása még mindig a Microsoft B2C. A hitelesítőnek még a Microsoft minta alkalmazás bérlője is volt. Mivel a Microsoft a saját tartományhoz kapcsolódott, nem látja a figyelmeztetést, mint amit a kiállítási folyamat során tapasztaltunk. Ez a következő szakaszban lesz frissítve.
    
   ![új engedély kérése](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. A hitelesítő adatok ellenőrzése nem sikerült.

## <a name="next-steps"></a>Következő lépések

Most, hogy már van egy VC-t kiállító mintakód a kiállítótól, lehetővé teszi a következő szakasz folytatását, ahol a saját identitás-szolgáltató használatával hitelesítheti a felhasználókat a ellenőrizhető hitelesítő adatok beszerzésére tett kísérletekhez, és használhatja a bemutató kérések aláírását.

> [!div class="nextstepaction"]
> [Oktatóanyag – ellenőrizhető hitelesítő adatok kiadása és ellenőrzése a bérlő használatával](issue-verify-verifiable-credentials-your-tenant.md)


