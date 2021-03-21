---
title: Mi az az Azure Data Share?
description: Ismerje meg, hogyan oszthat meg egyszerűen és biztonságosan több ügyfelet és partnert az Azure-adatmegosztás használatával.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: overview
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: 3a7c73e4a5ba00155ab905f28edbcb0eb42c0539
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724863"
---
# <a name="what-is-azure-data-share"></a>Mi az az Azure Data Share?

Napjaink világában az adatgyűjtés kulcsfontosságú stratégiai eszköznek tekinthető, amelyet számos szervezetnek egyszerűen és biztonságosan kell megosztania ügyfeleivel és partnereivel. Manapság számos módon teheti ezt meg, többek között FTP-n, e-mailben, API-kon keresztül, hogy csak néhányat említsünk. A szervezetek könnyen elveszítik a nyomon követését, hogy kik osztják meg az adatvédelmet a segítségével. Az adatmegosztás FTP-n keresztül vagy a saját API-infrastruktúrájának kiépítésével gyakran költséges a kiépítése és felügyelete. A megosztások ezen módszereinek nagy léptékű használata esetén a felügyelet terhelése is fennáll. 

Számos szervezetnek a megosztott adatmennyiségért kell felvennie a fiókot. Az elszámoltathatóság mellett számos szervezet egyszerűen vezérelheti, kezelheti és figyelheti az összes adatmegosztást. A mai világban, ahol az adatmennyiség várhatóan exponenciálisan növekedni fog, a szervezeteknek egyszerű módszert kell megosztaniuk big data. Az ügyfeleknek a legfrissebb adatokra van szükségük annak biztosításához, hogy képesek legyenek időben betekinteni a bepillantást.

Az Azure-beli adatmegosztás lehetővé teszi, hogy a szervezetek egyszerűen és biztonságosan osszák meg az adatmegosztást több ügyféllel és partnerrel. Néhány kattintással kiépíthet egy új adatmegosztási fiókot, adatkészleteket adhat hozzá, valamint meghívhatja ügyfeleit és partnereit az adatmegosztásába. Minden esetben az adatszolgáltatók felügyelete alá tartoznak az általuk megosztott adatok. Az Azure Data Share-rel egyszerűen kezelhető és figyelhető, hogy milyen adatokat osztottak meg, mikor osztották meg őket, illetve hogy kikkel. 

Az adatszolgáltatók az adatmegosztásra vonatkozó használati feltételek megadásával szabályozhatják az adatkezelés módját. Az adatfelhasználónak el kell fogadnia ezeket a feltételeket ahhoz, hogy adatokat fogadhasson. Az adatszolgáltatók meghatározhatják, hogy az adatfelhasználók milyen gyakorisággal kapjanak frissítéseket. Az új frissítésekhez való hozzáférést az adatszolgáltató bármikor visszavonhatja. 

Az Azure-adatmegosztás segít az elemzések fejlesztésében azáltal, hogy megkönnyíti a harmadik féltől származó adatok egyesítését az elemzési és AI-forgatókönyvekhez. Az Azure Analytics eszközeinek segítségével könnyedén felkészítheti, feldolgozhatja és elemezheti az Azure-adatmegosztás használatával megosztott adatmennyiségeket. 

Az adatszolgáltatónak és az adatfogyasztónak is rendelkeznie kell egy Azure-előfizetéssel az adatmegosztáshoz és az adatfogadáshoz. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Azure-adatmegosztási forgatókönyvek

Az Azure-adatmegosztás több különböző iparágban is felhasználható. Előfordulhat például, hogy egy kiskereskedő szeretné megosztani a legutóbbi értékesítési adatpontot a szállítókkal. Az Azure-adatmegosztás használatával a kiskereskedelmi adatmegosztást állíthat be, amely tartalmazza az értékesítési adatpontot az összes szállítónál, és megoszthatja az értékesítést óránként vagy naponta. 

Az Azure-adatmegosztás használatával egy adott iparág számára is létrehozhat egy adatpiacot. Például egy olyan kormány vagy kutatóintézet, amely rendszeresen oszt meg anonim információkat a népesség növekedésével kapcsolatban harmadik felekkel. 

Az Azure-adatmegosztás egy másik használati esete egy adatkonzorcium létrehozása. Például számos különböző kutatóintézet egyetlen megbízható törzstel oszthatja meg az adatmegosztást. Az adatokat elemezze, összesíti vagy dolgozza fel az Azure Analytics-eszközök használatával, majd megoszthatja őket az érdekelt felekkel. 

## <a name="how-it-works"></a>Működés

Az Azure-beli adatmegosztás jelenleg pillanatkép-alapú megosztást és helyi megosztást biztosít. 

A pillanatkép-alapú megosztásban az adatok átkerülnek az adatszolgáltató Azure-előfizetésére, és az adatfogyasztó Azure-előfizetésében landolnak. Adatszolgáltatóként egy adatmegosztást kell kiépíteni, és meg kell hívnia a címzetteket az adatmegosztásra. Az adatfogyasztók e-mailben kapják meg az adatmegosztásra vonatkozó meghívót. Miután egy adatfogyasztó elfogadja a meghívót, elindíthatják a velük megosztott adatok teljes pillanatképét. Ezek az adathozzáférések az adatfogyasztók Storage-fiókba érkeznek. Az adatfogyasztók rendszeres, növekményes frissítéseket kaphatnak a velük megosztott adatmegosztáshoz, így mindig a legújabb verzióval rendelkeznek. 

Az adatszolgáltatók az adatfogyasztók növekményes frissítését is lehetővé tehetik a velük megosztott adatokhoz egy pillanatkép-ütemterv alapján. A pillanatkép-ütemtervek óránkénti vagy napi rendszerességgel is elérhetők. Amikor az adatfogyasztó elfogadja és konfigurálja az adatmegosztást, előfizethet egy pillanatkép-ütemtervre. Ez olyan esetekben hasznos, amikor a megosztott adatok rendszeresen frissülnek, és az adatok fogyasztójának a legfrissebb adatokra van szüksége. 

![adatmegosztási folyamat](media/data-share-flow.png)

Amikor az adatfogyasztó elfogad egy adatmegosztást, a rendszer az adott adattárban fogadhatja az adott adattárolóban tárolt adatmennyiséget. Ha például az adatszolgáltató az Azure Blob Storage használatával oszt meg adatmegosztást, az adatfogyasztó ezeket az adatAzure Data Lake Storet is megkapja. Hasonlóképpen, ha az adatszolgáltató az Azure szinapszis Analyticsből származó adatokkal osztozik, az adatfogyasztó kiválaszthatja, hogy szeretné-e fogadni az adatait egy Azure Data Lake Storeba, egy Azure SQL Database vagy egy Azure szinapszis Analyticsbe. SQL-alapú forrásokból való megosztás esetén az adatok fogyasztója azt is kiválaszthatja, hogy az adatok a parketta vagy a CSV szolgáltatásban érkeznek-e. 

A helyszíni megosztással az adatszolgáltatók megoszthatják az adatok másolását az adatok másolása nélkül. Miután megtörténik a megosztási kapcsolat létrehozása a Meghívási folyamaton keresztül, a rendszer egy szimbolikus hivatkozást hoz létre az adatszolgáltató forrás-adattára és az adatfogyasztó céljának adattára között. Az adatfogyasztók valós időben tudják olvasni és lekérdezni az adataikat a saját adattárával. A forrás adattár változásai azonnal elérhetők az adatfogyasztó számára. A helyszíni megosztás jelenleg az Azure Adatkezelő számára érhető el.

## <a name="key-capabilities"></a>Főbb képességek

Az Azure-adatmegosztás lehetővé teszi az adatszolgáltatók számára a következőket:

* Adatok megosztása a szervezeten kívüli ügyfelekkel és partnerekkel [támogatott adattárak](supported-data-stores.md) listájából

* Nyomon követheti, hogy ki osztotta meg az adatait a

* Pillanatkép vagy helyi megosztás kiválasztása

* Az adatfogyasztók milyen gyakran kapnak frissítéseket az adataihoz

* Lehetővé teheti ügyfelei számára, hogy igény szerint lekérje az adataik legújabb verzióját, vagy az Ön által meghatározott időközönként automatikusan megkapják az adataik növekményes módosításait

Az Azure-adatmegosztás lehetővé teszi az adatfogyasztók számára a következőket: 

* A megosztva tárolt adattípusok leírásának megtekintése

* Az adathasználati feltételek megtekintése

* Azure-beli adatmegosztási meghívás elfogadása vagy elutasítása

* Fogadja el az Önnel megosztott adatmegosztást egy [támogatott adattárba](supported-data-stores.md).

* Adatok elérése helyben vagy a megosztott adatok teljes vagy növekményes pillanatképének kiváltása

A fent felsorolt főbb képességek a Azure Portalon vagy a REST API-kon keresztül támogatottak. További információ az Azure-beli adatmegosztás REST API-kkal való használatáról: dokumentáció. 

## <a name="supported-regions"></a>Támogatott régiók

Az Azure-beli adatmegosztást használó Azure-régiók [listáját az Azure](https://azure.microsoft.com/global-infrastructure/services/?products=data-share) -beli adatmegosztást ismertető oldalon tekintheti meg és keresheti meg. 

Az Azure-beli adatmegosztások által tárolt metaadatok esetében Délkelet-Ázsiában (Szingapúr) a régión belül és minden más támogatott régió esetében a Geo tárolja azt. Az Azure-beli adatmegosztás nem tárolja a megosztott adatmennyiség másolatát. Az adatokat a rendszer az alapul szolgáló adattár tárolja. Ha például egy adatszolgáltató egy, az USA nyugati régiójában található Azure Data Lake Storage-fiókban tárolja az adattárat, akkor az adattárolás helye. Ha az adatok megosztása egy Nyugat-Európában található Azure Storage-fiókkal történik a pillanatképen keresztül, általában az adatok közvetlenül a Nyugat-Európában található Azure Storage-fiókba kerülnek. 

Az Azure-beli adatmegosztási szolgáltatásnak nem kell elérhetőnek lennie a régióban a szolgáltatás kihasználása érdekében. Ha például egy olyan régióban található Azure Storage-fiókban tárolt adatai vannak, amelyben az Azure-adatmegosztás még nem érhető el, akkor továbbra is használhatja a szolgáltatást az adatmegosztáshoz. 

## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.
