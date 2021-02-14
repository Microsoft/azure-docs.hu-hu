---
title: Blobok helyreállítható törlése
titleSuffix: Azure Storage
description: A Blobok Soft törlésével megvédheti adatait, így könnyebben helyreállíthatja az adatokat, amikor az alkalmazás vagy egy másik Storage-fiók felhasználója hibásan módosítja vagy törölte azokat.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a370a7f04e0e43b96e4a574313c4f24c4990ab6f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390357"
---
# <a name="soft-delete-for-blobs"></a>Blobok helyreállítható törlése

A Blobok helyreállítható törlésével megvédheti az adatait véletlenül vagy hibásan, vagy törölheti azokat. Ha a Blobok helyreállított törlését engedélyezték a Storage-fiókhoz, a Blobok, a blobok és a pillanatképek a Storage-fiókban való törlése a megadott megőrzési időtartamon belül megtörténik.

Ha egy alkalmazás vagy egy másik Storage-fiók felhasználója véletlenül nem módosíthatja vagy törölheti az adatait, a Microsoft a Soft delete bekapcsolását javasolja. További információ a Soft delete engedélyezéséről: a [Blobok Soft delete engedélyezése és kezelése](./soft-delete-blob-enable.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-soft-delete-for-blobs"></a>A Blobok Soft delete szolgáltatásának ismertetése

Ha a Blobok helyreállított törlését engedélyezték a Storage-fiókon, akkor a törölt objektumokat a megadott adatmegőrzési időtartamon belül állíthatja helyre. Ez a védelem kiterjed minden blobra (Blobok, hozzáfűzési Blobok vagy Blobok letiltására), amelyeket a rendszer a felülírás eredményeképpen töröl.

A következő ábra azt mutatja be, hogyan állítható vissza a törölt blob, ha engedélyezve van a blob-törlés:

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="Az a diagram, amely bemutatja, hogyan lehet visszaállítani egy helyreállítható blobot":::

Ha egy meglévő blobban vagy pillanatképben lévő adatok törlődnek, miközben a blob-alapú törlés engedélyezve van, de a blob verziószámozása nincs engedélyezve, akkor a rendszer egy helyreállított pillanatképet hoz létre a felülírt adatok állapotának mentéséhez. A megadott megőrzési időszak lejárta után az objektum véglegesen törölve lesz.

Ha a blob verziószámozása és a blob Soft delete egyaránt engedélyezve van a Storage-fiókban, akkor a Blobok törlése egy új verziót hoz létre, nem pedig egy nem törölt pillanatképet. Az új verzió nem törlődik, és a rendszer nem távolítja el, ha a helyreállítható törlés megőrzési ideje lejár. A Blobok helyreállított verziói a megőrzési időn belül visszaállíthatók a [blob törlésének](/rest/api/storageservices/undelete-blob) visszavonása művelet meghívásával. A blob később visszaállítható az egyik verzióról a [blob másolása](/rest/api/storageservices/copy-blob) művelet meghívásával. További információ a Blobok verziószámozásának és a Soft delete együttes használatáról: [blob verziószámozása és a Soft delete](versioning-overview.md#blob-versioning-and-soft-delete).

A helyreállított törölt objektumok csak akkor láthatók, ha explicit módon vannak felsorolva.

A blob resoft delete visszafelé kompatibilis, így nem kell módosítania az alkalmazásokat, hogy kihasználhassa a szolgáltatás által biztosított védelmet. Az [Adathelyreállítás](#recovery) azonban bevezeti a blob API új **törlését** .

A blob Soft DELETE az új és a meglévő általános célú v2-, általános célú v1-és blob Storage-fiókokhoz is elérhető. A standard és a Premium típusú fiókok egyaránt támogatottak. A blob resoft delete minden tárolási réteghez elérhető, beleértve a gyakori, a ritka és az archív csomagokat is. A helyreállítható törlés nem felügyelt lemezekhez érhető el, amelyek a borítók alatti oldal-Blobok, de a felügyelt lemezekhez nem érhető el.

### <a name="configuration-settings"></a>Konfigurációs beállítások

Új fiók létrehozásakor alapértelmezés szerint le van tiltva a Soft delete szolgáltatás. A helyreállítható törlés alapértelmezés szerint le van tiltva a meglévő Storage-fiókok esetében is. A Storage-fiókhoz bármikor engedélyezheti vagy letilthatja a törlést.

Ha engedélyezi a helyreállítható törlést, konfigurálnia kell a megőrzési időtartamot. A megőrzési időtartam azt jelzi, hogy a rendszer mennyi időt tárol és biztosít a helyreállítható adatok tárolásához. Az explicit módon törölt objektumok esetében a megőrzési időszak órája az adatok törlésekor kezdődik. Az adatok felülírása esetén a helyreállítható törlési funkció által létrehozott, nem törölt verziók vagy Pillanatképek esetében az óra a verzió vagy a pillanatkép létrehozásakor elindul. A megőrzési időtartam 1 és 365 nap között lehet.

Bármikor módosíthatja a helyreállítható törlés megőrzési időtartamát. A frissített megőrzési idő csak az újonnan törölt adatokra vonatkozik. A korábban törölt adatok lejárnak az adatok törlésekor beállított megőrzési időtartam alapján. A helyreállított törölt objektumok törlésére tett kísérlet nem befolyásolja a lejárati idejét.

Ha letiltja a helyreállítható törlést, továbbra is hozzáférhet és helyreállíthatja azokat a Storage-fiókban, amelyeket a szolgáltatás engedélyezése közben mentett.

### <a name="saving-deleted-data"></a>Törölt adattárolók mentése

A helyreállítható törlés megőrzi az adatait sok esetben, ha az objektumokat törölték vagy felülírják.

Ha a blobot a **put blob**, a **Letiltás** vagy a **blob másolása** használatával írja felül, a rendszer automatikusan létrehozza a blob állapotának verzióját vagy pillanatképét az írási művelet előtt. Ez az objektum csak akkor látható, ha a helyreállított objektumok explicit módon vannak felsorolva. A [helyreállított](#recovery) objektumok listázásával kapcsolatos információkért tekintse meg a helyreállítás szakaszt.

![Egy ábra, amely bemutatja, hogyan tárolódnak a Blobok pillanatképei, mivel azok felül vannak írva a Put blob használatával, a letiltási listával vagy a blob másolásával.](media/soft-delete-blob-overview/storage-blob-soft-delete-overwrite.png)

*A puha törölt adatértékek szürkék, míg az aktív adatértékek kék színűek. A legutóbb írt adatértékek a régebbi adatelemek alatt jelennek meg. Ha a B0 felülírja a B1-sel, a rendszer létrehoz egy, a B0 készült, törölt pillanatképet. Ha a B1 felülíródik a B2-mel, a rendszer létrehoz egy, a B1-ről készült, törölt pillanatképet.*

> [!NOTE]  
> A helyreállítható törlés csak a másolási műveletek felülírását teszi elérhetővé, ha a cél blob fiókjához be van kapcsolva.

> [!NOTE]  
> A helyreállítható törlés nem teszi elérhetővé a Blobok felülírásának védelmét az archív szinten. Ha az archívumban található blobot egy új blobtal felülírják bármely szinten, a felülírt blob véglegesen lejár.

Ha a **törlési blobot** pillanatképként hívja meg, akkor a pillanatképet a rendszer nem töröltként jelöli meg. Nem jön létre új pillanatkép.

![Egy diagram, amely azt mutatja, hogyan történik a Blobok pillanatképének törlése a DELETE blob használatakor.](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-delete-snapshot.png)

*A puha törölt adatértékek szürkék, míg az aktív adatértékek kék színűek. A legutóbb írt adatértékek a régebbi adatelemek alatt jelennek meg. Ha a **Pillanatkép-blob** hívása megtörténik, a B0 a blob aktív állapota lesz. A B0-pillanatkép törlését követően a rendszer törli a jelölést.*

Ha a **törlési blobot** egy alapblobra hívja (bármely olyan blob, amely nem pillanatkép), akkor a blobot a rendszer a töröltként jelöli meg. A korábbi viselkedéssel összhangban az aktív pillanatképekkel rendelkező Blobok **törlésének** meghívása hibát jelez. A **blob törlésének** meghívása egy blobon, ha a törölt Pillanatképek nem adnak vissza hibát. A blobokat és az összes pillanatképét egyetlen művelettel törölheti, ha a Soft delete be van kapcsolva. Így az alap blob és a pillanatképek nem törlődnek.

![Egy ábra, amely bemutatja, hogy mi történik, ha a rendszer törli a blogot egy alap blobon.](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-include.png)

*A puha törölt adatértékek szürkék, míg az aktív adatértékek kék színűek. A legutóbb írt adatértékek a régebbi adatelemek alatt jelennek meg. Itt törölheti a **törlési blobot** , hogy törölje a B2-et és az összes kapcsolódó pillanatképet. A rendszer az aktív blobot, a B2-et és az összes kapcsolódó pillanatképet törölve jelöli.*

> [!NOTE]  
> Ha a rendszer felülírja a törölt blobokat, a rendszer automatikusan létrehoz egy, a blob állapotára vonatkozó, az írási művelet előtt törölt pillanatképet. Az új blob örökli a felülírt blob szintjét.

A helyreállítható törlés nem menti az adatokat tároló vagy fiók törlése esetén, sem a blob metaadatainak és a blob tulajdonságainak felülírásával. Ha a Storage-fiók törlését szeretné biztosítani, a Azure Resource Manager használatával is beállíthat zárolást. További információkért tekintse [meg az erőforrások zárolása a váratlan változások elkerülése érdekében](../../azure-resource-manager/management/lock-resources.md)Azure Resource Manager cikket.  A tárolók véletlen törléssel való ellátásához konfigurálja a tárolók helyreállított törlését a Storage-fiókhoz. További információ: a [tárolók helyreállítható törlése (előzetes verzió)](soft-delete-container-overview.md).

A következő táblázat részletesen ismerteti a Soft delete bekapcsolásakor elvárt viselkedést:

| REST API művelet | Erőforrás típusa | Description | Változás a viselkedésben |
|--------------------|---------------|-------------|--------------------|
| [Törlés](/rest/api/storagerp/StorageAccounts/Delete) | Fiók | Törli a Storage-fiókot, beleértve a benne található összes tárolót és blobot.                           | Nincs változás. A törölt fiókban lévő tárolók és Blobok nem lesznek helyreállítva. |
| [Tároló törlése](/rest/api/storageservices/delete-container) | Tároló | Törli a tárolót, beleértve a benne található összes blobot is. | Nincs változás. A törölt tárolóban lévő Blobok nem lesznek helyreállítva. |
| [Put Blob](/rest/api/storageservices/put-blob) | Blobok letiltása, hozzáfűzése és lapja | Új blob létrehozása vagy egy meglévő blob cseréje egy tárolón belül | Ha egy meglévő blob cseréjét használja, a rendszer automatikusan létrehoz egy pillanatképet a blob állapotáról a hívás előtt. Ez egy korábban eltávolított blobra is vonatkozik, ha az és csak akkor, ha az azonos típusú blobtal (blokk, Hozzáfűzés vagy lap) van lecserélve. Ha egy másik típusú blob váltja fel, az összes meglévő helyreállított törlési érték véglegesen lejár. |
| [Delete Blob](/rest/api/storageservices/delete-blob) | Blobok letiltása, hozzáfűzése és lapja | A törléshez a blob vagy a blob pillanatképét jelöli. A blob vagy pillanatkép később törlődik a Garbage gyűjtemény során | Ha egy blob-pillanatkép törlésére használatos, a pillanatképet a rendszer a törlésre kijelöltként jelöli meg. Ha egy blob törlésére használatos, a blob törlésre kerül. |
| [Copy Blob](/rest/api/storageservices/copy-blob) | Blobok letiltása, hozzáfűzése és lapja | Egy forrás blobot másol egy célként megadott blobba ugyanabban a Storage-fiókban vagy egy másik Storage-fiókban. | Ha egy meglévő blob cseréjét használja, a rendszer automatikusan létrehoz egy pillanatképet a blob állapotáról a hívás előtt. Ez egy korábban eltávolított blobra is vonatkozik, ha az és csak akkor, ha az azonos típusú blobtal (blokk, Hozzáfűzés vagy lap) van lecserélve. Ha egy másik típusú blob váltja fel, az összes meglévő helyreállított törlési érték véglegesen lejár. |
| [Put blokk](/rest/api/storageservices/put-block) | Blokkblobok | Létrehoz egy új blokkot, amelyet egy blokk blob részeként kíván véglegesíteni. | Ha egy blokknak az aktív blobhoz való elvégzésére van szükség, nincs változás. Ha egy blokkot töröl egy olyan blobra, amely nem törlődik, egy új blob jön létre, és a rendszer automatikusan létrehoz egy pillanatképet a helyreállított blob állapotának rögzítéséhez. |
| [Tiltási lista](/rest/api/storageservices/put-block-list) | Blokkblobok | Egy blobot véglegesít a blokk-blobot alkotó blokk-azonosítók megadásával. | Ha egy meglévő blob cseréjét használja, a rendszer automatikusan létrehoz egy pillanatképet a blob állapotáról a hívás előtt. Ez egy korábban eltávolított blobra is vonatkozik, ha az egy blokkos blob. Ha egy másik típusú blob váltja fel, az összes meglévő helyreállított törlési érték véglegesen lejár. |
| [Oldal elhelyezése](/rest/api/storageservices/put-page) | Lapblobok | Különböző lapokat ír egy oldal blobba. | Nincs változás. A rendszer nem menti az oldal blobjának a művelettel felülírt vagy törölt lapjait, és nem helyreállítható. |
| [Blokk hozzáfűzése](/rest/api/storageservices/append-block) | Hozzáfűző blobok | Adatblokkot ír egy hozzáfűző blob végére | Nincs változás. |
| [BLOB tulajdonságainak beállítása](/rest/api/storageservices/set-blob-properties) | Blobok letiltása, hozzáfűzése és lapja | Beállítja a blobhoz definiált Rendszertulajdonságok értékeit. | Nincs változás. A felülírt blob-tulajdonságok nem helyreállítható. |
| [BLOB metaadatainak beállítása](/rest/api/storageservices/set-blob-metadata) | Blobok letiltása, hozzáfűzése és lapja | A megadott blobhoz tartozó felhasználó által definiált metaadatokat állítja be egy vagy több név-érték párokként. | Nincs változás. A felülírt blob-metaadatok nem helyreállítható. |

Fontos megjegyezni, hogy a **put oldal** meghívásával felülírja vagy törli az oldal blobjának tartományait, nem fog automatikusan pillanatképeket létrehozni. A virtuálisgép-lemezeket a Blobok végzik, és a **put Page** használatával írhatnak be adatbevitelt.

### <a name="recovery"></a>Helyreállítási

Ha meghívja a [blob törlésének](/rest/api/storageservices/undelete-blob) visszavonása műveletet egy helyreállított alapszintű blobban, a rendszer visszaállítja és az összes kapcsolódó, törölt pillanatképet aktívként. A **blob törlésének törlésére** szolgáló művelet hívása aktív alapszintű blobon visszaállítja az összes társított, törölt pillanatképet aktívként. Ha a pillanatképek aktívként lettek visszaállítva, a felhasználó által létrehozott pillanatképeket hasonlítják, nem írják felül az alap blobot.

Ha egy blobot egy adott helyreállítható törölt pillanatképre szeretne visszaállítani, meghívhatja a blob **törlését** az alap blobon. Ezután átmásolhatja a pillanatképet a most aktív blobon keresztül. A pillanatképet egy új blobba is másolhatja.

![Egy diagram, amely bemutatja, hogy mi történik a blob törlésének visszavonásakor.](media/soft-delete-blob-overview/storage-blob-soft-delete-recover.png)

*A puha törölt adatértékek szürkék, míg az aktív adatértékek kék színűek. A legutóbb írt adatértékek a régebbi adatelemek alatt jelennek meg. Itt a **blob törlésének** visszavonása a B blobon történik, így az alap blobot, a B1-t és az összes kapcsolódó pillanatképet – itt csak B0 – aktívként kell visszaállítani. A második lépésben a B0 a rendszer az alap blobon másolja át. Ez a másolási művelet létrehoz egy, a B1-ről készült, törölt pillanatképet.*

A törölt blobok és blob-Pillanatképek megtekintéséhez dönthet úgy, hogy a törölt fájlokat is belefoglalja a **List blobba**. Dönthet úgy, hogy csak a nem megfelelően törölt alapblobokat vagy a törölt Blobok pillanatképeit is megtekinti. Az összes nem törölt adattal kapcsolatban megtekintheti az adattörlés időpontját, valamint azt, hogy hány nap elteltével kell véglegesen lejár az adatmennyiség.

### <a name="example"></a>Példa

A következő egy olyan .NET-parancsfájl konzoljának kimenete, amely feltölti, felülírja, pillanatképeket, töröl és helyreállít egy *HelloWorld* nevű blobot, ha a Soft delete be van kapcsolva:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

A kimenetet előkészítő alkalmazásra mutató hivatkozásról a [következő lépések](#next-steps) című szakaszban olvashat.

## <a name="pricing-and-billing"></a>Árak és számlázás

A rendszer az aktív adatforgalommal megegyező sebességgel számítja fel az összes helyreállított törlési értéket. A beállított megőrzési időtartam után véglegesen törölt adatokért nem számítunk fel díjat. A pillanatképek és az azokra vonatkozó költségek mélyebb megismeréséhez lásd: a [Pillanatképek felmerülésének ismertetése](./snapshots-overview.md).

A pillanatképek automatikus generálásával kapcsolatos tranzakciókért nem számítunk fel díjat. A blob-tranzakciók **törlésének** visszavonása az írási műveletek díjszabása alapján történik.

Az Azure Blob Storage díjszabásával kapcsolatos további információkért tekintse meg az [azure blob Storage díjszabási oldalát](https://azure.microsoft.com/pricing/details/storage/blobs/).

Amikor először bekapcsolja a Soft delete szolgáltatást, a Microsoft azt javasolja, hogy egy rövid megőrzési időszak használatával jobban megértse, hogyan befolyásolja a szolgáltatás a számlát.

Ha engedélyezi a gyakori felülírású adatok törlését, megnövelheti a tárolási kapacitást, és a Blobok listázása nagyobb késést eredményezhet. Ezt a további költségeket és késést úgy csökkentheti, ha a gyakran felülírt, különálló Storage-fiókban tárolja azokat, amelyekben a Soft delete le van tiltva.

## <a name="faq"></a>GYIK

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Használhatom a blob-rétegek API-ját a Blobok a Soft delete-pillanatképekkel való létrehozására?

Igen. A helyreállított törölt Pillanatképek az eredeti szinten maradnak, de az alap blob az új rétegre kerül.

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>A Premium Storage-fiókokhoz a blob pillanatkép-korlátja 100. A rugalmasan törölt Pillanatképek száma a korlát felé?

Nem, a nem kötelező törölt Pillanatképek nem számítanak bele a korlátba.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Ha olyan teljes fiókot vagy tárolót törölek, amelynél a Soft delete be van kapcsolva, a rendszer minden társított blobot ment?

Nem, ha töröl egy teljes fiókot vagy tárolót, az összes társított blob véglegesen törölve lesz. A Storage-fiókok véletlen törlésével kapcsolatos további információkért lásd: [erőforrások zárolása a váratlan változások megelőzése érdekében](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Megtekinthetem a törölt adatokhoz tartozó kapacitási metrikákat?

A rendszer az összes Storage-fiók kapacitásának részeként tartalmazza a helyreállított törölt adatmennyiséget. A tárolási kapacitás nyomon követésével és figyelésével kapcsolatos további információkért lásd: [Storage Analytics](../common/storage-analytics.md).

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Elolvashatom és kimásolhatom a Blobok helyreállított törölt pillanatképeit?  

Igen, de először a blob törlését kell meghívnia.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Elérhető-e a Soft Delete a virtuális gépek lemezei számára?  

A Soft delete szolgáltatás a prémium és a standard szintű nem felügyelt lemezek esetében is elérhető, amelyek a borítón lévő lapokat tartalmazó Blobok. A helyreállítható törlés csak a **blob törlésével**, a **Blobok**, a **letiltási listák** és a **Blobok másolásával** kapcsolatos adatok helyreállításához nyújt segítséget. A **put oldal** hívása által felülírt adathalmaz nem helyreállítható.

Az Azure-beli virtuális gépek nem felügyelt lemezre való írást végeznek a **put lapra** hívásokkal, így a Soft delete használatával visszavonhatók az írások egy nem felügyelt lemezre egy Azure-beli virtuális gépről nem támogatott forgatókönyv.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Módosítani kell a meglévő alkalmazásaikat a Soft delete használatára?

A Soft delete előnyeit kihasználhatja a használt API-verziótól függetlenül. Azonban a Soft Deleted blobok és a blob-Pillanatképek listázásához és helyreállításához az [Azure Storage REST API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) vagy újabb 2017-07-29 verzióját kell használnia. A Microsoft azt javasolja, hogy mindig az Azure Storage API legújabb verzióját használja.

## <a name="next-steps"></a>Következő lépések

- [Blobok helyreállítható törlésének engedélyezése](./soft-delete-blob-enable.md)
- [BLOB verziószámozása](versioning-overview.md)