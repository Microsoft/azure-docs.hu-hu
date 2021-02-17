---
title: Az Azure arc-kompatibilis kiszolgáló beléptetése az Azure Sentinelbe
description: Ismerje meg, hogyan veheti fel az Azure arc-kompatibilis kiszolgálókat az Azure Sentinelbe, és proaktív módon figyelje a biztonsági állapotukat.
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 2364ba72ac5b10ec4e1f433cc6d591c3ca389ecd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584734"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Azure arc-kompatibilis kiszolgálók előkészítése az Azure Sentinel szolgáltatásba

Ez a cikk segítséget nyújt az Azure arc-kompatibilis kiszolgáló [Azure sentinelbe](../../sentinel/overview.md) való bevezetésében, valamint a biztonsággal kapcsolatos események összegyűjtésében. Az Azure Sentinel egyetlen megoldást biztosít a riasztások észlelésére, a fenyegetések láthatóságára, a proaktív vadászatra és a veszélyforrások megválaszolására a vállalaton belül.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy teljesítette a következő követelményeket:

- Egy [Log Analytics-munkaterület](../../azure-monitor/logs/data-platform-logs.md). További információ az Log Analytics munkaterületekről: [a Azure monitor naplók telepítésének megtervezése](../../azure-monitor/logs/design-logs-deployment.md).

- Az Azure Sentinel [engedélyezve van az előfizetésében](../../sentinel/quickstart-onboard.md).

- A gép vagy a kiszolgáló az Azure arc-kompatibilis kiszolgálókhoz csatlakozik.

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Azure arc-kompatibilis kiszolgálók előkészítése az Azure Sentinel szolgáltatásba

Az Azure Sentinel számos, a Microsoft-megoldások számára elérhető összekötőt tartalmaz, és valós idejű integrációt biztosít. Fizikai és virtuális gépek esetén telepítheti a naplókat gyűjtő Log Analytics ügynököt, és továbbíthatja őket az Azure Sentinelnek. Az arc-kompatibilis kiszolgálók a következő módszerekkel támogatják a Log Analytics ügynök telepítését:

- A VM-bővítmények keretrendszer használata.

    Ez a funkció az Azure arc-kompatibilis kiszolgálókon lehetővé teszi, hogy az Log Analytics Agent virtuálisgép-bővítményt egy nem Azure-beli Windows-és/vagy Linux-kiszolgálóra telepítse. A virtuálisgép-bővítmények a következő módszerekkel kezelhetők a hibrid gépeken vagy az arc-kompatibilis kiszolgálók által felügyelt kiszolgálókon:

    - A [Azure Portal](manage-vm-extensions-portal.md)
    - Az [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - Azure [Resource Manager-sablonok](manage-vm-extensions-template.md)

- Azure Policy használata.

    Ezzel a módszerrel a Azure Policy [log Analytics Agent üzembe helyezése Linux vagy a Windows Azure arc gépek](../../governance/policy/samples/built-in-policies.md#monitoring) beépített házirendjének használatával naplózhatja, hogy az ív használatára képes kiszolgáló rendelkezik-e a log Analytics ügynökkel. Ha az ügynök nincs telepítve, az automatikusan szervizelési feladat használatával telepíti azt. Ha azt tervezi, hogy Azure Monitor for VMs használatával figyeli a gépeket, Ehelyett használja a [Azure monitor for VMS engedélyezése](../../governance/policy/samples/built-in-initiatives.md#monitoring) kezdeményezést az log Analytics-ügynök telepítéséhez és konfigurálásához.

Javasoljuk, hogy Azure Policy használatával telepítse a Windows vagy Linux rendszerhez készült Log Analytics-ügynököt.

Az ív-kompatibilis kiszolgálók csatlakoztatása után az adatai streamet kezdenek az Azure Sentinelbe, és készen állnak a használat megkezdésére. Megtekintheti a naplókat a [beépített munkafüzetekben](../../sentinel/quickstart-get-visibility.md) , és megkezdheti a lekérdezések létrehozását a log Analyticsban [az adatvizsgálathoz](../../sentinel/tutorial-investigate-cases.md).

## <a name="next-steps"></a>Következő lépések

Ismerje meg [a fenyegetések észlelését az Azure sentinelben](../../sentinel/tutorial-detect-threats-built-in.md).