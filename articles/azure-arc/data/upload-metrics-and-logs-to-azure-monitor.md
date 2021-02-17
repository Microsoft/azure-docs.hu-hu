---
title: Használati adatok, metrikák és naplók feltöltése a Azure Monitorba
description: Az erőforrás-leltár, a használati adatok, a metrikák és a naplók feltöltése a Azure Monitorba
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: ac9c8efbe29bf1420a94d486b650758cc22bec2f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575752"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Használati adatok, metrikák és naplók feltöltése a Azure Monitorba

A használati adatokat rendszeresen exportálhatja számlázási célokra, a metrikák figyelésére és a naplókra, majd feltöltheti azt az Azure-ba. A három adattípus exportálásával és feltöltésével az adatvezérlő, az SQL felügyelt példánya és a PostgreSQL nagy kapacitású Server Group-erőforrások is létrehozhatók és frissíthetők az Azure-ban.

> [!NOTE] 
> Az előzetes verzió ideje alatt az Azure arc-kompatibilis adatszolgáltatások használata díjmentes.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

A használati adatok, metrikák vagy naplók feltöltése előtt a következőkre lesz szüksége:

* Eszközök telepítése 
* [Az erőforrás-szolgáltató regisztrálása `Microsoft.AzureData`](#register-the-resource-provider) 
* [A szolgáltatásnév létrehozása](#create-service-principal)

## <a name="install-tools"></a>Eszközök telepítése

A szükséges eszközök a következők: 
* Azure CLI (az) 
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

Lásd: [eszközök telepítése](./install-client-tools.md).

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

A metrikák vagy felhasználói adatok Azure-ba való feltöltése előtt gondoskodnia kell arról, hogy az Azure-előfizetése `Microsoft.AzureArcData` regisztrálva legyen az erőforrás-szolgáltatónál.

Az erőforrás-szolgáltató ellenőrzéséhez futtassa a következő parancsot:

```azurecli
az provider show -n Microsoft.AzureArcData -o table
```

Ha az erőforrás-szolgáltató jelenleg nincs regisztrálva az előfizetésben, regisztrálhatja. A regisztráláshoz futtassa a következő parancsot.  A parancs végrehajtása igénybe vehet egy-két percet.

```azurecli
az provider register -n Microsoft.AzureArcData --wait
```

## <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az egyszerű szolgáltatás a használati és mérőszámi adatok feltöltésére szolgál.

Az alábbi parancsokkal hozza létre a metrikák feltöltésére szolgáló szolgáltatásnevet:

> [!NOTE]
> Egy egyszerű szolgáltatásnév létrehozásához [bizonyos engedélyek szükségesek az Azure-ban](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Egyszerű szolgáltatásnév létrehozásához frissítse az alábbi példát. Cserélje le a `<ServicePrincipalName>` nevet az egyszerű szolgáltatásnév nevére, és futtassa a parancsot:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

Ha korábban létrehozta a szolgáltatásnevet, és csak le kell kérnie az aktuális hitelesítő adatokat, futtassa a következő parancsot a hitelesítő adat alaphelyzetbe állításához.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Ha például egy nevű szolgáltatásnevet szeretne létrehozni `azure-arc-metrics` , futtassa a következő parancsot:

```
az ad sp create-for-rbac --name azure-arc-metrics
```

Példa a kimenetre:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Mentse a `appId` , `password` és `tenant` értékeket egy környezeti változóban későbbi használatra. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

Miután létrehozta a szolgáltatásnevet, rendelje hozzá a szolgáltatásnevet a megfelelő szerepkörhöz. 

## <a name="assign-roles-to-the-service-principal"></a>Szerepkörök társítása az egyszerű szolgáltatáshoz

A parancs futtatásával rendelje hozzá az egyszerű szolgáltatásnevet ahhoz az `Monitoring Metrics Publisher` előfizetéshez tartozó szerepkörhöz, amelyben az adatbázis-példány erőforrásai találhatók:

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Windows-környezetből való futtatáskor dupla idézőjeleket kell használnia a szerepkörök neveihez.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

Példa a kimenetre:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

Ha az egyszerű szolgáltatás hozzá van rendelve a megfelelő szerepkörhöz, folytathatja a metrikák vagy a felhasználói adatok feltöltését. 

## <a name="upload-logs-metrics-or-user-data"></a>Naplók, mérőszámok vagy felhasználói adatok feltöltése

A naplók, mérőszámok vagy felhasználói adatok feltöltésének konkrét lépései a feltöltött információk típusától függően változhatnak. 

[Naplók feltöltése a Azure Monitorba](upload-logs.md)

[Metrikák feltöltése a Azure Monitorba](upload-metrics.md)

[Használati adatok feltöltése a Azure Monitorba](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Általános útmutató a használat, a metrikák exportálásához és feltöltéséhez

Az Azure arc-kompatibilis adatszolgáltatások létrehozási, olvasási, frissítési és törlési műveleteinek naplózása számlázási és figyelési célból történik. Vannak olyan háttér-szolgáltatások, amelyek figyelik ezeket a szifilisz-műveleteket, és megfelelően kiszámítják a felhasználást. A használat vagy a felhasználás tényleges kiszámítása ütemezett alapon történik, és a háttérben történik. 

Az előzetes verzió ideje alatt ez a folyamat éjjel történik. Az általános útmutató a használat napi egyszeri feltöltése. Ha a használati adatokat több alkalommal exportálják és töltötték fel ugyanazon a 24 órás időszakon belül, akkor csak az erőforrás-leltár frissül Azure Portal de nem az erőforrás-használat.

A metrikák feltöltéséhez az Azure monitor csak az utolsó 30 perces adatokat fogadja el ([További információ](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). A metrikák feltöltésére vonatkozó útmutató a metrikák az exportfájl létrehozása utáni azonnali feltöltésére szolgál, így megtekintheti a teljes adathalmazt Azure Portalban. Ha például a 2:00 ÓRAKOR exportálta a metrikákat, és a feltöltési parancsot 2:50 ÓRAKOR futtatta. Mivel a Azure Monitor csak az elmúlt 30 percben fogadja az adatgyűjtést, előfordulhat, hogy a portálon nem jelennek meg az összes információ. 

## <a name="next-steps"></a>Következő lépések

[Az egyszerű szolgáltatások ismertetése](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[Számlázási adatok feltöltése az Azure-ba, és megtekintés a Azure Portal](view-billing-data-in-azure.md)

[Az Azure arc adatkezelő erőforrásának megtekintése Azure Portal](view-data-controller-in-azure-portal.md)