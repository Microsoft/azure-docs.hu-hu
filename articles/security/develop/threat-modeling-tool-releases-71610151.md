---
title: Microsoft Threat Modeling Tool kiadás 10/16/2019 – Azure
description: A veszélyforrások modellezése eszköz kiadási 7.1.61015.1 tartozó kibocsátási megjegyzések dokumentálása.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 761ad45496e799a555b60480ff575b9d8f30e984
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516985"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>A Threat Modeling Tool frissített kiadása: 7.1.61015.1 – 2019. 10. 16.

A Microsoft Threat Modeling Tool (TMT) verziójának 7.1.61015.1 október 16 2019-én adták ki, és a következő módosításokat tartalmazza:

- Javított kisegítő lehetőségek
- Hibajavítások
- Új rajzsablonok a Azure Logic Apps és az Azure Adatkezelő

## <a name="notable-bug-fixes"></a>Jelentős hibajavítások

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Továbbfejlesztett visszamenőleges kompatibilitás a "Threat Modeling Tool 2016"-ben létrehozott fájlokkal

A "Threat Modeling Tool 2016" fájlban létrehozott veszélyforrási modell fájljainak megnyitásával vagy megjelenítésével kapcsolatos hibák kijavítva.

## <a name="feature-enhancements"></a>Funkciók továbbfejlesztése

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Új rajzsablonok a Azure Logic Apps és az Azure Adatkezelő

A Azure Logic Apps és az Azure Adatkezelő új rajzsablonja hozzá lett adva az Azure-rajzsablonhoz, valamint a hozzájuk kapcsolódó fenyegetésekhez és enyhítésekhez.

![Azure Logic Apps és az Azure Adatkezelő-rajzsablonok](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Ismert problémák

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>A várt tartományokon kívüli prioritási értékekkel kapcsolatos hibák

Egyes ügyfelek a következő hibaüzenetet kaptak a "Threat Modeling Tool 2016" vagy egyéni sablonokban létrehozott fájlok megnyitásakor:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Ez a probléma a vizsgálat alatt áll

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