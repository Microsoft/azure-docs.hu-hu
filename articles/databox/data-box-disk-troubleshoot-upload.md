---
title: Adatfeltöltés a naplók használatával – problémamegoldás
titleSuffix: Azure Data Box Disk
description: Leírja, hogyan használhatók a naplók és a hibák elhárítása, amikor az adatfeltöltés Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 4b53cf607bdf60c785c7324d9ede526a0983b7e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97605275"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Az adatok feltöltésével kapcsolatos problémák elhárításához szükséges naplók ismertetése Azure Data Box Disk

Ez a cikk Microsoft Azure Data Box Diskra vonatkozik, és leírja az adatok Azure-ba való feltöltésekor megjelenő problémákat.

## <a name="about-upload-logs"></a>A naplók feltöltése

Az adatközpontban az Azure-ba való feltöltéskor a rendszer `_error.xml` `_verbose.xml` minden egyes Storage-fiókhoz hoz létre fájlokat. Ezeket a naplókat a rendszer ugyanarra a Storage-fiókba tölti fel, amelyet az adatok feltöltéséhez használt. 

Mindkét napló ugyanabban a formátumban van, és XML-leírást tartalmaz azokról az eseményekről, amelyek az adatok lemezről az Azure Storage-fiókba való másolása során történtek.

A részletes napló tartalmazza az összes blob vagy fájl másolási műveletének állapotát, míg a hibanapló csak a feltöltés során hibákat észlelő Blobok vagy fájlok adatait tartalmazza.

A hibanapló ugyanazzal a szerkezettel rendelkezik, mint a részletes napló, de kiszűri a sikeres műveleteket.

## <a name="download-logs"></a>Naplók letöltése

A feltöltési naplók megkereséséhez hajtsa végre az alábbi lépéseket.

1. Ha bármilyen hiba merül fel az Azure-ba való feltöltéskor, a portál megjeleníti a diagnosztikai naplókat tartalmazó mappa elérési útját.

    ![A portálon található naplókra mutató hivatkozás](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Nyissa meg a **waies**.

    ![hibák és részletes naplók](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

Minden esetben megjelenik a hibák naplói és a részletes naplók. Válassza ki az egyes naplókat, és töltsön le egy helyi másolatot.

## <a name="sample-upload-logs"></a>Minta feltöltési naplók

Alább látható egy minta `_verbose.xml` . Ebben az esetben a rendelés sikeresen befejeződött, hibák nélkül.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

Ugyanezen megrendelésnél az `_error.xml` alább látható egy minta.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Az `_error.xml` alábbi minta látható, ahol a rendelés hibákkal fejeződött be. 

Ebben az esetben a hibaüzenet `Summary` tartalmaz egy szakaszt és egy másik szakaszt, amely az összes fájl szintű hibát tartalmazza. 

A `Summary` tartalmazza a `ValidationErrors` és a `CopyErrors` . Ebben az esetben a rendszer 8 fájlt vagy mappát töltött fel az Azure-ba, és nincs érvényesítési hiba. Az Azure Storage-fiókba való másoláskor 5 fájl vagy mappa sikeresen feltöltve. A fennmaradó 3 fájlt vagy mappát az Azure-tároló elnevezési konvenciói alapján átnevezték, majd sikeresen feltöltöttük az Azure-ba.

A fájl szintjének állapota az, `BlobStatus` amely a Blobok feltöltésére tett összes műveletet ismerteti. Ebben az esetben a rendszer három tárolót nevez át, mert azok a mappák, amelyekhez az adatmásolt, nem feleltek meg a tárolók Azure elnevezési konvencióinak. A tárolókban feltöltött Blobok esetében az új tároló neve, az Azure-beli blob elérési útja, az eredeti érvénytelen elérési út és a blob mérete is szerepel.
  
```xml
 <?xml version="1.0" encoding="utf-8"?>
  <DriveLog Version="2018-10-01">
    <DriveId>18041C582D7E</DriveId>
    <Summary>
     <!--Summary for validation and data copy to Azure -->
      <ValidationErrors>
        <None Count="8" />
      </ValidationErrors>
      <CopyErrors>
        <Completed Count="5" Description="No errors encountered" />
        <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
      </CopyErrors>
    </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
      <SizeInBytes>71528448</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Status>CompletedWithErrors</Status>
  </DriveLog>
```

## <a name="data-upload-errors"></a>Adatfeltöltési hibák

Az Azure-ba történő adatfeltöltés során generált hibák a következő táblázatban láthatók.

| Hibakód | Description                   |
|-------------|------------------------------|
|`None` |  A művelet sikeresen befejeződött.           |
|`Renamed` | Sikerült átnevezni a blobot.   |
|`CompletedWithErrors` | A feltöltés hibákkal fejeződött be. A hibás fájlok részletei a naplófájlban találhatók.  |
|`Corrupted`|Az adatfeldolgozás során kiszámított CRC nem felel meg a feltöltés során kiszámított CRC-nek.  |  
|`StorageRequestFailed` | Az Azure Storage-kérelem sikertelen.   |     
|`LeasePresent` | Ez az tétel bérletbe kerül, és egy másik felhasználó használja. |
|`StorageRequestForbidden` |Hitelesítési problémák miatt nem lehet feltölteni. |
|`ManagedDiskCreationTerminalFailure` | Nem sikerült feltölteni a felügyelt lemezként. A fájlok az átmeneti Storage-fiókban az oldal Blobok néven érhetők el. Manuálisan is átalakíthatja a blobokat a felügyelt lemezekre.  |
|`DiskConversionNotStartedTierInfoMissing` | Mivel a VHD-fájlt az előkészített rétegek mappáján kívül másolták, a felügyelt lemez nem lett létrehozva. A fájlt a rendszer a rendelés létrehozásakor megadott módon feltölti az átmeneti tárolási fiókba. Manuálisan is konvertálhatja egy felügyelt lemezre.|
|`InvalidWorkitem` | Nem tölthetők fel az adatok, mert nem felel meg az Azure elnevezési és korlátozási konvencióinak.|
|`InvalidPageBlobUploadAsBlockBlob` | Egy előtaggal rendelkező tárolóban van feltöltve blokk-blobként `databoxdisk-invalid-pb-` .|
|`InvalidAzureFileUploadAsBlockBlob` | A tárolóban lévő blokk blobként van feltöltve a-előtaggal `databoxdisk-invalid-af` .|
|`InvalidManagedDiskUploadAsBlockBlob` | A tárolóban lévő blokk blobként van feltöltve a-előtaggal `databoxdisk-invalid-md` .|
|`InvalidManagedDiskUploadAsPageBlob` |Egy előtaggal rendelkező tárolóban fel van töltve az oldal blobként `databoxdisk-invalid-md-` . |
|`MovedToOverflowShare` |A fájlok új megosztásra való feltöltésekor az eredeti megosztás mérete túllépte az Azure-beli maximális méretkorlátot. Az új fájlmegosztás nevének az eredeti neve a (z) előtaggal `-2` .   |
|`MovedToDefaultAzureShare` |Olyan feltöltött fájlok, amelyek egyetlen mappa sem részei az alapértelmezett megosztásnak. A megosztás neve a (z) karakterlánccal kezdődik `databox-` . |
|`ContainerRenamed` |A fájlok tárolója nem felelt meg az Azure elnevezési konvencióinak, és át lett nevezve. Az új név a (z `databox-` ) előtaggal kezdődik, és az eredeti név SHA1-kivonatával van ellátva. |
|`ShareRenamed` |A fájlok megosztása nem felelt meg az Azure elnevezési konvencióinak, és át lett nevezve. Az új név a (z `databox-` ) előtaggal kezdődik, és az eredeti név SHA1 kivonatával van ellátva. |
|`BlobRenamed` |Ezek a fájlok nem feleltek meg az Azure elnevezési konvencióknak, és át lettek nevezve. Keresse `BlobPath` meg az új név mezőjét. |
|`FileRenamed` |Ezek a fájlok nem feleltek meg az Azure elnevezési konvencióknak, és át lettek nevezve. Keresse `FileStoragePath` meg az új név mezőjét. |
|`DiskRenamed` |Ezek a fájlok nem feleltek meg az Azure elnevezési konvencióknak, és át lettek nevezve. Keresse `BlobPath` meg az új név mezőjét. |


## <a name="next-steps"></a>Következő lépések

- [Támogatási jegy megnyitása Data Box Disk problémákhoz](data-box-disk-contact-microsoft-support.md).
