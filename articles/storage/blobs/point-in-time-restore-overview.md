---
title: Időponthoz való visszaállítás a blokk Blobok esetében
titleSuffix: Azure Storage
description: A blokkos Blobok időponthoz való visszaállítása védelmet nyújt a véletlen törléssel vagy a sérüléssel szemben azáltal, hogy lehetővé teszi a Storage-fiókok előző állapotba való visszaállítását egy adott időpontban.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b959038753dd15282de357da746ef9b0e0cf2be5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802267"
---
# <a name="point-in-time-restore-for-block-blobs"></a>Időponthoz való visszaállítás a blokk Blobok esetében

Az időponthoz való visszaállítás védelmet nyújt a véletlen törlés vagy a sérülés ellen azáltal, hogy lehetővé teszi a blob-adatblokkok korábbi állapotba való visszaállítását. Az időponthoz való visszaállítás olyan esetekben hasznos, amikor egy felhasználó vagy alkalmazás véletlenül törli az adatvesztést, vagy ha egy alkalmazáshiba sérült az adatok között. Az időponthoz való visszaállítás olyan tesztelési forgatókönyveket is lehetővé tesz, amelyekben a további tesztek futtatása előtt egy ismert állapotba kell visszaállítani az adatkészletet.

Az időponthoz való visszaállítás az általános célú v2-alapú Storage-fiókok esetében csak a standard szintű teljesítmény esetén támogatott. Csak a gyakori és ritka elérésű hozzáférési szinteken lévő adatok állíthatók vissza az időponthoz tartozó visszaállítással.

Ha meg szeretné tudni, hogyan engedélyezhető az időponthoz tartozó visszaállítás egy Storage-fiókhoz, tekintse [meg az időponthoz való visszaállítást a Blobok adatain](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Az időpontra történő visszaállítás működése

Az időponthoz tartozó visszaállítás engedélyezéséhez létre kell hoznia egy felügyeleti szabályzatot a Storage-fiókhoz, és meg kell adnia egy megőrzési időtartamot. A megőrzési időszak alatt visszaállíthatja a blokk blobokat a jelen állapotból egy korábbi időpontban lévő állapotba.

Egy időponthoz tartozó visszaállítás kezdeményezéséhez hívja meg a blob- [tartományok visszaállítása](/rest/api/storagerp/storageaccounts/restoreblobranges) műveletet, és adja meg a visszaállítási pontot UTC idő szerint. Megadhatja a visszaállítandó tároló-és blob-nevek lexicographical, vagy kihagyhatja a tartományt a Storage-fiókban lévő összes tároló visszaállításához. Egy visszaállítási műveletben legfeljebb 10 lexicographical-tartomány támogatott.

Az Azure Storage elemzi a megadott Blobok összes módosítását a kért visszaállítási pont között, az UTC időpontban és a jelen pillanatban megadva. A visszaállítási művelet atomi, így az összes módosítás visszaállításával vagy meghibásodásával teljesen sikeres lesz. Ha vannak olyan Blobok, amelyek nem állíthatók vissza, a művelet meghiúsul, és az érintett tárolók olvasási és írási műveletei folytatódnak.

Az alábbi ábrán látható, hogy az időponthoz tartozó visszaállítás hogyan működik. Egy vagy több tároló vagy blob tartománya *n* nappal ezelőtt lett visszaállítva állapotba, ahol *n* az adott időponthoz tartozó visszaállításhoz megadott megőrzési időtartamnál kisebb vagy azzal egyenlő. Ennek hatására a megőrzési időszak során történt írási és törlési műveletek visszaállíthatók.

:::image type="content" source="media/point-in-time-restore-overview/point-in-time-restore-diagram.png" alt-text="Ábra, amely bemutatja, hogy az adott időpontban visszaállítja a tárolókat egy korábbi állapotba":::

Egyszerre csak egy visszaállítási műveletet lehet futtatni a Storage-fiókban. A visszaállítási művelet nem szakítható meg, ha folyamatban van, de egy második visszaállítási művelet is végrehajtható az első művelet visszavonásához.

A **blob-tartományok visszaállítása** művelet egy visszaállítási azonosítót ad vissza, amely egyedileg azonosítja a műveletet. Egy adott időponthoz tartozó visszaállítás állapotának megtekintéséhez hívja meg a visszaállítási **állapot lekérése** műveletet a **blob-tartományok visszaállítása** művelet által VISSZAadott visszaállítási azonosítóval.

> [!IMPORTANT]
> Ha visszaállítási műveletet hajt végre, az Azure Storage blokkolja a művelet időtartamára visszaállított tartományokban lévő Blobok adatműveleteit. Az olvasási, írási és törlési műveletek blokkolva vannak az elsődleges helyen. Ezért az olyan műveletek, mint például a tárolók listázása a Azure Portalban előfordulhat, hogy nem a várt módon hajtják végre a visszaállítási műveletet.
>
> A másodlagos hely olvasási műveletei a visszaállítási művelet során folytatódnak, ha a Storage-fiók földrajzilag replikálódik.

> [!CAUTION]
> Az időponthoz való visszaállítás támogatja a csak blokk blobokon végrehajtott műveletek elleni visszaállítást. A tárolón végrehajtott műveletek nem állíthatók vissza. Ha például töröl egy tárolót a Storage-fiókból a tároló [törlése](/rest/api/storageservices/delete-container) művelet meghívásával, a tároló nem állítható vissza időponthoz kapcsolódó visszaállítási művelettel. A teljes tároló törlése helyett törölje az egyes blobokat, ha később szeretné visszaállítani őket.

### <a name="prerequisites-for-point-in-time-restore"></a>Az időponthoz tartozó visszaállítás előfeltételei

Az időponthoz való visszaállításhoz az alábbi Azure Storage-funkciók engedélyezésére van szükség az időponthoz való visszaállítás engedélyezése előtt:

- [Helyreállítható törlés](soft-delete-blob-overview.md)
- [Csatorna módosítása](storage-blob-change-feed.md)
- [BLOB verziószámozása](versioning-overview.md)

A szolgáltatások engedélyezése további díjakat eredményezhet. Győződjön meg arról, hogy tisztában van a számlázási szempontokkal, mielőtt engedélyezi az időponthoz való visszaállítást és az előfeltételként szükséges szolgáltatásokat.

### <a name="retention-period-for-point-in-time-restore"></a>Időponthoz tartozó visszaállítás megőrzési időtartama

Amikor engedélyezi az időponthoz való visszaállítást egy Storage-fiókhoz, meg kell adnia egy megőrzési időtartamot. A Storage-fiókban lévő Blobok a megőrzési időszak alatt visszaállíthatók.

A megőrzési időtartam néhány percet vesz igénybe, miután engedélyezte az időponthoz tartozó visszaállítást. Ne feledje, hogy a Blobok nem állíthatók vissza a megőrzési időszak kezdete előtt állapotba. Ha például az 1. május 1-től engedélyezte az időponthoz való visszaállítást 30 nap megtartásával, akkor a május 15-én legfeljebb 15 napig állíthatja vissza. Június 1-jén az adatok 1 és 30 nap között állíthatók vissza.

Az időponthoz tartozó visszaállítás megőrzési időtartamának legalább egy nappal kisebbnek kell lennie a helyreállítható törléshez megadott megőrzési időtartamnál. Ha például a helyreállítható törlés megőrzési időtartama 7 nap, akkor az időponthoz tartozó visszaállítás megőrzési időtartama 1 és 6 nap között lehet.

> [!IMPORTANT]
> Az adatok egy halmazának visszaállításához szükséges idő a visszaállítási időszakban végrehajtott írási és törlési műveletek számától függ. Például egy 1 000 000 objektummal rendelkező, naponta hozzáadott 3 000-objektummal rendelkező fiók, valamint a naponta törölt 1 000-objektumok esetében körülbelül két óra elteltével vissza kell állítani egy 30 napos pontot a múltban. A megőrzési időtartam és a helyreállítás több mint 90 nappal a múltban nem ajánlott olyan fiók esetében, amely ezen a változási aránnyal rendelkezik.

### <a name="permissions-for-point-in-time-restore"></a>Az időponthoz való visszaállításhoz szükséges engedélyek

A visszaállítási művelet elindításához az ügyfélnek írási engedéllyel kell rendelkeznie a Storage-fiókban lévő összes tárolóhoz. Ha engedélyeket szeretne adni a Azure Active Directory (Azure AD) szolgáltatással való visszaállítási művelet engedélyezéséhez, rendelje hozzá a **Storage-fiók közreműködői** szerepkört a rendszerbiztonsági tag számára a Storage-fiók, az erőforráscsoport vagy az előfizetés szintjén.

## <a name="limitations-and-known-issues"></a>Korlátozások és ismert problémák

A blokk-Blobok időponthoz való visszaállítása a következő korlátozásokkal és ismert problémákkal rendelkezik:

- Egy adott időponthoz tartozó visszaállítási művelet részeként csak a standard általános célú v2-es Storage-fiókban található Blobok állíthatók vissza. A Blobok, a blobok és a prémium blokk Blobok hozzáfűzése nem állítható vissza. 
- Ha törölt egy tárolót a megőrzési időszak alatt, a rendszer nem állítja vissza a tárolót az időponthoz tartozó visszaállítási művelettel. Ha olyan Blobok egy tartományát kísérli meg visszaállítani, amelyek blobokat tartalmaznak egy törölt tárolóban, az időponthoz tartozó visszaállítási művelet sikertelen lesz. A tárolók törlésből való védelmének megismeréséhez lásd: [tárolók törlésének törlése (előzetes verzió)](soft-delete-container-overview.md).
- Ha egy blob a jelen pillanatban és a visszaállítási pont közötti időszakban a gyors és a lassú elérési szint között mozgott, a blob vissza lesz állítva az előző szintjére. A blokk-Blobok archiválási szinten való visszaállítása nem támogatott. Ha például egy blob két nappal ezelőtt át lett helyezve a gyakori elérésű szintről az archív szintre, és a visszaállítási művelet egy három nappal korábbi pontra állítja vissza a rendszert, a blob nem lesz vissza állítva a gyakori elérésű szintre. Archivált blob visszaállításához először helyezze át az archiválási szintről. További információkért lásd: [Blobok adatainak rehidratálása az archív szintről](storage-blob-rehydration.md).
- Egy olyan blokk, amely egy [put blokkon](/rest/api/storageservices/put-block) keresztül lett feltöltve, vagy az [URL-címről](/rest/api/storageservices/put-block-from-url), de nem a [put blokk](/rest/api/storageservices/put-block-list)használatával véglegesítve van, nem része a blobnak, ezért a visszaállítási művelet részeként nem lesz visszaállítva.
- Az aktív bérlettel rendelkező Blobok nem állíthatók vissza. Ha egy aktív bérlettel rendelkező blob szerepel a visszaállítani kívánt Blobok tartományában, a visszaállítási művelet atomi módon sikertelen lesz. A visszaállítási művelet megkezdése előtt szüntesse meg az aktív címbérletek megadását.
- A pillanatképeket a rendszer nem hozza létre vagy törli a visszaállítási művelet részeként. A rendszer csak az alap blobot állítja vissza az előző állapotába.
- Azure Data Lake Storage Gen2 lapos és hierarchikus névterek visszaállítása nem támogatott.

> [!IMPORTANT]
> Ha a blokk-blobokat a 2020. szeptember 22. előtti pontra állítja vissza, a rendszer az időponthoz tartozó visszaállításra vonatkozó előzetes korlátozásokat is érvénybe lép. A Microsoft azt javasolja, hogy az általánosan elérhető időpontra vonatkozó visszaállítási funkció kihasználása érdekében válasszon egy olyan visszaállítási pontot, amely a 2020. szeptember 22-én egyenlő vagy annál újabb.

## <a name="pricing-and-billing"></a>Árak és számlázás

Az időponthoz tartozó visszaállítás nem díjköteles. Az időponthoz való visszaállítás engedélyezése azonban lehetővé teszi a blob verziószámozását, a helyreállítható törlést és a hírcsatorna módosítását is, amelyek mindegyike további díjakat eredményezhet.

Az időponthoz való visszaállítás számlázása a visszaállítási művelet végrehajtásához feldolgozott adatok mennyiségétől függ. A feldolgozott adatmennyiség a visszaállítási pont és a jelen pillanatban történt változások száma alapján történik. Tegyük fel például, hogy a blob-adat tárolási fiókban való letiltásának viszonylag állandó változása miatt az 1 nap múlva visszaadott visszaállítási művelet 1 – 10 nap múlva vissza fog térni a visszaállításra.

A visszaállítási művelet költségeit a helyreállítási időszak során módosított adatmennyiség becsléséhez tekintse át a változási hírcsatorna naplójában. Ha például a változási csatorna megőrzési időtartama 30 nap, és a változási csatorna mérete 10 MB, akkor a korábbi 10 napos időpontra való visszaállítás körülbelül egyharmadát veszi igénybe az adott régióban egy LRS-fiókhoz. A korábban 27 nappal korábbi időpontra történő visszaállítás a felsorolt díjak körülbelül kilenc tizedét fogja kifizetni.

Az időponthoz tartozó visszaállítás díjszabásáról további információt a [Blobok díjszabásának blokkolása](https://azure.microsoft.com/pricing/details/storage/blobs/)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

- [Időponthoz tartozó visszaállítás végrehajtása a blob-adatok blokkolása közben](point-in-time-restore-manage.md)
- [A hírcsatornák támogatásának módosítása az Azure-ban Blob Storage](storage-blob-change-feed.md)
- [Blobok helyreállítható törlésének engedélyezése](./soft-delete-blob-enable.md)
- [BLOB-verziószámozás engedélyezése és kezelése](versioning-enable.md)
