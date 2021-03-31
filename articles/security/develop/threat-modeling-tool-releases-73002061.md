---
title: Microsoft Threat Modeling Tool kiadás 02/11/2020 – Azure
description: A veszélyforrások modellezése eszköz kiadási 7.3.00206.1 tartozó kibocsátási megjegyzések dokumentálása.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 9f9b162460cd2e7a624c1ad3f992011487d1b795
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94516934"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Threat Modeling Tool Update kiadás 7.3.00206.1-02/11/2020

A Microsoft Threat Modeling Tool verziójának 7.3.00206.1 (TMT) február 11 2020-én adták ki, és a következő módosításokat tartalmazza:

- Hibajavítások

## <a name="notable-bug-fixes"></a>Jelentős hibajavítások

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>A várt tartományokon kívüli prioritási értékekkel kapcsolatos hibák

Egyes ügyfelek a következő hibaüzenetet kaptak a "Threat Modeling Tool 2016" vagy egyéni sablonokban létrehozott fájlok megnyitásakor:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Ez a probléma ebben a kiadásban lett feloldva.

## <a name="system-requirements"></a>System requirements (Rendszerkövetelmények)

- Támogatott operációs rendszerek
  - [Microsoft Windows 10 évfordulós frissítés](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) vagy újabb
- .NET-verzió szükséges
  - [.Net-4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) vagy újabb
- További követelmények
  - Az eszköz és a sablonok frissítéseinek fogadásához internetkapcsolat szükséges.

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A Threat Modeling Tool dokumentációja a [docs.microsoft.com](./threat-modeling-tool.md)található, és [az eszköz használatával](./threat-modeling-tool-getting-started.md)kapcsolatos információkat tartalmaz.

## <a name="next-steps"></a>Következő lépések

Töltse le a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)legújabb verzióját.