---
title: Azure Data Box Disk – hibaelhárítás | Microsoft Docs
description: Ez a cikk az Azure Data Box Disk szolgáltatásban jelentkező hibák elhárítását írja le.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67805709"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>A Azure Data Box Disk érvényesítési problémáinak elhárítása naplók használatával

Ez a cikk Microsoft Azure Data Box Diskre vonatkozik. A cikk azt ismerteti, hogyan használhatók a naplók a megoldás telepítésekor megjelenő érvényesítési problémák elhárításához.

## <a name="validation-tool-log-files"></a>Ellenőrző eszköz naplófájljai

Ha az [ellenőrzési eszközzel](data-box-disk-deploy-copy-data.md#validate-data)ellenőrzi a lemezeken lévő adatellenőrzéseket, a rendszer egy *error. xml fájlt* hoz létre a hibák naplózásához. A naplófájl a meghajtó `Drive:\DataBoxDiskImport\logs` mappájában található. Az érvényesítés futtatásakor a rendszer a hibanapló hivatkozását is megadja.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Ha több munkamenetet is futtat az ellenőrzéshez, a rendszer egy naplófájlt hoz létre munkamenetenként.

- Itt látható a hibanapló mintája, ha a `PageBlob` mappába betöltött adat nem 512 bájtra van igazítva. A PageBlob feltöltött összes adatnak 512 bájthoz igazítottnak kell lennie, például egy VHD-vagy VHDX. A fájlban található hibák a `<Errors>` és a figyelmeztetései. `<Warnings>`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Itt látható a hibanapló mintája, ha a tároló neve érvénytelen. A (z) vagy `BlockBlob` `PageBlob` `AzureFile` a (z) alatt létrehozott mappa az Azure Storage-fiókban található tároló lesz. A tároló nevének meg kell felelnie az [Azure elnevezési konvencióinak](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Ellenőrző eszköz hibái

A *error. XML* fájlban található hibák a megfelelő ajánlott műveletekkel együtt a következő táblázatban vannak összefoglalva.

| Hibakód| Leírás                       | Ajánlott műveletek               |
|------------|--------------------------|-----------------------------------|
| `None` | Az adatellenőrzés sikeresen megtörtént. | Semmit nem kell tenni. |
| `InvalidXmlCharsInPath` |Nem sikerült létrehozni a jegyzékfájlt, mert a fájl elérési útja érvénytelen karaktereket tartalmaz. | A folytatáshoz távolítsa el ezeket a karaktereket.  |
| `OpenFileForReadFailed`| Nem lehetett feldolgozni a fájlt. Ennek oka lehet egy hozzáférési probléma vagy a fájlrendszer sérülése.|Hiba miatt nem sikerült beolvasni a fájlt. A hiba részletei a kivételek. |
| `Not512Aligned` | Ez a fájl nem érvényes formátumú a PageBlob mappához.| Csak a `PageBlob` mappához igazított 512 bájtos adatok feltöltése. Távolítsa el a fájlt a PageBlob mappából, vagy helyezze át a BlockBlob mappába. Próbálja megismételni az ellenőrzést.|
| `InvalidBlobPath` | A fájl elérési útja nem a felhőben érvényes blob-elérési úttal van leképezve, mint az Azure Blob elnevezési konvenciói.|A fájl elérési útjának átnevezéséhez kövesse az Azure elnevezési irányelveit. |
| `EnumerationError` | Nem lehetett enumerálni a fájlt az érvényesítéshez. |Ennek a hibának több oka is lehet. Ennek legvalószínűbb oka a fájlhoz való hozzáférés. |
| `ShareSizeExceeded` | A fájl miatt az Azure-fájlmegosztás mérete túllépte az Azure-os korlátot 5 TB-nál.|Csökkentse a megosztásban lévő adatméretet, hogy az megfeleljen az [Azure-objektumok méretének](data-box-disk-limits.md#azure-object-size-limits). Próbálja megismételni az ellenőrzést. |
| `AzureFileSizeExceeded` | A fájl mérete meghaladja az Azure-fájlméret korlátait.| Csökkentse a fájl méretét vagy az adatmennyiséget úgy, hogy az megfeleljen az Azure- [objektumok méretére vonatkozó korlátozásoknak](data-box-disk-limits.md#azure-object-size-limits). Próbálja megismételni az ellenőrzést.|
| `BlockBlobSizeExceeded` | A fájl mérete meghaladja az Azure Block blob méretének korlátozásait. | Csökkentse a fájl méretét vagy az adatmennyiséget úgy, hogy az megfeleljen az Azure- [objektumok méretére vonatkozó korlátozásoknak](data-box-disk-limits.md#azure-object-size-limits). Próbálja megismételni az ellenőrzést. |
| `ManagedDiskSizeExceeded` | A fájlméret meghaladja az Azure Managed Disk size korlátait. | Csökkentse a fájl méretét vagy az adatmennyiséget úgy, hogy az megfeleljen az Azure- [objektumok méretére vonatkozó korlátozásoknak](data-box-disk-limits.md#azure-object-size-limits). Próbálja megismételni az ellenőrzést. |
| `PageBlobSizeExceeded` | A fájlméret meghaladja az Azure Managed Disk size korlátait. | Csökkentse a fájl méretét vagy az adatmennyiséget úgy, hogy az megfeleljen az Azure- [objektumok méretére vonatkozó korlátozásoknak](data-box-disk-limits.md#azure-object-size-limits). Próbálja megismételni az ellenőrzést. |
| `InvalidShareContainerFormat`  |A címtár neve nem felel meg a tárolók és a megosztások Azure elnevezési konvencióinak.         |A lemezen a már meglévő mappák alatt létrehozott első mappa a Storage-fiókban található tároló lesz. Ez a megosztás vagy tároló neve nem felel meg az Azure elnevezési konvencióinak. Nevezze át a fájlt úgy, hogy az megfeleljen az [Azure elnevezési konvencióinak](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Próbálja megismételni az ellenőrzést.   |
| `InvalidBlobNameFormat` | A fájl elérési útja nem a felhőben érvényes blob-elérési úttal van leképezve, mint az Azure Blob elnevezési konvenciói.|Nevezze át a fájlt úgy, hogy az megfeleljen az [Azure elnevezési konvencióinak](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Próbálja megismételni az ellenőrzést. |
| `InvalidFileNameFormat` | A fájl elérési útja nem képezi le érvényes elérési utat a felhőben az Azure-fájl elnevezési konvenciói alapján. |Nevezze át a fájlt úgy, hogy az megfeleljen az [Azure elnevezési konvencióinak](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Próbálja megismételni az ellenőrzést. |
| `InvalidDiskNameFormat` | A fájl elérési útja nem a felhőben érvényes, hanem az Azure által felügyelt lemez elnevezési konvenciói alapján van leképezve. |Nevezze át a fájlt úgy, hogy az megfeleljen az [Azure elnevezési konvencióinak](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Próbálja megismételni az ellenőrzést.       |
| `NotPartOfFileShare` | A fájlok feltöltési elérési útja érvénytelen. Töltse fel a fájlokat Azure Files egy mappájába.   | Távolítsa el a hibát tartalmazó fájlokat, és töltse fel ezeket a fájlokat egy előlétrehozott mappába. Próbálja megismételni az ellenőrzést. |
| `NonVhdFileNotSupportedForManagedDisk` | Nem VHD-fájl nem tölthető fel felügyelt lemezként. |Távolítsa el a nem VHD- `ManagedDisk` fájlokat a mappából, mivel ezek nem támogatottak, vagy `PageBlob` helyezze át ezeket a fájlokat egy mappába. Próbálja megismételni az ellenőrzést. |


## <a name="next-steps"></a>További lépések

- [Az adatfeltöltési hibák](data-box-disk-troubleshoot-upload.md)elhárítása.
