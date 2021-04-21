---
title: TLS engedélyezése oldalkocsi tárolóval
description: SSL- vagy TLS-végpont létrehozása egy Azure Container Instances-ban futó tárolócsoporthoz az Nginx oldalkocsi tárolóban való futtatásával
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 906a1f239d7050ea17fd7d1425138049ebf045c1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790976"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>TLS-végpont engedélyezése oldalkocsi-tárolóban

Ez a cikk bemutatja, hogyan hozhat létre tárolócsoportot egy alkalmazástárolóval és egy TLS/SSL-szolgáltatót futtató oldalkocsi tárolóval. [](container-instances-container-groups.md) Ha külön TLS-végponttal hoz létre tárolócsoportot, az alkalmazáskód módosítása nélkül engedélyezheti a TLS-kapcsolatokat az alkalmazás számára.

Egy példa tárolócsoportot állíthat be, amely két tárolóból áll:
* Egy alkalmazástároló, amely egy egyszerű webalkalmazást futtat a nyilvános Microsoft [aci-helloworld rendszerkép](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) használatával. 
* Egy TLS használatára konfigurált, nyilvános [Nginx-rendszerképet](https://hub.docker.com/_/nginx) futtató oldalkocsi-tároló. 

Ebben a példában a tárolócsoport csak a 443-as portot teszi elérhetővé az Nginx számára a nyilvános IP-címével. Az Nginx a HTTPS-kéréseket a társ webalkalmazáshoz, amely belsőleg figyel a 80-as porton. A példát olyan tárolóalkalmazásokhoz is adaptálhatja, amelyek más portokat figyelnek. 

További [lépések a](#next-steps) TLS tárolócsoportban való engedélyezésének egyéb megközelítési lépéseiről.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.55-ös vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Az Nginx TLS-szolgáltatóként való beállításhoz TLS-/SSL-tanúsítvány szükséges. Ez a cikk bemutatja, hogyan hozhat létre és állíthat be önaírt TLS-/SSL-tanúsítványt. Éles forgatókönyvek esetén tanúsítványt kell beszereznie egy hitelesítésszolgáltatótól.

Önaírt TLS-/SSL-tanúsítvány létrehozásához használja az Azure Cloud Shell-ban és számos Linux-disztribúcióban elérhető [OpenSSL](https://www.openssl.org/) eszközt, vagy használjon egy hasonló ügyféleszközt az operációs rendszerben.

Először hozzon létre egy tanúsítványkérelmet (.csr fájl) egy helyi munkakönyvtárban:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Kövesse az utasításokat az azonosítási információk hozzáadásához. A Köznév mezőben adja meg a tanúsítványhoz társított állomásnevet. Ha a rendszer jelszót kér, nyomja le az Enter billentyűt, és hagyja ki a jelszó hozzáadását.

Az alábbi parancs futtatásával hozza létre az önaírt tanúsítványt (.crt fájlt) a tanúsítványkérelemből. Például:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Most három fájlnak kell lennie a könyvtárban: a tanúsítványkérelem ( ), a titkos kulcs ( ), valamint az `ssl.csr` `ssl.key` önaírt tanúsítvány ( `ssl.crt` ). A későbbi `ssl.key` lépésekben a és `ssl.crt` a et használhatja.

## <a name="configure-nginx-to-use-tls"></a>Az Nginx konfigurálása a TLS használatára

### <a name="create-nginx-configuration-file"></a>Nginx konfigurációs fájl létrehozása

Ebben a szakaszban egy konfigurációs fájlt hoz létre az Nginx számára a TLS használatára. Először másolja a következő szöveget egy nevű új `nginx.conf` fájlba. A Azure Cloud Shell a Visual Studio Code használatával hozhatja létre a fájlt a munkakönyvtárban:

```console
code nginx.conf
```

Győződjön meg arról, hogy az alkalmazásban `location` a megfelelő `proxy_pass` portot adja meg. Ebben a példában a tárolóhoz a 80-as portot `aci-helloworld` adhatja meg.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize TLS/SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive TLS/SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64-kódolású titkos kulcsok és konfigurációs fájl

A Base64 kódolja az Nginx konfigurációs fájlt, a TLS-/SSL-tanúsítványt és a TLS-kulcsot. A következő szakaszban adja meg a kódolt tartalmat egy YAML-fájlban, amely a tárolócsoport üzembe helyezéséhez használatos.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Tárolócsoport üzembe helyezése

Most a tárolócsoport üzembe helyezéséhez adja meg a tárolókonfigurációkat egy [YAML-fájlban.](container-instances-multi-container-yaml.md)

### <a name="create-yaml-file"></a>YAML-fájl létrehozása

Másolja a következő YAML-t egy nevű új `deploy-aci.yaml` fájlba. A Azure Cloud Shell a Visual Studio Code használatával hozhatja létre a fájlt a munkakönyvtárban:

```console
code deploy-aci.yaml
```

Adja meg a base64 kódolású fájlok tartalmát, ahol a alatt `secret` szerepel. Például a `cat` base64 kódolású fájlok tartalmának a Az üzembe helyezés során a rendszer hozzáadja ezeket a fájlokat egy titkos [kötethez](container-instances-volume-secret.md) a tárolócsoportban. Ebben a példában a titkos kötet az Nginx-tárolóhoz van csatlakoztatva.

```YAML
api-version: 2019-12-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>A tárolócsoport üzembe helyezése

Hozzon létre egy erőforráscsoportot [az az group create paranccsal:](/cli/azure/group#az_group_create)

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

A tárolócsoportot az [az container create paranccsal](/cli/azure/container#az_container_create) helyezheti üzembe, argumentumként átkulálva a YAML-fájlt.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Központi telepítési állapot megtekintése

Az üzembe helyezés állapotának megtekintéséhez használja az [az container show parancsot:](/cli/azure/container#az_container_show)

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Sikeres üzembe helyezés esetén a kimenet az alábbihoz hasonló:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-tls-connection"></a>A TLS-kapcsolat ellenőrzése

A böngészőben navigáljon a tárolócsoport nyilvános IP-címére. A példában látható IP-cím `52.157.22.76` , tehát az URL-cím **https://52.157.22.76** . A futó alkalmazás az Nginx-kiszolgáló konfigurációja miatt HTTPS protokollt kell használnia. HTTP-kapcsolaton keresztüli kapcsolódási kísérlet meghiúsul.

![Képernyőkép a böngészőről, ahol egy Azure-tárolópéldányban futó alkalmazás látható](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Mivel ez a példa egy önaírt tanúsítványt használ, nem pedig egy hitelesítésszolgáltatótól származó tanúsítványt, a böngésző biztonsági figyelmeztetést jelenít meg, amikor HTTPS-kapcsolaton keresztül csatlakozik a webhelyhez. Előfordulhat, hogy el kell fogadnia a figyelmeztetést, vagy módosítania kell a böngésző vagy a tanúsítvány beállításait a lapra való továbblépéshez. Ez várt működés.

>

## <a name="next-steps"></a>Következő lépések

Ez a cikk bemutatja, hogyan állíthat be egy Nginx-tárolót a tárolócsoportban futó webalkalmazás TLS-kapcsolatainak engedélyezéséhez. Ezt a példát olyan alkalmazásokhoz is adaptálhatja, amelyek a 80-as porton kívül más portokat is figyelnek. Az Nginx konfigurációs fájl frissítésével automatikusan átirányíthatja a kiszolgálói kapcsolatokat a 80-as (HTTP-) porton a HTTPS használatára.

Bár ez a cikk nginx-et használ az oldalkocsiban, használhat egy másik TLS-szolgáltatót, például a [Caddyt.](https://caddyserver.com/)

Ha a tárolócsoportot [](container-instances-vnet.md)egy Azure-beli virtuális hálózatban telepíti, más lehetőségeket is figyelembe vehet a TLS-végpont háttértárolópéldányhoz való engedélyezéséhez, például:

* [Azure Functions-proxyk](../azure-functions/functions-proxies.md)
* [Azure API Management](../api-management/api-management-key-concepts.md)
* [Azure Application Gateway](../application-gateway/overview.md) – tekintse meg a minta üzembe [helyezési sablont.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)
