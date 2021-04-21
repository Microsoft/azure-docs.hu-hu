---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2f50a1d046b7b7d3fd7507ef87f8f29096a4faa4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774563"
---
Nem: a kiszolgálóvégpont eltávolítása nem olyan, mint egy kiszolgáló újraindítása! A kiszolgálóvégpont eltávolítása és újralétrehozása szinte soha nem megfelelő megoldás a szinkronizálással, a felhőbeli rétegezéssel vagy a kiszolgáló egyéb aspektusainak Azure File Sync. A kiszolgálóvégpont eltávolítása kipusztító művelet. Adatvesztést okozhat abban az esetben, ha rétegzett fájlok léteznek a kiszolgálóvégpont névterében kívül. További információ: Miért léteznek rétegzett fájlok a [kiszolgálóvégpont-névtéren](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) kívül. Az is előfordulhat, hogy a kiszolgálóvégpont névterében található rétegzett fájlokhoz nem érhetőek el fájlok. Ezek a problémák nem oldják meg a kiszolgálóvégpont újbóli létrehozása esetén. A rétegzett fájlok akkor is létezhetnek a kiszolgálóvégpont névterében, ha még nem volt engedélyezve a felhőbeli rétegezés. Ezért javasoljuk, hogy csak akkor távolítsa el a kiszolgálóvégpontot, ha abba szeretné hagyni az Azure File Sync használatát ezzel a mappával, vagy ha egy Microsoft-mérnök kifejezetten erre utasítja. További információ a kiszolgálóvégpont eltávolításáról: [Kiszolgálóvégpont eltávolítása.](../articles/storage/file-sync/file-sync-server-endpoint.md#remove-a-server-endpoint)    
