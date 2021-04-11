---
title: A tartomány összekapcsolása a decentralizált azonosítóval (DID) (előzetes verzió)
description: Ismerje meg a DNS-kötést?
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: be7db16a8e3a827d08c0db637961bf004af1d621
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170026"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>A tartomány összekapcsolása a decentralizált azonosítóval (DID)

> [!IMPORTANT]
> Azure Active Directory ellenőrizhető hitelesítő adatok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A cikk tartalma:
> [!div class="checklist"]
> * Miért van szükségünk a tartományhoz való csatlakozásra?
> * Hogyan lehet összekapcsolni a DIDs és a tartományokat?
> * Hogyan működik a tartomány-összekapcsolási folyamat?
> * Hogyan működik az ellenőrzés/nem ellenőrzött tartomány logikája?

## <a name="prerequisites"></a>Előfeltételek

Ha a saját tartományához szeretné kapcsolni, a következőket kell elvégeznie.

- Fejezze be a [első lépések](get-started-verifiable-credentials.md) és az azt követő [oktatóanyag-készletet](enable-your-tenant-verifiable-credentials.md).

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Miért van szükségünk a tartományhoz való csatlakozásra?

A nem a meglévő rendszerekhez rögzített azonosítóként indul el. A nem hasznos, mert egy felhasználó vagy szervezet maga is rendelkezhet, és szabályozhatja azt. Ha a szervezettel kommunikáló entitás nem tudja, hogy "ki" az a tagja, akkor a DID nem annyira hasznos.

Ha a-t egy tartományhoz csatolja, azzal megoldja a kezdeti megbízhatósági problémát azáltal, hogy lehetővé teszi bármely entitás számára, hogy kriptográfiailag ellenőrizze a DID és a tartomány közötti kapcsolatot.

## <a name="how-do-we-link-dids-and-domains"></a>Hogyan lehet összekapcsolni a DIDs és a tartományokat?

Létrehozunk egy kapcsolatot egy tartomány és egy között egy olyan nyílt szabvány bevezetésével, amelyet a decentralizált identitási alaprendszer írt be [jól ismert DID-konfiguráció](https://identity.foundation/.well-known/resources/did-configuration/)néven. A Azure Active Directory (Azure AD) ellenőrizhető hitelesítő adatok szolgáltatása segít a szervezetnek a DID és a tartomány közötti kapcsolat létrehozásában, amely tartalmazza a DID-ban megadott tartományi információkat, és létrehozta a jól ismert konfigurációs fájlt:

1. Az Azure AD a szervezet beállítása során megadott tartományi információkat használja a szolgáltatási végpontnak a DID-dokumentumon belüli írásához. Az Ön által folytatott kommunikációt végző felek láthatják azt a tartományt, amelyet a saját jogcímei alapján társít.  

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

2. Az Azure AD-ben ellenőrizhető hitelesítőadat-szolgáltatás egy megfelelő, jól ismert konfigurációs erőforrást hoz létre, amelyet a tartományában tárolhat. A konfigurációs fájl tartalmazza a "DomainLinkageCredential" credentialType önaláírt hitelesítő adatait, amely a saját tartományának eredetével rendelkezik. Itt látható egy példa arra a konfigurációs dokumentumra, amelyet a rendszer a gyökértartomány URL-címén tárol.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

Miután elvégezte a jól ismert konfigurációs fájl használatát, a fájlt elérhetővé kell tennie a HRE a ellenőrizhető hitelesítő adatokhoz való engedélyezésekor megadott tartománynév használatával.

* A jól ismert, a tartomány gyökerében található konfigurációs fájl üzemeltetése.
* Ne használja az átirányítást.
* A konfigurációs fájl terjesztéséhez használja a HTTPS protokollt.

>[!IMPORTANT]
>Microsoft Authenticator nem tartja tiszteletben az átirányításokat, a megadott URL-címnek a végső cél URL-címnek kell lennie.

## <a name="user-experience"></a>Felhasználó felület 

Ha egy felhasználó egy kiállítási folyamaton vagy egy ellenőrizhető hitelesítő adaton alapul, tudnia kell, hogy a szervezetről és a vállalatról van szó. Ha a tartomány a hitelesíthető hitelesítőadat-tárca, Microsoft Authenticator, ellenőrzi, hogy történt-e kapcsolat a DID-dokumentum tartományával, és az eredménytől függően két különböző tapasztalatot jelenít meg a felhasználók számára.

## <a name="verified-domain"></a>Ellenőrzött tartomány

Mielőtt Microsoft Authenticator egy **ellenőrzött** ikont jelenít meg, néhány dolognak igaznak kell lennie:

* A saját maga által kibocsátott Open ID-(SIOP-) kérelem aláírásának rendelkeznie kell egy szolgáltatási végponttal a csatolt tartományhoz.
* A gyökértartomány nem használ átirányítást, és HTTPS-t használ.
* A DID dokumentumban felsorolt tartomány feloldható, jól ismert erőforrással rendelkezik.
* A jól ismert erőforrás ellenőrizhető hitelesítő adatainak aláírása ugyanazzal a TETTtel történt, mint a folyamat elindításához Microsoft Authenticator használt SIOP aláírásához.

Ha az összes korábban említett érték igaz, akkor Microsoft Authenticator egy ellenőrzött lapot jelenít meg, és tartalmazza az érvényesített tartományt.

![új engedély kérése](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Nem ellenőrzött tartomány

Ha a fentiek bármelyike nem igaz, a Microsoft Authenticator teljes oldal figyelmeztetést jelenít meg a felhasználónak, hogy a tartomány nem ellenőrzött, a felhasználó egy kockázatos tranzakció közepén található, és körültekintően kell eljárnia. Úgy döntöttünk, hogy ezt az útvonalat használja:

* A DID nem egy tartományhoz van rögzítve.
* A konfiguráció beállítása nem megfelelő.
* Az a felhasználó, aki interakcióban van, rosszindulatú, és valójában nem tudja bizonyítani, hogy a tartomány tulajdonosa (mivel valójában nem). Ennek az utolsó pontnak az az oka, hogy a figyelmeztető üzenet propagálásának elkerülése érdekében fontos, hogy a felhasználót a felhasználó által ismert tartományhoz csatolja.

![nem ellenőrzött tartományi figyelmeztetés a hitelesítő adatok hozzáadása képernyőn](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Jól ismert konfiguráció terjesztése

1. Navigáljon a Beállítások lapra a ellenőrizhető hitelesítő adatok között, és válassza a **tartomány ellenőrzése** lehetőséget.

   ![A tartomány ellenőrzése a beállításokban](media/how-to-dnsbind/settings-verify.png) 

2. Töltse le az alábbi képen látható did-configuration.jsfájlt.

   ![Jól ismert konfiguráció letöltése](media/how-to-dnsbind/verify-download.png) 

3. Másolja a JWT, nyissa meg a [JWT.MS](https://www.jwt.ms) , és ellenőrizze, hogy helyes-e a tartomány.

4. Másolja a DID-t, és nyissa meg az [ion Network Explorert](https://identity.foundation/ion/explorer) , és ellenőrizze, hogy a DID-dokumentum tartalmazza-e ugyanezt a tartományt. 

5. A jól ismert konfigurációs erőforrást a megadott helyen tárolja. Például: https://www.example.com/.well-known/did-configuration.json

6. Tesztelje a kiállítást vagy a bemutatót Microsoft Authenticator az ellenőrzéshez. Győződjön meg arról, hogy a hitelesítő "figyelmeztetés a nem biztonságos alkalmazásokról" beállítás be van kapcsolva.

>[!NOTE]
>Alapértelmezés szerint a "figyelmeztetés a nem biztonságos alkalmazásokról" beállítás be van kapcsolva.

Gratulálunk, most már bootstrapped a megbízható webes megbízhatóságot!

## <a name="next-steps"></a>Következő lépések

Ha a bevezetéskor nem a megfelelő tartományi adatokat adja meg, akkor a módosítást el kell [végeznie](how-to-opt-out.md). Jelenleg nem támogatjuk a DID-dokumentum frissítését. A kikapcsolás és a visszahívása új márka létrehozását eredményezi.