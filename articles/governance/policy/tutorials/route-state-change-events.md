---
title: 'Oktatóanyag: az Azure CLI-vel való Event Grid az útválasztási szabályzat állapotának változási eseményei'
description: Ebben az oktatóanyagban úgy konfigurálja Event Grid, hogy figyelje a házirend állapotának változási eseményeit, és meghívjon egy webhookot.
ms.date: 03/29/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1fe87e4fd3349df7d8f5d57b2b2d95f95ed3fba8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735539"
---
# <a name="tutorial-route-policy-state-change-events-to-event-grid-with-azure-cli"></a>Oktatóanyag: az Azure CLI-vel való Event Grid az útválasztási szabályzat állapotának változási eseményei

Ebből a cikkből megtudhatja, hogyan állíthat be Azure Policy esemény-előfizetéseket a házirend-állapotváltozás eseményeinek webes végpontra való küldéséhez. Azure Policy a felhasználók előfizethetnek az erőforrásokra vonatkozó házirend-állapot módosításakor kibocsátott eseményekre. Ezek az események indíthatnak webhookokat, [Azure Functionseket](../../../azure-functions/index.yml), [Azure Storage-várólistákat](../../../storage/queues/index.yml)vagy bármely más, a [Azure Event Grid](../../../event-grid/index.yml)által támogatott eseménykezelőt. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek eseményadatokat dolgoznak fel és műveleteket hajtanak végre. Az oktatóanyag leegyszerűsítése érdekében azonban elküldheti az eseményeket egy olyan webalkalmazásnak, amely összegyűjti és megjeleníti az üzeneteket.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.76 vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

- Még ha korábban is használta Azure Policy vagy Event Grid, regisztrálja újra a megfelelő erőforrás-szolgáltatókat:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Provider register: Register the Azure Event Grid provider
  az provider register --namespace Microsoft.EventGrid
  ```

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Event Grid-témakörök Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. 

A következő példában létrehozunk egy nevű erőforráscsoportot `<resource_group_name>` a _westus_ helyen. A `<resource_group_name>` elemet az erőforráscsoport egyedi nevére cserélje le.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az group create --name <resource_group_name> --location westus
```

## <a name="create-an-event-grid-system-topic"></a>Event Grid rendszer-témakör létrehozása

Most, hogy van egy erőforráscsoport, hozzunk létre egy [rendszertémakört](../../../event-grid/system-topics.md). A Event Grid rendszertémaköre egy vagy több olyan eseményt jelent, amelyet az Azure-szolgáltatások, például a Azure Policy és az Azure Event Hubs tesznek közzé. Ez a rendszertémakör a `Microsoft.PolicyInsights.PolicyStates` Azure Policy állapot változásaihoz tartozó témakör típusát használja. Cserélje le a `<SubscriptionID>` **hatókör** -paramétert az előfizetés azonosítójával és az `<resource_group_name>` **Erőforrás-csoport** paraméterben a korábban létrehozott erőforráscsoporthoz.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az eventgrid system-topic create --name PolicyStateChanges --location global --topic-type Microsoft.PolicyInsights.PolicyStates --source "/subscriptions/<SubscriptionID>" --resource-group "<resource_group_name>"
```

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A témakörre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. A végpont általában az eseményadatok alapján hajt végre műveleteket. Az útmutató egyszerűsítése érdekében egy olyan [előre létrehozott webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) helyezünk üzembe, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

A `<your-site-name>` elemet a webalkalmazás egyedi nevére cserélje le. A webalkalmazás nevének egyedinek kell lennie, mert a DNS-bejegyzés része.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=<your-site-name> hostingPlanName=viewerhost
```

Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

A helynek megjelenített üzenetek nélkül kell megjelennie.

## <a name="subscribe-to-the-system-topic"></a>Előfizetés a System témakörre

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni, és hová szeretné küldeni azokat. Az alábbi példa előfizet a létrehozott rendszertémakörre, és átadja az URL-címet a webalkalmazásból végpontként az esemény-értesítések fogadásához. Az `<event_subscription_name>` elemet cserélje le az esemény-előfizetéshez választott névre. A `<resource_group_name>` és `<your-site-name>` elemnél a korábban létrehozott értékeket adja meg.

A webalkalmazás végpontjának az `/api/updates/` utótagot kell tartalmaznia.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Create the subscription
az eventgrid system-topic event-subscription create \
  --name <event_subscription_name> \
  --resource-group <resource_group_name> \
  --system-topic-name PolicyStateChanges \
  --endpoint https://<your-site-name>.azurewebsites.net/api/updates
```

Tekints meg újra a webalkalmazást, ahol láthatja, hogy az fogadta az előfizetés érvényesítési eseményét. Az eseményadatok kibontásához kattintson a szem ikonra. Az Event Grid elküldi az érvényesítési eseményt, így a végpont megerősítheti, hogy eseményadatokat akar kapni. A webalkalmazás az előfizetés érvényesítéséhez szükséges kódot tartalmaz.

:::image type="content" source="../media/route-state-change-events/view-subscription-event.png" alt-text="Képernyőkép a Event Grid előfizetés-ellenőrzési eseményről az előre elkészített webes alkalmazásban.":::

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és hozzárendeli a **kötelező címkét az erőforráscsoportok** definíciójában. Ez a házirend-definíció azokat az erőforráscsoportokat azonosítja, amelyekből hiányzik a szabályzat-hozzárendelés során konfigurált címke.

A következő parancs futtatásával hozzon létre egy szabályzat-hozzárendelési hatókört a létrehozott erőforráscsoporthoz az Event Grid-témakör tárolásához:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az policy assignment create --name 'requiredtags-events' --display-name 'Require tag on RG' --scope '<ResourceGroupScope>' --policy '<policy definition ID>' --params '{ "tagName": { "value": "EventTest" } }'
```

Az előző parancs a következő információkat használja:

- **Name** – A hozzárendelés tényleges neve. Ebben a példában a _rendszer requiredtags-eseményeket_ használt.
- **DisplayName** – A szabályzat-hozzárendelés megjelenített neve. Ebben az esetben a _kötelező címkét használja a RG-on_.
- **Hatókör** – A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ez egyetlen előfizetéstől teljes erőforráscsoportokig terjedhet. Győződjön meg arról, hogy a &lt;scope&gt; helyett az erőforráscsoport neve szerepel. Az erőforráscsoport-hatókör formátuma a következő: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>` .
- **Szabályzat** – A szabályzatdefiníció azonosítója, amely alapján létre fogja hozni a hozzárendelést. Ebben az esetben ez a szabályzat-definíció AZONOSÍTÓját _igényli az erőforráscsoportok címkéjén_. A szabályzatdefiníció azonosítójának lekéréséhez futtassa ezt a parancsot: `az policy definition list --query "[?displayName=='Require a tag on resource groups']"`

A szabályzat-hozzárendelés létrehozása után várjon, amíg a **Microsoft. PolicyInsights. PolicyStateCreated** esemény értesítése megjelenik a webalkalmazásban. Az általunk létrehozott erőforráscsoport a nem `data.complianceState` _megfelelő_ értéket jeleníti meg a kezdéshez.

:::image type="content" source="../media/route-state-change-events/view-policystatecreated-event.png" alt-text="Képernyőkép az előkészített webalkalmazásban az erőforráscsoport számára létrehozott Event Grid előfizetés-szabályzat állapota eseményről.":::

> [!NOTE]
> Ha az erőforráscsoport más szabályzat-hozzárendeléseket is örököl az előfizetés vagy a felügyeleti csoport hierarchiájában, akkor az egyes események is megjelennek. A tulajdonság kiértékelésével erősítse meg, hogy az esemény a hozzárendelés ebben az oktatóanyagban `data.policyDefinitionId` .

## <a name="trigger-a-change-on-the-resource-group"></a>Az erőforráscsoport módosításának elindítása

Ahhoz, hogy az erőforráscsoport megfelelő legyen, a **EventTest** nevű címkét kötelező megadni. Adja hozzá a címkét az erőforráscsoporthoz az alábbi paranccsal, `<SubscriptionID>` majd cserélje le az előfizetési azonosítót és az `<ResourceGroup>` erőforráscsoport nevét:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az tag create --resource-id '/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>' --tags EventTest=true
```

Miután hozzáadta a kötelező címkét az erőforráscsoporthoz, várjon, amíg a **Microsoft. PolicyInsights. PolicyStateChanged** esemény értesítése megjelenik a webalkalmazásban. Bontsa ki az eseményt, és az `data.complianceState` érték most már _megfelelőnek_ jelenik meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát ezzel a webalkalmazással, és Azure Policy esemény-előfizetést, ne törölje a cikkben létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő parancs használatával törölheti a cikkben létrehozott erőforrásokat.

A `<resource_group_name>` elemet cserélje le a fent létrehozott erőforráscsoportra.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan hozhat létre témaköröket és esemény-előfizetéseket a Azure Policyhoz, további információ a szabályzatok állapotának változási eseményeiről, illetve arról, hogy milyen Event Grid segíthet:

- [Azure Policy állapot-változási eseményekre való reagálás](../concepts/event-overview.md)
- [Event Grid Azure Policy sémájának részletei](../../../event-grid/event-schema-policy.md)
- [Bevezetés az Event Grid használatába](../../../event-grid/overview.md)