---
title: Adattárolás az Azure Network Watcherhoz | Microsoft Docs
description: Ez a cikk segítséget nyújt az Azure Network Watcher szolgáltatás adattárolásának megismeréséhez.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: b5aa8167031c3b871c6a6a4d84159c3c284bf241
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018428"
---
# <a name="data-residency-for-azure-network-watcher"></a>Adattárolás az Azure Network Watcher
A Csatlakozáskezelő (előzetes verzió) szolgáltatás kivételével az Azure Network Watcher nem tárolja az ügyféladatokat.


## <a name="connection-monitor-preview-data-residency"></a>A Csatlakozáskezelő (előzetes verzió) adattárolási szolgáltatás
A Csatlakozáskezelő (előzetes verzió) szolgáltatás az ügyféladatokat tárolja. Ezeket az adatfájlokat a Network Watcher automatikusan tárolja egyetlen régióban. Így a kapcsolat figyelője (előzetes verzió) automatikusan megfelel a régión belüli adattárolási követelményeknek, beleértve a [megbízhatósági központban](https://azuredatacentermap.azurewebsites.net/)megadott követelményeket is.

## <a name="data-residency"></a>Adattárolási hely
Az Azure-ban az ügyféladatok egyetlen régióban való tárolását lehetővé tevő funkció jelenleg csak az Ázsia és a Csendes-óceáni térség geo és Dél-Brazília (Sao Paulo állam) régiójában, a brazíliai geo régióban érhető el. Az összes többi régió esetében az ügyféladatokat a Geo tárolja. További információ: [Adatvédelmi központ](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Következő lépések

* Olvassa el a [Network Watcher](./network-watcher-monitoring-overview.md)áttekintése című témakört.