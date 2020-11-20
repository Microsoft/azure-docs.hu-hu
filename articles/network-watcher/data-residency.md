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
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: 2e6a92a4d08f1603f480a990ad437a90302a8189
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966088"
---
# <a name="data-residency-for-azure-network-watcher"></a>Adattárolás az Azure Network Watcher
A Csatlakozáskezelő (előzetes verzió) szolgáltatás kivételével az Azure Network Watcher nem tárolja az ügyféladatokat.


## <a name="connection-monitor-preview-data-residency"></a>A Csatlakozáskezelő (előzetes verzió) adattárolási szolgáltatás
A Csatlakozáskezelő (előzetes verzió) szolgáltatás az ügyféladatokat tárolja. Ezeket az adatfájlokat a Network Watcher automatikusan tárolja egyetlen régióban. Így a kapcsolat figyelője (előzetes verzió) automatikusan megfelel a régión belüli adattárolási követelményeknek, beleértve a [megbízhatósági központban](https://azuredatacentermap.azurewebsites.net/)megadott követelményeket is.

## <a name="data-residency"></a>Adattárolási hely
Az Azure-ban az ügyféladatok egyetlen régióban való tárolását lehetővé tevő funkció jelenleg csak az Ázsia és a Csendes-óceáni térség geo és Dél-Brazília (Sao Paulo állam) régiójában, a brazíliai geo régióban érhető el. Az összes többi régió esetében az ügyféladatokat a Geo tárolja. További információ: [Adatvédelmi központ](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Következő lépések

* Olvassa el a [Network Watcher](./network-watcher-monitoring-overview.md)áttekintése című témakört.