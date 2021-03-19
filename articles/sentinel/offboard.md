---
title: Az Azure Sentinel eltávolítása | Microsoft Docs
description: Az Azure Sentinel-példány törlése.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: f9c400b55b0da47495db4f1ff4ceb86aa39fe2cc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90885841"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Az Azure Sentinel eltávolítása a munkaterületről

Ha már nem szeretné használni az Azure Sentinelt, ez a cikk azt ismerteti, hogyan távolíthatja el azt a munkaterületről.

## <a name="how-to-remove-azure-sentinel"></a>Az Azure Sentinel eltávolítása

A következő eljárással távolíthatja el az Azure Sentinel alkalmazást a munkaterületről:

1. Lépjen az **Azure Sentinel** elemre, majd a **Beállítások** elemre, és válassza az **Azure Sentinel eltávolítása** lapot.

1. Az Azure Sentinel eltávolítása előtt használja a jelölőnégyzeteket, és tudassa velünk, hogy miért távolítja el.

1. Válassza **Az Azure Sentinel eltávolítása a munkaterületről** lehetőséget.
    
    ![A SecurityInsights-megoldás törlése](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Mi történik a színfalak mögött?

A megoldás eltávolításakor az Azure Sentinel akár 48 órát is igénybe vesz a törlési folyamat első fázisának elvégzéséhez.

A leválasztást követően a offboarding folyamat megkezdődik.

**Az összekötők konfigurációját a rendszer eltávolítja:**
-   Office 365

-   AWS

-   Microsoft-szolgáltatások biztonsági riasztásai: Microsoft Defender for Identity (*korábbi nevén Azure ATP*), Microsoft Cloud App Security beleértve Cloud Discovery Shadow it Reporting, Azure ad Identity Protection, Microsoft Defender for Endpoint (*korábban Microsoft Defender ATP*), Azure Defender-riasztások a Azure Security Center

-   Fenyegetések felderítése

-   Gyakori biztonsági naplók (beleértve a CEF-alapú naplókat, a Barracuda-t és a syslog-t) (ha Azure Security Centerről kap Azure Defender-riasztásokat, a rendszer ezeket a naplókat továbbra is gyűjti.)

-   Windows biztonsági események (ha Azure Security Centerről kap Azure Defender-riasztásokat, a rendszer ezeket a naplókat továbbra is gyűjti.)

Az első 48 órán belül az adatkezelési és elemzési szabályok (beleértve a valós idejű Automation-konfigurációt) többé nem lesznek elérhetők vagy lekérdezhető az Azure Sentinelben.

**30 nap elteltével a rendszer eltávolítja ezeket az erőforrásokat:**

-   Incidensek (beleértve a vizsgálati metaadatokat is)

-   Elemzési szabályok

-   Könyvjelzők

A forgatókönyveket, mentett munkafüzeteket, mentett veszélyforrás-keresési lekérdezéseket és a jegyzetfüzeteket a rendszer nem távolítja el. **Előfordulhat, hogy az eltávolított adatvesztés miatt egyesek megszakadnak. Ezeket manuálisan is eltávolíthatja.**

A szolgáltatás eltávolítása után 30 napos türelmi időszak áll rendelkezésre, amely során újra engedélyezheti a megoldást, és a rendszer visszaállítja az adatait és az elemzési szabályokat, de a konfigurált összekötőket újra kell kapcsolni.

> [!NOTE]
> Ha eltávolítja a megoldást, az előfizetése továbbra is regisztrálva lesz az Azure Sentinel erőforrás-szolgáltatóban. **Manuálisan távolíthatja el.**




## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan távolíthatja el az Azure Sentinel szolgáltatást. Ha meggondolja magát, és újra szeretné telepíteni:
- Ismerkedés [Az Azure Sentinel](quickstart-onboard.md)beszerzésével.
