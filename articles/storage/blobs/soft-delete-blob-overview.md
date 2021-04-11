---
title: Blobok helyreállítható törlése
titleSuffix: Azure Storage
description: A Blobok Soft törlésével megvédheti adatait, így könnyebben helyreállíthatja az adatokat, amikor az alkalmazás vagy egy másik Storage-fiók felhasználója hibásan módosítja vagy törölte azokat.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c0cc29e6cbc9be1e7683b1b4412fa47f71c0538d
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227024"
---
# <a name="soft-delete-for-blobs"></a>Blobok helyreállítható törlése

A blob Soft delete egy adott blobot, pillanatképet vagy verziót biztosít a véletlen törlésekről vagy felülírásokról azáltal, hogy a törölt adatok megadott ideig tart a rendszeren. A megőrzési időszak alatt a törölt objektumot visszaállíthatja az állapotára a törlésük időpontjában. A megőrzési időszak lejárta után az objektum véglegesen törölve lesz.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="recommended-data-protection-configuration"></a>Ajánlott adatvédelmi konfiguráció

A blob-törlés a blob-adatvédelemmel kapcsolatos átfogó adatvédelmi stratégia részét képezi. A blob-adatai optimális védelme érdekében a Microsoft a következő adatvédelmi funkciók mindegyikének engedélyezését javasolja:

- Tároló-helyreállítható törlés a törölt tárolók visszaállításához. A tárolók helyreállítható törlésének engedélyezéséről a tárolók helyreállítható [törlésének engedélyezése és kezelése](soft-delete-container-enable.md)című témakörben olvashat bővebben.
- BLOB verziószámozása, hogy automatikusan fenntartsa a blob korábbi verzióit. Ha a blob verziószámozása engedélyezve van, visszaállíthatja a blob egy korábbi verzióját az adatok helyreállításához, ha az hibásan van módosítva vagy törölve. A blob verziószámozásának engedélyezéséről a [blob verziószámozásának engedélyezése és kezelése](versioning-enable.md)című témakörben olvashat bővebben.
- BLOB törölje a törlést, hogy visszaállítsa a törölt blobot, pillanatképet vagy verziót. Ha szeretné megtudni, hogyan engedélyezheti a Blobok törlését, olvassa el a következőt: Blobok eltávolításának [engedélyezése és kezelése](soft-delete-blob-enable.md).

További információ a Microsoft adatvédelmi javaslatairól: [Adatvédelem – áttekintés](data-protection-overview.md).

## <a name="how-blob-soft-delete-works"></a>A blob-törlés működése

Ha egy Storage-fiókhoz engedélyezi a blob-törlést, megadhat egy megőrzési időtartamot a törölt objektumok számára 1 és 365 nap között. A megőrzési időtartam azt jelzi, hogy a törlés vagy a felülírás után mennyi ideig marad elérhető az adatok. Az óra a megőrzési időtartamon kezdődik, amint egy objektumot töröl vagy felülír.

Amíg a megőrzési idő aktív, visszaállíthatja a törölt blobokat a pillanatképekkel együtt, vagy törölheti a törölt verziót a [blob törlésének](/rest/api/storageservices/undelete-blob) visszavonása művelet meghívásával. A következő ábra azt szemlélteti, hogyan lehet visszaállítani a törölt objektumokat, ha engedélyezve van a blob-törlés:

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="Az a diagram, amely bemutatja, hogyan lehet visszaállítani egy helyreállítható blobot":::

Bármikor módosíthatja a helyreállítható törlés megőrzési időtartamát. A frissített megőrzési idő csak a megőrzési időszak megváltozása után törölt adatokra vonatkozik. A megőrzési időszak megváltozása előtt törölt minden olyan adatmegőrzési időszakot, amely a törlés után érvényben volt.

A törölt objektum törlésére tett kísérlet nem befolyásolja a lejárati idejét.

Ha letiltja a blob-alapú törlést, továbbra is elérheti és helyreállíthatja a lágyan törölt objektumokat a Storage-fiókban, amíg el nem telik a törlés megőrzési ideje.

A blob verziószámozása az általános célú v2, a blob és a blob Storage-fiókok esetében érhető el. A Azure Data Lake Storage Gen2-mel való használatra engedélyezett hierarchikus névtérrel rendelkező Storage-fiókok jelenleg nem támogatottak.

Az Azure Storage 2017-07-29-es és újabb verziója REST API támogatja a blob Soft delete használatát.

> [!IMPORTANT]
> Az egyes Blobok, Pillanatképek és verziók visszaállításához csak a Blobok törlését lehet használni. A tárolók és azok tartalmának visszaállításához engedélyezni kell a tárolók helyreállított törlését is a Storage-fiókhoz. A Microsoft azt javasolja, hogy a tárolók helyreállítható törlésével és a Blobok verziószámozásával együtt a blob-adatvédelem teljes védelmének biztosítása érdekében engedélyezze a tárolók További információ: [Adatvédelem – áttekintés](data-protection-overview.md).
>
> A blob resoft delete nem véd a Storage-fiókok törlésével. A Storage-fiók törlésével szembeni védelemhez állítson be egy zárolást a Storage-fiók erőforrásán. További információ a Storage-fiókok zárolásáról: [Azure Resource Manager zárolás alkalmazása egy Storage-fiókra](../common/lock-account-resource.md).

### <a name="how-deletions-are-handled-when-soft-delete-is-enabled"></a>A törlések kezelése, ha a Soft delete engedélyezve van

Ha a blob-alapú törlés engedélyezve van, akkor a Blobok törlésével törölhető. Nincs pillanatkép létrehozva. Ha a megőrzési idő lejár, a rendszer véglegesen törli a helyreállított blobot.

Ha egy blobhoz Pillanatképek tartoznak, a blob nem törölhető, ha a pillanatképek is törlődnek. A blobok és Pillanatképek törlésekor a blob és a pillanatképek is törölve lesznek. Nem jönnek létre új Pillanatképek.

Az alap blob törlése nélkül is törölhet egy vagy több aktív pillanatképet. Ebben az esetben a pillanatképet nem sikerült törölni.

A lágyan törölt objektumok csak akkor láthatók, ha nincsenek kifejezetten megjelenítve vagy listázva. A Soft Deleted Objects listázásával kapcsolatos további információkért lásd: a [puha törlésű Blobok kezelése és visszaállítása](soft-delete-blob-manage.md).

### <a name="how-overwrites-are-handled-when-soft-delete-is-enabled"></a>A felülírások kezelése a Soft delete engedélyezésekor

Ha olyan műveletet hív meg, mint a [blob elhelyezése](/rest/api/storageservices/put-blob), a [letiltási lista](/rest/api/storageservices/put-block-list)vagy a [blob másolása](/rest/api/storageservices/copy-blob) , felülírja a blobban lévőket. Ha a blob-törlés engedélyezve van, a Blobok felülírása automatikusan létrehoz egy, a blob állapotára vonatkozó, a írási művelet előtt törölt pillanatképet. Ha a megőrzési idő lejár, a rendszer véglegesen törli a helyreállított pillanatképet.

A helyreállítható törölt Pillanatképek csak akkor láthatók, ha a nem törölt objektumok explicit módon jelennek meg vagy szerepelnek a felsorolásban. A Soft Deleted Objects listázásával kapcsolatos további információkért lásd: a [puha törlésű Blobok kezelése és visszaállítása](soft-delete-blob-manage.md).

A másolási műveletek elvégzéséhez engedélyezni kell a blob-alapú törlést a cél Storage-fiókhoz.

A blob-helyreállító törlés nem véd a blob-metaadatok vagy-tulajdonságok írására szolgáló műveletekkel szemben. Ha a blob metaadatai vagy tulajdonságai frissülnek, a rendszer nem törli a nem törölt pillanatképeket.

A blob Soft delete nem engedi felülírni a Blobok védelmét az archiválási szinten. Ha az archiválási szinten lévő blobot egy adott szinten egy új blob írja felül, akkor a felülírt blob véglegesen törlődik.

A prémium szintű Storage-fiókok esetében a Soft-Deleted Pillanatképek nem számítanak bele az 100-as blob-korlátba.

### <a name="restoring-soft-deleted-objects"></a>Lágyan törölt objektumok visszaállítása

A nem törölt blobokat a megőrzési időtartamon belül úgy állíthatja vissza, hogy a [Törlés törlése](/rest/api/storageservices/undelete-blob) műveletet kéri. A **blob törlésének** visszavonása művelet visszaállítja a blobokat és a hozzájuk rendelt összes helyreállítható pillanatképet. A megőrzési időszak alatt törölt Pillanatképek visszaállíthatók.

Ha a blob törlését nem kívánja törölni egy olyan blobon, amely nem **törölhető** , a rendszer visszaállítja a blobhoz társított összes, az összes helyreállítatlan törölt pillanatképet. Ha a blob nem tartalmaz pillanatképeket, és nem törölhető, a **blob törlésének** meghívása nincs hatással.

A nem törölt Pillanatképek alap blobba való előléptetéséhez először hívja meg az alap blob **törlési blobját** a blob és a pillanatképek visszaállításához. Ezután másolja a kívánt pillanatképet az alap blobon. A pillanatképet egy új blobba is másolhatja.

A helyreállított blobokban vagy pillanatképekben tárolt adatok nem olvashatók be, amíg az objektum vissza nem áll.

A nem törölt objektumok visszaállításával kapcsolatos további információkért lásd: a [puha törlésű Blobok kezelése és visszaállítása](soft-delete-blob-manage.md).

## <a name="blob-soft-delete-and-versioning"></a>Blobos törlés és verziószámozás

Ha a blob verziószámozása és a blob Soft delete egyaránt engedélyezve van egy Storage-fiókhoz, akkor a Blobok felülírása automatikusan létrehoz egy új verziót. Az új verzió nem törlődik, és a rendszer nem távolítja el, ha a helyreállítható törlés megőrzési ideje lejár. Nem készíthetők helyreállított Pillanatképek. Amikor töröl egy blobot, a blob aktuális verziója egy korábbi verzió lesz, és a rendszer törli az aktuális verziót. A rendszer nem hoz létre új verziót, és nem hoz létre nem törölt pillanatképeket.

A helyreállítható törlés és verziószámozás engedélyezése együtt védi a blob-verziók törlését. Ha a helyreállítható törlés engedélyezve van, egy verzió törlése egy nem törölt verziót hoz létre. A **blob törlése** művelettel visszaállíthatja a nem törölt verziót, feltéve, hogy a blob aktuális verziója van. Ha nincs aktuális verzió, akkor a **blob törlésének** meghívása előtt át kell másolnia egy korábbi verziót az aktuális verzióra.

> [!NOTE]
> A törölt Blobok **törlésének** visszavonása művelet meghívása, ha a Verziószámozás engedélyezve van, visszaállítja a nem törölt verziókat vagy pillanatképeket, de nem állítja vissza az alap blobot. Az alap blob visszaállításához léptesse elő az előző verziót az alap blobba másolással.

A Microsoft azt javasolja, hogy az optimális adatvédelem érdekében a Storage-fiókok esetében is engedélyezze a verziószámozást és a Blobok törlését. További információ a Blobok verziószámozásának és a Soft delete együttes használatáról: [blob verziószámozása és a Soft delete](versioning-overview.md#blob-versioning-and-soft-delete).

## <a name="blob-soft-delete-protection-by-operation"></a>BLOB-törlési védelem művelet szerint

A következő táblázat ismerteti a törlési és írási műveletek várható viselkedését, ha a blobos törlés engedélyezve van, vagy a blob verziószámozásával vagy anélkül:

| REST API műveletek | Helyreállítható törlés engedélyezve | Helyreállítható törlés és Verziószámozás engedélyezve |
|--|--|--|
| [Storage-fiók törlése](/rest/api/storagerp/storageaccounts/delete) | Nincs változás. A törölt fiókban lévő tárolók és Blobok nem lesznek helyreállítva. | Nincs változás. A törölt fiókban lévő tárolók és Blobok nem lesznek helyreállítva. |
| [Tároló törlése](/rest/api/storageservices/delete-container) | Nincs változás. A törölt tárolóban lévő Blobok nem lesznek helyreállítva. | Nincs változás. A törölt tárolóban lévő Blobok nem lesznek helyreállítva. |
| [Delete Blob](/rest/api/storageservices/delete-blob) | Ha egy blob törlésére használatos, a blob törlésre kerül. <br /><br /> Ha a blob-Pillanatképek törlésére használatos, a pillanatképet a rendszer törlésre kijelöltként jelöli meg. | Ha a Blobok törlésére használatos, a jelenlegi verzió a korábbi verzió lesz, és a rendszer törli az aktuális verziót. A rendszer nem hoz létre új verziót, és nem hoz létre nem törölt pillanatképeket.<br /><br /> Ha egy blob-verzió törlésére használatos, a verzió a következőként lesz megjelölve: Soft Deleted. |
| [BLOB törlésének visszavonása](/rest/api/storageservices/delete-blob) | Visszaállítja egy blobot és a megőrzési időn belül törölt pillanatképeket. | Visszaállítja egy blobot és a megőrzési időn belül törölt verziókat. |
| [Put Blob](/rest/api/storageservices/put-blob)<br />[Tiltási lista](/rest/api/storageservices/put-block-list)<br />[Copy Blob](/rest/api/storageservices/copy-blob)<br />[BLOB másolása URL-címről](/rest/api/storageservices/copy-blob) | Ha a hívás aktív blobon történik, akkor a művelet előtt a blob állapotának pillanatképe automatikusan létrejön. <br /><br /> Ha egy helyreállított blobon van meghívva, akkor a blob korábbi állapotának pillanatképe csak akkor jön létre, ha az azonos típusú blobtal van lecserélve. Ha a blob eltérő típusú, akkor a rendszer véglegesen törli az összes meglévő helyreállított törölt adatmennyiséget. | A rendszer automatikusan létrehoz egy új verziót, amely rögzíti a blob állapotát a művelet előtt. |
| [Put blokk](/rest/api/storageservices/put-block) | Ha egy blokkot aktív blobba kíván véglegesíteni, nincs változás.<br /><br />Ha egy blokkot egy olyan blobhoz kíván véglegesíteni, amely nem törölhető, létrejön egy új blob, és a rendszer automatikusan létrehoz egy pillanatképet a helyreállított blob állapotának rögzítéséhez. | Nincs változás. |
| [Oldal elhelyezése](/rest/api/storageservices/put-page)<br />[Oldal elhelyezése az URL-címről](/rest/api/storageservices/put-page-from-url) | Nincs változás. A rendszer nem menti az oldal blobjának a művelettel felülírt vagy törölt lapjait, és nem helyreállítható. | Nincs változás. A rendszer nem menti az oldal blobjának a művelettel felülírt vagy törölt lapjait, és nem helyreállítható. |
| [Blokk hozzáfűzése](/rest/api/storageservices/append-block)<br />[Blokk hozzáfűzése URL-címről](/rest/api/storageservices/append-block-from-url) | Nincs változás. | Nincs változás. |
| [BLOB tulajdonságainak beállítása](/rest/api/storageservices/set-blob-properties) | Nincs változás. A felülírt blob-tulajdonságok nem helyreállítható. | Nincs változás. A felülírt blob-tulajdonságok nem helyreállítható. |
| [BLOB metaadatainak beállítása](/rest/api/storageservices/set-blob-metadata) | Nincs változás. A felülírt blob-metaadatok nem helyreállítható. | A rendszer automatikusan létrehoz egy új verziót, amely rögzíti a blob állapotát a művelet előtt. |
| [Set Blob Tier](/rest/api/storageservices/set-blob-tier) | Az alap blob átkerül az új rétegbe. Az aktív vagy a nem törölt Pillanatképek az eredeti szinten maradnak. Nem lett létrehozva helyreállított pillanatkép. | Az alap blob átkerül az új rétegbe. Az aktív vagy a nem törölt verziók az eredeti szinten maradnak. Nem jön létre új verzió. |

## <a name="pricing-and-billing"></a>Árak és számlázás

A rendszer az aktív adatforgalommal megegyező sebességgel számítja fel az összes helyreállított törlési értéket. A megőrzési idő eltelte után véglegesen törölt adatokért nem számítunk fel díjat.

Ha engedélyezi a helyreállítható törlést, a Microsoft azt javasolja, hogy egy rövid megőrzési időszak használatával jobban megértse, hogyan befolyásolja a szolgáltatás a számlát. A minimálisan ajánlott megőrzési idő hét nap.

Ha engedélyezi a gyakori felülírású adatok törlését, megnövelheti a tárolási kapacitást, és a Blobok listázása nagyobb késést eredményezhet. Ezt a további költségeket és késést úgy csökkentheti, ha a gyakran felülírt, különálló Storage-fiókban tárolja azokat, amelyekben a Soft delete le van tiltva.

Nem számítunk fel olyan tranzakciókat, amelyek a pillanatképek vagy verziók automatikus generálásával kapcsolatosak, amikor a blobot felülírják vagy törlik. Az írási műveletek tranzakciós arányában a **blob törlésének** visszavonására irányuló hívásokért kell fizetnie.

A Blob Storage díjszabásával kapcsolatos további információkért tekintse meg a [blob Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/blobs/) ismertető oldalt.

## <a name="blob-soft-delete-and-virtual-machine-disks"></a>BLOB – Soft delete és virtuálisgép-lemezek  

A blob Soft Delete a prémium és a standard szintű nem felügyelt lemezek esetében is elérhető, amelyek a borítók alatti lapokat képező Blobok. A Soft DELETE segítségével helyreállíthatja a törölt adatokat, vagy felülírhatja a **blob törlése**, a **blob**, a **put tiltólista** és a **Blobok másolása** műveleteit.

A **put lapra** irányuló hívás által felülírt adathalmaz nem helyreállítható. Az Azure-beli virtuális gépek nem felügyelt lemezre való írást végeznek a **put lapra** hívásokkal, így a Soft delete használatával visszavonhatók az írások egy nem felügyelt lemezre egy Azure-beli virtuális gépről nem támogatott forgatókönyv.

## <a name="next-steps"></a>Következő lépések

- [Blobok helyreállítható törlésének engedélyezése](./soft-delete-blob-enable.md)
- [A nem törölt Blobok kezelése és visszaállítása](soft-delete-blob-manage.md)
- [BLOB verziószámozása](versioning-overview.md)