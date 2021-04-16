---
title: Az Azure Automation felügyelt identitás letiltása
description: Ez a cikk azt ismerteti, hogyan tilthat le és távolíthat el felügyelt identitást egy Azure Automation fiókhoz.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2021
ms.topic: conceptual
ms.openlocfilehash: 74d029db48f64b38eb323150068e173746d379b7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495056"
---
# <a name="disable-your-azure-automation-account-managed-identity"></a>Az Azure Automation felügyelt identitás letiltása

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

- További információ a felügyelt identitás engedélyezéséről a Azure Automation: Felügyelt identitás engedélyezése és használata az [Automationben.](enable-managed-identity-for-automation.md)

- Az Automation-fiók biztonságának áttekintését lásd: [Automation-fiókhitelesítés áttekintése.](automation-security-overview.md)
