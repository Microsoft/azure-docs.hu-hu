---
title: Az Azure Automation identitás letiltása (előzetes verzió)
description: Ez a cikk azt ismerteti, hogyan tilthat le és távolíthat el felügyelt identitást egy Azure Automation fiókhoz.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: e17e1afda50d9a0263067a77bf26435f53b4f237
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519272"
---
# <a name="disable-your-azure-automation-account-managed-identity-preview"></a>Az Azure Automation identitás letiltása (előzetes verzió)

A rendszer által hozzárendelt identitások kétféleképpen tilthatóak le a Azure Automation. Ezt a feladatot a Azure Portal vagy egy Azure Resource Manager (ARM) sablon használatával is befejezheti.

## <a name="disable-managed-identity-in-the-azure-portal"></a>Felügyelt identitás letiltása a Azure Portal

A felügyelt identitást letilthatja a Azure Portal függetlenül attól, hogy a felügyelt identitás eredetileg hogyan lett beállítva.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Lépjen az Automation-fiókjához, és válassza az **Identitás lehetőséget** a **Fiókbeállítások alatt.**

1. Állítsa a **Rendszer által hozzárendelt** beállítást **Ki, majd** nyomja le a Mentés **gombot.** Amikor a rendszer megerősítést kér, nyomja le az **Igen gombot.**

A felügyelt identitás el lesz távolítva, és már nem rendelkezik hozzáféréssel a célerőforráshoz.

## <a name="disable-using-azure-resource-manager-template"></a>Letiltás Azure Resource Manager sablon használatával

Ha egy Azure Resource Manager-sablonnal hozta létre az Automation-fiók felügyelt identitását, a sablon újrafelhasználásával és a beállításainak módosításával letilthatja a felügyelt identitást. Állítsa az identitásobjektum gyermektulajdonságának típusát **None** (Nincs) típusra az alábbi példában látható módon, majd futtassa újra a sablont.

```json
"identity": { 
   "type": "None" 
} 
```

A rendszer által hozzárendelt identitás ezzel a módszerrel való eltávolítása az Azure AD-ból is törli azt. A rendszer automatikusan eltávolítja a rendszer által hozzárendelt identitásokat az Azure AD-ból, amikor törlik az alkalmazás erőforrását, amelyhez hozzá vannak rendelve.

## <a name="next-steps"></a>Következő lépések

- További információ a felügyelt identitás engedélyezéséről a Azure Automation: [Enable and use managed identity for Automation (preview) (Felügyelt](enable-managed-identity-for-automation.md)identitás engedélyezése és használata az Automationben (előzetes verzió) ).

- Az Automation-fiók biztonságának áttekintését lásd: [Automation-fiókhitelesítés áttekintése.](automation-security-overview.md)
