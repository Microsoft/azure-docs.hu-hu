---
title: Alapértékek automatikus biztonsági mentése az Azure-alkalmazás konfigurációs üzleteiből
description: Megtudhatja, hogyan állíthatja be az alkalmazás konfigurációs tárolói közötti kulcs-értékek automatikus biztonsági mentését.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: b3e0bcad7beccc31e1772fbb24ffad7f502b8140
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454243"
---
# <a name="back-up-app-configuration-stores-automatically"></a>Alkalmazás-konfigurációs tárolók automatikus biztonsági mentése

Ebből a cikkből megtudhatja, hogyan állíthatja be az elsődleges Azure-alkalmazás konfigurációs tárolójából származó kulcs-értékek automatikus biztonsági mentését egy másodlagos tárolóba. Az automatikus biztonsági mentés az Azure Event Grid integrációját használja az alkalmazás konfigurációjával. 

Miután beállította az automatikus biztonsági mentést, az alkalmazás konfigurációja közzéteszi az eseményeket, hogy a rendszer a konfigurációs tárolóban lévő kulcs-értékekben történt módosításokat Azure Event Grid. Event Grid számos olyan Azure-szolgáltatást támogat, amelyekről a felhasználók előfizethetnek a kulcs-érték létrehozásakor, frissítésekor vagy törlésekor.

## <a name="overview"></a>Áttekintés

Ebben a cikkben az Azure üzenetsor-tárolót fogja használni a Event Grid eseményeinek fogadására, és a Azure Functions időzítő-triggerét használja a kötegekben lévő események feldolgozásához. 

Amikor egy függvény aktiválódik az események alapján, beolvassa az elsődleges alkalmazás konfigurációs tárolójából módosított kulcsok legújabb értékeit, és ennek megfelelően frissíti a másodlagos tárolót. Ezzel a beállítással több, egyetlen biztonsági mentési művelettel elvégezhető módosítást is összekapcsolhat, ami elkerüli az alkalmazás konfigurációs tárolóinak túlzott kéréseit.

![Az alkalmazás-konfigurációs tároló biztonsági mentésének architektúráját bemutató ábra.](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Az erőforrások kiépítése

Az alkalmazások konfigurációs tárolóinak biztonsági mentésének motivációja az, hogy több konfigurációs áruházat használjon különböző Azure-régiókban az alkalmazás földrajzi rugalmasságának növeléséhez. Ennek eléréséhez az elsődleges és a másodlagos tárolónak különböző Azure-régiókban kell lennie. Az oktatóanyagban létrehozott összes más erőforrás kiépíthető bármely tetszőleges régióban. Ennek az az oka, hogy ha az elsődleges régió nem működik, nem lesz új biztonsági mentés, amíg az elsődleges régió újra elérhetővé nem válik.

Ebben az oktatóanyagban egy másodlagos tárolót fog létrehozni a `centralus` régióban, és minden más erőforrást a `westus` régióban.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)].

## <a name="prerequisites"></a>Előfeltételek 

- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) az Azure-fejlesztési számítási feladattal.

- [.Net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Az oktatóanyaghoz az Azure CLI 2.3.1-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal.

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `<resource_group_name>` helyen.  A `<resource_group_name>` elemet az erőforráscsoport egyedi nevére cserélje le.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Alkalmazás-konfigurációs tárolók létrehozása

Hozza létre az elsődleges és a másodlagos alkalmazás konfigurációs tárolóit különböző régiókban.
Cserélje le a `<primary_appconfig_name>` és `<secondary_appconfig_name>` a nevet a konfigurációs tárolók egyedi neveire. Az egyes tárolók nevének egyedinek kell lennie, mert DNS-névként van használatban.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Hozzon létre egy Storage-fiókot és egy várólistát a Event Grid által közzétett események fogadásához.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Előfizetés az alkalmazás konfigurációs adattár eseményeire

Előfizet az alábbi két eseményre az elsődleges alkalmazás konfigurációs tárolójából:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

A következő parancs létrehoz egy Event Grid-előfizetést a várólistára eljuttatott két eseményhez. A végpont típusa a, a `storagequeue` végpont pedig a várólista-azonosítóra van beállítva. Cserélje le az `<event_subscription_name>` nevet az esemény-előfizetés számára választott névre.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-functions-for-handling-events-from-queue-storage"></a>Függvények létrehozása a várólista-tároló eseményeinek kezelésére

### <a name="set-up-with-ready-to-use-functions"></a>Üzembe helyezés használatra kész függvények használatával

Ebben a cikkben olyan C#-függvényeket fog dolgozni, amelyek a következő tulajdonságokkal rendelkeznek:
- Futásidejű verem .NET Core 3,1
- Azure Functions futtatókörnyezet 3. x verziója
- Az időzítő által aktivált függvény 10 percenként

Annak érdekében, hogy megkönnyítse az adatai biztonsági mentését, [teszteltük és közzétettünk egy olyan függvényt](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) , amelyet a kód módosítása nélkül használhat. Töltse le a Project-fájlokat, és [tegye közzé őket a Visual studióból a saját Azure Function-alkalmazásban](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> A letöltött kódban ne módosítsa a környezeti változókat. A szükséges Alkalmazásbeállítások a következő szakaszban hozhatók létre.
>

### <a name="build-your-own-function"></a>Saját függvény létrehozása

Ha a korábban megadott mintakód nem felel meg a követelményeknek, saját függvényt is létrehozhat. A biztonsági mentés befejezéséhez a függvénynek képesnek kell lennie a következő feladatok végrehajtására:
- Rendszeresen olvassa el a várólista tartalmát, és ellenőrizze, hogy tartalmaz-e értesítéseket a Event Gridról. A megvalósítás részleteiért tekintse meg a [Storage ÜZENETSOR SDK](../storage/queues/storage-quickstart-queues-dotnet.md) -t.
- Ha a várólista [Event Grid származó esemény-értesítéseket](./concept-app-configuration-event.md#event-schema)tartalmaz, bontsa `<key, label>` ki az esemény üzeneteiből származó összes egyedi információt. A kulcs és a címke kombinációja az elsődleges tároló kulcs-érték változásainak egyedi azonosítója.
- Az elsődleges tároló összes beállításának olvasása. Csak azokat a beállításokat frissítse a másodlagos tárolóban, amelyek megfelelő eseménnyel rendelkeznek a várólistában. Törölje az összes olyan beállítást a másodlagos tárolóból, amely szerepel a várólistában, de az elsődleges tárolóban nem. Az [app CONFIGURATION SDK](https://github.com/Azure/AppConfiguration#sdks) használatával programozott módon érheti el a konfigurációs tárolókat.
- Üzenetek törlése a sorból, ha a feldolgozás során nem történt kivétel.
- A hibakezelés megvalósítása igény szerint. Tekintse át az előző kódrészletet, hogy lásson néhány olyan gyakori kivételt, amelyet érdemes lehet kezelni.

A függvények létrehozásáról további információt a következő témakörben talál: a [függvény létrehozása az Azure-ban, amely időzítővel aktiválódik](../azure-functions/functions-create-scheduled-function.md) , és [Azure functions a Visual Studióval](../azure-functions/functions-develop-vs.md).


> [!IMPORTANT]
> A legjobb döntés szerint válassza ki az időzítő ütemtervet az elsődleges konfigurációs tároló módosításának gyakorisága alapján. A függvény futtatása túl gyakran előfordulhat, hogy a tárolási kérelmeket a rendszer leállítja.
>


## <a name="create-function-app-settings"></a>Function alkalmazás beállításainak létrehozása

Ha egy általunk megadott függvényt használ, a következő Alkalmazásbeállítások szükségesek a Function alkalmazásban:
- `PrimaryStoreEndpoint`: Az elsődleges alkalmazás konfigurációs tárolójához tartozó végpont. Például: `https://{primary_appconfig_name}.azconfig.io`.
- `SecondaryStoreEndpoint`: Végpont a másodlagos alkalmazás konfigurációs tárolójához. Például: `https://{secondary_appconfig_name}.azconfig.io`.
- `StorageQueueUri`: Várólista URI-ja. Például: `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`.

A következő parancs létrehozza a szükséges Alkalmazásbeállítások alkalmazását a Function alkalmazásban. Cserélje le a helyére `<function_app_name>` a Function alkalmazás nevét.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>Hozzáférés biztosítása a Function alkalmazás felügyelt identitásához

A következő parancs vagy a [Azure Portal](../app-service/overview-managed-identity.md#add-a-system-assigned-identity) használatával adja hozzá a rendszerhez rendelt felügyelt identitást a Function alkalmazáshoz.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> A szükséges erőforrás-létrehozási és-szerepkör-kezelés végrehajtásához a fióknak `Owner` a megfelelő hatókörben (az előfizetésben vagy az erőforráscsoportban) engedélyekkel kell rendelkeznie. Ha segítségre van szüksége a szerepkör-hozzárendeléshez, Ismerje meg, [Hogyan rendelhet hozzá Azure-szerepköröket a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).

Az alábbi parancsokkal vagy a [Azure Portal](./howto-integrate-azure-managed-service-identity.md#grant-access-to-app-configuration) használatával biztosíthatja, hogy a Function app felügyelt identitása hozzáférjen az alkalmazás konfigurációs tárolói számára. A következő szerepkörök használata:
- Rendelje hozzá a `App Configuration Data Reader` szerepkört az elsődleges alkalmazás konfigurációs tárolójához.
- Rendelje hozzá a `App Configuration Data Owner` szerepkört a másodlagos alkalmazás konfigurációs tárolójához.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

A következő parancs vagy a [Azure Portal](../storage/common/storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal) használatával biztosíthatja a Function app-hozzáférésének felügyelt identitását a várólistához. Rendelje hozzá a `Storage Queue Data Contributor` szerepkört a várólistában.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Alkalmazás-konfigurációs esemény elindítása

Az összes működésének teszteléséhez létrehozhat, frissíthet vagy törölhet egy kulcs-értéket az elsődleges tárolóból. Ezt a változást a másodlagos tárolóban automatikusan meg kell tekinteni néhány másodperccel az időzítő-eseményindítók Azure Functions után.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Kiváltotta az eseményt. Néhány pillanat múlva a Event Grid elküldi az eseményről szóló értesítést a várólistára. *A függvény következő ütemezett futtatása után* tekintse meg a másodlagos tároló konfigurációs beállításait, és ellenőrizze, hogy tartalmazza-e az elsődleges tároló frissített kulcs-értékét.

> [!NOTE]
> A [függvényt](../azure-functions/functions-manually-run-non-http.md) a tesztelés és a hibaelhárítás során manuálisan is aktiválhatja anélkül, hogy az ütemezett időzítő-triggerre kellene várnia.

Miután ellenőrizte, hogy a biztonsági mentési függvény sikeresen lefutott, láthatja, hogy a kulcs már szerepel a másodlagos tárolóban.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem látja az új beállítást a másodlagos tárolóban:

- Győződjön meg arról, hogy a biztonsági mentési függvény aktiválva lett, *miután* létrehozta a beállítást az elsődleges tárolóban.
- Lehetséges, hogy Event Grid nem tudta időben elküldeni az esemény-értesítést a várólistára. Ellenőrizze, hogy az üzenetsor továbbra is tartalmazza-e az eseményről szóló értesítést az elsődleges tárolóból. Ha igen, aktiválja újra a biztonsági mentési függvényt.
- Az esetleges hibákkal és figyelmeztetésekkel kapcsolatban keresse meg [Azure functions naplókat](../azure-functions/functions-create-scheduled-function.md#test-the-function) .
- A [Azure Portal](../azure-functions/functions-how-to-use-azure-function-app-settings.md#get-started-in-the-azure-portal) használatával győződjön meg arról, hogy az Azure Function alkalmazás helyes értékeket tartalmaz a Azure functions által beolvasni próbált Alkalmazásbeállítások számára.
- Az [Azure Application Insights](../azure-functions/functions-monitoring.md?tabs=cmd)használatával beállíthatja a Azure functions figyelését és riasztását is. 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy ezzel az alkalmazás-konfigurációval és esemény-előfizetéssel folytatja a munkát, ne törölje a cikkben létrehozott erőforrásokat. Ha nem folytatja a műveletet, a következő parancs használatával törölheti a cikkben létrehozott erőforrásokat.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan állíthatja be a kulcs-értékek automatikus biztonsági mentését, további információkat tudhat meg arról, hogyan növelheti az alkalmazás földrajzi rugalmasságát:

- [Rugalmasság és vészhelyreállítás](concept-disaster-recovery.md)
