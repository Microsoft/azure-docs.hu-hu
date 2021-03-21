---
title: Azure import/export importálási feladatok javítása – v1 | Microsoft Docs
description: Ismerje meg, hogy miként javítható az Azure import/export szolgáltatás használatával létrehozott és futtatott importálási feladatok.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: af6234a81e8f79424cc3b1ec4178a175d9ff0146
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98706458"
---
# <a name="repairing-an-import-job"></a>Importálási feladat javítása
Előfordulhat, hogy a Microsoft Azure Import/Export szolgáltatás néhány fájlt vagy egy fájl részeit nem tudja átmásolni a Windows Azure Blob szolgáltatásba. A hibák néhány lehetséges okát jelenthetik például:  
  
-   Sérült fájlok  
  
-   Sérült meghajtók  
  
-   A Storage-fiók kulcsa módosult a fájl átvitele közben.  
  
A Microsoft Azure Import/Export eszközt futtathatja az importálási feladatok másolási naplófájljaival. Az eszköz feltölti a hiányzó fájlokat vagy egy fájl részeit a Windows Azure Storage-fiókjába az importálási feladatok elvégzéséhez.  
  
## <a name="repairimport-parameters"></a>RepairImport paraméterek

A következő paraméterek megadhatók a **RepairImport**: 
  
|||  
|-|-|  
|**/r:**<RepairFile\>|**Szükséges.** A javítási fájl elérési útja, amely nyomon követi a javítás állapotát, és lehetővé teszi egy megszakított javítás folytatását. Minden meghajtónak rendelkeznie kell egy javítási fájllal. Amikor elindít egy javítást egy adott meghajtón, a helyreállítási fájl elérési útját adja meg, amely még nem létezik. Egy megszakított javítás folytatásához át kell adni egy meglévő javítási fájl nevét. Mindig a célként megadott meghajtóhoz tartozó javítási fájlt határozza meg.|  
|**/logdir:**<LogDirectory\>|**Választható.** A naplózási könyvtár. A részletes naplófájlokat a rendszer erre a könyvtárba írja. Ha nincs megadva naplózási könyvtár, a rendszer az aktuális könyvtárat fogja használni a napló könyvtáraként.|  
|**/d:**<TargetDirectories\>|**Szükséges.** Egy vagy több pontosvesszővel tagolt könyvtár, amely tartalmazza az importált eredeti fájlokat. Az importálási meghajtó is felhasználható, de nem kötelező, ha az eredeti fájlok alternatív helyei elérhetők.|  
|**/BK:**<BitLockerKey\>|**Választható.** Ha azt szeretné, hogy az eszköz feloldja a titkosított meghajtót, ahol az eredeti fájlok elérhetők, akkor a BitLocker-kulcsot is meg kell adni.|  
|**/SN:**<StorageAccountName\>|**Szükséges.** Az importálási feladatokhoz tartozó Storage-fiók neve.|  
|**/SK:**<StorageAccountKey\>|**Kötelező** , ha és csak akkor, ha nincs megadva tároló sas. Az importálási feladatokhoz tartozó Storage-fiókhoz tartozó fiók kulcsa.|  
|**/csas:**<ContainerSas\>|**Kötelező** , ha nincs megadva a Storage-fiók kulcsa. Az importálási feladatokhoz társított Blobok elérésére szolgáló tároló SAS.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Szükséges.** A meghajtó másolási naplófájljának elérési útja (vagy a részletes napló vagy a hibanapló). A fájlt a Windows Azure import/export szolgáltatás hozza létre, és a feladatokhoz társított blob Storage-ból tölthető le. A másolási naplófájl a kijavítani kívánt hibás Blobokkal vagy fájlokkal kapcsolatos információkat tartalmaz.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Választható.** A kétértelműség feloldásához használt szövegfájl elérési útja, ha több, azonos nevű fájllal rendelkezik, amely ugyanabban a feladatokban lett importálva. Az eszköz első futtatásakor a fájl az összes kétértelmű névvel feltölthető. Az eszköz későbbi futtatása ezt a fájlt használja a kétértelműség feloldásához.|  
  
## <a name="using-the-repairimport-command"></a>A RepairImport parancs használata  
Ha a hálózaton keresztüli adatátvitel útján szeretné javítani az importálási adatátvitelt, meg kell adnia azokat a címtárakat, amelyek tartalmazzák az importált eredeti fájlokat a `/d` paraméter használatával. Adja meg a Storage-fiókból letöltött másolási naplófájlt is. A részleges hibákkal rendelkező importálási feladatok kijavításához egy tipikus parancssor a következőképpen néz ki:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
A másolási naplófájl következő példájában egy fájl 1 64-K darabja sérült az importálási feladathoz szállított meghajtón. Mivel ez a hiba csak egy jelzett, a feladatokban található többi blob importálása sikeresen megtörtént.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Ha a rendszer átadja ezt a másolási naplót az Azure import/export eszköznek, az eszköz megpróbálja a fájl importálását befejezni a hiányzó tartalomnak a hálózaton keresztül történő másolásával. A fenti példát követve az eszköz megkeresi az eredeti fájlt `\animals\koala.jpg` a két könyvtáron belül `C:\Users\bob\Pictures` `X:\BobBackup\photos` . Ha a fájl `C:\Users\bob\Pictures\animals\koala.jpg` létezik, az Azure import/export eszköz a hiányzó adattartományt másolja a megfelelő blobba `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg` .  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Ütközések feloldása RepairImport használatakor  
Bizonyos helyzetekben előfordulhat, hogy az eszköz nem tudja megkeresni vagy megnyitni a szükséges fájlt a következő okok egyike miatt: a fájl nem található, a fájl nem érhető el, a fájl neve nem egyértelmű, vagy a fájl tartalma már nem megfelelő.  
  
Nem egyértelmű hiba fordulhat elő, ha az eszköz megpróbálja megkeresni a `\animals\koala.jpg` fájlt, és az adott névvel rendelkező fájl is létezik a és a alatt `C:\Users\bob\pictures` `X:\BobBackup\photos` . Ez mind `C:\Users\bob\pictures\animals\koala.jpg` `X:\BobBackup\photos\animals\koala.jpg` az importálási feladatok meghajtóján, mind a rendszeren megtalálható.  
  
`/PathMapFile`Ez a beállítás lehetővé teszi a hibák megoldását. Megadhatja annak a fájlnak a nevét, amely az eszköz által helytelenül azonosított fájlok listáját tartalmazza. A következő parancssori példa feltöltése `9WM35C2V_pathmap.txt` :  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Az eszköz ezután megírja a problémás fájl elérési útját a következőre: `9WM35C2V_pathmap.txt` minden sorban egy. Előfordulhat például, hogy a fájl a következő bejegyzéseket tartalmazza a parancs futtatása után:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 A lista minden fájlján meg kell próbálnia a fájl megkeresését és megnyitását, hogy az elérhető legyen az eszköz számára. Ha azt szeretné, hogy az eszköz explicit módon hol keresse meg a fájlt, módosítsa az elérésiút-leképezési fájlt, és adja hozzá az elérési utat az egyes fájlokhoz ugyanazon a sorban, a TAB karakterrel elválasztva:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Miután elvégezte a szükséges fájlok elérhetővé tételét az eszköz számára, vagy az elérésiút-leképezési fájl frissítését, az importálási folyamat befejezéséhez futtassa újra az eszközt.  
  
## <a name="next-steps"></a>Következő lépések
 
* [Az Azure import/export eszköz beállítása](storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
* [Feladatok állapotának áttekintése a másolási naplófájlokkal](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Exportálási feladat javítása](./storage-import-export-tool-repairing-an-export-job-v1.md)