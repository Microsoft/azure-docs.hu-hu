---
title: Azure Time Series Insights Gen2-környezet létrehozása az Azure CLI-Azure Time Series Insights Gen2 használatával | Microsoft Docs
description: Ismerje meg, hogyan állíthat be környezetet Azure Time Series Insights Gen2 az Azure CLI használatával.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: ed185413cff155610b2b088b1791169e33f6ce7a
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103464434"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Azure Time Series Insights Gen2-környezet létrehozása az Azure CLI használatával

Ez a dokumentum végigvezeti egy új Time Series Insights Gen2-környezet létrehozásán.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy Azure Storage-fiókot a környezete [hűtőházi tárolójához](./concepts-storage.md#cold-store). Ez a fiók a régi adatok hosszú távú megőrzésére és elemzésére szolgál.

> [!NOTE]
> A kódban cserélje le a `mytsicoldstore` nevet a hűtőházi Storage-fiókjához tartozó egyedi névre.

Először hozza létre a Storage-fiókot:

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>A környezet létrehozása

Most, hogy létrehozta a Storage-fiókot, és a neve és a felügyeleti kulcsa hozzá van rendelve a változóhoz, futtassa az alábbi parancsot a Azure Time Series Insights környezet létrehozásához:

> [!NOTE]
> A kódban cserélje le a következőt egyedi névvel a forgatókönyvhöz:
>
> * `my-tsi-env` a környezet nevével.
> * `my-ts-id-prop` az idősorozat-azonosító tulajdonságának nevével.

> [!IMPORTANT]
> A környezet idősorozat-azonosítója például egy adatbázis-partíciós kulcs. Az idősorozat-azonosító a Time Series-modell elsődleges kulcsaként is működik.
>
> További információ: [ajánlott eljárások az idősorozat-azonosító kiválasztásához.](./how-to-select-tsid.md)

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Azure Time Series Insights környezet eltávolítása

Az Azure CLI-vel törölhet egy adott erőforrást, például egy Time Series Insights környezetet, vagy törölhet egy erőforráscsoportot és minden erőforrását, beleértve a Time Series Insights környezeteket is.

[Time Series Insights környezetek törléséhez](/cli/azure/ext/timeseriesinsights/tsi/environment?view=azure-cli-latest#ext_timeseriesinsights_az_tsi_environment_delete)futtassa a következő parancsot:

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

[A Storage-fiók törléséhez](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_delete)futtassa a következő parancsot:

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

Egy erőforráscsoport és az összes erőforrás [törléséhez](/cli/azure/group#az-group-delete) futtassa a következő parancsot:

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg az Azure Time Series Insights Gen2-környezet adatfolyam-betöltési [eseményeinek forrásaival](./concepts-streaming-ingestion-event-sources.md) .
* Útmutató [IoT hubhoz](./how-to-ingest-data-iot-hub.md) való kapcsolódáshoz
