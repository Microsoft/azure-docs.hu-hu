---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7151c110fd50f7485aa0b130832aace4f3143ad9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750340"
---
- Ez a funkció jelenleg csak prémium SSD-k esetén támogatott.
- A lemez szintjeinek megváltoztatásához le kell szabadítania a virtuális gépet, vagy le kell választania a lemezt egy futó virtuális gépről.
- A P60, a P70 és a P80 teljesítményszint csak a 4 096 GiB-nál nagyobb lemezek esetében használható.
- A lemez teljesítményi szintje csak 12 óránként lehet visszaminősíthető.

## <a name="change-performance-tier-without-downtime-preview"></a>Teljesítményszint módosítása állásidő nélkül (előzetes verzió)

Általában le kell szabadítania a virtuális gépet, vagy le kell választania a lemezt a teljesítményi szintje módosításához. Ha azonban engedélyezi ezt az előzetes verziójú szolgáltatást, nem kell felszabadítani a virtuális gépet, vagy leválasztani a lemezt a rétegek módosításához. Az előzetes verzióra [itt](https://aka.ms/liveperftiersignup)regisztrálhat.

Az előnézet a következő korlátozásokkal rendelkezik:
- Csak a EastUS2EUAP régióban érhető el.
- Jelenleg nem érhető el megosztott lemezekhez
- Az API-val Azure Resource Manager sablonokat kell használnia az `2020-12-01` állásidő nélküli teljesítményszint módosításához.