---
title: (Másolás) Log Analytics-riasztások kiterjesztése az Azure Government Cloud
description: Másolnunk riasztások a Log Analytics az OMS-portálon az Azure-riasztások, a folyamat áttekintése olyan címzési gyakori ügyfél problémákat ismerteti.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: efd2fc9d164564126b7b641ef35bbb10c4834f49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996402"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>A Log Analytics-riasztások kiterjesztése az Azure-riasztások

> [!NOTE]
> A Microsoft már az Azure nyilvános verzióihoz készült cikkben leírt folyamat befejeződött. Azonban továbbra is érvényes a US government verziók.  

Nemrég, amíg az Azure Log Analytics a saját riasztási funkcióval rendelkezik, amely sikerült proaktívan kaphat értesítést a Log Analytics-adatok alapuló feltételek tartalmazza. A Microsoft Operations Management Suite portál riasztási szabályok felügyelt. A riasztások új kezelőfelülete mostantól integrálva van riasztási között különböző szolgáltatások, Microsoft Azure-ban. Érhető el **riasztások** alatt az Azure Monitor az Azure Portalon, és támogatja a vizsgálati naplók, metrikák, a riasztások és a Log Analytics és az Azure Application Insights-naplók.

Az Azure Government felhőalapú szolgáltatást használó ügyfeleink OMS-portálon is [önkéntesen terjeszthető ki a riasztási szabályok az Azure](alerts-extend-tool.md) a **2019. február 1.**. Kezdési **2019. március 1.** - Microsoft auto-kiterjeszti rendszeres módon minden meglévő riasztási szabályok az Azure Government - OMS-portálon az Azure-bA; a break a figyelésre és állásidő nélkül. Ekkor vagy később létrehozott minden új munkaterület **2019. március 1.** az Azure Government cloudban OMS portál lesz automatikus terjesztve az Azure-bA.

## <a name="benefits-of-extending-your-alerts"></a>A riasztások kiterjesztését az előnyei
Nincsenek számos előnnyel jár. a létrehozása és kezelése például a riasztások az Azure Portalon:

- Eltérően az Operations Management Suite portálján, ahol csak 250 riasztások létrehozása sikerült, és tekinthetők meg, az Azure Alerts rendelkezik ilyen korlátozás nélkül.
- Az Azure Alerts kezelése, enumerálásához és megtekintheti a riasztási típusokat. Korábban a sikerült csak ekkor a Log Analytics-riasztásokkal.
- A figyelés és riasztások, a felhasználók számára hozzáférést korlátozhatja a [Azure Monitor szerepkör](../../azure-monitor/platform/roles-permissions-security.md).
- Az Azure Alerts használhat [Műveletcsoportok](../../azure-monitor/platform/action-groups.md). Ez lehetővé teszi, hogy egynél több olyan művelet végrehajtását. Akkor is, az SMS küldése a telefonhívás, egy Azure Automation-runbook meghívása, egy webhook meghívása és az IT Service Management (ITSM)-összekötő konfigurálása. 

## <a name="process-of-extending-your-alerts"></a>A riasztások kiterjesztésének folyamata
Áttérés a riasztások a Log Analytics az Azure Alerts folyamata nem foglalja magában a riasztások definícióit, a lekérdezés vagy a konfiguráció semmilyen módon módosítása. Az egyetlen változás szükség, hogy az Azure-ban, akkor minden művelet végrehajtására műveletcsoport használatával. Műveletcsoportok már társítva a riasztás, ha azok szerepelnek a kiterjesztett az Azure-bA.

Riasztások a Log Analytics-munkaterület, ki kell terjeszteni az Azure-bA ütemezésekor mindaddig működnek, és nem az egyébként veszélyeztetheti a konfigurációt. Ütemezése, a riasztások előfordulhat, hogy nem érhető el, a módosítás átmenetileg, de továbbra is új Azure-riasztások létrehozásához ebben az időszakban. Ha próbál szerkeszteni vagy létrehozni a riasztásokat az Operations Management Suite-portálon, akkor a beállítást, így a Log Analytics-munkaterületet hoz létre. Azt is beállíthatja az Azure Portalon az Azure-riasztások létrehozásához őket.

 ![Riasztások létrehozása Log Analytics vagy az Azure Alerts beállítás képernyőképe](media/alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Riasztások kiterjesztése a Log Analytics az Azure-ba nem díjkötelesek a fiókjához. Lekérdezés alapú Log Analytics-riasztások Azure Alerts használatával nem kell fizetniük keretein belül, és a megadott feltételek a [házirend díjszabás az Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).  


### <a name="how-to-extend-your-alerts-voluntarily"></a>A riasztások önkéntesen bővítése
Kiterjeszti a riasztásokat az Azure Alerts szolgáltatáshoz, az Operations Management Suite portálon elérhető a varázslót használhatja, vagy megteheti szoftveresen egy API-val. További információkért lásd: [riasztások kiterjesztése az Azure Operations Management Suite portál és API-bA](alerts-extend-tool.md).

## <a name="experience-after-extending-your-alerts"></a>A riasztások kiterjesztését az után élmény
Után a riasztások bővítve lettek az Azure Alerts szolgáltatáshoz, ezek továbbra is az Operations Management Suite portálon felügyeleti nem működnek, mint a korábban is.

![Képernyőkép az Operations Management Suite portálján, a felsorolt riasztások](media/alerts-extend/PostExtendList.png)

Ha egy meglévő riasztást szerkesztheti, vagy hozzon létre egy új riasztás az Operations Management Suite portálon próbál, automatikusan átirányítjuk az Azure Alerts szolgáltatáshoz.  

> [!NOTE]
> Győződjön meg arról, hogy az egyéni felhasználók számára, akik hozzáadása vagy szerkesztése a riasztások rendelt engedélyek megfelelően hozzárendeli az Azure-ban. Szeretné megtudni, hogy milyen engedélyeket kell biztosítania, lásd: [engedélyek az Azure Monitor és a riasztások](../../azure-monitor/platform/roles-permissions-security.md).  
> 

Hozzon létre a riasztások továbbra is a [Log Analytics API](../../azure-monitor/platform/api-alerts.md) és [Log Analytics-erőforrás sablon](../../azure-monitor/insights/solutions-resources-searches-alerts.md). Ha így tesz, meg kell adnia Műveletcsoportok.

## <a name="next-steps"></a>További lépések

* Ismerje meg az eszközöket, [kezdeményezési Log Analyticsből származó riasztások kiterjesztése az Azure-ba való](alerts-extend-tool.md).
* Tudjon meg többet a [Azure Alerts élmény](../../azure-monitor/platform/alerts-overview.md).
* Ismerje meg, hogyan hozhat létre [naplóriasztások az Azure Alerts](alerts-unified-log.md).

