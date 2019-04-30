---
title: A Log Analytics az Azure Automation-Runbook művelet felhasználó által kezdeményezett |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan futtathat egy Automation-runbook egy Log Analytics keresési eredmény igény szerinti a.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: 9194d5fe6553607ac5a0bb4e133da97f53790984
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424754"
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Az Automation-Runbook egy naplót a Log Analytics keresési eredmény művelet végrehajtása

> [!NOTE]
> Runbook indítása a keresési eredmények funkciója a klasszikus Naplókeresési portál, amely 2019. február 15-én érvénytelenné válnak. Műveletcsoport elindíthat egy runbookot, az egyéb műveletek mellett beállíthatja egy [riasztási szabály](../platform/alerts-log.md) az Azure monitorban.

A napló keresési eredmény az Azure Log Analyticsben, mostantól kiválaszthatja **művelet végrehajtása** egy Automation-runbook futtatását.  A runbook segítségével kijavítani a problémát, vagy egy másik műveletet, például kapcsolatos hibaelhárításhoz, mert egy e-mailt, vagy hozzon létre egy szolgáltatási kérelmet. 


## <a name="components-and-features-used"></a>Használt összetevők és szolgáltatások
* [Az Azure Automation-fiók](../../automation/automation-quickstart-create-account.md)
* [Log Analytics-munkaterület](../../azure-monitor/log-query/log-query-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>A naplóbeli keresés runbook kezdeményezése

A művelet végrehajtása a egy eseményt, és a egy runbookot, a napló keresési eredmények között, első lépésként létrehozni egy naplókeresést, és az eredmények közül egy runbook igény szerinti hívhatók meg. Ez a klasszikus napló keresési funkciója érhető el a [az Azure portal](../../azure-monitor/log-query/log-query-overview.md). Ebben a példában egy Naplókeresés Ez a funkció az alapszintű bemutatója az Azure Portalról elvégzése.

1. Az Azure Portalon kattintson a **minden szolgáltatás** válassza **Log Analytics**.  
2. Válassza ki a Log Analytics-munkaterületre.
3. Válassza ki a munkaterület **naplók (klasszikus)**.  
4. A naplóbeli keresés lapon hajtsa végre a naplók keresése.  
5. A napló keresési eredmények között kattintson a három pont egy mezőt, és az előugró ablakban válassza a balra **művelet végrehajtása a**.<br><br> ![Válassza ki a művelet eltarthat keresési eredmény](./media/take-action/log-search-takeaction-menuoption.png) 
6. Válassza ki **futtathat runbookot** , és válassza ki a runbook futtatását.  Minden runbook választhat a Log Analytics-munkaterülethez társított Automation-fiókban.  Vegye figyelembe a következőket:

    * Runbookok címkék szerint vannak rendszerezve.
    * Runbook bemeneti paraméter értékét a mezőket a keresési eredmények a közvetlenül lehet kiválasztani.  Egy legördülő lista jelenik meg az eredményt pedig válassza ki az összes elérhető mező megjelenítése.  
    * Választhatja a runbook futtatása egy [hibrid runbook-feldolgozó](../../automation/automation-hybrid-runbook-worker.md) , hogy telepítette a számítógépen, amelyen a probléma, ha rendelkezik egy megfelelő hibrid Runbook-feldolgozó csoport, amely csak az ezen a számítógépen, amelynek tartalmazza.  Ha a hibrid feldolgozócsoport neve megegyezik a számítógép, amely a naplózott keresési eredményeknek a nevét, majd a csoport automatikusan aktiválódik.    

6. Miután rákattintott **futtatása**, a runbookfeladat oldalán nyílik meg, hogy a feladat állapotának áttekintése.   

Ha kiválaszt egy runbookot, amely úgy lett konfigurálva, hogy [nevű Log Analytics-riasztásokból](../../automation/automation-create-alert-triggered-runbook.md), nevű bemeneti paraméter van **WebhookData** , amely **objektum** típusa.  A bemeneti paraméter megadása kötelező, ha a keresési eredmények átadása a runbookot, így azt is alakíthatja a JSON-formátumú karakterláncot egy objektumtípust, így az adott elem, amelyre hivatkozhat a runbook-tevékenységek szűrése szüksége.  Kiválasztásával ehhez **keresési eredmény (objektum)** a legördülő listából.<br><br> ![Válassza ki a Webhook-adatobjektum runbook paraméter](media/take-action/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>További lépések

* Tekintse át a [Log Analytics naplóbeli keresési referencia](../../azure-monitor/log-query/log-query-overview.md) megtekintéséhez az összes keresési mezők és elérhető a Log Analytics metszettel.
* Megtudhatja, hogyan lehet Automation-runbook hívása automatikusan, tekintse át a [egy Azure Automation-runbook hívása Log Analytics-riasztásokból](../../automation/automation-create-alert-triggered-runbook.md).  
