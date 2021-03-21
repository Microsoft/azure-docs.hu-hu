---
title: Tároló futtatása példa a Docker Run parancsra
titleSuffix: Azure Cognitive Services
description: Docker-futtatási parancs az állapotfigyelő tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/12/2020
ms.author: aahi
ms.openlocfilehash: af8fec56c32b52e2af584e59f08db6cc7129c9c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98947652"
---
## <a name="install-the-container"></a>A tároló telepítése

Több módon is telepítheti és futtathatja az Text Analytics for Health tárolót. 

- A [Azure Portal](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare) használatával hozzon létre egy Text Analytics-erőforrást, és a Docker használatával szerezze be a tárolót.
- Az alábbi PowerShell-és Azure CLI-parancsfájlok segítségével automatizálhatja az erőforrások üzembe helyezését és a tárolók konfigurációját.

### <a name="run-the-container-locally"></a>Futtassa helyileg a tárolót

Ha a tároló rendszerképének letöltése után szeretné futtatni a tárolót a saját környezetében, keresse meg a rendszerkép AZONOSÍTÓját:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Hajtsa végre a következő `docker run` parancsot. Cserélje le az alábbi helyőrzőket a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | A Text Analytics erőforrás kulcsa. A Azure Portal az erőforrás **kulcs és végpont** lapján található. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | A Text Analytics API elérésére szolgáló végpont. A Azure Portal az erőforrás **kulcs és végpont** lapján található. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | A tároló rendszerkép-azonosítója. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | A tároló bemeneti könyvtára. | Windows: `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

A parancs a következőket hajtja végre:

- Feltételezi, hogy a bemeneti könyvtár létezik a gazdagépen.
- Text Analytics futtat az állapotfigyelő tárolóból a tároló képéből
- 6 CPU-mag és 12 gigabájt (GB) memória foglalása
- Elérhetővé teszi a 5000-es TCP-portot, és kiosztja a tárolóhoz tartozó pszeudo-TTY-t
- A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

### <a name="demo-ui-to-visualize-output"></a>Bemutató felhasználói felület a kimenet megjelenítéséhez

> [!NOTE]
> A bemutató csak az Text Analytics for Health tárolóban érhető el.

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat nyújt.  Egy vizualizációs eszközt is biztosítottunk a tárolóban, amely a tároló végpontjának hozzáfűzésével érhető el `/demo` . Például:

```
http://<serverURL>:5000/demo
```

Az alábbi példában szereplő cURL-kérelem használatával küldjön le egy lekérdezést arra a tárolóra, amelyet telepített, majd a `serverURL` megfelelő értékkel cserélje le a változót.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>A tároló telepítése az Azure Web App for Containers használatával

Az Azure [Web App for containers](https://azure.microsoft.com/services/app-service/containers/) egy Azure-erőforrás, amely a Felhőbeli tárolók futtatására szolgál. Olyan beépített funkciókat kínál, mint például az automatikus skálázás, a Docker-tárolók támogatása és a Docker-összeállítás, a HTTPS-támogatás és még sok más.

> [!NOTE]
> Az Azure Web App használatával automatikusan egy tartományt fog kapni `<appservice_name>.azurewebsites.net`

Futtassa ezt a PowerShell-szkriptet az Azure CLI használatával egy Web App for Containers létrehozásához, az előfizetése és a tároló-rendszerkép használatával a HTTPS protokollon keresztül. Várjon, amíg a szkript befejeződik (körülbelül 25-30 perc) az első kérés elküldése előtt.

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>A tároló telepítése az Azure Container instance használatával

Az üzembe helyezést az Azure Container instance (ACI) használatával is egyszerűbbé teheti. Az ACI egy olyan erőforrás, amely lehetővé teszi a Docker-tárolók igény szerinti futtatását egy felügyelt, kiszolgáló nélküli Azure-környezetben. 

Lásd: a [Azure Container instances használata](../../containers/azure-container-instance-recipe.md) az ACI-erőforrások Azure Portal használatával történő üzembe helyezésének lépéseihez. Az Azure CLI-vel az alábbi PowerShell-szkriptet is használhatja, amely egy ACI-t hoz létre az előfizetésében a tároló képe alapján.  Várjon, amíg a szkript befejeződik (körülbelül 25-30 perc) az első kérés elküldése előtt.  Az ACI-erőforrások maximális számának korlátozása miatt ne jelölje be ezt a beállítást, ha várhatóan több mint 5 nagy dokumentumot (körülbelül 5000 karaktert) kíván elküldeni.
A rendelkezésre állással kapcsolatos információkért tekintse meg az [ACI regionális támogatási](../../../container-instances/container-instances-region-availability.md) cikkét. 

> [!NOTE] 
> Azure Container Instances nem tartalmaz HTTPS-támogatást a beépített tartományokhoz. Ha HTTPS-re van szüksége, manuálisan kell konfigurálnia, beleértve a tanúsítvány létrehozását és a tartomány regisztrálását. Ezt az alábbi, NGINX-mel kapcsolatos utasításokat követve teheti meg.

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Biztonságos ACI-kapcsolat

Alapértelmezés szerint az ACI és a Container API használatával nem biztosítunk biztonságot. Ennek az az oka, hogy általában a tárolók egy olyan Pod részeként futnak, amely egy hálózati híddal kívülről van védve. Az előtérben lévő összetevővel azonban módosíthat egy tárolót, így a tároló végpontja magánjellegű marad. Az alábbi példák az [NGINX](https://www.nginx.com) -et bejövő átjáróként használják a https/SSL és az ügyféltanúsítvány-alapú hitelesítés támogatásához.

> [!NOTE]
> Az NGINX egy nyílt forráskódú, nagy teljesítményű HTTP-kiszolgáló és-proxy. Az NGINX-tárolók a TLS-kapcsolatok egyetlen tárolóhoz való leállítására használhatók. Összetettebb, az NGINX-beli bejövő forgalomra épülő TLS-megszakítási megoldások is lehetségesek.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>NGINX beállítása bejövő átjáróként

A NGINX [konfigurációs fájlokat](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) használ a funkciók futásidőben történő engedélyezéséhez. Egy másik szolgáltatás TLS-megszakításának engedélyezéséhez meg kell adnia egy SSL-tanúsítványt a TLS-kapcsolat megszakításához és a  `proxy_pass` szolgáltatáshoz tartozó címek megadásához. Alább látható egy minta.


> [!NOTE]
> `ssl_certificate` az NGINX-tároló helyi fájlrendszerében megadható elérési utat vár. A számára megadott címnek `proxy_pass` elérhetőnek kell lennie az NGINX-tároló hálózatán belül.

Az NGINX-tároló betölti az összes olyan fájlt, `_.conf_` amely a http- `/etc/nginx/conf.d/` konfigurációs elérési úton van csatlakoztatva.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Példa Docker-összeállítás fájlra

Az alábbi példa bemutatja, hogyan hozható létre [Docker-összeállítási](https://docs.docker.com/compose/reference/overview) fájl az NGINX és a Text Analytics for Health containers telepítéséhez:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

A Docker-összeállítási fájl elindításához futtassa a következő parancsot egy konzolon a fájl legfelső szintjén:

```bash
docker-compose up
```

További információ: NGINX dokumentációja az [NGINX SSL-lezárásáról](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).