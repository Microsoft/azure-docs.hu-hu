---
title: CLI-minták Azure Monitor
description: CLI-parancsok Azure Monitor szolgáltatásokhoz. A Azure Monitor egy Microsoft Azure szolgáltatás, amely riasztási értesítések küldését teszi lehetővé, a konfigurált telemetria-adatok értékei alapján meghívja a webes URL-címeket, valamint az Cloud Services, a Virtual Machines és a Web Apps.
ms.subservice: ''
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/16/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 196a1d580bd5888a6604c3f98b108e39c08a3412
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627939"
---
# <a name="azure-monitor-cli-samples"></a>CLI-minták Azure Monitor
Ez a cikk a parancssori felület (CLI) parancsait mutatja be Azure Monitor szolgáltatások elérésének megkönnyítéséhez. A Azure Monitor lehetővé teszi a Cloud Services, a Virtual Machines és a Web Apps autoskálázását, illetve riasztási értesítések küldését vagy webes URL-címek meghívását a konfigurált telemetria-adatok értékei alapján.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem telepítette az Azure CLI-t, kövesse az [Azure CLI telepítésének](/cli/azure/install-azure-cli)utasításait. A [Azure Cloud Shell](/azure/cloud-shell) használatával a CLI-t interaktív módon is futtathatja a böngészőben. Tekintse meg az összes elérhető parancs teljes hivatkozását a [Azure monitor CLI-referenciában](/cli/azure/monitor). 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Első lépésként jelentkezzen be az Azure-fiókjába.

```azurecli
az login
```

A parancs futtatása után be kell jelentkeznie a képernyőn megjelenő utasítások segítségével. Az összes parancs az alapértelmezett előfizetés kontextusában működik.

A jelenlegi előfizetés részleteinek listázása.

```azurecli
az account show
```

Módosítsa a munkakörnyezetet egy másik előfizetésre.

```azurecli
az account set -s <Subscription ID or name>
```

Az összes támogatott Azure Monitor parancs listájának megtekintése.

```azurecli
az monitor -h
```

## <a name="view-activity-log"></a>Műveletnapló megtekintése

A műveletnapló eseményeinek listáját jeleníti meg.

```azurecli
az monitor activity-log list
```

Az összes elérhető beállítás megtekintése.

```azurecli
az monitor activity-log list -h
```

Naplók listázása resourceGroup alapján.

```azurecli
az monitor activity-log list --resource-group <group name>
```

A hívó által naplózott naplók listázása.

```azurecli
az monitor activity-log list --caller myname@company.com
```

Listázza a hívó által az erőforrástípus egy Dátumtartomány alapján megjelenő naplókat.

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Riasztások használata 
> [!NOTE]
> Jelenleg csak a (klasszikus) riasztások támogatottak a CLI-ben. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Riasztás (klasszikus) szabályok beolvasása egy erőforráscsoporthoz

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Metrikai riasztás (klasszikus) szabály létrehozása

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Riasztás (klasszikus) szabály törlése

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Log-profilok

Az ebben a szakaszban található információk a log-profilokkal használhatók.

### <a name="get-a-log-profile"></a>Log-profil beolvasása

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Adatmegőrzést biztosító log-profil hozzáadása

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Adatmegőrzési és EventHub-naplózási profil hozzáadása

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Log-profil eltávolítása

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnosztika

Az ebben a szakaszban található információk segítségével a diagnosztikai beállításokkal dolgozhat.

### <a name="get-a-diagnostic-setting"></a>Diagnosztikai beállítás beolvasása

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Diagnosztikai beállítások törlése

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Automatikus méretezés

Az ebben a szakaszban található információk segítségével az autoskálázási beállításokkal dolgozhat. Ezeket a példákat módosítania kell.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Erőforráscsoport méretezési beállításainak beolvasása

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Az autoskálázási beállítások beolvasása egy erőforráscsoport neve alapján

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Az autoskálázás beállításainak megadása

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
