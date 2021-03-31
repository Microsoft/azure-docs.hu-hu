---
title: Azure import/export-exportálási feladatok javítása – v1 | Microsoft Docs
description: Ismerje meg, hogy miként javítható az Azure import/export szolgáltatás használatával létrehozott és futtatott exportálási feladatok.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d84f26b2764a103a9b504c1480e88b58fed3c201
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98706467"
---
# <a name="repairing-an-export-job"></a>Exportálási feladat javítása
Az exportálási feladatok befejezése után a helyszíni Microsoft Azure Import/Export eszközt a következő helyre futtathatja:  
  
1.  Töltsön le minden olyan fájlt, amelyet az Azure import/export szolgáltatás nem tudott exportálni.  
  
2.  Ellenőrizze, hogy a meghajtón lévő fájlok exportálása megfelelő volt-e.  
  
A funkció használatához kapcsolódnia kell az Azure Storage-hoz.  
  
Az importálási feladatok javítására szolgáló parancs **RepairExport**.

## <a name="repairexport-parameters"></a>RepairExport paraméterek

A következő paraméterek megadhatók a **RepairExport**:  
  
|Paraméter|Leírás|  
|---------------|-----------------|  
|**/r: <RepairFile\>**|Kötelező. A javítási fájl elérési útja, amely nyomon követi a javítás állapotát, és lehetővé teszi egy megszakított javítás folytatását. Minden meghajtónak rendelkeznie kell egy javítási fájllal. Amikor elindít egy javítást egy adott meghajtón, egy olyan javítási fájl elérési útját adja át, amely még nem létezik. Egy megszakított javítás folytatásához át kell adni egy meglévő javítási fájl nevét. Mindig a célként megadott meghajtóhoz tartozó javítási fájlt határozza meg.|  
|**/logdir: <LogDirectory\>**|Választható. A naplózási könyvtár. A részletes naplófájlok erre a könyvtárba lesznek írva. Ha nincs megadva naplózási könyvtár, a rendszer az aktuális könyvtárat fogja használni a napló könyvtáraként.|  
|**/d: <TargetDirectory\>**|Kötelező. Az érvényesíteni és kijavítani kívánt könyvtár. Ez a könyvtár általában az exportálási meghajtó gyökérkönyvtára, de az exportált fájlok másolatát tartalmazó hálózati fájlmegosztás is lehet.|  
|**/BK: <BitLockerKey\>**|Választható. Ha azt szeretné, hogy az eszköz feloldja az exportált fájlok tárolására szolgáló titkosított fájlokat, akkor a BitLocker-kulcsot is meg kell adni.|  
|**/SN: <StorageAccountName\>**|Kötelező. Az exportálási feladatokhoz tartozó Storage-fiók neve.|  
|**/SK: <StorageAccountKey\>**|**Kötelező** , ha és csak akkor, ha nincs megadva tároló sas. Az exportálási feladatokhoz tartozó Storage-fiókhoz tartozó fiók kulcsa.|  
|**/csas: <ContainerSas\>**|**Kötelező** , ha nincs megadva a Storage-fiók kulcsa. Az exportálási feladatokhoz társított Blobok elérésére szolgáló tároló SAS.|  
|**/CopyLogFile: <DriveCopyLogFile\>**|Kötelező. A meghajtó másolási naplófájljának elérési útja. A fájlt a Windows Azure import/export szolgáltatás hozza létre, és a feladatokhoz társított blob Storage-ból tölthető le. A másolási naplófájl a kijavítani kívánt Blobokkal vagy fájlokkal kapcsolatos információkat tartalmaz.|  
|**/ManifestFile: <DriveManifestFile\>**|Választható. Az exportálási meghajtó jegyzékfájljának elérési útja. Ezt a fájlt a Windows Azure import/export szolgáltatás hozza létre, és az exportálási meghajtón tárolja. Opcionálisan a feladatokhoz társított Storage-fiókban található blobban is.<br /><br /> Az exportálási meghajtón lévő fájlok tartalma a fájlban található MD5-kivonatokkal lesz ellenőrizve. A rendszer letölti és átírja a sérült fájlokat a célhelyek könyvtáraiba.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>A RepairExport mód használata a sikertelen exportálások kijavítására  
Az Azure import/export eszköz használatával letöltheti azokat a fájlokat, amelyeket nem sikerült exportálni. A másolási naplófájl tartalmazza azon fájlok listáját, amelyeket nem sikerült exportálni.  
  
Az exportálási hibák okai a következők lehetnek:  
  
-   Sérült meghajtók  
  
-   A Storage-fiók kulcsa módosult az átvitel során  
  
Az eszköz **RepairExport** módban való futtatásához először össze kell kapcsolni az exportált fájlokat tartalmazó meghajtót a számítógépre. Ezután futtassa az Azure import/export eszközt, és adja meg a meghajtó elérési útját a `/d` paraméterrel. Meg kell adnia a letöltött meghajtó másolási naplófájljának elérési útját is. Az alábbi, a következő parancssori példa futtatja az eszközt a nem exportált fájlok kijavításához:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
A következő példa egy másolási naplófájl, amely azt mutatja, hogy a blob egyik blokkjának exportálása nem sikerült:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
A másolási naplófájl azt jelzi, hogy hiba történt, miközben a Windows Azure import/export szolgáltatás a blob blokkjának egyikét letölti a fájlra az exportálási meghajtón. A fájl többi összetevője sikeresen le lett töltve, és a fájl hossza helyesen van beállítva. Ebben az esetben az eszköz megnyitja a fájlt a meghajtón, letölti a blokkot a Storage-fiókból, és a 65536-es eltolású, 65536-es hosszúságú eltolással írja a fájl tartományba.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>A RepairExport használata a meghajtó tartalmának ellenőrzéséhez  
Az Azure import/export szolgáltatást a **RepairExport** beállítással is használhatja a meghajtó tartalmának ellenőrzéséhez. Az egyes exportálási meghajtókon található jegyzékfájl a meghajtó MD5s tartalmazza.  
  
Az Azure import/export szolgáltatás az exportálási folyamat során is mentheti a jegyzékfájlokat egy Storage-fiókba. A jegyzékfájlok helye a feladatok [lekérése](/rest/api/storageimportexport/jobs) művelettel érhető el a feladatok befejeződése után. A meghajtó-jegyzékfájl formátumával kapcsolatos további információkért lásd: [importálási/exportálási szolgáltatás jegyzékfájljának formátuma](/previous-versions/azure/storage/common/storage-import-export-file-format-metadata-and-properties).  
  
Az alábbi példa bemutatja, hogyan futtathatja az Azure import/export eszközt a **/ManifestFile** és a **/CopyLogFile** paraméterekkel:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
A következő példa egy jegyzékfájlt mutat be:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
A javítási folyamat befejezése után az eszköz beolvassa a jegyzékfájlban hivatkozott összes fájlt, és ellenőrzi a fájl integritását az MD5-kivonatokkal. A fenti jegyzékfájlhoz a következő összetevőket kell átesnie.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Az eszköz letölti az ellenőrzést nem teljesítő összes összetevőt, és a meghajtón ugyanarra a fájlra írja át a rendszer.  
  
## <a name="next-steps"></a>Következő lépések
 
* [Az Azure import/export eszköz beállítása](storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
* [Feladatok állapotának áttekintése a másolási naplófájlokkal](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Importálási feladat javítása](storage-import-export-tool-repairing-an-import-job-v1.md)