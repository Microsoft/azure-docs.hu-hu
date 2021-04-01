---
title: Azure Data Box Disk korlátok | Microsoft Docs
description: A rendszerkorlátokat és a Microsoft Azure Data Box Disk ajánlott méreteit ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 9975533e00b0ca184e7cc16c5d8ea51d4eafa0a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361706"
---
# <a name="azure-data-box-disk-limits"></a>Azure Data Box Disk korlátok


Vegye figyelembe ezeket a korlátokat a Microsoft Azure Data Box Disk megoldás üzembe helyezése és üzemeltetése során.

## <a name="data-box-service-limits"></a>Data Box szolgáltatási korlátok

 - Data Box szolgáltatás a [régió rendelkezésre állása területen](data-box-disk-overview.md#region-availability)felsorolt Azure-régiókban érhető el.
 - Data Box Disk támogatja az egyetlen Storage-fiókot.

## <a name="data-box-disk-performance"></a>Data Box Disk teljesítmény

A tesztjeink során USB 3.0-ás kapcsolat esetén a lemezeknél 430 MB/s-os adatátviteli sebességet is mértünk. A tényleges szám a használt fájl méretének függvényében eltérő lehet. Kisebb fájlok esetén gyengébb teljesítmény figyelhető meg.

## <a name="azure-storage-limits"></a>Azure Storage-korlátok

Ez a szakasz az Azure Storage szolgáltatásra vonatkozó korlátozásokat, valamint a Azure Files, az Azure Block blobok és az Azure Page Blobok szükséges elnevezési konvencióit írja le a Data Box szolgáltatásnak megfelelően. Alaposan tekintse át a tárolási korlátokat, és kövesse az összes javaslatot.

Az Azure Storage szolgáltatás korlátaival és a megosztások, tárolók és fájlok elnevezésére vonatkozó ajánlott eljárásokkal kapcsolatos legfrissebb információkért keresse fel a következőt:

- [Tárolók elnevezése és hivatkozása](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Megosztások elnevezése és hivatkozása](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blobok és lapok blob-konvencióinak letiltása](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Ha vannak olyan fájlok vagy könyvtárak, amelyek túllépik az Azure Storage szolgáltatás korlátozásait, vagy nem felelnek meg Azure Files/blob elnevezési konvencióknak, akkor ezeket a fájlokat vagy könyvtárakat a rendszer nem tölti be az Azure Storage-ba a Data Box szolgáltatáson keresztül.

## <a name="data-upload-caveats"></a>Az adatfeltöltés kikötései

- Ne másolja közvetlenül a lemezekre az Adatmásolást. Az Adatmásolás előre létrehozott *BlockBlob*,*PageBlob* és *AzureFile* mappákba.
- A *BlockBlob* és a *PageBlob* alá tartozó mappa egy tároló. A tárolók például *BlockBlob/tárolóként* és *PageBlob/tárolóként* jönnek létre.
- Ha van egy meglévő Azure-objektuma (például egy blob) a felhőben, amelynek a neve megegyezik az éppen másolt objektum nevével, Data Box Disk átnevezi a fájlt fájlként (1) a felhőben.
- A rendszer a *BlockBlob* és a *PageBlob* -megosztásba írt összes fájlt feltöltötte egy blokk-blobként és egy oldal blobként.
- A *BlockBlob* és a *PageBlob* mappák alatt létrehozott összes üres címtár-hierarchia nincs feltöltve.
- Ha bármilyen hiba merül fel az Azure-ba történő adatfeltöltés során, a rendszer egy naplófájlt hoz létre a célként szolgáló Storage-fiókban. A hibanapló elérési útja elérhető a portálon a feltöltés befejeződése után, és áttekintheti a naplót a kijavítani kívánt művelet elvégzéséhez. Ne törölje a forrás adatait a feltöltött adatok ellenőrzése nélkül.
- A fájl-metaadatok és az NTFS-engedélyek nem őrződnek meg, amikor a rendszer feltölti az adatokat Azure Filesba. A fájlok *utolsó módosított* attribútuma például nem lesz megtartva az adatmásoláskor.
- Ha a felügyelt lemezeket a sorrendben adta meg, tekintse át az alábbi további szempontokat:

    - Egy erőforráscsoport csak egy adott nevű felügyelt lemezt tartalmazhat az előre létrehozott mappákban és a teljes Data Box Disken. Ez azt is jelenti, hogy az előre létrehozott mappákba feltöltött VHD-knak egyedi névvel kell rendelkezniük. Győződjön meg arról, hogy a megadott név nem egyezik meg egy erőforráscsoport meglévő felügyelt lemez nevével sem. Ha a VHD-k azonos névvel rendelkeznek, akkor csak egy VHD lesz átalakítva egy azonos névvel rendelkező felügyelt lemezzé. A többi VHD-fájl lapblobként lesz feltöltve az előkészítési tárfiókba.
    - Mindig másolja át a VHD-kat az egyik előre létrehozott mappába. Ha a VHD-kat ezen mappákon kívülre vagy egy Ön által létrehozott mappába másolja, akkor a VHD-k nem felügyelt lemezekként, hanem lapblobként lesznek feltöltve az Azure Storage-fiókba.
    - Csak a rögzített VHD-ket lehet feltölteni felügyelt lemezek létrehozásához. A dinamikus VHD-k, a virtuális különbséglemezek és a VHDX-fájlok nem támogatottak.
    - Az előlétrehozott felügyelt lemezekre másolt nem VHD-fájlokat a rendszer nem konvertálja felügyelt lemezre.

## <a name="azure-storage-account-size-limits"></a>Azure Storage-fiók méretének korlátai

Az alábbiakban megtalálhatja a Storage-fiókba másolható adatméret korlátozásait. Győződjön meg arról, hogy a feltöltött adatok megfelelnek a határértékeknek. 

| Adatok típusa             | Alapértelmezett korlát          |
|--------------------------|------------------------|
| BLOB blokkolása, oldal blobja    | A korlátokkal kapcsolatos aktuális információk: [Azure Blob Storage méretezési célok](../storage/blobs/scalability-targets.md#scale-targets-for-blob-storage), [Azure standard Storage méretezési](../storage/common/scalability-targets-standard-account.md#scale-targets-for-standard-storage-accounts)célok és [Azure Files méretezési célok](../storage/files/storage-files-scale-targets.md). <br /><br /> A korlátok tartalmazzák az összes forrás adatait, beleértve a Data Box Disk is.|


## <a name="azure-object-size-limits"></a>Azure-objektum méretének korlátai

Itt láthatók a megírható Azure-objektumok méretei. Győződjön meg arról, hogy az összes feltöltött fájl megfelel a fenti korlátoknak.

| Azure-objektumtípus | Alapértelmezett korlát                                             |
|-------------------|-----------------------------------------------------------|
| BLOB letiltása        | ~ 4,75 TiB                                                 |
| Oldal Blobja         | 8 TiB <br> (Az oldal blob-formátumában feltöltött összes fájlnak 512 bájtnak kell lennie, különben a feltöltés meghiúsul. <br> A VHD és a VHDX egyaránt 512 bájtra van igazítva.) |
|Azure Files        | 1 TiB <br> Legfeljebb a megosztás mérete 5 TiB     |
| Felügyelt lemezek     |4 TiB <br> A mérettel és a korlátokkal kapcsolatos további információkért lásd: <li>[A felügyelt lemezek skálázhatósági céljai](../virtual-machines/disks-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Az Azure Block blob, az oldal blobja és a fájl elnevezési konvenciói

| Entitás                                       | Konvenciók                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tárolók nevei a blob és az oldal blobja számára <br> Azure Files fájlmegosztás neve | Érvényes DNS-névnek kell lennie, amely 3 – 63 karakter hosszú lehet. <br>  Betűvel vagy számmal kell kezdődnie. <br> Csak kisbetűket, számokat és kötőjelet (-) tartalmazhat. <br> Minden kötőjel (-) előtt és után közvetlenül egy betűnek vagy számnak kell állnia. <br> A tárolók nevében nem szerepelhetnek egymást követő kötőjelek. |
| Az Azure Files címtár-és fájlnevei     |<li> Kis-és nagybetűket megőrző, kis-és nagybetűk megkülönböztetése, és legfeljebb 255 karakter hosszú lehet. </li><li> A perjel (/) nem végződhet. </li><li>Ha meg van adni, a rendszer automatikusan eltávolítja. </li><li> A következő karakterek nem engedélyezettek: <code>" \\ / : \| < > * ?</code></li><li> A fenntartott URL-karaktereket escape-karakterrel kell jelölni. </li><li> A nem engedélyezett URL-címek elérési útja nem megengedett. \\A kódok, például a uE000 nem érvényes Unicode-karakterek. Bizonyos ASCII-vagy Unicode-karakterek, például a vezérlési karakterek (kell lennie 0x00 – 0x1F, \\ u0081 stb.) nem engedélyezettek. A Unicode karakterláncokat a HTTP/1.1-es verzióra vonatkozó szabályoknál lásd: RFC 2616, 2,2. szakasz, alapszintű szabályok és RFC 3987. </li><li> A következő fájlnevek nem engedélyezettek: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK $, dot Character (.), és két pont (...).</li>|
| Blobnevek blokkblob és lapblob esetén      | A blobnevek megkülönböztetik a kis- és nagybetűket, illetve bármilyen karakterkombinációt tartalmazhatnak <br> A blob nevének 1–1024 karakter hosszúságúnak kell lennie. <br> A fenntartott URL-karaktereket escape-karakterrel kell jelölni. <br>A blob nevét alkotó részleges útvonalak száma legfeljebb 254 lehet. A részleges útvonalak két elválasztókarakter (pl. a perjel „/”) közötti sztringek, amelyek megegyeznek egy virtuális könyvtár nevével. |

## <a name="managed-disk-naming-conventions"></a>Felügyelt lemez elnevezési konvenciói

| Entitás | Konvenciók                                             |
|-------------------|-----------------------------------------------------------|
| Felügyelt lemezek nevei       | <li> A névnek 1 – 80 karakter hosszúnak kell lennie. </li><li> A névnek betűvel vagy számmal kell kezdődnie, betűvel, számmal vagy aláhúzással kell végződnie. </li><li> A név csak betűket, számokat, aláhúzásokat, pontokat és kötőjeleket tartalmazhat. </li><li>   A névnek nem lehetnek szóközök vagy `/` .                                              |

## <a name="next-steps"></a>Következő lépések

- A [rendszerkövetelmények áttekintése Data Box Disk](data-box-disk-system-requirements.md)