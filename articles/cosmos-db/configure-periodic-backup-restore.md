---
title: Azure Cosmos DB fiók konfigurálása rendszeres biztonsági mentéssel
description: Ez a cikk azt ismerteti, hogyan konfigurálható Azure Cosmos DB-fiókok rendszeres biztonsági mentéssel a biztonsági mentési intervallummal. és megőrzés. Azt is megtudhatja, hogyan lehet a támogatási szolgálattal visszaállítani az adatait.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 69a9f0a82f5c19504564825e47f69ab8414e0909
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565835"
---
# <a name="configure-azure-cosmos-db-account-with-periodic-backup"></a>Azure Cosmos DB fiók konfigurálása rendszeres biztonsági mentéssel
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Azure Cosmos DB rendszeres időközönként automatikusan biztonsági másolatot készít az adatokról. Az automatikus biztonsági mentések az adatbázis-műveletek teljesítményét vagy rendelkezésre állását nem befolyásolják. Az összes biztonsági mentés külön tárolódik a tárolási szolgáltatásokban, és ezek a biztonsági másolatok globálisan replikálódnak a regionális katasztrófák elleni rugalmasságra. A Azure Cosmos DB, nem csupán az adatai, hanem az adatbiztonsági másolatok is nagyon redundánsak, és a regionális katasztrófák miatt rugalmasak. A következő lépések bemutatják, hogyan végzi el a Azure Cosmos DB az adatok biztonsági mentését:

* A Azure Cosmos DB 4 óránként automatikusan teljes biztonsági másolatot készít az adatbázisról, és a rendszer minden időpontban csak a legújabb két biztonsági mentést tárolja. Ha az alapértelmezett intervallumok nem elégségesek a számítási feladatokhoz, módosíthatja a biztonsági mentés időközét és a megőrzési időszakot a Azure Portal. A biztonsági mentési konfigurációt az Azure Cosmos-fiók létrehozásakor vagy azt követően is módosíthatja. Ha törli a tárolót vagy az adatbázist, a Azure Cosmos DB 30 napig megőrzi egy adott tároló vagy adatbázis meglévő pillanatképeit.

* Azure Cosmos DB tárolja ezeket a biztonsági másolatokat az Azure Blob Storage-ban, míg a tényleges adatok helyileg, Azure Cosmos DB belül találhatók.

* A kis késleltetés garantálása érdekében a biztonsági mentés pillanatképét az Azure Blob Storage tárolja ugyanabban a régióban, mint az aktuális írási régió (vagy az írási régiók **egyike** ), ha többrégiós írási konfigurációval rendelkezik. A regionális katasztrófákkal szembeni rugalmasság érdekében a rendszer az Azure Blob Storage-ban tárolt biztonsági mentési adatok minden pillanatképét újrareplikálja egy másik régióba a georedundáns tárolás (GRS) használatával. A biztonsági másolat replikálási régiója függ a forrásrégiótól és a forrásrégióhoz társított régiópártól. További információért tekintse meg a [geo-redundáns párok listáját az Azure-régiókról](../best-practices-availability-paired-regions.md) szóló cikkben. Ezt a biztonsági másolatot közvetlenül nem érheti el. Az Azure Cosmos DB csapatától támogatási kérésen keresztül igényelheti a biztonsági másolat visszaállítását.

  Az alábbi képen látható, hogy az USA nyugati régiójában lévő három elsődleges fizikai partícióval rendelkező Azure Cosmos-tároló hogyan készül biztonsági mentésben egy távoli Azure Blob Storage-fiókban az USA nyugati régiójában, majd replikálva az USA keleti régiójába:

  :::image type="content" source="./media/configure-periodic-backup-restore/automatic-backup.png" alt-text="A GRS Azure Storage összes Cosmos DB entitásának rendszeres teljes biztonsági mentése." lightbox="./media/configure-periodic-backup-restore/automatic-backup.png" border="false":::

* A biztonsági mentéseket az alkalmazás teljesítményének vagy rendelkezésre állásának befolyásolása nélkül kell elvégezni. Azure Cosmos DB végrehajtja az adatok biztonsági mentését a háttérben anélkül, hogy külön kiosztott átviteli sebességet (RUs) kellene használnia, és nem befolyásolja az adatbázis teljesítményét és rendelkezésre állását.

## <a name="modify-the-backup-interval-and-retention-period"></a><a id="configure-backup-interval-retention"></a>A biztonsági mentés intervallumának és megőrzési idejének módosítása

Azure Cosmos DB automatikusan teljes biztonsági másolatot készít az adatairól 4 óránként és bármikor, a legújabb két biztonsági mentést tárolja. Ez a konfiguráció az alapértelmezett beállítás, és többletköltség nélkül elérhető. Az Azure Cosmos-fiók létrehozásakor vagy a fiók létrehozása után megváltoztathatja az alapértelmezett biztonsági mentési időközt és a megőrzési időtartamot. A biztonsági mentési konfiguráció az Azure Cosmos-fiók szintjén van beállítva, és minden fióknál külön konfigurálnia kell. Miután konfigurálta egy fiók biztonsági mentési beállításait, a rendszer az adott fiókban lévő összes tárolóra alkalmazza. A biztonsági mentési beállításokat jelenleg csak az Azure Portalon változtathatja meg.

Ha véletlenül törölte vagy megsérült az adatai, az **adatok visszaállítására vonatkozó támogatási kérelem létrehozása előtt győződjön meg arról, hogy a fiók biztonsági mentése legalább hét napig megnövekszik. Az eseménytől számított 8 órán belül növelheti az adatmegőrzést.** Így az Azure Cosmos DB csapatának elég ideje lesz a fiók visszaállítására.

A következő lépésekkel módosíthatja egy meglévő Azure Cosmos-fiók alapértelmezett biztonsági mentési beállításait:

1. Jelentkezzen be a [Azure Portalba.](https://portal.azure.com/)
1. Navigáljon az Azure Cosmos-fiókjához, és nyissa meg a **biztonsági mentés & visszaállítás** panelt. Frissítse a biztonsági mentési időközt és a biztonsági mentés megőrzési időtartamát igény szerint.

   * **Biztonsági mentés időköze** – ez az az időtartam, amikor a Azure Cosmos db megkísérel biztonsági másolatot készíteni az adatairól. A biztonsági mentés nem nulla időt vesz igénybe, és bizonyos esetekben előfordulhat, hogy az alsóbb rétegbeli függőségek miatt sikertelen lesz. Azure Cosmos DB megpróbálja a legjobb biztonsági mentést készíteni a beállított időközönként, azonban nem garantálja, hogy a biztonsági mentés az adott időintervallumon belül befejeződik. Ezt az értéket órában vagy percben is konfigurálhatja. A biztonsági mentési időköz nem lehet kevesebb, mint 1 óra, és 24 óránál hosszabb. Ha megváltoztatja ezt az időközt, az új intervallum az utolsó biztonsági mentés időpontjától kezdve lép érvénybe.

   * **Biztonsági mentés megőrzése** – azt a pontot jelöli, amelyben az egyes biztonsági másolatok megmaradnak. Azt órákban vagy napokban is konfigurálhatja. A minimális megőrzési időtartam nem lehet kevesebb, mint a biztonsági mentési időköz (órában), és nem lehet nagyobb, mint 720 óra.

   * **Megőrzött Adatmásolatok** – alapértelmezés szerint a rendszer díjmentesen két biztonsági másolatot készít az adatairól. Ha kettőnél több példányra van szüksége, külön díjat számítunk fel. A további másolatok pontos árának megismeréséhez tekintse meg a [díjszabási oldal](https://azure.microsoft.com/pricing/details/cosmos-db/) felhasznált tároló szakaszát.

   :::image type="content" source="./media/configure-periodic-backup-restore/configure-backup-interval-retention.png" alt-text="Egy meglévő Azure Cosmos-fiók biztonsági mentési intervallumának és megőrzésének konfigurálása." border="true":::

Ha a fiók létrehozása során konfigurálja a biztonsági mentési beállításokat, beállíthatja a **biztonsági mentési szabályzatot**, amely akár **rendszeres** , akár **folyamatos**. Az időszakos házirend lehetővé teszi a biztonsági mentés intervallumának és a biztonsági másolatok megőrzésének konfigurálását. A folyamatos házirend jelenleg csak a regisztráláskor érhető el. A Azure Cosmos DB csapat felméri a munkaterhelést, és jóváhagyja a kérést.

:::image type="content" source="./media/configure-periodic-backup-restore/configure-periodic-continuous-backup-policy.png" alt-text="Rendszeres vagy folyamatos biztonsági mentési szabályzatot konfigurálhat az új Azure Cosmos-fiókokhoz." border="true":::

## <a name="request-data-restore-from-a-backup"></a><a id="request-restore"></a>Adatok visszaállításának kérése biztonsági másolatból

Ha véletlenül törli az adatbázist vagy egy tárolót, [egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) is betölthet, vagy [meghívhatja az Azure-támogatást](https://azure.microsoft.com/support/options/) , hogy visszaállítsa az adatokat az automatikus online biztonsági mentésből. Az Azure-támogatás csak olyan kiválasztott csomagokhoz érhető el, mint a **standard**, a **fejlesztői** és a magasabb szintű csomagok. Az Azure-támogatás nem érhető el **alapszintű** csomaggal. A különböző támogatási csomagokról az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/) oldalon tájékozódhat.

A biztonsági mentés egy adott pillanatképének visszaállításához Azure Cosmos DB szükséges, hogy az adatok elérhetők legyenek az adott pillanatkép biztonsági mentési ciklusa során.
A visszaállítás kérelmezése előtt a következő adatokat kell megkapnia:

* Készítse elő az előfizetés-AZONOSÍTÓját.

* Az adatok véletlen törlése vagy módosítása alapján készüljön fel további információkra. Javasoljuk, hogy a rendelkezésre álló információk alapján csökkentse a háttér-és a korlátot, amely bizonyos időérzékeny esetekben hátrányos lehet.

* Ha a teljes Azure Cosmos DB fiókot törli, meg kell adnia a Törölt fiók nevét. Ha egy olyan fiókot hoz létre, amelynek a neve megegyezik a Törölt fiók nevével, ossza meg a támogatási csapattal, mert ez segít meghatározni a megfelelő fiókot. Javasoljuk, hogy minden egyes törölt fiókhoz különböző támogatási jegyeket kelljen benyújtani, mivel ez a művelet lekicsinyíti a visszaállítási állapotot.

* Ha egy vagy több adatbázis törölve van, adja meg az Azure Cosmos-fiókot, valamint az Azure Cosmos-adatbázis nevét, és adja meg, hogy létezik-e új adatbázis ugyanazzal a névvel.

* Ha egy vagy több tároló törölve van, adja meg az Azure Cosmos-fiók nevét, az adatbázis nevét és a tároló nevét. És adja meg, hogy létezik-e azonos nevű tároló.

* Ha véletlenül törölte vagy megsérült az adatai, akkor 8 órán belül kapcsolatba kell lépnie az [Azure támogatási szolgálatával](https://azure.microsoft.com/support/options/) , hogy a Azure Cosmos db csapat segítséget nyújtson a biztonsági másolatokból származó adatok visszaállításához. **Mielőtt támogatási kérelmet hozna létre az adatok visszaállításához, ügyeljen arra, hogy legalább hét napig [növelje a fiók biztonsági mentésének megőrzését](#configure-backup-interval-retention) . Az eseménytől számított 8 órán belül növelheti az adatmegőrzést.** Így a Azure Cosmos DB támogatási csapatnak elég ideje lesz a fiókja visszaállítására.

Az Azure Cosmos-fiók neve, az adatbázis neve és a tároló neve mellett adja meg azt az időpontot is, ameddig az adatok visszaállíthatók. Fontos, hogy a lehető legpontosabban lehessen megállapítani az elérhető legjobb biztonsági mentéseket. **Azt is fontos, hogy az időpontot UTC szerint határozza meg.**

Az alábbi képernyőfelvétel azt szemlélteti, hogyan lehet támogatási kérést létrehozni egy tárolóhoz (gyűjtemény/gráf/tábla) az adatAzure Portal használatával történő visszaállításához. Adja meg az egyéb részleteket, például az adattípust, a visszaállítás célját, az adatok törlésének időpontját, hogy segítsen a kérés rangsorolásában.

:::image type="content" source="./media/configure-periodic-backup-restore/backup-support-request-portal.png" alt-text="Hozzon létre egy biztonsági mentési támogatási kérelmet Azure Portal használatával." border="true":::

## <a name="considerations-for-restoring-the-data-from-a-backup"></a>Az adatok biztonsági másolatból való visszaállításának szempontjai

Az alábbi helyzetekben véletlenül törölheti vagy módosíthatja az adatait:  

* Törölje a teljes Azure Cosmos-fiókot.

* Töröljön egy vagy több Azure Cosmos-adatbázist.

* Töröljön egy vagy több Azure Cosmos-tárolót.

* Az Azure Cosmos-elemek (például dokumentumok) törlése vagy módosítása egy tárolón belül. Ezt a konkrét esetet általában adatsérülésnek nevezzük.

* A megosztott ajánlati adatbázisban lévő megosztott ajánlat-adatbázis vagy-tárolók törlődnek vagy sérültek.

A Azure Cosmos DB az összes fenti helyzetben képes visszaállítani az adatok visszaállítását. Visszaállításkor új Azure Cosmos-fiók jön létre a visszaállított adattároláshoz. Ha nincs megadva az új fiók neve, a formátuma lesz `<Azure_Cosmos_account_original_name>-restored1` . Az utolsó számjegy akkor nő, amikor több visszaállítást próbálnak meg. Egy előre létrehozott Azure Cosmos-fiókba nem állíthatók vissza az adathalmazok.

Ha véletlenül töröl egy Azure Cosmos-fiókot, visszaállíthatja az adathalmazt egy azonos nevű új fiókba, ha a fiók neve nincs használatban. Ezért azt javasoljuk, hogy a törlés után ne hozza újra létre a fiókot. Mivel ez nem csak azt akadályozza meg, hogy a visszaállított adatok ugyanazt a nevet használják, de a megfelelő fiókot is felhasználja a nehéz helyreállításhoz.

Ha véletlenül töröl egy Azure Cosmos-adatbázist, visszaállíthatja a teljes adatbázist vagy a tárolók egy részhalmazát az adatbázisban. Az adatbázisokban is kiválaszthat meghatározott tárolókat, és visszaállíthatja őket egy új Azure Cosmos-fiókba.

Ha véletlenül töröl vagy módosít egy vagy több elemet egy tárolón belül (az adatsérülési eset), meg kell adnia a visszaállításhoz szükséges időt. Az idő fontos, ha adatsérülés van. Mivel a tároló él, a biztonsági mentés továbbra is fut, így ha a megőrzési időszakon túl várakozik (az alapértelmezett érték nyolc óra), a rendszer felülírja a biztonsági mentéseket. Ha meg szeretné akadályozni, hogy a biztonsági mentés felül legyen írva, növelje a fiók biztonsági mentését legalább hét napig. A legjobb, ha az adatsérüléstől számított 8 órán belül növelje az adatmegőrzést.

Ha véletlenül törölte vagy megsérült az adatai, akkor 8 órán belül kapcsolatba kell lépnie az [Azure támogatási szolgálatával](https://azure.microsoft.com/support/options/) , hogy a Azure Cosmos db csapat segítséget nyújtson a biztonsági másolatokból származó adatok visszaállításához. Így a Azure Cosmos DB támogatási csapatnak elég ideje lesz a fiókja visszaállítására.

> [!NOTE]
> Az adat visszaállítása után a rendszer nem az összes forrás-képességet vagy beállítást a visszaállított fiókra hajtja végre. A következő beállítások nem kerülnek át az új fiókra:
> * VNET hozzáférés-vezérlési listája
> * Tárolt eljárások, eseményindítók és felhasználó által definiált függvények
> * Több régióra vonatkozó beállítások  

Ha az átviteli sebességet az adatbázis szintjén adja meg, a biztonsági mentési és visszaállítási folyamat ebben az esetben a teljes adatbázis szintjén történik, és nem az egyes tárolók szintjén. Ilyen esetekben nem választhatja ki a visszaállítani kívánt tárolók részhalmazát.

## <a name="required-permissions-to-change-retention-or-restore-from-the-portal"></a>A portálon való megőrzés vagy visszaállítás megváltoztatásához szükséges engedélyek
A szerepkör [CosmosdbBackupOperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator), tulajdonosának vagy közreműködőinek részét képező rendszerbiztonsági tag jogosult a visszaállítás igénylésére vagy a megőrzési időtartam módosítására.

## <a name="understanding-costs-of-extra-backups"></a>További biztonsági másolatok költségeinek megismerése
Két biztonsági mentés ingyenes, és az extra biztonsági mentések díjszabása a [biztonsági mentési tár díjszabásában](https://azure.microsoft.com/en-us/pricing/details/cosmos-db/)ismertetett biztonsági mentési tár régió alapú díjszabása alapján történik. Például, ha a biztonsági másolat megőrzési beállítása 240 óra, azaz 10 nap, a biztonsági mentés időköze pedig 24 óra. Ez a biztonsági mentési adat 10 másolatát jelenti. Feltételezve, hogy az USA 2. nyugati régiójában 1 TB adat található, a díj a megadott hónapban 0,12 * 1000 * 8 lesz a biztonsági mentési tár számára. 


## <a name="options-to-manage-your-own-backups"></a>A saját biztonsági mentések kezelésére szolgáló beállítások

Azure Cosmos DB SQL API-fiókokkal a következő módszerek egyikével is megőrizheti saját biztonsági mentését:

* A [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) használatával időnként áthelyezheti az adatátvitelt az Ön által választott tárhelyre.

* A teljes biztonsági mentéshez, illetve a növekményes változtatásokhoz és a saját tárolóban történő tárolásához használja a Azure Cosmos DB [változási hírcsatorna](change-feed.md) használatát az adat rendszeres olvasásához.

## <a name="post-restore-actions"></a>Visszaállítás utáni műveletek

Az adatok visszaállításának elsődleges célja a véletlenül törölt vagy módosított adatok helyreállítása. Ezért javasoljuk, hogy először vizsgálja meg a helyreállított adatok tartalmát, hogy biztosan tartalmazza a várt adatokat. Ha minden jól látható, áttelepítheti az adatátvitelt az elsődleges fiókba. Habár lehetséges, hogy a visszaállított fiókot új aktív fiókként használja, ez nem ajánlott megoldás, ha éles számítási feladatokkal rendelkezik. 

Az adatvisszaállítás után értesítést kap az új fiók nevéről (általában a formátuma `<original-name>-restored1` ), valamint azt az időpontot, amikor a fiókot visszaállították. A visszaállított fióknak ugyanaz a kiosztott átviteli sebessége, az indexelési szabályzatok és az eredeti fiókkal azonos régióban kell lennie. Az előfizetés-rendszergazda vagy a rendszergazda láthatja a visszaállított fiókot.

### <a name="migrate-data-to-the-original-account"></a>Az adatáttelepítés az eredeti fiókba

Az alábbi módokon térhet vissza az eredeti fiókba:

* Használja a [Azure Cosmos db adatáttelepítési eszközt](import-data.md).
* Használja a [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Használja a [változási csatornát](change-feed.md) Azure Cosmos db.
* Írhat saját egyéni kódot is.

Javasoljuk, hogy azonnal törölje a tárolót vagy az adatbázist az áttelepítés után. Ha nem törli a visszaállított adatbázisokat vagy tárolókat, a kérések egysége, a tárterület és a kimenő forgalom költségeit is felszámítjuk.

## <a name="next-steps"></a>Következő lépések

* A visszaállítási kérelem elvégzéséhez forduljon az Azure ügyfélszolgálatához, és küldjön [egy jegyet a Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* A folyamatos biztonsági mentés konfigurálása és kezelése [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [parancssori](continuous-backup-restore-command-line.md)felület vagy [Azure Resource Manager](continuous-backup-restore-template.md)használatával.
* [Kezelheti](continuous-backup-restore-permissions.md) az adathelyreállításhoz szükséges engedélyeket a folyamatos biztonsági mentési módban.
