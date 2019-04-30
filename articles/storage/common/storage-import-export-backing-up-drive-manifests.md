---
title: Az Azure Import/Export meghajtójegyzékek biztonsági mentése |} A Microsoft Docs
description: Megtudhatja, hogyan szeretné, hogy a Microsoft Azure Import/Export szolgáltatás automatikusan biztonsági másolat a meghajtójegyzékek.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ea223ea3ccd113014ceabff34cc4d0174abb1ddf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483128"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Azure Import/Export-feladatok meghajtójegyzékek biztonsági mentése a meghajtó

Meghajtójegyzékek automatikusan biztonsági másolat készíthető a blobokhoz beállításával a `BackupDriveManifest` tulajdonságot `true` a a [Put feladat](/rest/api/storageimportexport/jobs) vagy [feladat tulajdonságainak](/rest/api/storageimportexport/jobs) REST API-műveleteket. Alapértelmezés szerint a meghajtójegyzékek nem készül biztonsági másolat. A meghajtó jegyzékfájl biztonsági másolatokat egy tárolóban, a feladathoz hozzárendelt a tárfiókban található blokkblobok formájában tárolja. Alapértelmezés szerint a tároló neve a `waimportexport`, de megadhat egy másik nevet a a `DiagnosticsPath` tulajdonság hívásakor a `Put Job` vagy `Update Job Properties` műveleteket. A biztonsági mentési jegyzékfájl blob a következő formátumban lesznek elnevezve: `waies/jobname_driveid_timestamp_manifest.xml`.

 URI-ját egy feladat a biztonsági mentési meghajtójegyzékek meghívásával lehet lekérdezni a [Get Job](/rest/api/storageimportexport/jobs) műveletet. A blob URI-t adja vissza a `ManifestUri` minden meghajtó tulajdonság.

## <a name="next-steps"></a>További lépések

* [Az Import/Export szolgáltatás REST API használatával](storage-import-export-using-the-rest-api.md)
