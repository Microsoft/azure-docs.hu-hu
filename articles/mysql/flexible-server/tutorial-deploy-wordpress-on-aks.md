---
title: 'Oktatóanyag: WordPress üzembe helyezése az AK-fürtön a MySQL rugalmas kiszolgálóval az Azure CLI használatával'
description: Megtudhatja, hogyan hozhat létre és helyezhet üzembe WordPress-t az AK-ban Azure Database for MySQL-rugalmas kiszolgálóval.
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: mvc
ms.openlocfilehash: 6f2b3d9f38fc04428678a71c9942fc7aa2182dc8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217193"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>Oktatóanyag: WordPress-alkalmazás üzembe helyezése az AK-ban Azure Database for MySQL-rugalmas kiszolgálóval

Ebben a rövid útmutatóban egy WordPress-alkalmazást helyez üzembe az Azure Kubernetes Service (ak) fürtön Azure Database for MySQL rugalmas kiszolgálóval (előzetes verzió) az Azure CLI használatával. 
Az **[AK](../../aks/intro-kubernetes.md)** egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. **[Azure Database for MySQL – a rugalmas kiszolgáló (előzetes verzió)](overview.md)** egy teljes körűen felügyelt adatbázis-szolgáltatás, amely részletesebb szabályozást és rugalmasságot biztosít az adatbázis-felügyeleti funkciók és a konfigurációs beállítások használatával. Jelenleg a rugalmas kiszolgáló előzetes verzióban érhető el.

> [!NOTE]
> - Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el
> - Ez a rövid útmutató feltételezi, hogy a Kubernetes-fogalmak, a WordPress és a MySQL alapszintű ismeretekkel rendelkeznek.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI legújabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

> [!NOTE]
> Ha a rövid útmutatóban helyileg futtatja a parancsokat (Azure Cloud Shell helyett), győződjön meg arról, hogy rendszergazdaként futtatja a parancsokat.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Hozzon létre egy erőforráscsoportot a *WordPress-Project* használatával az [az Group Create] [az-Group-Create] paranccsal a *eastus* helyen.

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> Az erőforráscsoport helye az erőforráscsoport metaadatait tárolja. Emellett az erőforrások az Azure-ban futnak, ha nem ad meg egy másik régiót az erőforrás létrehozásakor.

A következő példa kimenete azt mutatja, hogy az erőforráscsoport sikeresen létrejött:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>AKS-fürt létrehozása

Használja az [az aks create](/cli/azure/aks#az-aks-create) parancsot egy AKS-fürt létrehozásához. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. A művelet végrehajtása több percet is igénybe vehet.

```azurecli-interactive
az aks create --resource-group wordpress-project --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Néhány perc elteltével a parancs befejeződik, és a fürthöz tartozó JSON-formátumú adatokat adja vissza.

> [!NOTE]
> AK-fürt létrehozásakor a rendszer automatikusan létrehoz egy második erőforráscsoportot az AK-erőforrások tárolásához. Lásd: [miért jön létre két erőforráscsoport az AK-val?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürtök kezeléséhez a [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), a Kubernetes parancssori ügyfélprogramot kell használnia. Ha Azure Cloud Shellt használ, `kubectl` már telepítve van. A helyi telepítéshez `kubectl` használja az az [AK install-CLI](/cli/azure/aks#az-aks-install-cli) parancsot:

```azurecli-interactive
az aks install-cli
```

Az [az aks get-credentials](/cli/azure/aks#az-aks-get-credentials) paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name myAKSCluster
```

> [!NOTE]
> A fenti parancs a [Kubernetes konfigurációs fájljának](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)alapértelmezett helyét használja, amely a következő: `~/.kube/config` . Megadhat egy másik helyet a Kubernetes konfigurációs fájljához a *--file* használatával.

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

A következő példakimenet az előző lépésekben létrehozott csomópontot mutatja be. Győződjön meg arról, hogy a csomópont állapota *kész*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL rugalmas kiszolgáló létrehozása
Hozzon létre egy rugalmas kiszolgálót az az [MySQL flexibilis-Server Create](/cli/azure/mysql/flexible-server)paranccsal. A következő parancs egy kiszolgálót hoz létre az Azure CLI helyi környezetében lévő szolgáltatás-alapértékek és-értékek használatával:

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

A létrehozott kiszolgáló az alábbi attribútumokkal rendelkezik:
- Egy új üres adatbázis ```flexibleserverdb``` jön létre, amikor a kiszolgálót először kiépítik. Ebben a rövid útmutatóban ezt az adatbázist fogjuk használni.
- Automatikusan létrehozott kiszolgálónév, rendszergazdai Felhasználónév, rendszergazdai jelszó, erőforráscsoport neve (ha még nincs meghatározva helyi környezetben), és az erőforráscsoport megegyező helyén
- Szolgáltatás alapértelmezett értékei a fennmaradó kiszolgálói konfigurációkhoz: számítási szint (feltört), számítási méret/SKU (B1MS), biztonsági mentési megőrzési időtartam (7 nap) és MySQL-verzió (5,7)
- A nyilvános hozzáférés argumentum lehetővé teszi, hogy olyan kiszolgálót hozzon létre, amely a tűzfalszabályok által védett nyilvános hozzáféréssel rendelkezik. Azáltal, hogy az IP-címet adja hozzá a tűzfalszabály hozzáadásához az ügyfélszámítógépről való hozzáférés engedélyezéséhez.
- Mivel a parancs helyi környezetet használ, létrehozza a kiszolgálót az erőforráscsoporthoz ```wordpress-project``` és a régióban ```eastus``` .


### <a name="build-your-wordpress-docker-image"></a>WordPress Docker-rendszerkép összeállítása

Töltse le a [legújabb WordPress](https://wordpress.org/download/) -verziót. Hozzon létre új könyvtárat ```my-wordpress-app``` a projekthez, és használja ezt az egyszerű mappastruktúrát

```
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
        . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```


Nevezze át a nevet, ```wp-config-sample.php``` ```wp-config.php``` és cserélje le a 21 – 32. sort a következő kódrészletre. Az alábbi kódrészlet a Kubernetes manifest-fájlból olvassa be az adatbázis-gazdagépet, a felhasználónevet és a jelszót.

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

### <a name="create-a-dockerfile"></a>Dockerfile létrehozása
Hozzon létre egy új Docker, és másolja a kódrészletet. Ez a Docker az Apache webkiszolgáló PHP-vel való beállításával és a mysqli-bővítmény engedélyezésével foglalkozik.

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>A Docker-rendszerkép összeállítása
Győződjön meg arról, hogy a ```my-wordpress-app``` parancs használatával a terminálban van a könyvtárban ```cd``` . A rendszerkép létrehozásához futtassa a következő parancsot:

``` bash

docker build --tag myblog:latest .

```

Telepítse a lemezképet a [Docker hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) vagy az [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md)szolgáltatásba.

> [!IMPORTANT]
>Ha az Azure Container regdistry (ACR) használja, futtassa a parancsot az ```az aks update``` ACR-fiók az AK-fürthöz való csatolásához.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Kubernetes manifest-fájl létrehozása

A Kubernetes jegyzékfájl a fürt kívánt állapotát határozza meg, például a tároló lemezképeit. Hozzon létre egy nevű manifest `mywordpress.yaml` -fájlt, és másolja a következő YAML-definícióba.

>[!IMPORTANT]
> - Cserélje le a helyére a ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` tényleges WordPress Docker-rendszerkép nevét és címkéjét, például: ```docker-hub-user/myblog:latest``` .
> - Frissítse az ```env``` alábbi szakaszt a ```SERVERNAME``` saját ```YOUR-DATABASE-USERNAME``` MySQL- ```YOUR-DATABASE-PASSWORD``` kiszolgálójának frissítésével.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>A WordPress üzembe helyezése az AK-fürtön
Telepítse az alkalmazást a [kubectl Apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f mywordpress.yaml
```

A következő példa kimenete a sikeresen létrehozott központi telepítéseket és szolgáltatásokat mutatja:

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes szolgáltatás az előtér-alkalmazást az internethez teszi elérhetővé. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot a `--watch` argumentummal.

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

Kezdetben a *WordPress-blog* szolgáltatás *külső IP-címe* *függőben* jelenik meg.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha a *külső IP-* cím *függőben* ÁLLAPOTRÓL tényleges nyilvános IP-címről változik, akkor a `CTRL-C` figyelési folyamat leállításához használja a következőt: `kubectl` . A következő példa kimenete a szolgáltatáshoz hozzárendelt érvényes nyilvános IP-címet jeleníti meg:

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>A WordPress böngészése

Nyisson meg egy webböngészőt a szolgáltatás külső IP-címére a WordPress telepítési oldalának megtekintéséhez.

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="A WordPress telepítése sikeres az AK-ra és a MySQL rugalmas kiszolgálóra":::

>[!NOTE]
> - A WordPress-webhely jelenleg nem HTTPS protokollt használ. Javasoljuk, hogy [a TLS-t a saját tanúsítványainak használatával engedélyezze](../../aks/ingress-own-tls.md).
> - A fürthöz [http-útválasztást](../../aks/http-application-routing.md) is engedélyezhet.

## <a name="clean-up-the-resources"></a>Az erőforrások eltávolítása

Az Azure-költségek elkerülése érdekében törölje a szükségtelen erőforrásokat.  Ha a fürtre már nincs szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését](../../aks/kubernetes-service-principal.md#additional-considerations) ismertető cikket. Felügyelt identitás használata esetén az identitást a platform felügyeli, és nem szükséges az eltávolítás.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [érheti el a Kubernetes webes irányítópultját](../../aks/kubernetes-dashboard.md) az AK-fürthöz
- Ismerje meg, hogyan [méretezheti a fürtöt](../../aks/tutorial-kubernetes-scale.md)
- Ismerje meg, hogyan kezelheti a [MySQL rugalmas kiszolgálót](./quickstart-create-server-cli.md)
- Ismerje meg, hogyan konfigurálhatja az adatbázis-kiszolgáló [kiszolgálói paramétereit](./how-to-configure-server-parameters-cli.md) .

