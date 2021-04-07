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
ms.openlocfilehash: d1dc108ecec93dddeb768eb61af425ba67f23002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393139"
---
# <a name="continuous-backup-with-point-in-time-restore-preview-feature-in-azure-cosmos-db"></a>Folyamatos biztonsági mentés az időponthoz tartozó visszaállítás (előzetes verzió) szolgáltatással Azure Cosmos DB
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> A Azure Cosmos DB időponthoz tartozó visszaállítási funkciója (folyamatos biztonsági mentési mód) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB az időponthoz tartozó visszaállítási funkció (előzetes verzió) több forgatókönyvben is segítséget nyújt, például a következőket:

* Egy tárolón belüli véletlen írási vagy törlési műveletből való helyreállításhoz.
* Törölt fiók, adatbázis vagy tároló visszaállítása.
* Bármely régióba (ahol a biztonsági másolatok léteztek) visszaállíthatók a visszaállítási időpontban.

Azure Cosmos DB végrehajtja az adatok biztonsági mentését a háttérben anélkül, hogy külön kiosztott átviteli sebességet (RUs) kellene használnia, és nem befolyásolja az adatbázis teljesítményét és rendelkezésre állását. A folyamatos biztonsági mentés minden olyan régióban folyamatban van, ahol a fiók létezik. Az alábbi képen látható, hogy egy, az USA nyugati régiójában található írási régióval rendelkező tároló, a keleti és az USA 2. keleti régiójában található régiók biztonsági mentése egy távoli Azure Blob Storage-fiókba történik a megfelelő régiókban. Alapértelmezés szerint minden egyes régió helyileg redundáns tárolási fiókokban tárolja a biztonsági mentést. Ha a régión engedélyezve vannak a [rendelkezésre állási zónák](high-availability.md#availability-zone-support) , akkor a biztonsági mentést Zone-Redundant Storage-fiókokban tárolja a rendszer.

:::image type="content" source="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" alt-text="Azure Cosmos DB az adatbiztonsági mentést az Azure Blob Storageba." lightbox="./media/continuous-backup-restore-introduction/continuous-backup-restore-blob-storage.png" border="false":::

A visszaállításhoz rendelkezésre álló időablak (más néven megőrzési időtartam) a következő két: *30 nap vissza a múltban* , vagy *akár az erőforrás-létrehozási időpontig*. A visszaállítás időpontjának időbélyege a megőrzési időtartamon belül lehet.

A nyilvános előzetes verzióban visszaállíthatja a Azure Cosmos DB fiókot az SQL API-hoz, vagy MongoDB a tartalom időpontját egy másik fiókhoz a [Azure Portal](continuous-backup-restore-portal.md), az [Azure parancssori felület](continuous-backup-restore-command-line.md) (a CLI), a [Azure PowerShell](continuous-backup-restore-powershell.md)vagy a [Azure Resource Manager](continuous-backup-restore-template.md)használatával.

## <a name="what-is-restored"></a>Mi lesz visszaállítva?

Állandó állapot esetén a forrásoldali fiókon végrehajtott összes mutáció (beleértve az adatbázisokat, tárolókat és elemeket is) a rendszer aszinkron módon készít biztonsági mentést 100 másodpercen belül. Ha a biztonsági mentési adathordozó (azaz az Azure Storage) nem érhető el vagy nem érhető el, a mutációk helyileg megmaradnak, amíg az adathordozó vissza nem áll, és a rendszer kiüríti azokat, hogy megakadályozza a visszaállítható műveletek megbízhatóságának elvesztését.

A kiosztott átviteli tárolókat, a megosztott átviteli adatbázist és a teljes fiókot bármilyen kombinációban visszaállíthatja. A visszaállítási művelet visszaállítja az összes adatot és az indextulajdonságokat egy új fiókba. A visszaállítási folyamat biztosítja, hogy egy fiókban, adatbázisban vagy tárolóban visszaállított összes adat konzisztens legyen a megadott visszaállítási időpontig. A visszaállítás időtartama a visszaállítandó adatok mennyiségétől függ.

> [!NOTE]
> A folyamatos biztonsági mentési móddal a biztonsági mentések minden olyan régióban megtalálhatók, ahol a Azure Cosmos DB-fiók elérhető. Az egyes régiókban tárolt biztonsági mentések alapértelmezés szerint helyileg redundánsak, és a zóna redundáns, ha a fiókjában engedélyezve van a [rendelkezésre állási zóna](high-availability.md#availability-zone-support) funkció az adott régióban. A visszaállítási művelet mindig új fiókba állítja vissza az adattárolást.

## <a name="what-is-not-restored"></a>Mit nem érint a visszaállítás?

A következő konfigurációk nem állíthatók vissza az időponthoz tartozó helyreállítás után:

* Tűzfal, VNET, magánhálózati végpont beállításai.
* Konzisztenciabeállítások. Alapértelmezés szerint a fiók visszaállítása munkamenet-konzisztenciával történik.  
* Régiók.
* Tárolt eljárások, eseményindítók, UDF.

A visszaállítás befejeződése után ezeket a konfigurációkat a visszaállított fiókhoz is hozzáadhatja.

## <a name="restore-scenarios"></a>Visszaállítási forgatókönyvek

Az alábbiakban az adott időponthoz tartozó visszaállítási funkció által tárgyalt főbb forgatókönyvek találhatók. Az [a] és a [c] közötti forgatókönyvek azt mutatják be, hogyan lehet visszaállítani a visszaállítást, ha a visszaállítási időbélyeget előre ismeri.
Előfordulhatnak azonban olyan helyzetek, amikor nem ismeri a véletlen törlés vagy sérülés pontos idejét. A [d] és az [e] forgatókönyvek azt mutatják be, hogyan _derítheti fel_ a visszaállítási időbélyeget az új Event feed API-k használatával a helyreállítható adatbázison vagy tárolón.

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" alt-text="A visszaállítható fiókok időbélyegeit tartalmazó életciklus-események." lightbox="./media/continuous-backup-restore-introduction/restorable-account-scenario.png" border="false":::

a. **Törölt fiók visszaállítása** – a visszaállítani kívánt törölt fiókok a **visszaállítás** ablaktáblán láthatók. Például, ha az *A fiók* törölve van az időbélyeg T3-as időbélyegzővel. Ebben az esetben az időbélyegzőt közvetlenül a T3, a hely, a célkiszolgáló neve, az erőforráscsoport és a célalkalmazás neve elegendő a [Azure Portal](continuous-backup-restore-portal.md#restore-deleted-account), a [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)vagy a [parancssori](continuous-backup-restore-command-line.md#trigger-restore)felületről történő visszaállításhoz.  

:::image type="content" source="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" alt-text="A helyreállítható adatbázisok és tárolók időbélyegzőit tartalmazó életciklus-események." lightbox="./media/continuous-backup-restore-introduction/restorable-container-database-scenario.png" border="false":::

b. Egy **adott régióban lévő fiók adatait állíthatja vissza** – például ha az *a fiók* létezik az *USA keleti* régiójában és az USA *nyugati* régiójában, az időbélyeg T3-ban. Ha az *USA nyugati* régiójában az a fiók egy példányára van szüksége, akkor [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)vagy [CLI](continuous-backup-restore-command-line.md#trigger-restore) használatával visszaállíthat egy időpontot, amely az USA nyugati régiója.

c. Egy **ismert visszaállítási időbélyegzővel rendelkező tárolóban lévő véletlen írási vagy törlési műveletből való helyreállítás** – például, **Ha biztos benne, hogy az** 1. *adatbázis* 1. *tárolójának* tartalmát véletlenül módosították a T3-as időbélyegzőn. A [Azure Portal](continuous-backup-restore-portal.md#restore-live-account), a [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)vagy a [CLI](continuous-backup-restore-command-line.md#trigger-restore) egy időpontját egy másik fiókba is elvégezheti, ha az időbélyeg T3-ban helyreállítja a kívánt állapotot a tárolóban.

d. Egy **fiók visszaállítása egy korábbi időpontra az adatbázis véletlen törlése előtt** – a [Azure Portalban](continuous-backup-restore-portal.md#restore-live-account)az esemény-hírcsatorna ablaktáblán meghatározhatja, hogy mikor lett törölve egy adatbázis, és megtalálja a visszaállítási időt. Hasonlóképpen, az [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) és a [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)használatával felderítheti az adatbázis-törlési eseményt az adatbázis-események hírcsatornájának enumerálásával, majd a szükséges paraméterekkel aktiválhatja a Restore parancsot.

e. **Egy fiók visszaállítása egy korábbi időpontra, mielőtt a tároló tulajdonságainak véletlen törlése vagy módosítása megtörtént.** – [Azure Portal](continuous-backup-restore-portal.md#restore-live-account)az esemény-hírcsatorna ablaktáblán meghatározhatja, hogy a rendszer mikor hozta létre, módosította vagy törölte a tárolót, hogy megtalálja a visszaállítási időt. Hasonlóképpen, az [Azure CLI](continuous-backup-restore-command-line.md#trigger-restore) és a [PowerShell](continuous-backup-restore-powershell.md#trigger-restore)segítségével az összes tároló eseményt felderítheti a Container Events-hírcsatorna enumerálásával, majd a szükséges paraméterekkel aktiválja a Restore parancsot.

## <a name="permissions"></a>Engedélyek

Azure Cosmos DB lehetővé teszi a folyamatos biztonsági mentési fiókra vonatkozó visszaállítási engedélyek elkülönítését és korlátozását egy adott szerepkör vagy rendszerbiztonsági tag számára. A fiók tulajdonosa aktiválhatja a visszaállítást, és hozzárendelhet egy szerepkört a többi résztvevőhöz a visszaállítási művelet elvégzéséhez. További információt az [engedélyek](continuous-backup-restore-permissions.md) című cikkben talál.

## <a name="pricing"></a><a id="continuous-backup-pricing"></a>Díjszabás

Azure Cosmos DB a folyamatos biztonsági mentést engedélyező fiókok esetében további havi díjat kell fizetnie *a biztonsági mentés tárolásához* és az *adatai visszaállításához*. A visszaállítási költségeket a rendszer minden alkalommal hozzáadja a visszaállítási művelet elindításához. Ha folyamatos biztonsági mentést tartalmazó fiókot konfigurál, de nem állítja vissza az adatait, a számlán csak a biztonsági másolatok tárolási költségeit számítjuk fel.

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

* A folyamatos biztonsági mentés esetében kizárólag az SQL-hez és a MongoDB-hez készült Azure Cosmos DB API-k támogatottak. A Cassandra, Table és Gremlin API-k még nem támogatottak.

* Az alapértelmezett időszakos biztonsági mentési házirenddel rendelkező meglévő fiók nem alakítható át folyamatos biztonsági mentési mód használatára.

* Az Azure szuverén és Azure Government felhőalapú régiói még nem támogatottak.

* Az ügyfél által felügyelt kulcsokkal rendelkező fiókok használata nem támogatott a folyamatos biztonsági mentéshez.

* A többrégiós írási fiókok nem támogatottak.

* A szinapszis-hivatkozással rendelkező fiókok használata nem támogatott.

* A visszaállított fiók ugyanabban a régióban jön létre, ahol a forrásfiók található. A fiók nem állítható vissza olyan régióba, ahol a forrásfiók nem létezett.

* A visszaállítási ablak csak 30 nap, és nem módosítható.

* A biztonsági mentések nem védettek automatikusan a katasztrófákkal szemben. Explicit módon fel kell vennie egy másik régiót, hogy rugalmassá tegye a fiókot és a biztonsági mentést.

* A visszaállítás folyamatban van, ne módosítsa vagy törölje azokat az identitás-és hozzáférés-kezelési (IAM) szabályzatokat, amelyek engedélyeket biztosítanak a fiók engedélyeinek, vagy megváltoztathatják a VNET, a tűzfal konfigurációját.

* Azok az SQL-hez vagy MongoDB-hez készült Azure Cosmos DB API-fiókok, amelyek a tároló létrehozása után egyedi indexet hoznak létre, nem támogatottak a folyamatos biztonsági mentés esetében. Csak olyan tárolók támogatottak, amelyek a tároló kezdeti létrehozásának részeként hoznak létre egyedi indexet. MongoDB-fiókok esetében egyedi indexet hoz létre a [bővítmény-parancsok](mongodb-custom-commands.md)használatával.

* Az időponthoz kötött visszaállítási funkció esetében a visszaállítás mindig új Azure Cosmos-fiókba történik. A már meglévő fiókba történő visszaállítás jelenleg nem támogatott. Ha szeretné, hogy visszajelzést kapjon a helyi visszaállításról, forduljon a Azure Cosmos DB csapatához a fiók képviselőjétől vagy a [UserVoice](https://feedback.azure.com/forums/263030-azure-cosmos-db)keresztül.

* A, a, a, a, a, a `RestorableDatabaseAccount` és a ( `RestorableSqlDatabases` `RestorableSqlContainer` `RestorableMongodbDatabase` `RestorableMongodbCollection`

* A visszaállítást követően előfordulhat, hogy bizonyos gyűjtemények esetében az konzisztens index újraépíthető. Az újraépítési művelet állapotát a [IndexTransformationProgress](how-to-manage-indexing-policy.md) tulajdonság segítségével tekintheti meg.

* Az újjáépítési művelet visszaállítja a tároló összes tulajdonságát, beleértve az élettartam (TTL) konfigurációját is. Ennek következtében előfordulhat, hogy a visszaállított adatok azonnal törlődnek, ha Ön úgy konfigurálta. Ennek elkerülése érdekében a visszaállítási időbélyegnek korábbinak kell lennie a TTL-tulajdonságok tárolóba való felvételének időpontjánál.

## <a name="next-steps"></a>Következő lépések

* A folyamatos biztonsági mentés konfigurálása és kezelése [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [parancssori](continuous-backup-restore-command-line.md)felület vagy [Azure Resource Manager](continuous-backup-restore-template.md)használatával.
* [Kezelheti](continuous-backup-restore-permissions.md) az adathelyreállításhoz szükséges engedélyeket a folyamatos biztonsági mentési módban.
* [Folyamatos biztonsági mentési mód erőforrás-modellje](continuous-backup-restore-resource-model.md)
