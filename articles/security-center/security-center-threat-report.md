---
title: Azure Security Center fenyegetésfelderítési jelentés | Microsoft Docs
description: Ezen az oldalon megtudhatja, hogy a vizsgálat során Azure Security Center veszélyforrások intelligencia-jelentéseit használja a biztonsági riasztásokkal kapcsolatos további információkért
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: ec6d227059c3f4fd1285f224e13169a2479bc65f
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438233"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure Security Center fenyegetés intelligencia jelentés

Ezen a lapon megtudhatja, hogy Azure Security Center a fenyegetések felderítésével kapcsolatos jelentései Hogyan segíthetnek a biztonsági riasztást kiváltó veszélyforrások megismerésében.


## <a name="what-is-a-threat-intelligence-report"></a>Mi a fenyegetésfelderítési jelentés?

Security Center veszélyforrások elleni védelem az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások biztonsági adatainak figyelésével működik. A fenyegetések azonosításához elemzi ezeket az információkat, és gyakran megvizsgálja a különböző forrásokból származó adatok közötti összefüggéseket. További információ: [How Azure Security Center észleli és reagál a fenyegetésekre](security-center-alerts-overview.md#detect-threats).

Ha a Security Center észleli a fenyegetést, egy [biztonsági riasztást](security-center-managing-and-responding-alerts.md)indít, amely részletes információkat tartalmaz az eseményről, beleértve a szervizeléssel kapcsolatos javaslatokat is. Az incidensek megvizsgálása és a fenyegetések elhárítása érdekében a Security Center az észlelt fenyegetésekkel kapcsolatos információkat tartalmazó veszélyforrás-felderítési jelentéseket nyújt. A jelentés olyan információkat tartalmaz, mint például a következők:

* A támadó identitása vagy társításai (ha ez az információ elérhető)
* A támadó céljai
* Jelenlegi és korábbi támadássorozatok (ha ez az információ elérhető)
* Támadók taktikája, eszközei és eljárásai
* A sérüléssel kapcsolatos mutatók (IoC), például URL-címek és fájlkivonatok
* A viktimológia, azaz az ipari és földrajzi gyakoriság segít annak meghatározásában, hogy az Ön Azure-erőforrásai veszélyeztetettek-e
* Kezelési és elhárítási információk

> [!NOTE]
> Az információ mennyisége minden jelentésben más, a részletesség pedig a kártevő tevékenységén és gyakoriságán alapul.

A Security Centerben három típusa van a fenyegetési jelentéseknek, amelyek a támadástól függően változnak. Az elérhető jelentések a következők:

* **Tevékenység-csoport jelentés**: a támadók, a célkitűzések és a taktikák mély merülését biztosítják.
* **Kampányjelentés**: adott támadási kampányok részleteire összpontosít.
* **Fenyegetésösszegző jelentés**: az előző két jelentés összes elemét tartalmazza.

Az ilyen típusú információk az incidensek válaszának folyamata során hasznosak, ahol a támadás forrását, a támadó motivációit és a probléma enyhítését célzó folyamatban lévő nyomozást végeznek a jövőben.



## <a name="how-to-access-the-threat-intelligence-report"></a>Hogyan értheti el a fenyegetésfelderítési jelentést?

1. A Security Center oldalsávján nyissa meg a **biztonsági riasztások** lapot.
1. Válasszon ki egy riasztást. 
    Megnyílik a riasztások részletei lap a riasztással kapcsolatos további részletekkel. Alább látható a **ransomware-mutatók észlelt** riasztás részletei lap.

    [![Ransomware-mutatók észlelt riasztás részletei lap](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. Válassza ki a jelentésre mutató hivatkozást, és egy PDF-fájlt fog megnyílni az alapértelmezett böngészőben.

    [![Potenciálisan nem biztonságos műveleti riasztás részletei lap](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    Igény szerint letöltheti a PDF-jelentést. 

    >[!TIP]
    > Az egyes biztonsági riasztásokhoz elérthető információ mennyisége a riasztás típusától függően változik.



## <a name="next-steps"></a>Következő lépések

Ez az oldal azt ismerteti, hogyan nyitható meg a veszélyforrások felderítésére vonatkozó jelentések a biztonsági riasztások kivizsgálása során. A kapcsolódó információkat a következő lapokon tekintheti meg:

* [Biztonsági riasztások kezelése és válaszadás a Azure Security Center](security-center-managing-and-responding-alerts.md). A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [A biztonsági incidensek kezelésének Azure Security Center](security-center-incident.md)