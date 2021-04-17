---
title: Tartomány összekapcsolása a decentralizált azonosítóval (DID) (előzetes verzió) – Azure Active Directory hitelesítő adatok
description: Ismerje meg, hogyan lehet DNS-kötést kötni?
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: ad5bb6e45479b4cccfa0b002427066439135e468
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588445"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>A tartomány összekapcsolása a decentralizált azonosítóval (DID)

> [!IMPORTANT]
> Azure Active Directory hitelesítő adatok ellenőrizhetővé tehetők jelenleg nyilvános előzetes verzióban.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A cikk tartalma:
> [!div class="checklist"]
> * Miért kell a DID-et a tartományhoz kapcsolnunk?
> * Hogyan kapcsolhatóak össze a DID-ket és a tartományokat?
> * Hogyan működik a tartomány-összekapcsolási folyamat?
> * Hogyan működik az ellenőrző/nem ellenőrzött tartományi logika?

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a DID-t a tartományhoz csatolja, a következőket kell végrehajtania.

- Töltse ki [az Első lépések és](get-started-verifiable-credentials.md) az azt követő [oktatóanyag-készletet.](enable-your-tenant-verifiable-credentials.md)

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Miért kell a DID-et a tartományhoz kapcsolnunk?

A DID azonosítóként indul, amely nem a meglévő rendszerekhez van horgonyozva. A DID azért hasznos, mert egy felhasználó vagy szervezet tulajdonában lehet, és vezérelheti azt. Ha a szervezettel kommunikáló entitás nem tudja, hogy a DID kihez tartozik, akkor a DID nem olyan hasznos.

A DID tartományhoz kapcsolása azzal oldja meg a kezdeti megbízhatósági problémát, hogy lehetővé teszi bármely entitás számára a DID és a tartomány közötti kapcsolat titkosítását.

## <a name="how-do-we-link-dids-and-domains"></a>Hogyan kapcsolhatóak össze a DID-ket és a tartományokat?

Kapcsolatot hozunk létre egy tartomány és egy DID között egy, a Decentralized Identity Foundation által írt nyílt szabvány megvalósításával, amelyet [Well-Known DID-konfigurációnak nevezünk.](https://identity.foundation/.well-known/resources/did-configuration/) Az Azure Active Directory (Azure AD) ellenőrizhető hitelesítőadat-szolgáltatása segít a szervezetnek a DID és a tartomány közötti kapcsolat létrehozásában azáltal, hogy tartalmazza a DID-fájlban megadott tartományadatokat, és létrehozza a jól ismert konfigurációs fájlt:

1. Az Azure AD a szervezeti beállítás során a DID-dokumentumban található szolgáltatásvégpont írására használja fel a tartományi adatokat. A DID-sel kapcsolatba lépő összes fél láthatja azt a tartományt, amelyhez a DID-t társították.  

    ```json
        "service": [
          {
            "id": "#linkeddomains",
            "type": "LinkedDomains",
            "serviceEndpoint": {
              "origins": [
                "https://www.contoso.com/"
              ]
            }
          }
    ```

2. Az Azure AD ellenőrizhető hitelesítőadat-szolgáltatása egy jól ismert, megfelelő konfigurációs erőforrást hoz létre, amely a saját tartományán is létrehozható. A konfigurációs fájl tartalmazza a "DomainLinkageCredential" hitelesítő adatok önkibocsátott, ellenőrizhető hitelesítő adatait, amelyet a DID-fájl írt alá, és amely a tartomány eredetét tartalmazza. Példa a konfigurációs dokumentumra, amely a gyökértartomány URL-címében van tárolva.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

A jól ismert konfigurációs fájl létrehozása után elérhetővé kell tennie a fájlt az AAD ellenőrizhető hitelesítő adatokhoz való engedélyezésekor megadott tartománynév használatával.

* A jól ismert DID konfigurációs fájlt a tartomány gyökerében kell elosztani.
* Ne használjon átirányításokat.
* A konfigurációs fájl terjesztéséhez használja a https protokollt.

>[!IMPORTANT]
>Microsoft Authenticator nem támogatja az átirányításokat, a megadott URL-címnek a végső cél URL-címnek kell lennie.

## <a name="user-experience"></a>Felhasználó felület 

Amikor egy felhasználó egy kiállítási folyamaton megy keresztül, vagy ellenőrizhető hitelesítő adatokat ad meg, tudnia kell valamit a szervezetről és a DID-ről. Ha a tartomány az ellenőrizhető hitelesítőadat-Microsoft Authenticator, a ellenőrzi a DID és a tartomány közötti kapcsolatot a DID dokumentumban, és az eredménytől függően két különböző felhasználói élményt nyújt a felhasználóknak.

## <a name="verified-domain"></a>Ellenőrzött tartomány

Mielőtt Microsoft Authenticator megjelenít egy **Ellenőrzött** ikont, néhány dolognak igaznak kell lennie:

* Az önkibocsátott nyitott azonosítóra (SIOP) vonatkozó DID-kérelemnek szolgáltatási végponttal kell lennie a csatolt tartományhoz.
* A gyökértartomány nem használ átirányítást, és https-t használ.
* A DID-dokumentumban felsorolt tartomány feloldható, jól ismert erőforrással rendelkezik.
* A jól ismert erőforrás ellenőrizhető hitelesítő adata ugyanazokkal a DID-ekkel van aláírva, mint amely a folyamat indítani Microsoft Authenticator SIOP aláírásával történt.

Ha az előzőek mind igazak, akkor a Microsoft Authenticator egy ellenőrzött oldalt jelenít meg, és tartalmazza az ellenőrzött tartományt.

![új engedélykérés](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Nem ellenőrzött tartomány

Ha a fentiek bármelyike nem igaz, a Microsoft Authenticator teljes oldalú figyelmeztetést jelenít meg a felhasználónak arról, hogy a tartomány ellenőrizetlen, a felhasználó egy kockázatos tranzakció közepén van, és óvatosan kell eljárnia. Azért választottuk ezt az útvonalat, mert:

* A DID vagy nincs tartományhoz horgonyozva.
* A konfiguráció nem lett megfelelően beállítva.
* A felhasználó által kommunikált DID rosszindulatú, és valójában nem tudja igazolni, hogy a felhasználó a tartományhoz van-e használhatja (mivel valójában nem). Az utolsó pont miatt rendkívül fontos, hogy a DID-t a felhasználó által ismert tartományhoz csatolja, hogy elkerülje a figyelmeztető üzenet propagálását.

![nem ellenőrzött tartományra vonatkozó figyelmeztetés a hitelesítő adatok hozzáadása képernyőn](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Jól ismert konfiguráció terjesztése

1. Lépjen a Beállítások lapra az Ellenőrizhető hitelesítő adatok területen, és válassza **a Tartomány ellenőrzése lehetőséget.**

   ![A tartomány ellenőrzése a beállításokban](media/how-to-dnsbind/settings-verify.png) 

2. Töltse le did-configuration.jsaz alábbi képen látható fájlban található fájlt.

   ![Jól ismert konfiguráció letöltése](media/how-to-dnsbind/verify-download.png) 

3. Másolja a JWT-t, nyissa [meg jwt.ms](https://www.jwt.ms) és ellenőrizze, hogy a tartomány helyes-e.

4. Másolja ki a DID-t, és nyissa meg az [ION-Hálózattallózó,](https://identity.foundation/ion/explorer) és ellenőrizze, hogy a DID-dokumentum tartalmazza-e ugyanazt a tartományt. 

5. A jól ismert konfigurációs erőforrást a megadott helyen kell megadni. Például: `https://www.example.com/.well-known/did-configuration.json`

6. Tesztelje az érvényesíthető Microsoft Authenticator kiadását vagy bemutatóját. Győződjön meg arról, hogy az Authenticator "Figyelmeztetés a nem biztonságos alkalmazásokról" beállítása be van-e bekapcsolva.

>[!NOTE]
>Alapértelmezés szerint a "Figyelmeztetés a nem biztonságos alkalmazásokról" beállítás be van kapcsolva.

Gratulálunk, ezzel elindította a megbízhatósági hálót a DID-el!

## <a name="next-steps"></a>Következő lépések

Ha az regisztráció során nem a megfelelő tartományinformációt adja meg, és úgy dönt, hogy módosítja azt, le kell majd mondania a [következőt:](how-to-opt-out.md). Jelenleg nem támogatjuk a DID-dokumentum frissítését. A lemondás és a lemondás teljesen új DID-t hoz létre.
