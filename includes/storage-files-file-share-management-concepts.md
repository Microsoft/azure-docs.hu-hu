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
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597818"
---
Az Azure-fájlmegosztás *tárolási fiókokra*van telepítve, amelyek a tárolók megosztott készletét képviselő legfelső szintű objektumok. Ez a tárterület több fájlmegosztás, valamint más tárolási erőforrások, például blob-tárolók, várólisták vagy táblák üzembe helyezésére is használható. A Storage-fiókba telepített összes tárolási erőforrás megosztja az adott tárolási fiókra érvényes korlátokat. A Storage-fiók jelenlegi korlátainak megtekintéséhez lásd: [Azure Files skálázhatósági és teljesítményi célok](../articles/storage/files/storage-files-scale-targets.md).

A Azure Files központi telepítések esetében két fő típusú tárolási fiókot kell használni: 
- **Általános célú 2-es verziójú (GPv2) Storage-fiókok**: a GPv2 Storage-fiókok lehetővé teszik az Azure-fájlmegosztás szabványos/merevlemez-alapú (HDD-alapú) hardveren való üzembe helyezését. Az Azure-fájlmegosztás tárolásán kívül a GPv2 más tárolási erőforrásokat is tárolhat, például blob-tárolókat, várólistákat vagy táblákat. 
- **FileStorage Storage-fiókok**: a FileStorage Storage-fiókok lehetővé teszik az Azure-fájlmegosztás prémium/SSD-alapú (SSD-alapú) hardveren való üzembe helyezését. FileStorage-fiókokat csak az Azure-fájlmegosztás tárolására lehet használni; egy FileStorage-fiókban nem helyezhetők üzembe más tárolási erőforrások (blob-tárolók, várólisták, táblák stb.).

A Azure Portal, a PowerShell vagy a CLI számos más típusú tárolási fiókot is tartalmazhat. Két Storage-fióktípus, a BlockBlobStorage és a BlobStorage Storage-fiók nem tartalmazhat Azure-fájlmegosztást. A másik két típusú Storage-fióktípus az általános célú 1. verzió (GPv1) és a klasszikus Storage-fiókok, amelyek közül mindkettő tartalmazhatja az Azure-fájlmegosztást. Bár a GPv1 és a klasszikus Storage-fiókok tartalmazhatnak Azure-fájlmegosztást, a Azure Files legtöbb új funkciója csak a GPv2 és a FileStorage Storage-fiókokban érhető el. Ezért javasoljuk, hogy csak a GPv2 és a FileStorage Storage-fiókokat használja az új üzemelő példányokhoz, valamint a GPv1 és a klasszikus Storage-fiókok frissítését, ha azok már léteznek a környezetben.  