---
title: Az Azure Event Griddel kapcsolatos események szűrése
description: Bemutatja, hogyan hozhat létre, amely az események szűréséhez állítsa be az Azure Event Grid-előfizetéssel.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: spelluru
ms.openlocfilehash: 5bb95b80e12c818641e2be2b929cdfd01f8f5b5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304225"
---
# <a name="filter-events-for-event-grid"></a>Az Event Griddel kapcsolatos események szűrése

Ez a cikk bemutatja, hogyan használatával szűrhetők az események Event Grid-előfizetés létrehozásakor. Az események szűrése lehetőségei kapcsolatos további információkért lásd: [szűrést az Event Grid-előfizetések ismertetése esemény](event-filtering.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Eseménytípus szerint szűrheti.

Event Grid-előfizetés létrehozásakor, amelyeket megadhat [eseménytípusok](event-schema.md) a végponthoz való küldéséhez. Ebben a szakaszban található példák egy erőforráscsoportot az esemény-előfizetések létrehozása, de az elküldött események `Microsoft.Resources.ResourceWriteFailure` és `Microsoft.Resources.ResourceWriteSuccess`. Ha nagyobb rugalmasságot eseménytípusok események szűrés során, tekintse meg a szűrő a speciális operátorok és az adatok mezők szerint.

PowerShell esetén használja a `-IncludedEventType` paraméter-előfizetés létrehozásakor.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Azure CLI esetén használja a `--included-event-types` paraméter. Az alábbi példában az Azure CLI Bash-felületen:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

A Resource Manager-sablonnal, használja a `includedEventTypes` tulajdonság.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Szűrés tulajdonos szerint

Szűrheti a tulajdonos az eseményadatokat az eseményeket. Elején vagy végén az e-mail tárgyát megfelelő érték adható meg. Ha nagyobb rugalmasságot listázásakor, a tulajdonos által események, tekintse meg a szűrő a speciális operátorok és az adatok mezők szerint.

A következő PowerShell-példa hoz létre egy esemény-előfizetés szűri az elején által az e-mail tárgyát. Használja a `-SubjectBeginsWith` paraméter események megjelennek a meghatározott erőforráshoz való korlátozásához. A hálózati biztonsági csoport erőforrás-Azonosítóját adja át.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

A következő PowerShell-példa létrehoz egy előfizetést egy blob Storage. Események megjelennek a tulajdonosa, amely korlátozza `.jpg`.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

A következő Azure CLI-példa hoz létre egy esemény-előfizetés szűri az elején által az e-mail tárgyát. Használja a `--subject-begins-with` paraméter események megjelennek a meghatározott erőforráshoz való korlátozásához. A hálózati biztonsági csoport erőforrás-Azonosítóját adja át.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

A következő Azure CLI-példa létrehoz egy előfizetést egy blob Storage. Események megjelennek a tulajdonosa, amely korlátozza `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

A következő Resource Manager-sablon példában hozzon létre egy esemény-előfizetés szűri az elején által az e-mail tárgyát. Használja a `subjectBeginsWith` tulajdonság események megjelennek a meghatározott erőforráshoz való korlátozásához. A hálózati biztonsági csoport erőforrás-Azonosítóját adja át.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

A következő Resource Manager-sablon példa létrehoz egy előfizetést egy blob Storage. Események megjelennek a tulajdonosa, amely korlátozza `.jpg`.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Operátorok és az adatok szűrése

A nagyobb rugalmasság a szűrést operátorok és az események szűrése adattulajdonságok is használhatja.

### <a name="subscribe-with-advanced-filters"></a>Fizessen elő a speciális szűrők

Az operátorok és kulcsot is, használhatja a Speciális szűrés kapcsolatos további információkért lásd: [Speciális szűrés](event-filtering.md#advanced-filtering).

Ezek a példák egy egyéni témakör létrehozása. Ezeket az egyéni témakörre való feliratkozás, és az objektum az érték szerinti szűréshez. Az események, amelyek a kék szín tulajdonsága, vagy a piros, zöld küldhetők az előfizetés.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Vegye figyelembe, hogy az előfizetéshez [lejárati dátum](concepts.md#event-subscription-expiration) tartozik.

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Teszt szűrő

A szűrő teszteléséhez, és állítsa be a zöld szín mező küldünk egy eseményt. Mivel a zöld pedig egy értéket a szűrőben, az eseményt a rendszer továbbítja a végpontot.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Egy olyan forgatókönyvet, ahol nem érkezik meg az esemény teszteléséhez küldhet egy eseményt a szín a mezőben az sárga. Sárga nem egy, az eseményt az előfizetés nem lett elküldve az előfizetésben megadott értéket.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>További lépések

* Esemény kézbesítések figyelésével kapcsolatos további információkért lásd: [figyelő Event Grid üzenetkézbesítése](monitor-event-delivery.md).
* A hitelesítési kulcs kapcsolatos további információkért lásd: [Event Grid biztonsági és hitelesítési](security-authentication.md).
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).
