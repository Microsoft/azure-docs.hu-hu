---
title: Folyamatos biztonsági mentés a Azure Cosmos DB időponthoz tartozó visszaállítási funkciójával
description: Azure Cosmos DB időponthoz kapcsolódó visszaállítási funkciója segít helyreállítani az adatokat egy véletlen írási, törlési műveletből, vagy bármely régióba visszaállítani az adatokat. Ismerje meg a díjszabást és az aktuális korlátozásokat.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: references_regions
ms.openlocfilehash: f8ba08c6147320160e99e522536f00fc98855eb4
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527628"
---
# <a name="continuous-backup-with-point-in-time-restore-feature-in-azure-cosmos-db"></a>Folyamatos biztonsági mentés a Azure Cosmos DB időponthoz tartozó visszaállítási funkciójával
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB időponthoz kapcsolódó visszaállítási funkciója több forgatókönyvben is segít, például a következő esetekben:

* Egy tárolón belüli véletlen írási vagy törlési műveletből való helyreállításhoz.
* Törölt fiók, adatbázis vagy tároló visszaállítása.
* Bármely régióba (ahol a biztonsági másolatok léteztek) visszaállíthatók a visszaállítási időpontban.

Azure Cosmos DB végrehajtja az adatok biztonsági mentését a háttérben anélkül, hogy külön kiosztott átviteli sebességet (RUs) kellene használnia, és nem befolyásolja az adatbázis teljesítményét és rendelkezésre állását. A folyamatos biztonsági mentés minden olyan régióban folyamatban van, ahol a fiók létezik. Az alábbi képen látható, hogy egy, az USA nyugati régiójában található írási régióval rendelkező tároló, a keleti és az USA 2. keleti régiójában található régiók biztonsági mentése egy távoli Azure Blob Storage-fiókba történik a megfelelő régiókban. Alapértelmezés szerint minden egyes régió helyileg redundáns tárolási fiókokban tárolja a biztonsági mentést. Ha a régión engedélyezve vannak a [rendelkezésre állási zónák](high-availability.md#availability-zone-support) , akkor a biztonsági mentést Zone-Redundant Storage-fiókokban tárolja a rendszer.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Azure Cosmos DB az adatbiztonsági mentést az Azure Blob Storageba." lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

A visszaállításhoz rendelkezésre álló időablak (más néven megőrzési időtartam) a következő kettő alacsonyabb értéke: "30 nap vissza mostantól" vagy "az erőforrások létrehozásának ideje". A visszaállítás időpontjának időbélyege a megőrzési időtartamon belül lehet.

A nyilvános előzetes verzióban visszaállíthatja a Azure Cosmos DB fiókot az SQL API-hoz, vagy MongoDB a tartalom időpontját egy másik fiókhoz a [Azure Portal](continuous-backup-restore-portal.md), az [Azure parancssori felület](continuous-backup-restore-command-line.md) (a CLI), a [Azure PowerShell](continuous-backup-restore-powershell.md)vagy a [Azure Resource Manager](continuous-backup-restore-template.md)használatával.

## <a name="what-is-restored"></a>Mi a visszaállítva?

Állandó állapot esetén a forrásoldali fiókon végrehajtott összes mutáció (beleértve az adatbázisokat, tárolókat és elemeket is) a rendszer aszinkron módon készít biztonsági mentést 100 másodpercen belül. Ha a biztonsági mentési adathordozó (azaz az Azure Storage) nem érhető el vagy nem érhető el, a mutációk helyileg megmaradnak, amíg az adathordozó vissza nem áll, és a rendszer kiüríti azokat, hogy megakadályozza a visszaállítható műveletek megbízhatóságának elvesztését.

Dönthet úgy, hogy visszaállítja a kiosztott átviteli sebességű tárolók, a megosztott átviteli sebességű adatbázisok vagy a teljes fiók kombinációját. A visszaállítási művelet visszaállítja az összes és az index tulajdonságait egy új fiókba. A visszaállítási folyamat biztosítja, hogy egy fiókban, adatbázisban vagy tárolóban visszaállított összes érték konzisztens legyen a megadott visszaállítási időpontig. A visszaállítás időtartama a visszaállítani kívánt adatok mennyiségétől függ.

> [!NOTE]
> A folyamatos biztonsági mentési móddal a biztonsági mentések minden olyan régióban megtalálhatók, ahol a Azure Cosmos DB-fiók elérhető. Az egyes régiókban tárolt biztonsági mentések alapértelmezés szerint helyileg redundánsak, és a zóna redundáns, ha a fiókjában engedélyezve van a [rendelkezésre állási zóna](high-availability.md#availability-zone-support) funkció az adott régióban. A visszaállítási művelet mindig új fiókba állítja vissza az adattárolást.

## <a name="what-is-not-restored"></a>Mi nem állítható vissza?

A következő konfigurációk nem állíthatók vissza az időponthoz tartozó helyreállítás után:

* Tűzfal, VNET, magánhálózati végpont beállításai.
* Konzisztencia-beállítások. Alapértelmezés szerint a rendszer visszaállítja a fiókot a munkamenet konzisztenciájával.  
* Régiók.
* Tárolt eljárások, eseményindítók, UDF.

A visszaállítás befejeződése után ezeket a konfigurációkat a visszaállított fiókhoz is hozzáadhatja.

## <a name="restore-scenarios"></a>Visszaállítási forgatókönyvek

Az alábbiakban az adott időponthoz tartozó visszaállítási funkció által tárgyalt főbb forgatókönyvek találhatók. Az [a] és a [c] közötti forgatókönyvek azt mutatják be, hogyan lehet visszaállítani a visszaállítást, ha a visszaállítási időbélyeget előre ismeri. Előfordulhatnak azonban olyan helyzetek, amikor nem ismeri a véletlen törlés vagy sérülés pontos idejét. A [d] és az [e] forgatókönyvek azt mutatják be, hogyan _derítheti fel_ a visszaállítási időbélyeget az új Event feed API-k használatával a helyreállítható adatbázison vagy tárolón.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="A visszaállítható fiókok időbélyegeit tartalmazó életciklus-események." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

a. **Törölt fiók visszaállítása** – a visszaállítani kívánt törölt fiókok a **visszaállítás** ablaktáblán láthatók. Ha például a "fiók" törlődik az időbélyeg T3-as időpontjában. Ebben az esetben az időbélyegzőt közvetlenül a T3, a hely, a célkiszolgáló neve, az erőforráscsoport és a célalkalmazás neve elegendő a [Azure Portal](continuous-backup-restore-portal.md#restore-deleted-account), a [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)vagy a [parancssori](continuous-backup-restore-command-line.md)felületről történő visszaállításhoz.  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="A helyreállítható adatbázisok és tárolók időbélyegzőit tartalmazó életciklus-események." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

b. Egy **adott régióban lévő fiók adatait állíthatja vissza** – például ha "a fiók" létezik két régióban: "az USA keleti régiója" és az "USA nyugati régiója" az időbélyegző T3-on. Ha az "USA nyugati régiójában" lévő "A" fiók egy példányára van szüksége, akkor az USA nyugati régiójában, a célhelyen állíthatja be az időpontot [Azure Portal](continuous-backup-restore-portal.md), a [PowerShell](continuous-backup-restore-powershell.md)vagy a [CLI](continuous-backup-restore-command-line.md) használatával.

c. Egy **ismert visszaállítási időbélyegzővel rendelkező tárolóban lévő véletlen írási vagy törlési műveletből való helyreállítás** – például, **Ha biztos benne, hogy az** "1. adatbázis" tárolóban lévő "Container 1" tartalma véletlenül módosult az időbélyegzős T3-on. A [Azure Portal](continuous-backup-restore-portal.md), a [PowerShell](continuous-backup-restore-powershell.md)vagy a [CLI](continuous-backup-restore-command-line.md) egy időpontját egy másik fiókba is elvégezheti, ha az időbélyeg T3-ban helyreállítja a kívánt állapotot a tárolóban.

d. Egy **fiók visszaállítása egy korábbi időpontra az adatbázis véletlen törlése előtt** – a [Azure Portalban](continuous-backup-restore-portal.md)az esemény-hírcsatorna ablaktáblán meghatározhatja, hogy mikor lett törölve egy adatbázis, és megtalálja a visszaállítási időt. Hasonlóképpen, az [Azure CLI](continuous-backup-restore-command-line.md) és a [PowerShell](continuous-backup-restore-powershell.md)használatával felderítheti az adatbázis-törlési eseményt az adatbázis-események hírcsatornájának enumerálásával, majd a szükséges paraméterekkel aktiválhatja a Restore parancsot.

e. **Egy fiók visszaállítása egy korábbi időpontra, mielőtt a tároló tulajdonságainak véletlen törlése vagy módosítása megtörtént.** – [Azure Portal](continuous-backup-restore-portal.md)az esemény-hírcsatorna ablaktáblán meghatározhatja, hogy a rendszer mikor hozta létre, módosította vagy törölte a tárolót, hogy megtalálja a visszaállítási időt. Hasonlóképpen, az [Azure CLI](continuous-backup-restore-command-line.md) és a [PowerShell](continuous-backup-restore-powershell.md)segítségével az összes tároló eseményt felderítheti a Container Events-hírcsatorna enumerálásával, majd a szükséges paraméterekkel aktiválja a Restore parancsot.

## <a name="permissions"></a>Engedélyek

Azure Cosmos DB lehetővé teszi a folyamatos biztonsági mentési fiókra vonatkozó visszaállítási engedélyek elkülönítését és korlátozását egy adott szerepkör vagy rendszerbiztonsági tag számára. A fiók tulajdonosa aktiválhatja a visszaállítást, és hozzárendelhet egy szerepkört a többi résztvevőhöz a visszaállítási művelet elvégzéséhez. További információt az [engedélyek](continuous-backup-restore-permissions.md) című cikkben talál.

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>Díjszabás

Azure Cosmos DB a folyamatos biztonsági mentést engedélyező fiókok esetén további havi díjat számítunk fel a "biztonsági mentés tárolása" és az "adatvisszaállítás" céljából. A visszaállítási költségeket a rendszer minden alkalommal hozzáadja a visszaállítási művelet elindításához. Ha folyamatos biztonsági mentést tartalmazó fiókot konfigurál, de nem állítja vissza az adatait, a számlán csak a biztonsági másolatok tárolási költségeit számítjuk fel.

Az alábbi példa egy, az USA-ban nem kormányzati régióban üzembe helyezett Azure Cosmos-fiók árát veszi alapul. A díjszabás és a számítás a használt régiótól függően változhat, a legfrissebb díjszabási információkért tekintse meg a [Azure Cosmos db díjszabási oldalát](https://azure.microsoft.com/pricing/details/cosmos-db/) .

* A folyamatos biztonsági mentési szabályzattal engedélyezett fiókok esetében a biztonsági mentési tárterületre vonatkozó további havi díjat számítunk fel a következők szerint:

  $0,20/GB * adatméret GB-ban a fiókban * régiók száma

* Minden visszaállítási API meghívása egyszeri díjat számít fel. A díj az adat-visszaállítási mennyiség függvénye, amelyet a következőképpen számítunk fel:

  $0,15/GB * adatméret GB-ban.

Ha például 1 TB-nyi adat van két régióban, akkor:

* A biztonsági mentési tárolási díj kiszámítása a következőképpen történik: (1000 * 0,20 * 2) = $400 havonta

* A visszaállítási díj kiszámítása a következőképpen történik: (1000 * 0,15) = $150/visszaállítás

## <a name="current-limitations-public-preview"></a>Jelenlegi korlátozások (nyilvános előzetes verzió)

Az időponthoz tartozó visszaállítási funkció jelenleg nyilvános előzetes verzióban érhető el, és a következő korlátozásokkal rendelkezik:

* Csak az SQL és a MongoDB Azure Cosmos DB API-k támogatottak a folyamatos biztonsági mentéshez. A Cassandra, Table és Gremlin API-k még nem támogatottak.

* Az alapértelmezett időszakos biztonsági mentési házirenddel rendelkező meglévő fiók nem alakítható át folyamatos biztonsági mentési mód használatára.

* Az Azure szuverén és Azure Government felhőalapú régiói még nem támogatottak.

* Az ügyfél által felügyelt kulcsokkal rendelkező fiókok használata nem támogatott a folyamatos biztonsági mentéshez.

* A többrégiós írási fiókok nem támogatottak.

* A szinapszis-hivatkozással rendelkező fiókok használata nem támogatott.

* A visszaállított fiók ugyanabban a régióban jön létre, ahol a forrásoldali fiók létezik. A fiók nem állítható vissza olyan régióba, ahol a forrásoldali fiók nem létezik.

* A visszaállítási ablak csak 30 nap, és nem módosítható.

* A biztonsági mentések nem lesznek automatikusan leképezve. Explicit módon hozzá kell adnia egy másik régiót, hogy rugalmasságot biztosítson a fiókhoz és a biztonsági mentéshez.

* A visszaállítás folyamatban van, ne módosítsa vagy törölje azokat az identitás-és hozzáférés-kezelési (IAM) szabályzatokat, amelyek engedélyeket biztosítanak a fiók engedélyeinek, vagy megváltoztathatják a VNET, a tűzfal konfigurációját.

* Az olyan SQL-vagy MongoDB-fiókok Azure Cosmos DB API-k, amelyek egyedi indexet hoznak létre a tároló létrehozása után, nem támogatják a folyamatos biztonsági mentést. Csak olyan tárolók támogatottak, amelyek egyedi indexet hoznak létre a kezdeti tároló létrehozása során. MongoDB-fiókok esetében egyedi indexet hoz létre a [bővítmény-parancsok](mongodb-custom-commands.md)használatával.

* Az időponthoz tartozó visszaállítási funkció mindig új Azure Cosmos-fiókba áll vissza. Egy meglévő fiók visszaállítása jelenleg nem támogatott. Ha szeretné, hogy visszajelzést kapjon a helyi visszaállításról, forduljon a Azure Cosmos DB csapatához a fiók képviselőjétől vagy a [UserVoice](https://feedback.azure.com/forums/263030-azure-cosmos-db)keresztül.

* A, a, a, a, a, a `RestorableDatabaseAccount` és a ( `RestorableSqlDatabases` `RestorableSqlContainer` `RestorableMongodbDatabase` `RestorableMongodbCollection`

* A visszaállítást követően előfordulhat, hogy bizonyos gyűjtemények esetében az konzisztens index újraépíthető. Az újraépítési művelet állapotát a [IndexTransformationProgress](how-to-manage-indexing-policy.md) tulajdonság segítségével tekintheti meg.

* A visszaállítási folyamat visszaállítja egy tároló összes tulajdonságát, beleértve annak TTL-konfigurációját. Ennek eredményeképpen előfordulhat, hogy a rendszer azonnal törli a visszaállított adatkészletet, ha ezt a módszert konfigurálja. Ezen helyzet elkerülése érdekében a visszaállítási időbélyegnek az élettartam-tulajdonságok tárolóba való felvétele előtt kell lennie.

## <a name="next-steps"></a>Következő lépések

* A folyamatos biztonsági mentés konfigurálása és kezelése [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [parancssori](continuous-backup-restore-command-line.md)felület vagy [Azure Resource Manager](continuous-backup-restore-template.md)használatával.
* [Kezelheti](continuous-backup-restore-permissions.md) az adathelyreállításhoz szükséges engedélyeket a folyamatos biztonsági mentési módban.
* [Folyamatos biztonsági mentési mód erőforrás-modellje](continuous-backup-restore-resource-model.md)
