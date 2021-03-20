---
title: Microsoft Threat Modeling Tool kiadás 07/29/2020 – Azure
description: A veszélyforrások modellezése eszköz kiadási 7.3.00729.1 tartozó kibocsátási megjegyzések dokumentálása.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 3e6fcd52ad9cb6c127c14bac2f33223fb921519e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516373"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Threat Modeling Tool Update kiadás 7.3.00729.1-07/29/2020

A Microsoft Threat Modeling Tool (TMT) 7.3.00729.1 verziójának kiadása 29 2020 júliusában történt, és a következő módosításokat tartalmazza:

- Hibajavítások
 
## <a name="known-issues"></a>Ismert problémák

### <a name="errors-related-to-tmt7application-file-deserialization"></a>TMT7 kapcsolatos hibák. alkalmazásfájl deszerializálása

#### <a name="issue"></a>Probléma

Egyes ügyfelek a következő hibaüzenetet kaptak a Threat Modeling Tool letöltésekor:

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

Ez a hiba azért fordul elő, mert egyes böngészők nem támogatják natív módon a ClickOnce telepítését. Ezekben az esetekben a rendszer letölti a ClickOnce alkalmazásfájl a felhasználó merevlemezére.

#### <a name="workaround"></a>Áthidaló megoldás

Ez a hiba továbbra is megjelenik, ha a Threat Modeling Tool elindul, ha duplán kattint a TMT7. Application fájlra. A hiba megkerülése után azonban az eszköz rendesen fog működni. A Threat Modeling Tool elindítása helyett a TMT7. Application fájlra duplán kattintva a felhasználóknak a telepítés során a Windows menüjében létrehozott parancsikonokat kell használniuk a Threat Modeling Tool elindításához.

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