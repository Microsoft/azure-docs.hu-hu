---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 5658b68081fae8dab528030f7fc1cd6fe4935730
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102178359"
---
- Az igény szerinti bursás nem engedélyezhető olyan prémium SSD-meghajtón, amely kevesebb, mint 512 GiB. Az 512 GiB-nál kisebb prémium SSD-k minden esetben kredit-alapú kitörést fognak használni.
- Az igény szerinti bursás csak prémium SSD-k esetén támogatott. Ha az igény szerinti kikapcsolást engedélyező prémium SSD-lemez átvált egy másik lemezre, akkor a lemez kitörése le van tiltva.
- Az igény szerinti bursás nem tiltja le automatikusan magát a teljesítményszint megváltozásakor. Ha módosítani szeretné a teljesítményt, de nem kívánja tovább használni a lemezeket, le kell tiltania azt.
- Igény szerinti bursás csak akkor engedélyezhető, ha a lemez le van választva egy virtuális gépről, vagy ha a virtuális gép le van állítva. Az igény szerinti bursás az engedélyezése után 12 órával is letiltható.