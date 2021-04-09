---
title: Bevezetés az Azure hatáskörébe (előzetes verzió)
description: Ez a cikk áttekintést nyújt az Azure hatáskörébe, beleértve a funkcióit és az általa felmerülő problémákat. Az Azure hatáskörébe minden felhasználó regisztrálhatja, felderítheti, értelmezheti és felhasználhatja az adatforrásokat.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 11/30/2020
ms.openlocfilehash: 3fadfa9d4467209ddbe997afabba27a3752f685d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419932"
---
# <a name="what-is-azure-purview"></a>Mi az az Azure Purview?

> [!IMPORTANT]
> Az Azure-beli hatáskörébe jelenleg előzetes verzió érhető el. A [Microsoft Azure előzetes verzióinak kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) közé tartoznak azok a további jogi feltételek, amelyek az Azure olyan szolgáltatásaira vonatkoznak, amelyek béta-, előzetes verziójú vagy egyéb módon még nem lettek nyilvánosan elérhetők.

Az Azure hatáskörébe egy egységes adatirányítási szolgáltatás, amely segít a helyszíni, többfelhős és szoftveres (SaaS-) adatkezelésben és irányításban. Az adatkörnyezet átfogó, naprakész térképét az adatok automatikus észlelésével, a bizalmas adatok besorolásával és a végpontok közötti adatbontással egyszerűen létrehozhatja. Az adatfogyasztók számára értékes és megbízható adatkezelést lehet kitalálni.

Az Azure-beli hatáskörébe tartozó adatleképezés biztosítja az adatfelderítés és a hatékony adatkezelés alapjait. A hatáskörébe tartozó adatleképezés egy Felhőbeli natív Pásti szolgáltatás, amely a helyszíni és a Felhőbeli elemzési és üzemeltetési rendszerekben található vállalati adatok metaadatait rögzíti. A rendszer automatikusan naprakészen tartja a hatáskörébe tartozó adatleképezést a beépített automatizált ellenőrzési és besorolási rendszerrel. Az üzleti felhasználók intuitív felhasználói felületen konfigurálhatják és használhatják a hatáskörébe tartozó adatleképezést, és a fejlesztők programozott módon kezelhetik az adatleképezést a nyílt forráskódú Apache Atlas 2,0 API-k használatával.

Az Azure-beli hatáskörébe tartozó adatleképezési funkciók a hatáskörébe Data Catalog és a hatáskörébe tartozó adatelemzések a hatáskörébe Studióban egyesített élményként jelennek meg.
 
A hatáskörébe Data Catalog az üzleti és technikai felhasználók egyaránt gyorsan & könnyen megtalálják a releváns adatokat a különböző objektívek, például a Szószedet, a besorolások, az érzékenységi címkék és egyebek alapján. A hatáskörébe tartozó szakértők, az adatkezelők és a tisztviselők számára a hatáskörébe Data Catalog olyan adatkezelési funkciókat biztosít, mint például az üzleti szószedetek kezelése, és az adategységek címkézésének automatizálása a Szószedet feltételeit. Az adategységek és a gyártók vizuálisan nyomon követhetik az adategységek vonalát a helyszíni operációs rendszertől kezdve, a mozgáson, az átalakításon & a különböző adattárolási & a felhőben lévő feldolgozási rendszerek használatával, például a Power BI.

A hatáskörébe tartozó adatelemzések révén az adattisztek és a biztonsági tisztviselők egy madár nézetét tekinthetik meg, és egy pillantással megtudhatják, milyen adatokat vizsgálnak aktívan, hol vannak a bizalmas adatok, és hogyan mozognak.

## <a name="discovery-challenges-for-data-consumers"></a>Az adatok felfedezésének kihívásai az adatfelhasználók számára

A vállalati adatforrások felfedezése hagyományosan a közösségi ismereteken alapuló szerves folyamat volt. Ez a megközelítés számos kihívást jelent a vállalatok számára, akik a legtöbb értéket szeretnék kiszolgálni az adateszközökből:

* Mivel nincs központi hely az adatforrások regisztrálásához, a felhasználók nem tudnak az adatforrásokról, kivéve, ha egy másik folyamat részeként kapcsolatba lépnek a szolgáltatással.
* Ha a felhasználók nem ismerik az adatforrás helyét, nem tudnak csatlakozni az adatforráshoz egy ügyfélalkalmazás használatával. A hagyományos adatfelhasználási folyamatok során a felhasználóknak tudniuk kell a megfelelő kapcsolati sztringet, vagy az elérési utat.
* Az adatforrások rendeltetésszerű használata rejtve marad a felhasználók számára, kivéve, ha ismeri az adatforrás dokumentációjának helyét. Az adatforrások és a dokumentációk több helyen is előfordulhatnak, és többféle módon is felhasználható.
* Ha a felhasználóknak kérdése van egy adott információval kapcsolatban, személyesen kell megkeresniük az adatokért felelős szakembert vagy csapatot. Nincs explicit kapcsolat az adatközpontok és a használati perspektívákkal rendelkező szakértők között.
* Ha a felhasználók nem értik az adatforráshoz való hozzáférés kérelmezésének folyamatát, az adatforrás és a hozzá tartozó dokumentáció felfedése nem segít nekik elérni az adatokat.

## <a name="discovery-challenges-for-data-producers"></a>Az adatok felfedezésének kihívásai az adatalkotók számára

Miközben az adatfelhasználók mindezekkel az említett kihívásokkal néznek szembe, az információs adategységek létrehozásáért és fenntartásáért felelős felhasználóknak szintén megvannak a saját kihívásaik:

* Az adatforrások felcímkézése tájékoztató jellegű metaadatokkal gyakran hiábavalónak bizonyul. Az ügyfélalkalmazások általában figyelmen kívül hagyják az adatforrásban tárolt tájékoztatókat.
* Az adatforrások dokumentációjának létrehozása bonyolult lehet, és folyamatos felelőssége, hogy az adatforrásokkal szinkronban tartsa a dokumentációt. Előfordulhat, hogy a felhasználók nem bíznak az elavultnak tartott dokumentációban.
* Az adatforrásokhoz tartozó dokumentációk létrehozása és fenntartása összetett és időigényes feladat. A dokumentációkat az adatforrás minden felhasználója számára elérhetővé tenni pedig gyakran még inkább az.
* Állandó kihívás az adatforrásokhoz való hozzáférés korlátozása, és annak biztosítása, hogy az adatfelhasználók ismerjék a hozzáférések kérelmezésének menetét.

Az ilyen kihívások összevonásakor jelentős akadályt jelentenek azok a vállalatok, akik támogatni kívánják a vállalati adatok felhasználását és megismerését.

## <a name="discovery-challenges-for-security-administrators"></a>Felderítési kihívások a biztonsági rendszergazdák számára

Azok a felhasználók, akik felelősek a szervezeti adatok biztonságának biztosításáért, a fent felsorolt kihívásokkal rendelkezhetnek az adatfogyasztók és a termelők számára, valamint a következő további kihívásokkal:

* A szervezeti adatmennyiségek folyamatosan bővülnek, tárolódnak és új irányokban vannak megosztva. A bizalmas adatok feltárása, védelme és szabályozása olyan feladat, amely soha nem ér véget. Gondoskodni szeretne arról, hogy a szervezet tartalma a megfelelő személyekkel, alkalmazásokkal és a megfelelő engedélyekkel legyen megosztva.
* A szervezet adataiban felmerülő kockázati szintek megismeréséhez a tartalomnak részletesen meg kell keresnie a kulcsszavakat, a RegEx-mintákat és/vagy a bizalmas adattípusokat. A bizalmas adattípusok közé tartozhatnak a hitelkártya-számok, a társadalombiztosítási számok vagy a bankszámla-számok is. Folyamatosan figyelnie kell az összes adatforrást a bizalmas tartalomhoz, mivel a legkisebb adatvesztés is kritikus fontosságú lehet a szervezet számára.
* Annak biztosítása, hogy a szervezet továbbra is megfeleljen a vállalati biztonsági házirendeknek, kihívást jelentő feladat a tartalom növekedésével és változásaival kapcsolatban, valamint a követelmények és szabályzatok frissítése a digitális realitások módosítására. A biztonsági rendszergazdákat gyakran a lehető legrövidebb időn belül az adatok biztonságának biztosításával kell feldolgozni.

## <a name="azure-purview-advantages"></a>Az Azure hatáskörébe tartozó előnyök

Az Azure-beli hatáskörébe az előző szakaszban említett problémák megoldása, valamint a vállalatoknak a meglévő információs eszközökből való lehető legjobb kiszolgálása. A katalógus könnyen felderíthetővé és érthetővé teszi az adatforrásokat az azokat kezelő felhasználók számára.

Az Azure-beli hatáskörébe tartozik egy felhőalapú szolgáltatás, amelybe regisztrálhatja az adatforrásokat. A regisztráció során az adatok a meglévő helyükön maradnak, de a metaadatok másolatát hozzáadja az Azure hatáskörébe, valamint az adatforrás helyére mutató hivatkozást is tartalmaz. A metaadatok indexelésének köszönhetően az adatforrások egy egyszerű keresés által felfedezhetővé és könnyen értelmezhetővé válnak a felhasználók számára.

Az adatforrások regisztrálását követően bővítheti a metaadatokat. Vagy az a felhasználó, aki regisztrálta az adatforrást vagy egy másik felhasználót a vállalatnál, hozzáadja a metaadatokat. Bármely felhasználó megjegyzéseket fűzhet az adatforráshoz, ha leírást, címkéket vagy egyéb metaadatokat biztosít az adatforrás-hozzáféréshez. Ez a leíró metaadatok kiegészítik az adatforrásból regisztrált szerkezeti metaadatokat, például az oszlopnevek és az adattípusokat.

Az adatforrások regisztrálásának elsődleges célja, hogy a források felfedezhetők, értelmezhetők és felhasználhatók legyenek. A vállalati felhasználóknak adatokra lehet szükségük az üzleti intelligencia, az alkalmazásfejlesztés, az adattudomány és minden más olyan tevékenység terén, ahol fontos a megfelelő adatok használata. A adatkatalógus-felderítési élményt használva gyorsan megtalálják az igényeiknek megfelelő adatmennyiségeket, megismerhetik az adatmennyiséget, és az adatforrást a választott eszközön nyitják meg.

Ezzel egy időben a regisztrált adatforrások címkézésével, dokumentáció, illetve jegyzetek készítésével a felhasználók közre is tudnak működni a katalógus készítésében. Emellett új adatforrásokat is regisztrálhatnak, amelyeket a katalógus felhasználói közössége felderít, megértett és felhasználhat.

## <a name="in-region-data-residency"></a>Régión belüli adattárolás
Az Azure-beli hatáskörébe nem helyezi át vagy nem tárolja az ügyféladatokat azon a régión belül, ahol központilag telepítették.

## <a name="next-steps"></a>Következő lépések

Az Azure-beli hatáskörébe való ismerkedéshez tekintse meg [Az Azure-beli hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakört.
