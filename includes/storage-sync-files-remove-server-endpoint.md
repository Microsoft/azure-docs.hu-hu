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
ms.openlocfilehash: f730b2abad244132b06c0fa21f07a171559d22f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96309948"
---
Nem: a kiszolgálói végpont eltávolítása nem olyan, mint a kiszolgáló újraindítása. A kiszolgáló-végpont eltávolítása és újbóli létrehozása szinte soha nem megfelelő megoldás a szinkronizálási, a felhőalapú rétegek vagy a Azure File Sync egyéb aspektusainak kijavításához. A kiszolgálói végpontok eltávolítása romboló művelet. Adatvesztést okozhat abban az esetben, ha a többplatformos fájlok a kiszolgálói végpont névterén kívül vannak. További információért lásd: [miért léteznek a többplatformos fájlok a kiszolgálói végpont névterén kívül](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) . Vagy az is előfordulhat, hogy a kiszolgálói végponti névtérben található, többplatformos fájlokhoz nem érhető el fájlok. Ezek a problémák nem lesznek feloldva a kiszolgálói végpont újbóli létrehozásakor. A többhelyes fájlok akkor is létezhetnek a kiszolgálói végpont névterén belül, ha még nem volt engedélyezve a Felhőbeli rétegek használata. Ezért javasoljuk, hogy ne távolítsa el a kiszolgálói végpontot, hacsak nem szeretné leállítani az adott mappával való Azure File Sync használatát, vagy kifejezetten erre utasította a Microsoft mérnöke. A kiszolgálói végpontok eltávolításával kapcsolatos további információkért lásd: [kiszolgálói végpont eltávolítása](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
