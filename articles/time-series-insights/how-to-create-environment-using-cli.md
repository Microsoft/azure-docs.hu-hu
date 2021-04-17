---
title: 2. generációs Azure Time Series Insights létrehozása az Azure CLI használatával – Azure Time Series Insights Gen2 | Microsoft Docs
description: Megtudhatja, hogyan állíthat be egy környezetet Azure Time Series Insights Gen2-ben az Azure CLI használatával.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 17c1ea19f3879f5490922ab4b54f21773191eebd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484227"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>2. generációs Azure Time Series Insights létrehozása az Azure CLI használatával

Ez a dokumentum végigvezeti egy új, 2. generációs Time Series Insights létrehozásán.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy Azure Storage-fiókot a környezete hidegen [tárolt tárolója számára.](./concepts-storage.md#cold-store) Ez a fiók az előzményadatok hosszú távú megőrzésére és elemzésére lett kialakítva.

> [!NOTE]
> A kódban cserélje le a helyére a hideg `mytsicoldstore` tárfiók egyedi nevét.

Először hozza létre a tárfiókot:

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>A környezet létrehozása

Most, hogy létrejött a tárfiók, és a neve és a felügyeleti kulcsa hozzá van rendelve a változókhoz, futtassa az alábbi parancsot a Azure Time Series Insights létrehozásához:

> [!NOTE]
> A kódban cserélje le a következőket a forgatókönyv egyedi nevére:
>
> * `my-tsi-env` a környezet nevével.
> * `my-ts-id-prop` a Time Series Id tulajdonság nevével.

> [!IMPORTANT]
> A környezet Idősor-azonosítója olyan, mint egy adatbázispartíciós kulcs. A Time Series ID az idősorozat-modell elsődleges kulcsaként is működik.
>
> További információ: [Ajánlott eljárások idősor-azonosító kiválasztásához.](./how-to-select-tsid.md)

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Új Azure Time Series Insights eltávolítása

Az Azure CLI használatával törölhet egyéni erőforrásokat, például egy Time Series Insights-környezetet, vagy törölhet egy erőforráscsoportot és annak összes erőforrását, beleértve a Time Series Insights környezeteket is.

Az [új Time Series Insights törléséhez](/cli/azure/ext/timeseriesinsights/tsi/environment#ext_timeseriesinsights_az_tsi_environment_delete)futtassa a következő parancsot:

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

A [tárfiók törléséhez futtassa](/cli/azure/storage/account#az_storage_account_delete)a következő parancsot:

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

Egy [erőforráscsoport és annak](/cli/azure/group#az-group-delete) összes erőforrásának törléséhez futtassa a következő parancsot:

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [a 2.](./concepts-streaming-ingestion-event-sources.md) generációs környezethez Azure Time Series Insights streamelési eseményforrásokat.
* Ismerje meg, hogyan csatlakozhat egy [IoT Hub](./how-to-ingest-data-iot-hub.md)
