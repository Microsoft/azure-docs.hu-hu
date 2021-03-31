---
title: Naplók feltöltése az Azure Monitorba
description: Naplók feltöltése az Azure arc-kompatibilis adatszolgáltatásokhoz a Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: f3f29ae1ab868a96e6f70ed964f79c47bc591c4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92376215"
---
# <a name="upload-logs-to-azure-monitor"></a>Naplók feltöltése az Azure Monitorba

Időnként exportálhatja a naplókat, majd feltöltheti őket az Azure-ba. A naplók exportálása és feltöltése szintén létrehozza és frissíti az adatkezelőt, az SQL felügyelt példányát és a PostgreSQL nagy kapacitású Server Group erőforrásait az Azure-ban.

> [!NOTE] 
> Az előzetes verzió ideje alatt az Azure arc-kompatibilis adatszolgáltatások használata díjmentes.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Előkészületek

A naplók feltöltése előtt a következőket kell tennie: 

1. [Log Analytics-munkaterület létrehozása](#create-a-log-analytics-workspace)
1. [AZONOSÍTÓ és megosztott kulcs társítása környezeti változókhoz](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

Log Analytics-munkaterület létrehozásához hajtsa végre ezeket a parancsokat Log Analytics munkaterület létrehozásához, és állítsa be a hozzáférési információkat környezeti változókba.

> [!NOTE]
> Hagyja ki ezt a lépést, ha már rendelkezik munkaterülettel.

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

Példa a kimenetre:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>AZONOSÍTÓ és megosztott kulcs társítása környezeti változókhoz

Mentse a log Workspace-elemzést `customerId` környezeti változóként, amelyet később szeretne használni:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

Ez a parancs a log Analytics-munkaterülethez való kapcsolódáshoz szükséges hozzáférési kulcsokat adja vissza:

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Példa a kimenetre:

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Mentse az elsődleges kulcsot egy környezeti változóban később használni kívánt módon:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>Állítsa be a végső környezeti változókat, és erősítse meg

Az SPN-szolgáltató URL-címének beállítása környezeti változóban:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>Környezeti változók ellenőrzése

Győződjön meg arról, hogy az összes szükséges környezeti változó be van állítva, ha szeretné:


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

A környezeti változók beállításakor a naplók a log munkaterületre tölthetők fel. 

## <a name="upload-logs-to-azure-monitor"></a>Naplók feltöltése az Azure Monitorba

 Az Azure arc-kompatibilis SQL felügyelt példányok és a AzureArc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoportok naplófájljainak feltöltéséhez futtassa a következő CLI-parancsokat:

1. Jelentkezzen be az Azure arc-adatkezelőbe a használatával [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .

   ```console
   azdata login
   ```

   Az utasításokat követve állítsa be a névteret, a rendszergazdai felhasználónevet és a jelszót. 

1. Az összes napló exportálása a megadott fájlba:

   ```console
   azdata arc dc export --type logs --path logs.json
   ```

2. Naplók feltöltése egy Azure monitor log Analytics-munkaterületre:

   ```console
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Naplók megtekintése Azure Portal

A feltöltött naplókat a következő módszerrel tudja lekérdezni a naplólekérdezés-kezelővel:

1. Nyissa meg a Azure Portal, majd keresse meg a munkaterületet név szerint a felső sávon, majd válassza ki.
2. Válassza a naplók lehetőséget a bal oldali panelen.
3. Kattintson az első lépések elemre (vagy válassza ki a Első lépések oldalon található hivatkozásokat, ha szeretne többet megtudni a Log Analytics, ha új).
4. Az oktatóanyagot követve további információkat tudhat meg Log Analyticsről, ha első alkalommal használja Log Analytics.
5. Bontsa ki a táblázatok listájának alján lévő Egyéni naplók elemet, és látni fog egy „sql_instance_logs_CL” nevű táblázatot.
6. Válassza a tábla neve melletti "szem" ikont.
7. Válassza a "megtekintés a lekérdezés-szerkesztőben" gombot.
8. Ekkor egy olyan lekérdezéssel fog rendelkezni a lekérdezés-szerkesztőben, amely a naplóban a legutóbbi 10 eseményt mutatja.
9. Innen kísérletezhet a naplók lekérdezésszerkesztővel való lekérdezésével, riasztások beállításával stb.

## <a name="automating-uploads-optional"></a>Feltöltések automatizálása (nem kötelező)

Ha a mérőszámokat és a naplókat ütemezett alapon szeretné feltölteni, létrehozhat egy parancsfájlt, és néhány percenként elvégezheti az időzítő futtatását. Az alábbi példa a feltöltések automatizálását mutatja be egy linuxos rendszerhéj-parancsfájl használatával.

A kedvenc szöveg/kód szerkesztőjében adja hozzá a következő parancsfájlt a fájlhoz, és mentse parancsfájlként végrehajtható fájlként, például. sh (Linux/Mac) vagy. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

A parancsfájl végrehajtható fájljának elkészítése

```console
chmod +x myuploadscript.sh
```

Futtassa a szkriptet 20 percenként:

```console
watch -n 1200 ./myuploadscript.sh
```

Olyan Feladatütemezőt is használhat, mint például a cron vagy a Windows Feladatütemező, vagy egy Orchestrator, például a Ansible, a Puppet vagy a Chef.

## <a name="next-steps"></a>Következő lépések

[Metrikák és naplók feltöltése a Azure Monitorba](upload-metrics.md)

[Használati adatok, metrikák és naplók feltöltése a Azure Monitorba](upload-usage-data.md)

[Számlázási adatok feltöltése az Azure-ba, és megtekintés a Azure Portal](view-billing-data-in-azure.md)

[Az Azure arc adatkezelő erőforrásának megtekintése Azure Portal](view-data-controller-in-azure-portal.md)
