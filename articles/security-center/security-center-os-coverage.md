---
title: A Azure Security Center által támogatott platformok | Microsoft Docs
description: Ez a dokumentum a Azure Security Center által támogatott platformok listáját tartalmazza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 68cb738ae6e4689a0356ea56c1de2d383ea83ad6
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449935"
---
# <a name="supported-platforms"></a>Támogatott platformok 

Ezen a lapon láthatók a Azure Security Center által támogatott platformok és környezetek.

## <a name="combinations-of-environments"></a>Környezetek kombinációi <a name="vm-server"></a>

Azure Security Center a különböző típusú hibrid környezetekben támogatja a virtuális gépeket és a kiszolgálókat:

* Csak Azure
* Az Azure és a helyszíni
* Azure és más felhők
* Azure, egyéb felhők és helyszíni

Azure-előfizetésben aktivált Azure-környezet esetén a Azure Security Center automatikusan felderíti az előfizetésen belül üzembe helyezett IaaS-erőforrásokat.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Security Center a [log Analytics ügynöktől](../azure-monitor/platform/agents-overview.md#log-analytics-agent)függ. Győződjön meg arról, hogy a gépek az ügynök által támogatott operációs rendszerek valamelyikét futtatják az alábbi lapokon leírtak szerint:

* [Log Analytics ügynök a Windows által támogatott operációs rendszerekhez](../azure-monitor/platform/agents-overview.md#supported-operating-systems)
* [A Linux által támogatott operációs rendszerek Log Analytics ügynöke](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

Győződjön meg arról is, hogy a Log Analytics ügynök [megfelelően van konfigurálva, hogy az adatküldés Security Center](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Ha többet szeretne megtudni a Windows és Linux rendszerhez elérhető konkrét Security Center funkciókról, tekintse meg a [számítógépek szolgáltatás lefedettségét](security-center-services.md)ismertető témakört.

## <a name="managed-virtual-machine-services"></a>Felügyelt virtuális gépek szolgáltatásai <a name="virtual-machine"></a>

Az Azure által felügyelt szolgáltatások, például az Azure Kubernetes (ak), a Azure Databricks és egyebek mellett a virtuális gépek is létrejönnek egy ügyfél-előfizetésben. Security Center ezeket a virtuális gépeket is felhasználja, és a Log Analytics ügynök telepíthető és konfigurálható, ha támogatott operációs rendszer érhető el.

## <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

A Cloud Service-ben futó virtuális gépek is támogatottak. Csak az üzemi tárolóhelyeken futó Cloud Services-alapú webes és feldolgozói szerepköröket figyeli a rendszer. További információ a Cloud Services szolgáltatásról: [Az Azure Cloud Services áttekintése](../cloud-services/cloud-services-choose-me.md).

A Azure Stackban található virtuális gépek védelme is támogatott. A Security Center Azure Stack-nal való integrálásával kapcsolatos további információkért tekintse meg [a Azure stack virtuális gépek Security Centerba való](quickstart-onboard-machines.md)bevezetését ismertető témakört. 

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogy [a Security Center hogyan gyűjt adatokat az log Analytics ügynökkel](security-center-enable-data-collection.md).
- Megtudhatja [, hogyan kezeli és védi a Security Center az információkat](security-center-data-security.md).
- Megtudhatja, hogyan [tervezheti meg és értelmezheti a Azure Security Center elfogadásához szükséges tervezési szempontokat](security-center-planning-and-operations-guide.md).