---
title: 'Rövid útmutató: Többtárolós alkalmazás létrehozása'
description: A többtárolós alkalmazások Azure App Service első többtárolós alkalmazás üzembe helyezéssel.
keywords: azure app service, webalkalmazás, linux, docker, compose, többtárolós, többtárolós, webalkalmazás tárolókhoz, több tároló, tároló, wordpress, azure db for mysql, éles adatbázis tárolók használatával
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 0ea55c36c239b5ecdb51ef3dc7a3ff762718bd1e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768990"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Többtárolós (előzetes verziójú) alkalmazás létrehozása Docker Compose-konfigurációval

> [!NOTE]
> A többtárolós verzió előzetes verzióban érhető el.

A [Web App for Containers](overview.md#app-service-on-linux) segítségével rugalmasan használhatók a Docker-rendszerképek. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy többtárolós alkalmazást (előzetes verzió) Web App for Containers a Cloud Shell Docker Compose-konfigurációval. [](../cloud-shell/overview.md)

![Minta többtárolós alkalmazás a Web Apps for Containersben][1]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Ehhez a cikkhez az Azure CLI 2.0.32-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="download-the-sample"></a>A minta letöltése

Ebben a rövid útmutatóban a [Docker](https://docs.docker.com/compose/wordpress/#define-the-project) Compose-fájlját fogja használni. A konfigurációs fájl az [Azure-minták](https://github.com/Azure-Samples/multicontainerwordpress) között található.

[!code-yml[Main](../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

A Cloud Shellben hozzon létre egy quickstart könyvtárat, és lépjen a könyvtárba.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Ezután futtassa a következő parancsot a mintaalkalmazás-adattár a quickstart könyvtárba való klónozásához. Ezután lépjen a `multicontainerwordpress` könyvtárba.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

A Cloud Shell hozzon létre egy erőforráscsoportot az [`az group create`](/cli/azure/group#az_group_create) paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *USA déli középső régiója* helyen. A Linuxon futó, **Standard** szintű App Service-t támogató összes hely megtekintéséhez futtassa az [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice#az_appservice_list_locations) parancsot.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Az erőforráscsoportot és az erőforrásokat általában a közelében található régiókban hozhatja létre.

A parancs befejeződésekor a JSON-kimenet megjeleníti az erőforráscsoport tulajdonságait.

## <a name="create-an-azure-app-service-plan"></a>Azure App Service-csomag létrehozása

A Cloud Shell hozzon létre egy App Service-tervet az erőforráscsoportban az [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) paranccsal.

Az alábbi példa egy `myAppServicePlan` nevű App Service-csomag létrehozását mutatja be a **Standard** tarifacsomagban (`--sku S1`) és Linux-tárolóban (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Az App Service-csomag létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

<pre>
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

## <a name="create-a-docker-compose-app"></a>Docker Compose-alkalmazás létrehozása

> [!NOTE]
> Az Azure-App Services Docker Compose jelenleg legfeljebb 4000 karakterből állhat.

A Cloud Shell-terminálban hozzon létre egy többtárolós [webalkalmazást](overview.md#app-service-on-linux) az `myAppServicePlan` App Service-csomagban az [az webapp create](/cli/azure/webapp#az_webapp_create) paranccsal. Ne felejtse el lecserélni a helyére az alkalmazás _\<app_name>_ egyedi nevét (érvényes karakterek: `a-z` , és `0-9` `-` ).

```azurecli
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Keresse meg az üzembe helyezett alkalmazást a következő helyen: `http://<app_name>.azurewebsites.net`. Az alkalmazás betöltése eltarthat néhány percig. Ha hibaüzenet jelenik meg, várjon néhány percet, majd frissítse a böngészőt.

![Minta többtárolós alkalmazás a Web Apps for Containersben][1]

**Gratulálunk,** létrehozott egy többtárolós alkalmazást a Web App for Containers.

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Többtárolós WordPress-alkalmazás](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Egyéni tároló konfigurálása](configure-custom-container.md)

<!--Image references-->
[1]: media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
