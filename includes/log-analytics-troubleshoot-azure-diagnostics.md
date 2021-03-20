---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 6890c71ac7c265d46cc77751786fea4d0b228588
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179512"
---
### <a name="troubleshoot-azure-diagnostics"></a>Az Azure Diagnostics hibaelhárítása

Ha a következő hibaüzenetet kapja, a Microsoft.insights erőforrás-szolgáltató nincs regisztrálva:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Az erőforrás-szolgáltató regisztrálásához hajtsa végre a következő lépéseket az Azure Portalon:

1.  A bal oldalon található navigációs ablakban kattintson az *Előfizetések* elemre
2.  Válassza ki a hibaüzenetben szereplő előfizetést
3.  Kattintson az *Erőforrás-szolgáltatók* elemre
4.  Keresse meg a *Microsoft.insights* szolgáltatót
5.  Kattintson a *Regisztrálás* hivatkozásra

![Regisztrálja a microsoft.insights erőforrás-szolgáltatót](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

A *Microsoft.insights* erőforrás-szolgáltató regisztrálása után próbálja meg ismét konfigurálni a diagnosztikát.


Ha a PowerShellben a következő hibaüzenet jelenik meg, frissítenie kell a PowerShell verzióját:

`Set-AzDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Frissítse Azure PowerShell verzióját, kövesse az [Azure PowerShell telepítése](/powershell/azure/install-az-ps) című cikkben található utasításokat.
