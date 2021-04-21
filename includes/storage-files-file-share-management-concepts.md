---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c54bdc2738f9455926882c439ff8942c1c0a5a6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759763"
---
Az Azure-fájlmegosztások tárfiókban *vannak üzembe* osztva. Ezek olyan legfelső szintű objektumok, amelyek egy megosztott tárolókészletet képviselnek. Ez a tárolókészlet több fájlmegosztás, valamint egyéb tárolási erőforrások, például blobtárolók, üzenetsorok vagy táblák üzembe helyezésére használható. A tárfiókban üzembe helyezett összes tárolási erőforrás osztozik az adott tárfiókra vonatkozó korlátokon. A tárfiókok aktuális korlátaival kapcsolatos további Azure Files [és teljesítménycélokat.](../articles/storage/files/storage-files-scale-targets.md)

Két fő tárfióktípust fog használni a Azure Files telepítéséhez: 
- **Általános célú, 2. verziójú (GPv2)** tárfiókok: A GPv2-tárfiókok lehetővé teszik az Azure-fájlmegosztások standard/merevlemez-alapú (HDD-alapú) hardveren való üzembe helyezését. Az Azure-fájlmegosztások tárolása mellett a GPv2-tárfiókok más tárolási erőforrásokat, például blobtárolókat, üzenetsorokat vagy táblákat is tárolnak. 
- **FileStorage** tárfiókok: A FileStorage tárfiókok lehetővé teszik azure-fájlmegosztások prémium/SSD-alapú (SSD-alapú) hardveren való üzembe helyezését. A FileStorage-fiókok csak Azure-fájlmegosztások tárolására használhatók; A FileStorage-fiókban nem helyezhetők üzembe más tárolási erőforrások (blobtárolók, üzenetsorok, táblák stb.). SMB- és NFS-fájlmegosztásokat csak a FileStorage-fiókok helyezhetnek üzembe.

A tárfiókok számos más típussal is előfordulhatnak a Azure Portal, a PowerShellben vagy a parancssori felületben. Két tárfióktípus , a BlockBlobStorage és a BlobStorage tárfiók nem tartalmazhat Azure-fájlmegosztásokat. A másik két tárfióktípus az általános célú 1-es verzió (GPv1) és a klasszikus tárfiókok, amelyek egyaránt tartalmazhatnak Azure-fájlmegosztásokat. Bár a GPv1- és a klasszikus tárfiókok tartalmazhatnak Azure-fájlmegosztásokat, a Azure Files legtöbb új szolgáltatása csak GPv2- és FileStorage-tárfiókokban érhető el. Ezért azt javasoljuk, hogy csak GPv2- és FileStorage-tárfiókokat használjon az új üzemelő példányok esetében, és frissítse a GPv1- és a klasszikus tárfiókokat, ha azok már léteznek a környezetben.  