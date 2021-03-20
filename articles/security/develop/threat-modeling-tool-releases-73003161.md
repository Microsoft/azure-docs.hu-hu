---
title: Microsoft Threat Modeling Tool kiadás 03/22/2020 – Azure
description: A veszélyforrások modellezése eszköz kiadási 7.3.00316.1 tartozó kibocsátási megjegyzések dokumentálása.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: e182d40d20529b5743fa9105c68108a8ae55d943
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516900"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool Update kiadás 7.3.00316.1-03/22/2020

A Microsoft Threat Modeling Tool (TMT) 7.3.00316.1 verziója március 22 2020-én megjelent, és a következő módosításokat tartalmazza:

- Javított kisegítő lehetőségek
- Hibajavítások
- Új DiagramReader funkció

## <a name="notable-bug-fixes"></a>Jelentős hibajavítások

### <a name="exporting-the-threat-list-to-csv"></a>A fenyegetések listájának exportálása CSV-re

Az Exportálás CSV-fájlba funkció inkonzisztensen ki lett választva, hogy mely mezők legyenek exportálva a veszélyforrások listájáról. Mostantól a fenyegetések listájáról származó összes mező exportálva lesz a CSV-fájlba. 

### <a name="ux-bugs"></a>UX-hibák

- Az elsődleges munkafolyamat Súgó menüjében (létrehozás/Megnyitás/elemzés) és a sablon szerkesztői felülete mostantól konzisztens menüpontokkal rendelkezik.
- A rajzsablonok ablaktáblán a keresősáv mostantól szabványos kurzorral rendelkezik, és a megfelelő címkéket adta hozzá.

## <a name="new-features"></a>Új funkciók

### <a name="diagramreader-feature-has-been-added"></a>A DiagramReader funkció hozzá lett adva

Új DiagramReader funkció lett hozzáadva a főmenüben, miközben a modell meg van nyitva. Ez a funkció átalakítja a modell grafikus ábrázolását egy szöveges elbeszélésbe. 

## <a name="system-requirements"></a>System requirements (Rendszerkövetelmények)

- Támogatott operációs rendszerek:
  - [Microsoft Windows 10 évfordulós frissítés](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) vagy újabb
- .NET-verzió szükséges:
  - [.Net-4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) vagy újabb
- További követelmények:
  - Internetkapcsolat az eszköz és a sablonok frissítéseinek fogadásához

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A Threat Modeling Tool dokumentációja a [docs.microsoft.com](./threat-modeling-tool.md)található, és [az eszköz használatával](./threat-modeling-tool-getting-started.md)kapcsolatos információkat tartalmaz.

## <a name="next-steps"></a>Következő lépések

Töltse le a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)legújabb verzióját.