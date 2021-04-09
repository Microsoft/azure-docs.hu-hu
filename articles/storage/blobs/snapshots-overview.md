---
title: BLOB-Pillanatképek
titleSuffix: Azure Storage
description: Megtudhatja, hogyan működnek a blob-Pillanatképek, és hogyan történik a számlázásuk.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/02/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: debce0a1b4c09bb89cdceb1cd29e59e1976c939a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539152"
---
# <a name="blob-snapshots"></a>BLOB-Pillanatképek

A pillanatképek egy adott időpontban végrehajtott blob írásvédett verziója, amely egy adott időpontban történik.

> [!NOTE]
> A blob verziószámozása kiváló megoldást kínál a blob korábbi verzióinak karbantartására. További információ: [blob verziószámozása](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Tudnivalók a blob-pillanatképekről

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

A blob pillanatképe azonos az alap blobtal, azzal a különbséggel, hogy a blob URI-ja a blob URI-hoz fűzött **datetime** értékkel rendelkezik, hogy jelezze a pillanatkép készítésének időpontját. Ha például egy oldal blob URI `http://storagesample.core.blob.windows.net/mydrives/myvhd` -ja, a PILLANATKÉP URI-ja hasonló a következőhöz: `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` .

> [!NOTE]
> Minden pillanatkép megosztja az alap blob URI-JÁT. Az alap blob és a pillanatkép közötti egyetlen különbség a hozzáfűzött **datetime** érték.

A Blobok tetszőleges számú pillanatképet tartalmazhatnak. A pillanatképek mindaddig megmaradnak, amíg explicit módon nem törlik őket, vagy a [blob törlési](/rest/api/storageservices/delete-blob) műveletének részeként. Az alap blobhoz társított Pillanatképek enumerálásával nyomon követheti az aktuális pillanatképeket.

Egy blob pillanatképének létrehozásakor a rendszer a blob rendszertulajdonságait a pillanatképre másolja, és ugyanazokat az értékeket. Az alap blob metaadatait a rendszer a pillanatképbe is másolja, kivéve, ha a létrehozáskor külön metaadatokat ad meg a pillanatképhez. A pillanatkép létrehozása után elolvashatja, másolhatja vagy törölheti, de nem módosítható.

Az alap blobhoz társított bérletek nem érintik a pillanatképet. Nem lehet bérletet beszerezni egy pillanatképen.

Egy VHD-fájl tárolja a virtuálisgép-lemezek aktuális információit és állapotát. Leválaszthat egy lemezt a virtuális gépről, vagy leállíthatja a virtuális gépet, majd pillanatképet készíthet a VHD-fájlról. Ezt a pillanatkép-fájlt később is használhatja a VHD-fájl lekéréséhez az adott időpontban, és újból létrehozhatja a virtuális gépet.

## <a name="understand-how-snapshots-accrue-charges"></a>A pillanatképek felmerülési módjának ismertetése

### <a name="important-billing-considerations"></a>Fontos számlázási szempontok

A következő lista a pillanatképek létrehozásakor megfontolandó főbb pontokat tartalmazza.

- A Storage-fiók az egyedi blokkok vagy lapok díját terheli, függetlenül attól, hogy azok a blobban vagy a pillanatképben vannak-e. A fiókja nem számít fel további díjat a blobokhoz társított pillanatképekhez, amíg nem frissíti azt a blobot, amelyen alapulnak. Az alap blob frissítése után az a pillanatképtől eltér. Ebben az esetben az egyes Blobok vagy Pillanatképek egyedi blokkait vagy lapjait kell fizetnie.
- Ha egy blokkon belüli blokkot cserél le, a rendszer ezt a blokkot egy egyedi blokkként számítja fel. Ez akkor is igaz, ha a blokk ugyanazzal a blokk-AZONOSÍTÓval és ugyanazokkal az adatokkal rendelkezik, mint a pillanatképben. A blokk újbóli elkövetése után a rendszer az adott pillanatképtől eltér, és az adatokért kell fizetnie. Ugyanez a helyzet igaz egy olyan oldal blobján, amely azonos adattal frissült.
- Egy blokk blobjának frissítése egy olyan metódus meghívásával, amely felülírja a blob teljes tartalmát, a blob összes blokkját lecseréli. Ha az adott blobhoz pillanatkép van társítva, akkor az alap blobban és a pillanatképben lévő összes blokk már eltér egymástól, és a rendszer az összes blokkot felszámítja mindkét blobban. Ez akkor is igaz, ha az alap blobban lévő adatok és a pillanatkép azonos marad.
- Az Azure Blob service nem határozza meg, hogy két blokk tartalmaz-e azonos adathalmazt. Minden feltöltött és véglegesített blokk egyediként lesz kezelve, még akkor is, ha ugyanazokat az adatblokkokat és AZONOSÍTÓkat is tartalmazta. Mivel a díjak egyedi blokkokból állnak, fontos figyelembe venni, hogy a pillanatképet tartalmazó Blobok frissítése további egyedi blokkokat és további díjakat eredményez.

### <a name="minimize-costs-with-snapshot-management"></a>A költségek csökkentése a Snapshot Management szolgáltatással

Javasoljuk, hogy gondosan kezelje a pillanatképeket a további költségek elkerülése érdekében. Az alábbi ajánlott eljárásokat követve csökkentheti a pillanatképek tárolásához felmerülő költségeket:

- Egy blobhoz társított Pillanatképek törlése és újbóli létrehozása minden alkalommal, amikor frissíti a blobot, még akkor is, ha az alkalmazás megtervezése nem igényli a pillanatképek karbantartását. A blob Pillanatképek törlésével és újbóli létrehozásával biztosíthatja, hogy a blob és a pillanatképek ne legyenek elválasztva.
- Ha pillanatképeket tart fenn egy blobhoz, ne hívja meg azokat a metódusokat, amelyek felülírják a teljes blobot a blob frissítésekor. Ehelyett frissítse a lehető legkevesebb blokkot a költségek alacsonyra csökkentése érdekében.

### <a name="snapshot-billing-scenarios"></a>Pillanatkép-számlázási forgatókönyvek

A következő forgatókönyvek azt mutatják be, hogyan merülhetnek fel a díjak a blokkos blobok és a pillanatképek esetében.

## <a name="pricing-and-billing"></a>Árak és számlázás

Pillanatkép létrehozása, amely egy blob írásvédett példánya, további adattárolási díjat eredményezhet a fiókjához. Az alkalmazás tervezésekor fontos tisztában lennie azzal, hogy ezek a díjak hogyan merülhetnek fel, így csökkentheti a költségeket.

A blob-Pillanatképek, például a blob-verziók számlázása az aktív adatforgalommal megegyező sebességgel történik. A pillanatképek számlázásának módja attól függ, hogy explicit módon beállította-e a szintet az alap blobhoz vagy a pillanatképekhez (vagy verziókhoz). A blob-rétegekkel kapcsolatos további információkért lásd [: Azure Blob Storage: gyors, ritka elérésű és archív hozzáférési szintek](storage-blob-storage-tiers.md).

Ha nem módosította a Blobok vagy a pillanatképek szintjét, akkor a blob, a pillanatképek és az esetlegesen használt verziók egyedi adattömbökért kell fizetnie. További információ: számlázás, [Ha a blob-szintet nem adta meg explicit módon](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Ha módosított egy blobot vagy pillanatképet tartalmazó szintet, akkor a teljes objektumért díjat számítunk fel, függetlenül attól, hogy a blob és a pillanatkép végül ugyanabban a szinten van-e. További információ: számlázás, [Ha a blob szintjét explicit módon állították be](#billing-when-the-blob-tier-has-been-explicitly-set).

A blob-verziók számlázási adataival kapcsolatos további információkért lásd: [blob verziószámozása](versioning-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Számlázás, ha a blob szintje nincs explicit módon beállítva

Ha nem állította be explicit módon a blob rétegét egy alap blobhoz vagy annak valamelyik pillanatképéhez, akkor a blob, a pillanatképek és az esetlegesen használt verziók egyedi blokkokra vagy lapokra számítunk fel díjat. A blobok és Pillanatképek megosztása csak egyszer történik. Ha egy blob frissül, akkor az alap blobban lévő adatok eltérnek a pillanatképekben tárolt adatoktól, és az egyedi adatok egy blokk vagy lap alapján számítanak fel díjat.

Ha egy blokkon belüli blokkot cserél le, a rendszer ezt a blokkot egy egyedi blokkként számítja fel. Ez akkor is igaz, ha a blokk ugyanazzal a blokk-AZONOSÍTÓval és ugyanazokkal az adatokkal rendelkezik, mint a pillanatképben. Miután a blokk újra véglegesítve lett, a pillanatképtől eltér a pillanatképből, és az adatokért kell fizetnie. Ugyanez a helyzet igaz egy olyan oldal blobján, amely azonos adattal frissült.

A blob Storage nem rendelkezik annak megállapításához, hogy két blokk tartalmaz-e azonos adathalmazt. Minden feltöltött és véglegesített blokk egyediként lesz kezelve, még akkor is, ha ugyanazokat az adatblokkokat és AZONOSÍTÓkat is tartalmazta. Mivel a díjak az egyedi blokkok esetében merülnek fel, fontos szem előtt tartani, hogy ha a blob pillanatképekkel vagy verziókkal rendelkezik, további egyedi blokkok és további költségek is megmaradnak.

Ha egy blob pillanatképeket tartalmaz, a frissítési műveleteket a blokk blobokon hívja meg, hogy a lehető legkevesebb blokkot frissítik. Az írási műveletek, amelyek lehetővé teszik a részletes szabályozást a blokkoknál, a [blokk](/rest/api/storageservices/put-block) és a [put blokkot](/rest/api/storageservices/put-block-list)is fel kell venni. A [put blob](/rest/api/storageservices/put-blob) művelet ugyanakkor lecseréli egy blob teljes tartalmát, ezért további díjakat eredményezhet.

A következő forgatókönyvek azt mutatják be, hogyan merülhetnek fel a letiltási blobok és a pillanatképek, amikor a blob szintje nincs explicit módon beállítva.

#### <a name="scenario-1"></a>1\. példa

Az 1. forgatókönyvben az alap blob nem frissült a pillanatkép készítése után, ezért a díjak csak az 1., 2. és 3. egyedi blokkok esetében merülnek fel.

![1. ábra, az alap blob és a pillanatkép egyedi blokkokra vonatkozó számlázását mutatja.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>2\. példa

A 2. forgatókönyvben az alap blob frissült, de a pillanatkép nem. A 3. blokk frissült, annak ellenére, hogy ugyanazokat az adatokkal és ugyanazzal az AZONOSÍTÓval rendelkezik, nem ugyanaz, mint a 3. blokk a pillanatképben. Ennek eredményeképpen a fiók négy blokk után lesz felszámítva.

![2. ábra: az alap blob és a pillanatkép egyedi blokkokra vonatkozó számlázását mutatja.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>3\. példa

A 3. forgatókönyvben az alap blob frissült, de a pillanatkép nem. A 3. blokk lecserélve a 4-es blokkra az alap blobban, de a pillanatkép továbbra is a 3. blokkot tükrözi. Ennek eredményeképpen a fiók négy blokk után lesz felszámítva.

![3. ábra: az alap blob és a pillanatkép egyedi blokkokra vonatkozó számlázását mutatja.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>4\. példa

A 4. forgatókönyvben az alap blob teljesen frissítve lett, és az eredeti blokk egyikét sem tartalmazza. Ennek eredményeképpen a fiók minden nyolc egyedi blokk után díjat számít fel.

![4. ábra: az alap blob és a pillanatkép egyedi blokkokra vonatkozó számlázását mutatja.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> Ne hívjon fel olyan metódusokat, amelyek felülírják a teljes blobot, hanem az egyes blokkok frissítésével a költségek alacsonyak maradnak.

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Számlázás, ha a blob szintje explicit módon be van állítva

Ha explicit módon beállította a blob-szintet egy blobhoz vagy pillanatképhez (vagy verzióhoz), akkor a rendszer az új szinten lévő objektum teljes tartalomra vonatkozó díját számítja fel, függetlenül attól, hogy az eredeti szinten található objektummal osztozik-e. Az eredeti szinten a legrégebbi verzió tartalmának teljes hosszát is felszámítjuk. Az eredeti szinten maradó verziók vagy Pillanatképek a megosztható egyedi blokkokért lesznek felszámítva, ahogy azt a [számlázás nem adta meg explicit](#billing-when-the-blob-tier-has-not-been-explicitly-set)módon.

#### <a name="moving-a-blob-to-a-new-tier"></a>BLOB áthelyezése egy új szintjére

A következő táblázat ismerteti a Blobok vagy Pillanatképek számlázási viselkedését, ha az új szintjére kerül.

| Ha a blob-szintet explicit módon beállította... | Ezután számlázunk... |
|-|-|
| Egy pillanatképet tartalmazó alap blob | Az alapszintű blob az új szinten és az eredeti szinten lévő legrégebbi pillanatkép, valamint a többi pillanatkép egyedi blokkja. <sup>1</sup> |
| Egy korábbi verziót és egy pillanatképet tartalmazó alap blob | Az alap blob az új szinten, az eredeti szint legrégebbi verziója, valamint az eredeti szinten lévő legrégebbi pillanatkép, valamint a többi verzióban<sup>vagy pillanatképekben</sup>szereplő egyedi blokkok. |
| Pillanatkép | A pillanatkép az új rétegben és az alapszintű blobban az eredeti szinten, valamint a többi pillanatkép egyedi blokkja. <sup>1</sup> |

<sup>1</sup> Ha vannak olyan korábbi verziók vagy Pillanatképek, amelyek nem lettek áthelyezve az eredeti szintjéről, akkor ezek a verziók vagy Pillanatképek az általuk tartalmazott egyedi blokkok száma alapján lesznek felszámítva, a [számlázás, ha a blob szintjét nem adta meg explicit módon](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Az alábbi ábra azt szemlélteti, hogyan történik az objektumok számlázása, amikor egy pillanatképet tartalmazó blobot egy másik szintjére helyeznek át.

:::image type="content" source="media/snapshots-overview/snapshot-billing-tiers.png" alt-text="Diagram, amely bemutatja, hogyan történik az objektumok számlázása, amikor egy pillanatképet tartalmazó blob explicit módon van feltüntetve.":::

A blob, a verzió vagy a pillanatkép szintje explicit módon történő beállítása nem vonható vissza. Ha egy blobot egy új szintre helyez át, majd visszahelyezi az eredeti szintjére, akkor akkor is a teljes tartalomért kell fizetnie, ha az objektum más objektumokkal is megosztja az eredeti szintet.

A Blobok, verziók vagy Pillanatképek szintjét explicit módon beállító műveletek a következők:

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- A [blobot](/rest/api/storageservices/put-blob) a megadott szintűvé tegye
- [Letiltási lista](/rest/api/storageservices/put-block-list) megadása a megadott szintű szinten
- [Blob másolása](/rest/api/storageservices/copy-blob) megadott szintű példánnyal

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>BLOB törlése, ha a Soft delete engedélyezve van

Ha a blob-törlés engedélyezve van, ha olyan alapszintű blobot töröl vagy felülír, amelynek a szintje explicit módon be lett állítva, akkor a helyreállított blob összes korábbi verziója vagy pillanatképe teljes tartalommal lesz kiszámlázva. További információ a Blobok verziószámozásáról és a helyreállítható törlésről: a [blob verziószámozása és a Soft delete](versioning-overview.md#blob-versioning-and-soft-delete)használata.

A következő táblázat ismerteti a nem megfelelően törölt Blobok számlázási viselkedését attól függően, hogy a verziószámozás engedélyezett vagy le van tiltva. Ha a Verziószámozás engedélyezve van, egy új verzió jön létre, amikor egy blobot törölnek. Ha a verziószámozás le van tiltva, a Blobok törlésével egy törlési pillanatkép jön létre.

| Ha egy alapszintű blobot felülír a réteg explicit módon beállított szintjével... | Ezután számlázunk... |
|-|-|
| Ha a blob-alapú törlés és verziószámozás egyaránt engedélyezve van | A teljes tartalom hosszúságú összes meglévő verzió a szintjétől függetlenül. |
| Ha a blob-törlés engedélyezve van, de a verziószámozás le van tiltva | Az összes létező, teljes tartalommal rendelkező törlési pillanatkép a rétegtől függetlenül. |

## <a name="next-steps"></a>Következő lépések

- [BLOB verziószámozása](versioning-overview.md)
- [BLOB-pillanatkép létrehozása és kezelése a .NET-ben](snapshots-manage-dotnet.md)
- [Azure-beli nem felügyelt VM-lemezek biztonsági mentése növekményes pillanatképekkel](../../virtual-machines/windows/incremental-snapshots.md)
