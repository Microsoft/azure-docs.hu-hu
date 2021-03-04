---
title: Az Azure Cost Management + Billing áttekintése
description: Az Azure Cost Management + Billing szolgáltatásaival a számlázáshoz kapcsolódó adminisztrációs feladatokat hajthat végre, és kezelheti a költségekhez való számlázási célú hozzáférést. Használhatja az Azure-kiadások monitorozására és szabályozására, valamint az Azure-erőforrások használatának optimalizálására szolgáló szolgáltatásokat is.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/03/2021
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 9fe658a1755ce3731f220ec656845da1f861fa9b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050535"
---
# <a name="what-is-azure-cost-management--billing"></a>Mi az az Azure Cost Management + Billing?

A Microsoft Cloud használatával jelentősen növelheti az üzleti számítási feladatok teljesítményét. Emellett a szervezet eszközeinek kezeléséhez szükséges fenntartási költségeket is csökkenti. Ez az üzleti lehetőség azonban kockázatot is jelenthet, mivel pazarlást és hatékonysági hiányosságokat okozhat a felhőben üzemelő példányokban. Az Azure Cost Management + Billing a Microsoft által biztosított eszközök csomagja, amely a számítási feladatok költségeinek elemzéséhez, kezeléséhez és a számítási feladatok költségeinek optimalizálásához nyújt segítséget. A csomag használata segít biztosítani, hogy a szervezet kihasználja a felhő által kínált előnyöket.

Az Azure számítási feladatai felfoghatók úgy, mint a világítás egy házban. Ha napközben elhagyja a házat, felkapcsolva hagyja a lámpákat? Szívesen használna olyan izzókat, amelyek segítenek csökkenteni a havi energiaköltségeket? A szükségesnél több lámpa van egy szobában? Az Azure Cost Management + Billing használatával ehhez hasonló módon közelítheti meg a szervezet által használt számítási feladatokat.

Az Azure-termékek és -szolgáltatások esetén a fizetés használat alapján történik. Amikor Azure-erőforrásokat hoz létre és használ, díjat kell fizetnie az erőforrásokért. Az új erőforrások üzembe helyezésének egyszerűsége miatt a számítási feladatok költségei megfelelő elemzés és monitorozás nélkül jelentősen megnőhetnek. Az Azure Cost Management + Billing funkcióival az alábbiakat teheti:

- Számlázási és adminisztratív feladatok végrehajtása, például a számlák befizetése
- Költségekhez való számlázási hozzáférés kezelése
- A havi számlák kiállításához használt költség- és használati adatok letöltése
- A költségek proaktív adatelemzése
- Költségküszöbök beállítása
- A számítási feladatok költségoptimalizáló módosítási lehetőségeinek azonosítása

Ha több információra van szüksége arról, hogy szervezetileg hogyan közelíthető meg a költségkezelés, tekintse meg a következő cikket: [Az Azure Cost Management ajánlott eljárásai](./costs/cost-mgt-best-practices.md).

![A Cost Management + számlázási optimalizálási folyamat ábrája.](./media/cost-management-optimization-process.png)

## <a name="understand-azure-billing"></a>Az Azure Billing ismertetése

Az Azure Billing szolgáltatásaival áttekintheti a kiszámlázott költségeket, és kezelheti a számlázási információkhoz való hozzáférést. Nagyobb szervezetekben általában a beszerzési és pénzügyi csapatok végzik a számlázási feladatokat.

A számlázási fiók az Azure-ba való regisztráció során jön létre. A számlázási fiókban kezelheti számláit, fizetéseit és nyomon követheti kiadásait. Több számlázási fiókhoz is rendelkezhet hozzáféréssel. Előfordulhat például, hogy az egyéni projektjei kezeléséhez regisztrált az Azure-ba. Lehetséges tehát, hogy egyéni Azure-előfizetéssel és számlázási fiókkal rendelkezik. Emellett rendelkezhet hozzáféréssel a szervezete Nagyvállalati Szerződésén vagy egy Microsoft-ügyfélszerződésen keresztül is. Mindegyik forgatókönyvhöz egy külön számlázási fiók tartozik.

### <a name="billing-accounts"></a>Számlázási fiókok

Az Azure Portal jelenleg a következő típusú számlázási fiókokat támogatja:

- **Microsoft Online Services Program**: A Microsoft Online Services Program egyéni számlázási fiókjai akkor jönnek létre, amikor az Azure webhelyén keresztül regisztrál az Azure-ba. Ha például egy [ingyenes Azure-fiókra](./manage/create-free-services.md)regisztrál, az utólagos elszámolású díjszabással vagy a Visual Studio-előfizetővel kell eljárnia.

- **Nagyvállalati Szerződés**: Nagyvállalati Szerződéshez tartozó számlázási fiók akkor jön létre, amikor a szervezet Nagyvállalati Szerződést (EA) köt az Azure használatára.

- **Microsoft-ügyfélszerződés**: A Microsoft-ügyfélszerződéshez tartozó számlázási fiók akkor jön létre, amikor a szervezet a Microsoft képviselőjével együttműködve Microsoft-ügyfélszerződést köt. Egyes régiókban, ha a felhasználó az Azure-webhelyen regisztrál egy használatalapú fizetést használó fiókot, vagy frissíti [ingyenes Azure-fiókját](./manage/create-free-services.md), külön számlázási fiókot kaphat a Microsoft-ügyfélszerződéshez.

## <a name="understand-azure-cost-management"></a>Az Azure Cost Management ismertetése

Habár kapcsolódik hozzá, a számlázás eltér a költségkezeléstől. A számlázás a számlák kiállításának folyamata az ügyfelek részére árucikkekről vagy szolgáltatásokról, valamint a kereskedelmi kapcsolat kezelése.

A Cost Management rámutat szervezete költség- és a felhasználási mintáira, bővített analitikával. A Cost Management jelentései az Azure-szolgáltatások és a külső Marketplace-ajánlatok használatán alapuló költségeket jelenítik meg. A költségek a megegyezés szerinti árakon alapulnak, figyelembe véve a foglalási és Azure Hybrid Benefit-kedvezményeket. A jelentések együttesen kimutatják a belső és külső használati költségeket, valamint az Azure Marketplace-díjakat. Az egyéb díjak, például a foglalások, a támogatási díjak és az adók még nem látszanak a jelentésekben. A jelentések segítenek értelmezni a kiadásokat és az erőforrások felhasználtságát, továbbá könnyebben fellelhet esetleges rendellenességeket kiadásaiban. Ezen felül prediktív elemzések is elérhetők. A Cost Management Azure-beli felügyeleti csoportok, költségvetések és javaslatok használatával egyértelműen megmutatja, hogyan vannak rendszerezve költségei, és hogyan csökkenthetné azokat.

Az Azure Portal vagy pedig a különféle API-k használatával automatizálhatja az adatexportálást, hogy integrálhassa a költségadatokat külső rendszerekbe és folyamatokba. Emellett lehetősége van a számlázási adatok automatikus exportálására és jelentések ütemezésére is.

Az Azure Cost Management áttekintését ismertető videó megtekintésével gyors képet alkothat arról, hogyan segíthet az Azure Cost Management az Azure-ban felmerülő költségek csökkentésében. További videók megtekintéséhez látogasson el a [Cost Management YouTube-csatornájára](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

### <a name="plan-and-control-expenses"></a>Költségtervezés és -irányítás

A Cost Management a következő módokon segít a költségek tervezésében és szabályozásában: Költségelemzés, költségvetések, javaslatok és a Cost Management adatainak exportálása.

A költségelemzés használatával megvizsgálhatja és elemezheti szervezete költségeit. Megtekintheti szervezete összesített költségeit, hogy jobban átláthassa, hogy mely területeken vannak elhatárolt költségek, valamint felismerheti a kiadási trendeket. Továbbá láthatja az idők során felhalmozott költségeket, így a költségvetéshez viszonyítva készíthet havi, negyedéves vagy akár éves költségtrendbecsléseket is.

A költségvetések használatával egyszerűbben kalkulálhat előre, valamint biztosíthatja a pénzügyi átláthatóságot szervezetén belül. Segítségükkel elkerülheti a költségküszöbök vagy korlátok átlépését. Abban is hasznára lehetnek, hogy másokat értesíthessen kiadásaikról a proaktív költségkezelés érdekében. Továbbá kimutatják, hogyan alakulnak kiadásai az idő során.

A javaslatok a kihasználatlan vagy alacsony kihasználtságú erőforrások kimutatásával szemléltetik, hogyan optimalizálhatja és fejlesztheti a költséghatékonyságot. Emellett alacsonyabb költségű lehetőségeket is ajánlanak. Ha a javaslatok megfogadásával változtat erőforrásai kihasználásán, pénzt takaríthat meg. Hogy hozzákezdhessen, első lépésként érdemes átnéznie a költségoptimalizálási javaslatokat. Így tájékozódhat arról, hogy melyek a potenciálisan kevésbé hatékonyan kihasznált erőforrásai. Ezt követően az egyik javaslat alapján egy költséghatékonyabb módot választ az Azure-erőforrásai felhasználásához. Ezután csak jóvá kell hagynia a műveletet, hogy a módosítás biztosan sikeresen végbe menjen.

Ha külső rendszereket használ a költségadatokhoz való hozzáféréshez vagy áttekintésükhöz, az Azure-ból könnyedén kiexportálhatja az adatokat. Ezen felül beállíthat egy napi rendszerességű ütemezett exportot CSV-formátumban, az adatfájlokat pedig eltárolhatja az Azure Storage-ban. Ezt követően máris hozzáférhet az adatokhoz a külső rendszeren keresztül is.

### <a name="cloudyn-deprecation"></a>A Cloudyn kivezetése

A Cloudyn egy Cost Managementhez kapcsolódó Azure-szolgáltatás, amelyet 2020 végével kivezetünk a használatból. A meglévő Cloudyn-funkciók, ahol ez megoldható, közvetlenül az Azure Portallal lesznek integrálva. Új ügyfeleket már nem regisztrálunk, a támogatás azonban továbbra is elérhető lesz, amíg a kivezetés teljesen le nem zárul.
 
### <a name="additional-azure-tools"></a>További Azure-eszközök

Az Azure más olyan eszközökkel is rendelkezik, amelyek nem képezik az Azure Cost Management + Billing szolgáltatásainak a részét. Fontos szerepet játszanak azonban a költségkezelési folyamatban. Az eszközökkel kapcsolatos további tudnivalókért lásd az alábbi hivatkozásokat.

- [Azure Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) – segítségével becslést végezhet kezdeti felhőköltségeiről.
- [Azure Migrate](../migrate/migrate-services-overview.md) – felmérheti adatközpontja jelenlegi számítási feladatait, így betekintést nyerhet abba, hogy mit várjon el egy Azure helyettesítő megoldástól.
- [Azure Advisor](../advisor/advisor-overview.md) - azonosíthatja használaton kívüli virtuális gépeit, és javaslatokat kaphat Azure fenntartott példányok vásárlásával kapcsolatban.
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) – használja az aktuális helyszíni Windows Server- vagy SQL Server-licenceit az Azure-beli virtuális gépeken a költségek csökkentése érdekében.

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett a Cost Management + Billinggel, a következő lépés a szolgáltatás használatának a megkezdése.

- Használja az Azure Cost Managementet [költségeinek elemzésére](./costs/quick-acm-cost-analysis.md).
- További információért megtekintheti az [Azure Cost Management ajánlott eljárásait](./costs/cost-mgt-best-practices.md).