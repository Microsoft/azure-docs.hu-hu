---
title: Automatikus regisztráció az SQL IaaS-ügynök bővítménnyel
description: Ebből a témakörből megtudhatja, hogyan engedélyezheti az automatikus regisztrálási funkciót, hogy a Azure Portal használatával automatikusan regisztrálja az összes múltbeli és jövőbeli SQL Server virtuális gépet az SQL IaaS-ügynök bővítménnyel.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.openlocfilehash: 139852949a3744fd603cb197b2e27fa32679aae0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042419"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>Automatikus regisztráció az SQL IaaS-ügynök bővítménnyel
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

A Azure Portal automatikus regisztráció funkciójának engedélyezésével automatikusan regisztrálhatja az Azure Virtual Machines (VM) összes aktuális és jövőbeli SQL Serverét az [SQL IaaS-ügynök bővítményével](sql-server-iaas-agent-extension-automate-management.md) egyszerűsített módban. 

Ebből a cikkből megtudhatja, hogyan engedélyezheti az automatikus regisztrálási funkciót. Azt is megteheti, hogy [egyetlen virtuális gépet regisztrál](sql-agent-extension-manually-register-single-vm.md), vagy [tömegesen regisztrálja a virtuális gépeket](sql-agent-extension-manually-register-vms-bulk.md) az SQL IaaS-ügynök bővítménnyel. 

## <a name="overview"></a>Áttekintés

A SQL Server VM regisztrálása az [SQL IaaS-ügynök bővítménnyel](sql-server-iaas-agent-extension-automate-management.md) a funkciók teljes körű kihasználásának feloldásához. 

Ha engedélyezve van az automatikus regisztráció, a feladatok naponta futnak annak észleléséhez, hogy SQL Server telepítve van-e az előfizetésben lévő összes nem regisztrált virtuális gépre. Ezt úgy teheti meg, hogy az SQL IaaS-ügynök bővítmény bináris fájljait másolja a virtuális gépre, majd egy egyszeri segédprogramot futtat, amely a SQL Server beállításjegyzék-struktúrát ellenőrzi. Ha a rendszer észleli a SQL Server struktúrát, a virtuális gép egyszerűsített módban van regisztrálva a kiterjesztéssel. Ha nem létezik SQL Server struktúra a beállításjegyzékben, a bináris fájlok törlődnek.

Miután engedélyezte az automatikus regisztrációt az előfizetéshez, a SQL Server telepített összes aktuális és jövőbeli virtuális gép az SQL IaaS-ügynök bővítménye **egyszerűsített módban, állásidő nélkül, és a SQL Server szolgáltatás újraindítása nélkül** lesz regisztrálva. Továbbra is [manuálisan kell frissítenie a teljes kezelhetőségi módra](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) , hogy kihasználhassa a teljes szolgáltatáskészlet előnyeit. 

> [!IMPORTANT]
> Az SQL IaaS-ügynök bővítmény olyan adatokat gyűjt az expressz célra, amelyek az ügyfelek számára opcionális előnyöket biztosítanak SQL Server Azure-beli Virtual Machines való használatakor. A Microsoft ezeket az adatszolgáltatásokat nem használja fel a licencelési naplózáshoz az ügyfél előzetes belefoglalása nélkül. További információért tekintse meg a [SQL Server adatvédelmi kiegészítését](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Előfeltételek

A SQL Server VM a bővítménnyel való regisztrálásához a következőkre lesz szüksége: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/) és legalább [közreműködő szerepkör](../../../role-based-access-control/built-in-roles.md#all) -engedély.
- Egy Azure Resource Model [Windows Server 2008 R2 (vagy újabb) rendszerű virtuális gép](../../../virtual-machines/windows/quick-create-portal.md) , amelyen [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) központilag vagy Azure Government felhőben üzembe helyezhetők. A Windows Server 2008 nem támogatott. 


## <a name="enable"></a>Engedélyezés

Ha engedélyezni szeretné a SQL Server virtuális gépek automatikus regisztrációját a Azure Portalban, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon az [**SQL Virtual Machines**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) erőforrás oldalára. 
1. Válassza az **automatikus SQL Server VM regisztráció** lehetőséget az **automatikus regisztráció** lap megnyitásához. 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="Az automatikus regisztráció lap megnyitásához válassza az automatikus SQL Server VM regisztráció lehetőséget.":::

1. Válassza ki az előfizetést a legördülő menüből. 
1. Olvassa el a feltételeket, és ha elfogadja, válassza az **Elfogadom** lehetőséget. 
1. Válassza a **regisztráció** lehetőséget a funkció engedélyezéséhez és az összes jelenlegi és jövőbeli SQL Server virtuális gép automatikus regisztrálásához az SQL IaaS-ügynök bővítménnyel. Ez nem indítja újra a SQL Server szolgáltatást a virtuális gépeken. 

## <a name="disable"></a>Letiltás

Az automatikus regisztrációs funkció letiltásához használja az [Azure CLI](/cli/azure/install-azure-cli) -t vagy a [Azure PowerShell](/powershell/azure/install-az-ps) . Ha az automatikus regisztrálási funkció le van tiltva, SQL Server az előfizetéshez hozzáadott virtuális gépeket manuálisan kell regisztrálni az SQL IaaS-ügynök bővítménnyel. Ez nem törli a már regisztrált meglévő SQL Server virtuális gépek regisztrációját.



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha le szeretné tiltani az automatikus regisztrációt az Azure CLI használatával, futtassa a következő parancsot: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Ha Azure PowerShell használatával szeretné letiltani az automatikus regisztrációt, futtassa a következő parancsot: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Több előfizetés engedélyezése

A PowerShell használatával több Azure-előfizetéshez is engedélyezheti az automatikus regisztráció funkciót. 

Ehhez kövesse az alábbi lépéseket:

1. Mentse a [parancsfájlt](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) `.ps1` fájlba, például: `EnableBySubscription.ps1` . 
1. Navigáljon arra a helyre, ahová a parancsfájlt rendszergazdai parancssor vagy PowerShell-ablak használatával mentette. 
1. Kapcsolódjon az Azure-hoz ( `az login` ).
1. Futtassa a szkriptet, és adja át a SubscriptionIds paraméterként, például:   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Például: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

A sikertelen regisztrációs hibák tárolása `RegistrationErrors.csv` ugyanabban a címtárban történik, ahol mentette és futtatta a `.ps1` parancsfájlt. 

## <a name="next-steps"></a>Következő lépések

Frissítse a kezelhetőségi módot [teljes mértékben](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) , hogy kihasználhassa az SQL IaaS-ügynök bővítmény által biztosított összes szolgáltatáskészlet előnyeit. 
