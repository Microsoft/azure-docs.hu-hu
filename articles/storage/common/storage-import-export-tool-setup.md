---
title: Az Azure import/export eszköz beállítása | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be az Azure import/export szolgáltatáshoz készült meghajtó-előkészítési és-javítási eszközt.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: c7d2cac9402477090ae412dcf21265bec4609278
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85514184"
---
# <a name="setting-up-the-azure-importexport-tool"></a>Az Azure import/export eszköz beállítása

A Microsoft Azure Import/Export eszköz a Microsoft Azure Import/Export szolgáltatással használható meghajtó-előkészítési és-javítási eszköz. A következő függvényekhez használhatja az eszközt:

* Importálási feladatok létrehozása előtt az eszköz használatával másolhat egy Azure-adatközpontba szállítandó merevlemezekre az adatokból.
* Az importálási feladatok befejezését követően az eszköz használatával kijavíthatja az összes sérült, hiányzó vagy más Blobokkal ütköző blobot.
* Miután megkapta a meghajtókat egy befejezett exportálási feladatokból, ezzel az eszközzel kijavíthatja a meghajtókon sérült vagy hiányzó fájlokat.

## <a name="prerequisites"></a>Előfeltételek

Ha egy importálási feladatokhoz **készít meghajtókat** , a következő előfeltételeknek kell teljesülniük:

* Aktív Azure-előfizetéssel kell rendelkeznie.
* Az előfizetésnek tartalmaznia kell egy elegendő szabad hellyel rendelkező Storage-fiókot az importálni kívánt fájlok tárolásához.
* A Storage-fiók hozzáférési kulcsainak legalább egyike szükséges.
* Szüksége van egy számítógépre (a "számítógép másolása") a Windows 7, a Windows Server 2008 R2 vagy egy újabb Windows operációs rendszerre.
* A .NET-keretrendszer 4-es verzióját a másolási gépre kell telepíteni.
* A BitLockert engedélyezni kell a másolási gépen.
* Egy vagy több üres 2,5 hüvelykes vagy 3,5 hüvelykes SATAII vagy III vagy SSD merevlemezzel kell rendelkeznie a másolási géphez.
* Az importálni kívánt fájloknak elérhetőnek kell lenniük a másolási gépről, függetlenül attól, hogy hálózati megosztáson vagy helyi merevlemezen találhatók-e.

Ha egy részlegesen sikertelen **importálást próbál kijavítani** , a következőkre lesz szüksége:

* A naplófájlok másolása
* A Storage-fiók kulcsa

Ha részlegesen sikertelen **exportálást próbál meg kijavítani** , a következőkre lesz szüksége:

* A naplófájlok másolása
* Manifest-fájlok (nem kötelező)
* A Storage-fiók kulcsa

## <a name="installing-the-azure-importexport-tool"></a>Az Azure import/export eszköz telepítése

Először [töltse le az Azure import/export eszközt](https://www.microsoft.com/download/details.aspx?id=55280) , és csomagolja ki a számítógép egyik könyvtárába, például: `c:\WAImportExport` .

Az Azure import/export eszköz az alábbi fájlokat tartalmazza:

* dataset.csv
* driveset.csv
* hddid.dll
* Microsoft.Data.Services.Client.dll
* Microsoft.WindowsAzure.Storage.dll
* Microsoft. WindowsAzure. Storage. pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportExport.exe
* WAImportExport.exe.config
* WAImportExport. pdb
* WAImportExportCore.dll
* WAImportExportCore. pdb
* WAImportExportRepair.dll
* WAImportExportRepair. pdb

Ezután nyisson meg egy parancssori ablakot **rendszergazdai módban**, és váltson át a kibontott fájlokat tartalmazó könyvtárba.

A parancs súgójának kimenetéhez futtassa az eszközt ( `WAImportExport.exe` ) paraméterek nélkül:

```
WAImportExport, a client tool for Windows Azure Import/Export Service. Microsoft (c) 2013


Copy directories and/or files with a new copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>]
        [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]
        DataSet:<dataset.csv>

Add more drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>

Abort an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession

Resume an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession

List drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListDrives

List copy sessions:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListCopySessions

Repair a Drive:
    WAImportExport.exe RepairImport | RepairExport
        /r:<RepairFile> [/logdir:<LogDirectory>]
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]
        [/PathMapFile:<DrivePathMapFile>]

Preview an Export Job:
    WAImportExport.exe PreviewExport
        [/logdir:<LogDirectory>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>

Parameters:

    /j:<JournalFile>
        - Required. Path to the journal file. A journal file tracks a set of drives and
          records the progress in preparing these drives. The journal file must always
          be specified.
    /logdir:<LogDirectory>
        - Optional. The log directory. Verbose log files as well as some temporary
          files will be written to this directory. If not specified, current directory
          will be used as the log directory. The log directory can be specified only
          once for the same journal file.
    /id:<SessionId>
        - Optional. The session Id is used to identify a copy session. It is used to
          ensure accurate recovery of an interrupted copy session.
    /ResumeSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to resume the session.
    /AbortSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to abort the session.
    /sn:<StorageAccountName>
        - Required. Only applicable for RepairImport and RepairExport. The name of
          the storage account.
    /sk:<StorageAccountKey>
        - Required. The key of the storage account.
    /InitialDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of drives to prepare.
    /AdditionalDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of additional drives to be added.
    /r:<RepairFile>
        - Required. Only applicable for RepairImport and RepairExport.
          Path to the file for tracking repair progress. Each drive must have one
          and only one repair file.
    /d:<TargetDirectories>
        - Required. Only applicable for RepairImport and RepairExport.
          For RepairImport, one or more semicolon-separated directories to repair;
          For RepairExport, one directory to repair, e.g. root directory of the drive.
    /CopyLogFile:<DriveCopyLogFile>
        - Required. Only applicable for RepairImport and RepairExport. Path to the
          drive copy log file (verbose or error).
    /ManifestFile:<DriveManifestFile>
        - Required. Only applicable for RepairExport. Path to the drive manifest file.
    /PathMapFile:<DrivePathMapFile>
        - Optional. Only applicable for RepairImport. Path to the file containing
          mappings of file paths relative to the drive root to locations of actual files
          (tab-delimited). When first specified, it will be populated with file paths
          with empty targets, which means either they are not found in TargetDirectories,
          access denied, with invalid name, or they exist in multiple directories. The
          path map file can be manually edited to include the correct target paths and
          specified again for the tool to resolve the file paths correctly.
    /ExportBlobListFile:<ExportBlobListFile>
        - Required. Path to the XML file containing list of blob paths or blob path
          prefixes for the blobs to be exported. The file format is the same as the
          blob list blob format in the Put Job operation of the Import/Export Service
          REST API.
    /DriveSize:<DriveSize>
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.
          Note: 1 GB = 1,000,000,000 bytes
                1 TB = 1,000,000,000,000 bytes
    /DataSet:<dataset.csv>
        - Required. A .csv file that contains a list of directories and/or a list files
          to be copied to target drives.

    /silentmode
        - Optional. If not specified, it will remind you the requirement of drives and
          need your confirmation to continue.

Examples:

    Copy a data set to a drive:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /InitialDriveSet:driveset1.csv
        /DataSet:data.csv

    Copy another dataset to the same drive following the above command:
    WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#2 /DataSet:dataset2.csv

    Preview how many 1.5 TB drives are needed for an export job:
    WAImportExport.exe PreviewExport
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml
        /DriveSize:1.5TB

    Repair an finished import job:
    WAImportExport.exe RepairImport
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log
```

## <a name="next-steps"></a>Következő lépések

* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Meghajtóhasználat előnézete exportálási feladatokhoz](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [Feladatok állapotának áttekintése a másolási naplófájlokkal](../storage-import-export-tool-reviewing-job-status-v1.md)
* [Importálási feladat javítása](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
