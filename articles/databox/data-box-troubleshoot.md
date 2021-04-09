---
title: A Azure Data Box kapcsolatos problémák elhárítása Azure Data Box Heavy
description: Ismerteti, hogyan lehet elhárítani a Azure Data Box és Azure Data Box Heavy során észlelt problémákat, amikor az adatok másolása ezekre az eszközökre történik.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: bb70946fda4fad7a42fd885a2515cb0d82698eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124675"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box és Azure Data Box Heavy kapcsolatos problémák elhárítása

Ez a cikk a Azure Data Box vagy Azure Data Box Heavy importálási rendelésekhez való használatakor esetlegesen felmerülő problémák elhárításával kapcsolatos információkat ismerteti. A cikk azokat a lehetséges hibákat tartalmazza, amelyeket az adatok a Data Boxba való másolásakor, illetve az adatok Data Box importálási rendelésre való feltöltésekor észleltek.

A cikkben szereplő információk nem vonatkoznak a Data Boxhoz létrehozott exportálási rendelésekre.

## <a name="error-classes"></a>Hiba osztályai

A Data Box és Data Box Heavy hibáit a következőképpen összegzi:

| Hiba kategóriája *        | Leírás        | Javasolt művelet    |
|----------------------------------------------|---------|--------------------------------------|
| Tároló vagy megosztás neve | A tároló vagy a megosztás neve nem követi az Azure elnevezési szabályait.  |Töltse le a hibák listáját. <br> Nevezze át a tárolókat vagy a megosztásokat. [További információk](#container-or-share-name-errors).  |
| Tároló vagy megosztási méretkorlát | A tárolókban vagy megosztásokban lévő teljes adatmennyiség meghaladja az Azure-korlátot.   |Töltse le a hibák listáját. <br> Csökkentse a tárolóban vagy megosztásban lévő összesített adatmennyiséget. [További információk](#container-or-share-size-limit-errors).|
| Objektum vagy fájlméret korlátja | A tárolókban vagy megosztásokban lévő objektum vagy fájlok mérete meghaladja az Azure-korlátot.|Töltse le a hibák listáját. <br> Csökkentse a fájl méretét a tárolóban vagy a megosztásban. [További információk](#object-or-file-size-limit-errors). |    
| Adattípusok vagy fájltípusok | Az adatformátum vagy a fájl típusa nem támogatott. |Töltse le a hibák listáját. <br> Az oldal Blobok vagy a felügyelt lemezek esetében ügyeljen arra, hogy az adat 512 – bájt igazított legyen, és az előre létrehozott mappákba legyen másolva. [További információk](#data-or-file-type-errors). |
| Nem kritikus blob-vagy fájl-hibák  | A blob-vagy fájlnevek nem követik az Azure elnevezési szabályait, vagy a fájl típusa nem támogatott. | Előfordulhat, hogy ezek a Blobok vagy fájlok nem másolhatók, vagy a nevek változhatnak. [További információ a hibák kijavításáról](#non-critical-blob-or-file-errors). |

\* Az első négy hiba kategóriája kritikus hibák, és a szállítás előkészítése előtt meg kell oldani.


## <a name="container-or-share-name-errors"></a>Tároló vagy megosztási név hibái

Ezek a tárolókkal és a megosztási nevekkel kapcsolatos hibák.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Hiba leírása:** A tárolónak vagy a megosztás nevének 3 és 63 karakter közöttinek kell lennie. 

**Javasolt megoldás:** A Data Box vagy Data Box Heavy megosztás (SMB/NFS) alatt lévő mappa, amelyhez az adatmásolt, az Azure-tároló lesz a Storage-fiókban. 

- Az eszköz helyi webes FELÜLETének **Csatlakoztatás és másolás** lapján töltse le és tekintse át a hibákat tartalmazó mappanevek azonosításához szükséges fájlokat.
- Módosítsa a mappa nevét a Data Box vagy Data Box Heavy megosztás alatt, hogy a következőket biztosítsa:

    - A név 3 – 63 karakterből áll.
    - A nevek csak betűket, számokat és kötőjeleket tartalmazhatnak.
    - A nevek nem kezdődhetnek és nem végződhet kötőjelekkel.
    - A nevek nem rendelkezhetnek egymást követő kötőjelekkel.
    - Példák érvényes nevekre: `my-folder-1` , `my-really-extra-long-folder-111`
    - Példák a nem érvényes nevekre: `my-folder_1` ,,, `my` `--myfolder` `myfolder--` , `myfolder!`

    További információ: az Azure elnevezési konvenciói a [tárolók neveihez](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) és a [megosztási nevekhez](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Hiba leírása:** A tárolónak vagy a megosztás nevének csak betűkből, számokból vagy kötőjelből kell állnia.

**Javasolt megoldás:** A Data Box vagy Data Box Heavy megosztás (SMB/NFS) alatt lévő mappa, amelyhez az adatmásolt, az Azure-tároló lesz a Storage-fiókban. 

- Az eszköz helyi webes FELÜLETének **Csatlakoztatás és másolás** lapján töltse le és tekintse át a hibákat tartalmazó mappanevek azonosításához szükséges fájlokat.
- Módosítsa a mappa nevét a Data Box vagy Data Box Heavy megosztás alatt, hogy a következőket biztosítsa:

    - A név 3 – 63 karakterből áll.
    - A nevek csak betűket, számokat és kötőjeleket tartalmazhatnak.
    - A nevek nem kezdődhetnek és nem végződhet kötőjelekkel.
    - A nevek nem rendelkezhetnek egymást követő kötőjelekkel.
    - Példák érvényes nevekre: `my-folder-1` , `my-really-extra-long-folder-111`
    - Példák a nem érvényes nevekre: `my-folder_1` ,,, `my` `--myfolder` `myfolder--` , `myfolder!`

    További információ: az Azure elnevezési konvenciói a [tárolók neveihez](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) és a [megosztási nevekhez](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Hiba leírása:** A tároló neve és a megosztás neve nem kezdődhet és nem végződhet kötőjelekkel, és nem rendelkezhet egymás utáni kötőjelekkel.

**Javasolt megoldás:** A Data Box vagy Data Box Heavy megosztás (SMB/NFS) alatt lévő mappa, amelyhez az adatmásolt, az Azure-tároló lesz a Storage-fiókban. 

- Az eszköz helyi webes FELÜLETének **Csatlakoztatás és másolás** lapján töltse le és tekintse át a hibákat tartalmazó mappanevek azonosításához szükséges fájlokat.
- Módosítsa a mappa nevét a Data Box vagy Data Box Heavy megosztás alatt, hogy a következőket biztosítsa:

    - A név 3 – 63 karakterből áll.
    - A nevek csak betűket, számokat és kötőjeleket tartalmazhatnak.
    - A nevek nem kezdődhetnek és nem végződhet kötőjelekkel.
    - A nevek nem rendelkezhetnek egymást követő kötőjelekkel.
    - Példák érvényes nevekre: `my-folder-1` , `my-really-extra-long-folder-111`
    - Példák a nem érvényes nevekre: `my-folder_1` ,,, `my` `--myfolder` `myfolder--` , `myfolder!`

    További információ: az Azure elnevezési konvenciói a [tárolók neveihez](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) és a [megosztási nevekhez](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).
    
### <a name="error_file_or_directory_name_illegal"></a>ERROR_FILE_OR_DIRECTORY_NAME_ILLEGAL

**Hiba leírása**: a könyvtár vagy a tároló neve érvénytelen karaktereket tartalmaz.

**Javasolt megoldás**: a másolt könyvtár vagy tároló neve nem támogatott karaktereket tartalmaz.

- A helyi webes felhasználói felület kapcsolódás és másolás lapján töltse le a fájlt, és tekintse át a hibákat tartalmazó mappanevek azonosításához szükséges fájlokat. 
- Nevezze át a könyvtárat vagy a tárolókat annak biztosítására, hogy megfeleljenek az Azure elnevezési konvencióinak.

További információ: az Azure elnevezési konvenciói a [címtárakhoz](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) és a [tárolóhoz](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names).

## <a name="container-or-share-size-limit-errors"></a>Tároló vagy megosztási méretkorlát hibái

Ezek a tárolóban vagy megosztásban engedélyezett adatméretet meghaladó adatmennyiséggel kapcsolatos hibák.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Hiba leírása:** Az Azure-fájlmegosztás 5 TiB-ra korlátozza a megosztást, és a Storage-fiókon nincs engedélyezve a nagyméretű fájlmegosztás. Néhány megosztásnál túllépte a korlátot.

**Javasolt megoldás:** A helyi webes felhasználói felület **csatlakozási és másolás** lapján töltse le és tekintse át a hibaüzeneteket.

- Azonosítsa a hibát tartalmazó mappákat a hibák naplófájljaiban, és győződjön meg arról, hogy a mappában található fájlok 5 TiB alatt vannak.
- Az 5 TiB-korlát nem érvényes olyan Storage-fiókra, amely lehetővé teszi a nagyméretű fájlmegosztást. A megrendelés elhelyezésekor azonban nagy méretű fájlmegosztást kell konfigurálnia. 
  - Lépjen kapcsolatba [Microsoft ügyfélszolgálata](data-box-disk-contact-microsoft-support.md) , és kérjen egy új szállítási címkét.
  - [Engedélyezze a nagyméretű fájlmegosztást a Storage-fiókon.](../storage/files/storage-files-how-to-create-large-file-share.md#enable-large-files-shares-on-an-existing-account)
  - [Bontsa ki a fájlmegosztás elemet a Storage-fiókban](../storage/files/storage-files-how-to-create-large-file-share.md#expand-existing-file-shares) , és állítsa a kvótát 100 TiB-ra.
  
  
## <a name="object-or-file-size-limit-errors"></a>Objektum-vagy fájlméret-korlátozási hibák

Ezek az objektumok maximális méretét vagy az Azure-ban engedélyezett fájlt meghaladó adatmennyiséggel kapcsolatos hibák. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Hiba leírása:** A fájl mérete meghaladja a feltöltéshez szükséges maximális fájlméretet.

**Javasolt megoldás:** A blob vagy a fájlméret mérete meghaladja a feltöltéshez engedélyezett maximális korlátot.

- A helyi webes felhasználói felület **csatlakozási és másolás** lapján töltse le és tekintse át a hibaüzeneteket.
- Győződjön meg arról, hogy a blob és a fájl mérete nem haladja meg az Azure-objektum méretének korlátait.

## <a name="data-or-file-type-errors"></a>Adatfájlok vagy fájltípusok hibái

Ezek a tárolóban vagy megosztásban található nem támogatott fájltípussal vagy adattípussal kapcsolatos hibák. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Hiba leírása:** A blob vagy a fájl helytelenül van igazítva.

**Javasolt megoldás:** Az oldal blobjának megosztása Data Box vagy Data Box Heavy csak a 512 bájthoz igazított fájlokat támogatja (például VHD/VHDX). Az oldal blob-megosztására másolt összes adattal feltölti az Azure-ba.

Távolítsa el a nem VHD-vagy VHDX-adatait az oldal blob-megosztásáról. Használhat megosztásokat a Blobok vagy az Azure Files számára az általános adatforgalomhoz.

További információ: [az oldal Blobok áttekintése](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Hiba leírása:** Nem támogatott fájltípus található a felügyelt lemezes megosztásban. Csak a rögzített VHD-k engedélyezettek.

**Javasolt megoldás:**

- Győződjön meg arról, hogy csak a rögzített VHD-ket tölti fel a felügyelt lemezek létrehozásához.
- A VHDX-fájlok, illetve a **dinamikus** és a **különbséglemezek** VHD-k nem támogatottak.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Hiba leírása:** A felügyelt lemezek valamelyik már létező mappájában nem szerepelhet könyvtár. Ezekben a mappákban csak a rögzített VHD-k engedélyezettek.

**Javasolt megoldás:** A felügyelt lemezek esetében az egyes megosztásokon belül a következő három mappa jön létre, amelyek a Storage-fiókban lévő tárolóknak felelnek meg: prémium SSD, standard HDD és standard SSD. Ezek a mappák a felügyelt lemez teljesítményi szintjének felelnek meg.

- Győződjön meg arról, hogy az oldal blob-adatait (VHD-ket) a meglévő mappák egyikére másolja.
- A meglévő mappákban nem szerepelhet mappa vagy könyvtár. Távolítson el minden olyan mappát, amelyet a korábban már létező mappákban hozott létre.

További információ: [Másolás felügyelt lemezekre](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Hiba leírása:** A szimbolikus hivatkozások használata nem engedélyezett a Linux rendszerben. 

**Javasolt megoldás:** A szimbolikus hivatkozások általában hivatkozások, csövek és más hasonló fájlok. Távolítsa el a hivatkozásokat, vagy oldja fel a hivatkozásokat, és másolja ki az adatfájlokat.


## <a name="non-critical-blob-or-file-errors"></a>Nem kritikus blob-vagy fájl-hibák

Az Adatmásolás során látható Blobok, fájlok vagy tárolók neveivel kapcsolatos összes nem kritikus hibát a következő szakasz foglalja össze. Ha ezek a hibák jelennek meg, akkor a nevek módosítva lesznek, hogy megfeleljenek az Azure elnevezési konvencióinak. Az adatok feltöltésének megfelelő sorrendi állapota **figyelmeztetésekkel fejeződik** be.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Hiba leírása:** A blob-vagy fájlnevek nem támogatott vezérlő karaktereket tartalmaznak.

**Javasolt megoldás:** A másolt Blobok vagy fájlok nem támogatott karaktereket tartalmazó neveket tartalmaznak.

A helyi webes felhasználói felület **csatlakozási és másolás** lapján töltse le és tekintse át a hibaüzeneteket.
A nem támogatott karakterek eltávolításához távolítsa el vagy nevezze át a fájlokat.

További információ: az Azure elnevezési konvenciói a [Blobok neveihez](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) [és fájlnevekhez.](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Hiba leírása:** A blob vagy a fájlnevek érvénytelen karaktereket tartalmaznak.

**Javasolt megoldás:** A másolt Blobok vagy fájlok nem támogatott karaktereket tartalmazó neveket tartalmaznak.

A helyi webes felhasználói felület **csatlakozási és másolás** lapján töltse le és tekintse át a hibaüzeneteket.
A nem támogatott karakterek eltávolításához távolítsa el vagy nevezze át a fájlokat.

További információ: az Azure elnevezési konvenciói a [Blobok neveihez](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) [és fájlnevekhez.](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Hiba leírása:** A blob vagy a fájl neve helytelen karaktereket tartalmaz.

**Javasolt megoldás:** A másolt Blobok vagy fájlok nem támogatott karaktereket tartalmazó neveket tartalmaznak.

A helyi webes felhasználói felület **csatlakozási és másolás** lapján töltse le és tekintse át a hibaüzeneteket.
A nem támogatott karakterek eltávolításához távolítsa el vagy nevezze át a fájlokat.

További információ: az Azure elnevezési konvenciói a [Blobok neveihez](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) [és fájlnevekhez.](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Hiba leírása:** A blob vagy a fájlnév túl sok elérésiút-szegmenst tartalmaz.

**Javasolt megoldás:** A Blobok vagy a másolt fájlok száma túllépi az elérésiút-szegmensek maximális számát. Az elérésiút-szegmens az egymást követő elválasztó karakterek (például a perjel/) közötti karakterlánc.

- A helyi webes felhasználói felület **csatlakozási és másolás** lapján töltse le és tekintse át a hibaüzeneteket.
- Győződjön meg arról, hogy a [Blobok nevei](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) és [fájlnevei](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) megfelelnek az Azure elnevezési konvencióinak.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Hiba leírása:** A blob vagy a fájl neve túl hosszú.

**Javasolt megoldás:** A blob vagy a fájlnevek mérete meghaladja a maximális hosszt.

- A helyi webes felhasználói felület **csatlakozási és másolás** lapján töltse le és tekintse át a hibaüzeneteket.
- A blob neve nem lehet hosszabb 1 024 karakternél.
- Távolítsa el vagy nevezze át a blobot vagy fájlokat, hogy a nevek ne lépjék túl a 1024 karaktert.

További információ: az Azure elnevezési konvenciói a Blobok neveihez és fájlnevekhez.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Hiba leírása:** A blob vagy a fájlnév egyik szegmense túl hosszú.

**Javasolt megoldás:** A blob vagy a fájlnév egyik elérésiút-szegmense meghaladja a karakterek maximális számát. Az elérésiút-szegmens az egymást követő elválasztó karakterek (például a perjel/) közötti karakterlánc.

- A helyi webes felhasználói felület **csatlakozási és másolás** lapján töltse le és tekintse át a hibaüzeneteket.
- Győződjön meg arról, hogy a [Blobok nevei](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) és [fájlnevei](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) megfelelnek az Azure elnevezési konvencióinak.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Hiba leírása:** A felügyelt lemezes megosztásokhoz nem megfelelő tárolók vannak megadva.

**Javasolt megoldás:** A felügyelt lemezek esetében az egyes megosztásokon belül a következő mappák jönnek létre, amelyek a Storage-fiókban lévő tárolóknak felelnek meg: prémium SSD, standard HDD és standard SSD. Ezek a mappák a felügyelt lemez teljesítményi szintjének felelnek meg.

- Győződjön meg arról, hogy az oldal blob-adatait (VHD-ket) a meglévő mappák egyikére másolja. Csak a meglévő tárolóból származó adatok lettek feltöltve az Azure-ba.
- Minden olyan mappa, amely a prémium SSD, standard HDD és standard SSD azonos szinten jön létre, nem felel meg egy érvényes teljesítményi szintnek, és nem használható.
- Távolítsa el a teljesítmény szintjein kívül létrehozott fájlokat vagy mappákat.

További információ: [Másolás felügyelt lemezekre](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Következő lépések

- A [blob Storage rendszerkövetelményeinek Data Box](data-box-system-requirements-rest.md)megismerése.