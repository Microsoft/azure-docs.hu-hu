---
title: A fiókkal kapcsolatos problémák elhárítása Azure Automation
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a problémákat egy Azure-fiókkal.
services: automation
ms.subservice: ''
ms.date: 03/24/2020
ms.topic: troubleshooting
ms.openlocfilehash: 06c15136e9d2fabdf50031c8b4be455cf2f7bbca
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896579"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>A fiókkal kapcsolatos problémák elhárítása Azure Automation

Ez a cikk a Azure Automation-fiók használatakor felmerülő problémák megoldásait ismerteti. Az Automation-fiókokkal kapcsolatos általános információkért lásd: [Azure Automation fiók hitelesítésének áttekintése](../automation-security-overview.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Forgatókönyv: nem sikerült regisztrálni az Automation erőforrás-szolgáltatót az előfizetésekhez

### <a name="issue"></a>Probléma

Ha például az Automation-fiókban Update Management, a felügyeleti funkciókkal dolgozik, a következő hibaüzenet jelenik meg:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Ok

Az Automation erőforrás-szolgáltató nincs regisztrálva az előfizetésben.

### <a name="resolution"></a>Feloldás

Az Automation erőforrás-szolgáltató regisztrálásához kövesse az alábbi lépéseket a Azure Portalban:

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com).

2. Lépjen az **előfizetések** elemre, és válassza ki az előfizetését.   

3. A **Beállítások** területen válassza az **erőforrás-szolgáltatók** elemet.

4. Az erőforrás-szolgáltatók listájában ellenőrizze, hogy a **Microsoft. Automation** erőforrás-szolgáltató regisztrálva van-e.

5. Ha a szolgáltató nem szerepel a listáján, regisztrálja az [erőforrás-szolgáltatói regisztráció hibáinak elhárítása](../../azure-resource-manager/templates/error-register-resource-provider.md)című témakörben leírtak szerint.

## <a name="next-steps"></a>Következő lépések

Ha ez a cikk nem oldja meg a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) . Ez a hivatalos Microsoft Azure fiók az Azure-Közösség megfelelő erőforrásokhoz való csatlakoztatásához: válaszok, támogatás és szakértők.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése** lehetőséget.
