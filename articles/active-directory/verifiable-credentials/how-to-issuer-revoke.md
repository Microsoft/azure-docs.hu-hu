---
title: Ellenőrizhető hitelesítő adat visszavonása kiállítóként – Azure Active Directory ellenőrizhető hitelesítő adatok
description: Megtudhatja, hogyan vonhatja vissza a kiadott ellenőrizhető hitelesítő adatokat
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 50f270dde59860e7c9dd2ac1b35039d5855859e5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222843"
---
# <a name="revoke-a-previously-issued-verifiable-credential-preview"></a>Korábban kiadott ellenőrizhető hitelesítő adat visszavonása (előzetes verzió)

A ellenőrizhető hitelesítő adatok (VCs) használatának folyamata során nem csupán hitelesítő adatokat kell kiállítania, de időnként vissza kell vonnia őket. Ebben a cikkben áttekintjük a VC-specifikáció **status (állapot** ) tulajdonságát, és alaposabban szemügyre vesszük a visszavonási folyamatot, ezért érdemes lehet visszavonni a hitelesítő adatokat és az adatok és az adatvédelem következményeit.

> [!IMPORTANT]
> Azure Active Directory ellenőrizhető hitelesítő adatok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="status-property-in-verifiable-credentials-specification"></a>Állapot tulajdonság a ellenőrizhető hitelesítő adatok specifikációjában

Mielőtt megértettük a ellenőrizhető hitelesítő adatok visszavonásának következményeit, hasznos lehet tudni, hogy mi az **állapot-ellenőrzés** , és hogyan működik ma.

A [W3C ellenőrizhető hitelesítő adatok specifikációja](https://www.w3.org/TR/vc-data-model/) az 4,9 szakaszban található **status** tulajdonságra hivatkozik [:](https://www.w3.org/TR/vc-data-model/#status)

"Ez a specifikáció a következő **credentialStatus** -tulajdonságot határozza meg egy ellenőrizhető hitelesítő adat aktuális állapotával kapcsolatos információk felderítéséhez, például hogy fel van-e függesztve vagy visszavonva."

A W3C-specifikáció azonban nem határoz meg formátumot az állapot- **ellenőrzések** megvalósításához.

"Az állapot-sémák adatmodelljének, formátumának és protokolljainak meghatározása kívül esik ennek a specifikációnak a hatókörén. Egy ellenőrizhető hitelesítő adat-kiterjesztési beállításjegyzék [VC-EXTENSION-REGISTRY] létezik, amely elérhető állapot-sémákat tartalmaz olyan végrehajtók számára, akik ellenőrizhető hitelesítő adatok állapotának ellenőrzését szeretnék végrehajtani. "

>[!NOTE]
>Egyelőre a Microsoft állapot-ellenőrzési implementációja védett, de aktívan dolgozunk a DID-Közösséggel, hogy a szokásos módon igazodjanak.

## <a name="how-does-the-status-property-work"></a>Hogyan működik az **állapot** tulajdonság?

Minden Microsoft által kiadott ellenőrizhető hitelesítő adatban van egy credentialStatus nevű attribútum. A szolgáltatás a Microsoft által az Ön nevében felügyelt status API-val van feltöltve. Íme egy példa arra, hogy néz ki.

```json
    "credentialStatus": {
      "id": "https://portableidentitycards.azure-api.net/v1.0/7952032d-d1f3-4c65-993f-1112dab7e191/portableIdentities/card/status",
      "type": "PortableIdentityCardServiceCredentialStatus2020"
    }
```

A nyílt forráskódú ellenőrizhető hitelesítő adatok SDK kezeli az állapot API meghívását és a szükséges adatokat.

Miután megismerte az API-t, és megadta a megfelelő információkat, az API igaz vagy hamis értéket ad vissza. Igaz, hogy a ellenőrizhető hitelesítő adat továbbra is aktív a kiállítóval, és hamisan jelzi, hogy a kibocsátó aktívan visszavonta a ellenőrizhető hitelesítő adatokat.

## <a name="why-you-may-want-to-revoke-a-vc"></a>Miért érdemes visszavonni egy VC-t?

Minden ügyfélnek saját egyedi oka van a ellenőrizhető hitelesítő adatok visszavonására, de íme néhány gyakori téma, amelyet eddig hallottunk. 

- Tanulói azonosító: a tanuló már nem aktív tanuló az egyetemen.
- Alkalmazott azonosítója: az alkalmazott már nem aktív alkalmazott.
- Állapot-illesztőprogramok licence: az illesztőprogram már nem él ebben az állapotban.

## <a name="how-to-set-up-a-verifiable-credential-with-the-ability-to-revoke"></a>Ellenőrizhető hitelesítő adat beállítása a visszavonás lehetőséggel

A rendszer alapértelmezés szerint nem tárolja az összes ellenőrizhető hitelesítő adatot a Microsofttól. Ezért nem áll rendelkezésre olyan adat, amely egy adott ellenőrizhető hitelesítő adat-azonosító visszavonására hivatkozik. A kiállítónak meg kell adnia egy adott mezőt a Microsoft számára a ellenőrizhető hitelesítő adatok attribútumból, hogy indexelje, majd ezt követően a sót és a kivonatot.

>[!NOTE]
>A kivonatolás egy egyirányú titkosítási művelet, amely egy bemenetet vált ki ```preimage``` , és egy rögzített hosszúságú kivonatot hoz létre. Jelenleg nem lehet számítási szempontból megvalósítani a kivonatoló művelet visszafordítását.

Azt is megtudhatja, hogy a Microsoft melyik attribútumát szeretné indexelni. Az indexelés következménye, hogy az indexelt értékek használatával kereshetők meg a visszavonni kívánt virtuális merevlemezek ellenőrizhető hitelesítő adatai.

**Példa:** Alice egy Woodgrove alkalmazott. Alice elhagyta a Woodgrove, hogy működjön a Contosoon. Jane, a Woodgrove informatikai rendszergazdája megkeresi az Alice e-mail-címét a ellenőrizhető hitelesítő adatok visszavonása keresési lekérdezésben. Ebben a példában Jane, a Woodgrove által ellenőrzött alkalmazotti hitelesítő adatok e-mail-mezőjét indexelte. 

Tekintse meg az alábbi példát arra, hogyan módosul a szabályok fájlja, hogy tartalmazza az indexet.

```json
{
  "attestations": {
    "idTokens": [
      { 
        "mapping": {
          "Name": { "claim": "name" },
          "email": { "claim": "email", "indexed": true}
        },
        "configuration": "https://login.microsoftonline.com/tenant-id-here7/v2.0/.well-known/openid-configuration",
        "client_id": "c0d6b785-7a08-494e-8f63-c30744c3be2f",
        "redirect_uri": "vcclient://openid"
      }
    ]
  },
  "validityInterval": 25920000,
  "vc": {
    "type": ["WoodgroveEmployee"]
  }
}
```

>[!NOTE]
>Egy szabály fájlból csak egy attribútum indexelhető.  

## <a name="how-do-i-revoke-a-verifiable-credential"></a>Hogyan ellenőrizhető hitelesítő adat visszavonása

Miután beállította és ellenőrizte a hitelesítő adatokat a felhasználók számára, itt az ideje, hogy megtudja, hogyan vonhatja vissza a ellenőrizhető hitelesítő adatokat a VC panelen.

1. Navigáljon a Azure Active Directory ellenőrizhető hitelesítő adatok paneljére.
1. Válassza ki azt a ellenőrizhető hitelesítő adatot, amelyben korábban beállította az index jogcímet, és egy felhasználó számára ellenőrizhető hitelesítő adatokat adott ki. =
1. A bal oldali menüben válassza a **hitelesítő adatok** visszavonása hitelesítő adatok 
    ![ visszavonása lehetőséget.](media/how-to-issuer-revoke/settings-revoke.png) 
1. Keresse meg a visszavonni kívánt felhasználó index attribútumát. 

   ![A visszavonni kívánt hitelesítő adat megkeresése](media/how-to-issuer-revoke/revoke-search.png)

    >[!NOTE]
    >Mivel a rendszer csak az indexelt jogkivonatot tárolja a ellenőrizhető hitelesítő adatokból, csak egy pontos egyezés fogja feltölteni a keresési eredményeket. A bemenetet a rendszergazda keresi, és ugyanazt a kivonatoló algoritmust használjuk, hogy ellenőrizze, hogy van-e kivonatoló egyezés az adatbázisban.
    
1. Ha megtalálta a megfelelőt, válassza a **Visszavonás lehetőséget a visszavonni** kívánt hitelesítő adattól jobbra.

   ![Figyelmeztetés arról, hogy a felhasználó visszavonása után még mindig rendelkezik a hitelesítő adatokkal](media/how-to-issuer-revoke/warning.png) 

1. A sikeres visszavonás után megjelenik az állapot frissítése, és egy zöld szalagcím jelenik meg az oldal tetején. 
   ![A tartomány ellenőrzése a beállításokban](media/how-to-issuer-revoke/revoke-successful.png) 

Most, amikor egy függő entitás meghívja az adott ellenőrizhető hitelesítő adat állapotát, a bérlő nevében eljárva a Microsoft status API a "hamis" választ adja vissza.

## <a name="next-steps"></a>Következő lépések

Tesztelje a saját funkcionalitását egy teszt hitelesítő adatokkal a folyamathoz való használathoz. Az [oktatóanyagok áttekintésével](get-started-verifiable-credentials.md)megtekintheti, hogyan konfigurálhatja a bérlőt, hogy igazolható hitelesítő adatokat bocsásson ki.