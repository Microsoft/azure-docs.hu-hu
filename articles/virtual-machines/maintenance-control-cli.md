---
title: Azure-beli virtuális gépek karbantartásának vezérlése a CLI használatával
description: Megtudhatja, hogyan szabályozhatja, hogy mikor alkalmazza a rendszer a karbantartást az Azure-beli virtuális gépekre a Karbantartás vezérlő és a CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: c57f66eca5d15024c6b10e8fad12ddb575b9f894
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765898"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Frissítések szabályozása a karbantartás-vezérléssel és az Azure CLI-val

A karbantartás-vezérléssel eldöntheti, mikor alkalmazza a platformfrissítéseket az elkülönített virtuális gépek és az Azure-beli dedikált gazdagépek infrastruktúrájának gazdagép-infrastruktúráján. Ez a témakör az Azure CLI karbantartási vezérlési lehetőségeit tartalmazza. További információ a karbantartásvezérlés használatának előnyeiről, korlátozásairól és egyéb felügyeleti lehetőségeiről: Platformfrissítések kezelése [karbantartás-vezérléssel.](maintenance-control.md)

## <a name="create-a-maintenance-configuration"></a>Karbantartási konfiguráció létrehozása

Karbantartási `az maintenance configuration create` konfiguráció létrehozásához használja a et. Ebben a példában létrehozunk egy *myConfig* nevű karbantartási konfigurációt, amely a gazdagépre terjed ki. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host\
   --location eastus
```

Másolja ki a konfigurációazonosítót a kimenetből későbbi használatra.

A használata biztosítja, hogy a karbantartási `--maintenance-scope host` konfiguráció a gazda-infrastruktúra frissítéseit szabályozza.

Ha egy azonos nevű, de más helyen található konfigurációt próbál létrehozni, hibaüzenetet kap. A konfiguráció nevének egyedinek kell lennie az erőforráscsoportban.

A használatával lekérdezheti az elérhető karbantartási `az maintenance configuration list` konfigurációkat.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Karbantartási konfiguráció létrehozása ütemezett időszakkal
Ütemezett ablakot is deklarálhat, amikor az Azure alkalmazza a frissítéseket az erőforrásokon. Ebben a példában egy myConfig nevű karbantartási konfigurációt hozunk létre egy 5 órás ütemezett időszakkal minden hónap negyedik hétfőjére. Miután létrehozott egy ütemezett ablakot, már nem kell manuálisan alkalmaznia a frissítéseket.

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Month Fourth Monday" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

> [!IMPORTANT]
> A **karbantartás** időtartama *legfeljebb 2 óra* lehet. A **karbantartást** legalább egyszer kell beállítani 35 napon belül.

A karbantartás ismétlődése lehet napi, heti vagy havi. Néhány példa:
- **daily**– maintenance-window-recur-every: "Day" **vagy** "3Days"
- **weekly**- maintenance-window-recur-every: "3Weeks" **vagy** "Week Saturday,Sunday"
- **monthly**- maintenance-window-recur-every: "Month day23,day24" **vagy** "Month Last Sunday" **vagy** "Month Fourth Monday"


## <a name="assign-the-configuration"></a>A konfiguráció hozzárendelése

A használatával rendelje hozzá a konfigurációt az elkülönített virtuális `az maintenance assignment create` géphez vagy Azure Dedicated Host.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Alkalmazza a konfigurációt egy virtuális gépre a konfiguráció azonosítójával. Adja meg és adja meg a virtuális gépének nevét, a erőforráscsoportját pedig a virtuális gépnek a fájlban, valamint a virtuális `--resource-type virtualMachines` `--resource-name` gép `--resource-group` `--location` helyét. 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Dedikált gazdagép

A konfiguráció dedikált gazdagépre való alkalmazáshoz a gazdagépcsoport nevét és a nevet kell `--resource-type hosts` `--resource-parent-name` `--resource-parent-type hostGroups` tartalmaznia. 

A paraméter `--resource-id` a gazdagép azonosítója. A dedikált gazdagép [azonosítóját az az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view) használatával kaphatja meg.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Konfiguráció ellenőrzése

Ellenőrizheti, hogy a konfiguráció megfelelően lett-e alkalmazva, vagy ellenőrizheti, hogy jelenleg melyik konfiguráció van alkalmazva a `az maintenance assignment list` használatával.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Dedikált gazdagép 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Függőben lévő frissítések keresése

A `az maintenance update list` használatával láthatja, hogy vannak-e függőben lévő frissítések. Frissítse a --subscription et, hogy a virtuális gépet tartalmazó előfizetés azonosítója legyen.

Ha nincsenek frissítések, a parancs hibaüzenetet ad vissza, amely a következő szöveget tartalmazza: `Resource not found...StatusCode: 404` .

Frissítések esetén a rendszer csak egyet ad vissza, még akkor is, ha több frissítés van függőben. A frissítés adatait a rendszer egy objektumban fogja visszaadni:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Ellenőrizze, hogy vannak-e függőben lévő frissítések egy elkülönített virtuális géphez. Ebben a példában a kimenet táblázatként van formázva az olvashatóság érdekében.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Dedikált gazdagép

Egy dedikált gazdagép függőben lévő frissítésének ellenőrzése. Ebben a példában a kimenet táblázatként van formázva az olvashatóság érdekében. Cserélje le az erőforrások értékeit a saját értékeire.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Frissítések alkalmazása

Függőben `az maintenance apply update` lévő frissítések alkalmazáshoz használható. Sikeres telepítés után ez a parancs a frissítés részleteit tartalmazó JSON-t ad vissza.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Hozzon létre egy kérést, amely frissítéseket alkalmaz egy elkülönített virtuális gépre.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Dedikált gazdagép

Frissítések alkalmazása dedikált gazdagépre.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>A frissítések alkalmazásának állapotának ellenőrzése 

A frissítések előrehaladását a használatával `az maintenance applyupdate get` ellenőrizheti. 

A frissítési névvel láthatja az utolsó frissítés eredményeit, vagy lecserélheti a helyére a futtatáskor visszaadott `default` `myUpdateName` frissítés `az maintenance applyupdate create` nevét.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
A LastUpdateTime az az időpont, amikor a frissítés befejeződött, akár Ön, akár a platform kezdeményezte, ha nem használt önkarbantartási időt. Ha még soha nem alkalmazott frissítést karbantartási vezérléssel, az alapértelmezett értéket fog mutatni.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Dedikált gazdagép

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Karbantartási konfiguráció törlése

Karbantartási `az maintenance configuration delete` konfiguráció törléséhez használja a parancsot. A konfiguráció törlésével eltávolítja a karbantartási vezérlőt a társított erőforrásokból.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>Következő lépések
További információ: Karbantartás [és frissítések.](maintenance-and-updates.md)
