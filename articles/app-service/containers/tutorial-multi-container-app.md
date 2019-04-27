---
title: Többtárolós alkalmazás létrehozása a Web App for containers szolgáltatásban – Azure App Service-ben
description: Megtudhatja, hogyan használhat több tárolót az Azure-ban Docker Compose és Kubernetes konfigurációs fájlokkal, egy WordPress- és MySQL-alkalmazással.
keywords: azure app service, webalkalmazás, linux, docker, összeállítás, többtárolós, több tárolós, webalkalmazás tárolókhoz, több tároló, tároló, kubernetes, wordpress, mysql-hez készült azure db, éles adatbázis tárolókkal
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: cd7edb576264ac8bb8a076bbb4b2970579056f13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60767907"
---
# <a name="tutorial-create-a-multi-container-preview-app-in-web-app-for-containers"></a>Oktatóanyag: Többtárolós (előzetes verziójú) alkalmazás létrehozása a Web App for Containersben

A [Web App for Containers](app-service-linux-intro.md) segítségével rugalmasan használhatók a Docker-rendszerképek. Ezen oktatóanyagból megtudhatja, hogyan hozhat létre egy többtárolós alkalmazást a WordPress és a MySQL használatával. Ezt az oktatóanyagot a Cloud Shellben végezzük el, a parancsok azonban helyileg is futtathatók az [Azure CLI](/cli/azure/install-azure-cli) parancssori eszköz (2.0.32-es vagy újabb verzió) használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Docker Compose-konfiguráció átalakítása a Web App for Containers használatára
> * Kubernetes-konfiguráció átalakítása a Web App for Containers használatára
> * Többtárolós alkalmazás üzembe helyezése az Azure-ban
> * Alkalmazásbeállítások hozzáadása
> * Állandó tárolók használata a tárolókban
> * Csatlakozás az Azure Database for MySQL-hez
> * Hibák elhárítása

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez tapasztalattal kell rendelkeznie a [Docker Compose](https://docs.docker.com/compose/) vagy a [Kubernetes](https://kubernetes.io/) használatában.

## <a name="download-the-sample"></a>A minta letöltése

Ebben az oktatóanyagban a [Docker](https://docs.docker.com/compose/wordpress/#define-the-project) Compose-fájlját fogja használni, de módosítani fogja azt, hogy tartalmazza az Azure Database for MySQL szolgáltatást, állandó tárolókat és a Redist. A konfigurációs fájl az [Azure-minták](https://github.com/Azure-Samples/multicontainerwordpress) között található.

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

A támogatott konfigurációs lehetőségekről [Docker Compose beállítások](configure-custom-container.md#docker-compose-options).

A Cloud Shellben hozzon létre egy tutorial könyvtárat, és lépjen a könyvtárba.

```bash
mkdir tutorial

cd tutorial
```

Ezután futtassa a következő parancsot a mintaalkalmazás-adattár a tutorial könyvtárba való klónozásához. Ezután lépjen a `multicontainerwordpress` könyvtárba.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

A Cloud Shellben hozzon létre egy erőforráscsoportot az [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *USA déli középső régiója* helyen. A Linuxon futó, **Standard** szintű App Service-t támogató összes hely megtekintéséhez futtassa az [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) parancsot.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Az erőforráscsoportot és az erőforrásokat általában a közelében található régiókban hozhatja létre.

A parancs befejeződésekor a JSON-kimenet megjeleníti az erőforráscsoport tulajdonságait.

## <a name="create-an-azure-app-service-plan"></a>Azure App Service-csomag létrehozása

A Cloud Shellben hozzon létre egy App Service-csomagot az erőforráscsoportban az [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) paranccsal.

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

Az alábbi példa egy `myAppServicePlan` nevű App Service-csomag létrehozását mutatja be a **Standard** tarifacsomagban (`--sku S1`) és Linux-tárolóban (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Az App Service-csomag létrehozása után a Cloud Shell az alábbi példához hasonló információkat jelenít meg:

```json
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
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>A Docker Compose használata a WordPress-szel és MySQL-tárolókkal

## <a name="create-a-docker-compose-app"></a>Docker Compose-alkalmazás létrehozása

A Cloud Shellben hozzon létre egy többtárolós [webalkalmazást](app-service-linux-intro.md) az `myAppServicePlan` App Service-csomagban az [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) paranccsal. Ne felejtse el kicserélni az  _\<alkalmazás-neve >_ egy egyedi névre.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

A webalkalmazás létrehozása után a Cloud Shell az alábbi példához hasonló eredményeket jelenít meg:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Keresse meg az üzembe helyezett alkalmazást a következő helyen: `http://<app-name>.azurewebsites.net`. Az alkalmazás betöltése eltarthat néhány percig. Ha hibaüzenet jelenik meg, várjon néhány percet, majd frissítse a böngészőt. Ha problémába ütközik, és hibakeresést szeretne végezni, tekintse át a[tárolónaplókat](#find-docker-container-logs).

![Minta többtárolós alkalmazás a Web Apps for Containersben][1]

**Gratulálunk!** Létrehozott egy többtárolós alkalmazást a Web App for Containersben. A következő lépésben konfigurálja az alkalmazást az Azure Database for MySQL használatára. Egyelőre még ne telepítse a WordPresst.

## <a name="connect-to-production-database"></a>Kapcsolódás éles adatbázishoz

Éles környezetben nem ajánlott az adatbázistárolók használata. A helyi tárolók nem skálázhatók. Ehelyett használja az Azure Database for MySQL-t, amely skálázható.

### <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz

Hozzon létre egy Azure Database for MySQL-kiszolgálót az [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) paranccsal.

A következő parancsban cserélje le a MySQL-kiszolgáló nevét, ahol a  _&lt;mysql-kiszolgáló-neve >_ helyőrző (érvényes karakterek: `a-z`, `0-9`, és `-`). Ez a név része a MySQL-kiszolgáló állomásnevének (`<mysql-server-name>.database.windows.net`), és globálisan egyedinek kell lennie.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

A kiszolgáló létrehozása eltarthat néhány percig. A MySQL-kiszolgáló létrehozása után a Cloud Shell az alábbi példához hasonló információkat jelenít meg:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql-server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>",
  "location": "southcentralus",
  "name": "<mysql-server-name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Kiszolgáló tűzfalának konfigurálása

Az [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) parancs használatával hozzon létre egy tűzfalszabályt a MySQL-kiszolgáló számára az ügyfélkapcsolatok engedélyezéséhez. Ha a kezdő IP-cím és a záró IP-cím is 0.0.0.0 értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Még szigorúbb tűzfalszabályt is megadhat, ha [csak azokat a kimenő IP-címeket használja, amelyeket alkalmazása használ](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>A WordPress-adatbázis létrehozása

```azurecli-interactive
az mysql db create --resource-group myResourceGroup --server-name <mysql-server-name> --name wordpress
```

Az adatbázis létrehozása után a Cloud Shell az alábbi példához hasonló információkat jelenít meg:

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="configure-database-variables-in-wordpress"></a>Adatbázis-változók konfigurálása a WordPressben

A WordPress és az új MySQL-kiszolgáló csatlakoztatásához konfigurálni kell néhány WordPress-specifikus környezeti változót, például a `MYSQL_SSL_CA` által meghatározott SSL CA elérési utat. Az alábbi [egyéni rendszerkép](https://docs.microsoft.com/azure/app-service/containers/tutorial-multi-container-app#use-a-custom-image-for-mysql-ssl-and-other-configurations) a [DigiCert](https://www.digicert.com/) [Baltimore CyberTrust legfelső szintű tanúsítványát](https://www.digicert.com/digicert-root-certificates.htm) tartalmazza.

A módosítások elvégzéséhez használja az [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud Shellben. Az alkalmazásbeállítások megkülönböztetik a kis-és nagybetűket, és szóközzel vannak elválasztva.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WORDPRESS_DB_HOST="<mysql-server-name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql-server-name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Az alkalmazásbeállítás létrehozása után a Cloud Shell az alábbi példához hasonló információkat jelenít meg:

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql-server-name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql-server-name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  },
  {
    "name": "MYSQL_SSL_CA",
    "slotSetting": false,
    "value": "BaltimoreCyberTrustroot.crt.pem"
  }
]
```

A környezeti változók további információkért lásd: [környezeti változók konfigurálása](configure-custom-container.md#configure-environment-variables).

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>Egyéni MySQL SSL-rendszerkép és egyéb konfigurációk használata

Alapértelmezés szerint az Azure Database for MySQL az SSL-t használja. A WordPress esetében további konfigurálás szükséges az SSL és a MySQL együttes használatához. A WordPress „hivatalos rendszerképe” nem tartalmazza a további konfigurációt, de a felhasználók kényelme érdekében elérhető egy [egyéni rendszerkép](https://hub.docker.com/r/microsoft/multicontainerwordpress/builds/) is. A gyakorlatban a kívánt módosításokat a saját rendszerképéhez adná hozzá.

Az egyéni rendszerkép a WordPress „hivatalos rendszerképén” alapul, amely a [Docker Hubból](https://hub.docker.com/_/wordpress/) érhető el. Az egyéni rendszerkép a következő módosításokat tartalmazza az Azure Database for MySQL-hez:

* [Tartalmazza a Baltimore Cyber Trust főtanúsítvány-fájlt az SSL és a MySQL összekapcsolásához.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61)
* [A WordPress wp-config.php fájljában található MySQL SSL hitelesítésszolgáltató-tanúsítvány alkalmazásbeállítását használja.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163)
* [Hozzáadja a WordPress definiálását a MySQL SSL-hez szükséges MYSQL_CLIENT_FLAGS tulajdonsághoz.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164)

Az egyéni rendszerkép a következő módosításokat tartalmazza a Redisre vonatkozóan (ezt egy későbbi szakasz fogja használni):

* [PHP-bővítmény hozzáadása a Redis v4.0.2-höz.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [A fájlkibontáshoz szükséges kicsomagolás funkció hozzáadása.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [A Redis Object Cache 1.3.8 WordPress beépülő modul hozzáadása.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [A WordPress wp-config.php fájljában a Redis hosztnevére vonatkozó alkalmazásbeállítást használja.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Az egyéni rendszerkép használatához frissíteni kell a docker-compose-wordpress.yml fájlt. A Cloud Shellben írja be a `nano docker-compose-wordpress.yml` parancsot a nano szövegszerkesztő megnyitásához. Módosítsa az `image: microsoft/multicontainerwordpress` elemet a következőre: `image: wordpress`. Az adatbázistárolóra már nincs szükség. Távolítsa el a konfigurációs fájlból a `db`, `environment`, `depends_on` és `volumes` szakaszokat. A fájlnak a következő kódhoz hasonlóan kell kinéznie:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

Mentse a módosításokat, és lépjen ki a nanóból. A mentéshez a `^O`, a kilépéshez a `^X` parancsot használja.

### <a name="update-app-with-new-configuration"></a>Az alkalmazás frissítése egy új konfigurációval

A Cloud Shellben konfiguráljon újra egy többtárolós [webalkalmazást](app-service-linux-intro.md) az [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) paranccsal. Ne felejtse el kicserélni az  _\<alkalmazás-neve >_ a korábban létrehozott webalkalmazás nevére.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

A webalkalmazás újrakonfigurálása után a Cloud Shell az alábbi példához hasonló információkat jelenít meg:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
```

### <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Keresse meg az üzembe helyezett alkalmazást a következő helyen: `http://<app-name>.azurewebsites.net`. Az alkalmazás mostantól az Azure Database for MySQL-t használja.

![Minta többtárolós alkalmazás a Web App for Containers szolgáltatásban][1]

## <a name="add-persistent-storage"></a>Állandó tároló hozzáadása

A többtárolós alkalmazás mostantól a Web App for Containersben fut. Azonban ha most telepíti a WordPresst, majd később újraindítja az alkalmazást, azzal fog szembesülni, hogy a WordPress-telepítés eltűnt. Ennek az az oka, hogy a Docker Compose konfigurációja jelenleg egy, a tárolón belüli tárhelyre mutat. A tárolóba telepített fájlok nem maradnak meg az alkalmazás újraindítását követően. Ebben a szakaszban fog [adja hozzá az állandó tárolóból](configure-custom-container.md#use-persistent-shared-storage) a WordPress-tárolóba.

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Az állandó tároló használatához engedélyezni kell ezt a beállítást az App Service-ben. A módosítás elvégzéséhez használja az [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud Shellben. Az alkalmazásbeállítások megkülönböztetik a kis-és nagybetűket, és szóközzel vannak elválasztva.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Az alkalmazásbeállítás létrehozása után a Cloud Shell az alábbi példához hasonló információkat jelenít meg:

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="modify-configuration-file"></a>Konfigurációs fájl módosítása

A Cloud Shellben írja be a `nano docker-compose-wordpress.yml` parancsot a nano szövegszerkesztő megnyitásához.

A `volumes` beállítás leképezi a fájlrendszert egy könyvtárba a tárolóban. A `${WEBAPP_STORAGE_HOME}` egy környezeti változó az App Service szolgáltatásban, amely az alkalmazás állandó tárolójára mutat. Ezt a környezeti változót használhatja a kötetek beállításánál ahhoz, hogy a WordPress fájljai ne a tárolóba, hanem egy állandó tárolóba legyenek telepítve. Végezze el a fájlban a következő módosításokat:

A `wordpress` szakaszban adjon meg egy `volumes` beállítást, hogy a kód az alábbihoz hasonlóan nézzen ki:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>Az alkalmazás frissítése egy új konfigurációval

A Cloud Shellben konfiguráljon újra egy többtárolós [webalkalmazást](app-service-linux-intro.md) az [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) paranccsal. Ne felejtse el kicserélni az  _\<alkalmazás-neve >_ egy egyedi névre.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

A parancs a futtatása után a következő példához hasonló kimenetet ad vissza:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Keresse meg az üzembe helyezett alkalmazást a következő helyen: `http://<app-name>.azurewebsites.net`.

A WordPress-tároló mostantól az Azure Database for MySQL-t és az állandó tárolót használja.

## <a name="add-redis-container"></a>Redis-tároló hozzáadása

 A WordPress „hivatalos rendszerképe” nem tartalmazza a Redishez tartozó függőségeket. Ezeket a függőségeket, valamint és Redis és a WordPress együttes használatához szükséges további konfigurációkat egy [egyéni rendszerkép](https://github.com/Azure-Samples/multicontainerwordpress) tartalmazza. A gyakorlatban a kívánt módosításokat a saját rendszerképéhez adná hozzá.

Az egyéni rendszerkép a WordPress „hivatalos rendszerképén” alapul, amely a [Docker Hubból](https://hub.docker.com/_/wordpress/) érhető el. Az egyéni rendszerkép a következő módosításokat tartalmazza a Redisre vonatkozóan:

* [PHP-bővítmény hozzáadása a Redis v4.0.2-höz.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [A fájlkibontáshoz szükséges kicsomagolás funkció hozzáadása.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [A Redis Object Cache 1.3.8 WordPress beépülő modul hozzáadása.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [A WordPress wp-config.php fájljában a Redis hosztnevére vonatkozó alkalmazásbeállítást használja.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Adja hozzá a Redis-tárolót a konfigurációs fájl végéhez, hogy a fájl az alábbi példához hasonlóan néz ki:

[!code-yml[Main](../../../azure-app-service-multi-container/compose-wordpress.yml)]

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

A Redis használatához engedélyezni kell ezt a `WP_REDIS_HOST` beállítást az App Service-ben. Ez a beállítás *kötelező* ahhoz, hogy a WordPress kommunikálni tudjon a Redis-hoszttal. A módosítás elvégzéséhez használja az [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud Shellben. Az alkalmazásbeállítások megkülönböztetik a kis-és nagybetűket, és szóközzel vannak elválasztva.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WP_REDIS_HOST="redis"
```

Az alkalmazásbeállítás létrehozása után a Cloud Shell az alábbi példához hasonló információkat jelenít meg:

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql-server-name>"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
```

### <a name="update-app-with-new-configuration"></a>Az alkalmazás frissítése egy új konfigurációval

A Cloud Shellben konfiguráljon újra egy többtárolós [webalkalmazást](app-service-linux-intro.md) az [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) paranccsal. Ne felejtse el kicserélni az  _\<alkalmazás-neve >_ egy egyedi névre.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

A parancs a futtatása után a következő példához hasonló kimenetet ad vissza:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Keresse meg az üzembe helyezett alkalmazást a következő helyen: `http://<app-name>.azurewebsites.net`.

Végezze el a lépéseket, és telepítse a WordPresst.

### <a name="connect-wordpress-to-redis"></a>A WordPress csatlakoztatása a Redishez

Jelentkezzen be rendszergazdaként a WordPressbe. A bal oldali navigációs menüben válassza a **Beépülő modulok**, majd a **Telepített beépülő modulok** lehetőséget.

![WordPress beépülő modulok kiválasztása][2]

Összes beépülő modul megjelenítése

A beépülő modulok oldalon keresse meg a **Redis Object Cache** elemet, majd kattintson az **Aktiválás** lehetőségre.

![A Redis aktiválása][3]

Kattintson a **Settings** (Beállítások) lehetőségre.

![Kattintson a Beállítások lehetőségre][4]

Kattintson az **Object Cache engedélyezése** gombra.

![Kattintson az „Object Cache engedélyezése” gombra][5]

A WordPress csatlakozik a Redis-kiszolgálóhoz. A kapcsolat **állapota** ugyanezen az oldalon jelenik meg.

![A WordPress csatlakozik a Redis-kiszolgálóhoz. A kapcsolat **állapota** ugyanezen az oldalon jelenik meg.][6]

**Gratulálunk**, csatlakoztatta a WordPresst a Redishez. Az éles környezetbe helyezésre kész alkalmazás mostantól az **Azure Database for MySQL-t, az állandó tárolót és a Redist** használja. Így horizontálisan felskálázhatja több példányra az App Service-csomagot.

## <a name="use-a-kubernetes-configuration-optional"></a>Kubernetes-konfiguráció használata (opcionális)

Ez a szakasz azt ismerteti, hogyan helyezhető üzembe több tároló egy Kubernetes-konfiguráció használatával. Ehhez először el kell végeznie a korábbi lépéseket egy [erőforráscsoport](#create-a-resource-group) és egy [App Service-csomag](#create-an-azure-app-service-plan) létrehozásához. Mivel a legtöbb lépés nagyon hasonlít a Compose szakasz lépéseihez, a két útmutató egy kombinált konfigurációs fájlt használ.

### <a name="kubernetes-configuration-file"></a>Kubernetes konfigurációs fájl

Az oktatóanyag ezen részéhez a *kubernetes-wordpress.yml* fájlt fogjuk használni. Az alábbiakban láthatja a fájlt referenciaként:

[!code-yml[Main](../../../azure-app-service-multi-container/kubernetes-wordpress.yml)]

A támogatott konfigurációs lehetőségekről [Kubernetes konfigurációs lehetőségek](configure-custom-container.md#kubernetes-configuration-options)

### <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz

Hozzon létre egy kiszolgálót az Azure Database for MySQL szolgáltatásban az [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) paranccsal.

A következő parancsban cserélje le a MySQL-kiszolgáló nevét, ahol a  _&lt;mysql-kiszolgáló-neve >_ helyőrző (érvényes karakterek: `a-z`, `0-9`, és `-`). Ez a név része a MySQL-kiszolgáló állomásnevének (`<mysql-server-name>.database.windows.net`), és globálisan egyedinek kell lennie.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

A MySQL-kiszolgáló létrehozása után a Cloud Shell az alábbi példához hasonló információkat jelenít meg:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql-server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>",
  "location": "southcentralus",
  "name": "<mysql-server-name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Kiszolgáló tűzfalának konfigurálása

Az [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) parancs használatával hozzon létre egy tűzfalszabályt a MySQL-kiszolgáló számára az ügyfélkapcsolatok engedélyezéséhez. Ha a kezdő IP-cím és a záró IP-cím is 0.0.0.0 értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Még szigorúbb tűzfalszabályt is megadhat, ha [csak azokat a kimenő IP-címeket használja, amelyeket alkalmazása használ](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>A WordPress-adatbázis létrehozása

Hozzon létre egy [Azure Database for MySQL-kiszolgálót](#create-an-azure-database-for-mysql-server), ha még nem tette meg.

```azurecli-interactive
az mysql db create --resource-group myResourceGroup --server-name <mysql-server-name> --name wordpress
```

Az adatbázis létrehozása után a Cloud Shell az alábbi példához hasonló információkat jelenít meg:

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="create-a-multi-container-app-kubernetes"></a>Többtárolós alkalmazás létrehozása (Kubernetes)

A Cloud Shellben hozzon létre egy többtárolós [webalkalmazást](app-service-linux-intro.md) a `myResourceGroup` erőforráscsoportban és az `myAppServicePlan` App Service-csomagban az [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) paranccsal. Ne felejtse el kicserélni az  _\<alkalmazás-neve >_ egy egyedi névre.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --multicontainer-config-type kube --multicontainer-config-file kubernetes-wordpress.yml
```

A webalkalmazás létrehozása után a Cloud Shell az alábbi példához hasonló eredményeket jelenít meg:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-database-variables-in-wordpress"></a>Adatbázis-változók konfigurálása a WordPressben

A WordPress és az új MySQL-kiszolgáló csatlakoztatásához konfigurálni kell néhány WordPress-specifikus környezeti változót. A módosítás elvégzéséhez használja az [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud Shellben. Az alkalmazásbeállítások megkülönböztetik a kis-és nagybetűket, és szóközzel vannak elválasztva.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WORDPRESS_DB_HOST="<mysql-server-name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql-server-name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Az alkalmazásbeállítás létrehozása után a Cloud Shell az alábbi példához hasonló információkat jelenít meg:

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql-server-name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql-server-name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  }
]
```

### <a name="add-persistent-storage"></a>Állandó tároló hozzáadása

A többtárolós alkalmazás mostantól a Web App for Containersben fut. Az adatok az újraindításkor automatikusan törlődnek, mivel a fájlok nem állandóak. Ebben a szakaszban fog [adja hozzá az állandó tárolóból](configure-custom-container.md#use-persistent-shared-storage) a WordPress-tárolóba.

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Az állandó tároló használatához engedélyezni kell ezt a beállítást az App Service-ben. A módosítás elvégzéséhez használja az [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud Shellben. Az alkalmazásbeállítások megkülönböztetik a kis-és nagybetűket, és szóközzel vannak elválasztva.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Az alkalmazásbeállítás létrehozása után a Cloud Shell az alábbi példához hasonló információkat jelenít meg:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Keresse meg az üzembe helyezett alkalmazást a következő helyen: `http://<app-name>.azurewebsites.net`.

Az alkalmazás mostantól több tárolót futtat a Web App for Containersben.

![Minta többtárolós alkalmazás a Web App for Containers szolgáltatásban][1]

**Gratulálunk!** Létrehozott egy többtárolós alkalmazást a Web App for Containersben.

A Redis használatához kövesse a következő szakaszban ismertetett lépéseket: [A WordPress csatlakoztatása a Redishez](#connect-wordpress-to-redis).

## <a name="find-docker-container-logs"></a>Docker-tárolónaplók keresése

Ha több tároló használata során problémákat tapasztal, a tárolónaplókat a következő helyen érheti el: `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

A következőhöz hasonló kimenet fog megjelenni:

```json
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://<app-name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
```

Minden egyes tárolóhoz tartozik egy naplófájl, valamint a szülő folyamathoz is tartozik egy további napló. Másolja a vonatkozó `href` értéket a böngészőbe a napló megtekintéséhez.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Docker Compose-konfiguráció átalakítása a Web App for Containers használatára
> * Kubernetes-konfiguráció átalakítása a Web App for Containers használatára
> * Többtárolós alkalmazás üzembe helyezése az Azure-ban
> * Alkalmazásbeállítások hozzáadása
> * Állandó tárolók használata a tárolókban
> * Csatlakozás az Azure Database for MySQL-hez
> * Hibák elhárítása

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan képezhet le egyedi DNS-nevet az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név leképezése az alkalmazás](../app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg az egyéb erőforrások:

> [!div class="nextstepaction"]
> [Egyéni tároló konfigurálása](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png
