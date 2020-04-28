---
title: Microsoft Threat Modeling Tool áttekintése – Azure
description: A Microsoft Threat Modeling Tool áttekintése, amely az eszköz használatának első lépéseivel kapcsolatos információkat tartalmazza, beleértve a veszélyforrások modellezésének folyamatát is.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 02/16/2017
ms.openlocfilehash: 0d800102b6f6ff77944a2b625d3bcecef69c1ee9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75548747"
---
# <a name="microsoft-threat-modeling-tool"></a>Microsoft Threat Modeling Tool

A Threat Modeling Tool a Microsoft biztonsági fejlesztési életciklus (SDL) alapvető eleme. Lehetővé teszi a szoftverfejlesztők számára, hogy korán azonosítsák és csökkentsék a potenciális biztonsági problémákat, amikor viszonylag könnyen és költséghatékonyan oldják meg a megoldást. Ennek eredményeképpen jelentős mértékben csökkenti a teljes fejlesztési költségeket. Azt is megtervezték, hogy az eszközt nem biztonsági szakértők is megalakították, így a fenyegetések modellezése minden fejlesztő számára egyszerűbb, mivel egyértelmű útmutatást nyújt a veszélyforrások létrehozásához és elemzéséhez. 

Az eszköz a következőket teszi lehetővé:

* Kommunikáció a rendszerek biztonsági kialakításával
* A bevált módszertan használatával elemezheti a lehetséges biztonsági problémákra vonatkozó terveket
* A biztonsági problémák elhárításának ajánlása és kezelése

Íme néhány szerszámozási képesség és innováció, csak hogy néhányat említsünk:

* **Automatizálás:** Útmutató és visszajelzés a modell rajzolásához
* **Lépéshossz/elem:** Fenyegetések és enyhítések irányított elemzése
* **Jelentéskészítés:** Biztonsági tevékenységek és tesztelés az ellenőrzési fázisban
* **Egyedi módszertan:** Lehetővé teszi a felhasználók számára a fenyegetések jobb megjelenítését és megértését
* **Fejlesztőknek és szoftvereknek szánt központilag:** számos megközelítés az eszközökön vagy a támadón alapul. A szoftvereket középpontba vesszük. Olyan tevékenységekre építünk, amelyeket a szoftverfejlesztők és az építészek is ismernek – például képek rajzolásához a szoftver architektúrája számára
* **Tervezési elemzésre összpontosít:** A "fenyegetések modellezése" kifejezés a követelményekre vagy egy tervezési elemzési módszerre is utalhat. Időnként a kettő összetett keverékére hivatkozik. A veszélyforrások modellezésének Microsoft SDL megközelítése egy célirányos kialakítású elemzési módszer

## <a name="next-steps"></a>További lépések

Az alábbi táblázat a Threat Modeling Tool első lépéseihez tartalmaz fontos hivatkozásokat:

| Lépés  | Leírás                                                                                   |
| ----- | --------------------------------------------------------------------------------------------- |
| **1** | [A Threat Modeling Tool letöltése](https://aka.ms/threatmodelingtool)                                |
| **2** | [Olvassa el az első lépéseket ismertető útmutatót](threat-modeling-tool-getting-started.md)    |
| **3** | [Ismerkedjen meg a szolgáltatásokkal](threat-modeling-tool-feature-overview.md)   |
| **4** | [A generált veszélyforrások kategóriáinak megismerése](threat-modeling-tool-threats.md)   |
| **5** | [A generált fenyegetések enyhítésének megkeresése](threat-modeling-tool-mitigations.md) |

## <a name="resources"></a>További források

Íme néhány régebbi, a veszélyforrások modellezésével kapcsolatos cikk még ma:

* [Cikk a veszélyforrások modellezésének fontosságáról](https://docs.microsoft.com/archive/msdn-magazine/2009/january/security-briefs-getting-started-with-the-sdl-threat-modeling-tool)
* [Megbízható számítástechnika által közzétett képzés](https://www.microsoft.com/download/details.aspx?id=16420)

Ismerkedjen meg néhány Threat Modeling Tool szakértővel:

* [Fenyegetések kezelője](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Simone Curzi biztonsági blog](https://simoneonsecurity.com/)
