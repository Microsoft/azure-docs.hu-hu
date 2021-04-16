---
title: 'Oktatóanyag: A WordPress üzembe helyezése AKS-fürtön rugalmas MySQL-kiszolgálóval az Azure CLI használatával'
description: Megtudhatja, hogyan építhet ki és helyezhet üzembe gyorsan WordPresst az AKS-Azure Database for MySQL rugalmas kiszolgálóval.
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: b631173ed92905870e73e6c560d90aab08476ce1
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480154"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>Oktatóanyag: WordPress-alkalmazás üzembe helyezése AKS-Azure Database for MySQL rugalmas kiszolgálóval

Ebben a rövid útmutatóban egy WordPress-alkalmazást fog üzembe helyezni egy Azure Kubernetes Service-fürtön egy rugalmas Azure Database for MySQL (előzetes verzió) kiszolgálóval az Azure CLI használatával. 
**[Az AKS](../../aks/intro-kubernetes.md)** egy felügyelt Kubernetes-szolgáltatás, amely lehetővé teszi a fürtök gyors üzembe helyezését és kezelését. Azure Database for MySQL – A rugalmas kiszolgáló **[(előzetes verzió)](overview.md)** egy teljes körűen felügyelt adatbázis-szolgáltatás, amely részletesebb vezérlést és rugalmasságot biztosít az adatbázis-kezelési funkciókhoz és a konfigurációs beállításokhoz. Jelenleg a rugalmas kiszolgáló előzetes verzióban érhető el.

> [!NOTE]
> - Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el
> - Ez a rövid útmutató feltételezi a Kubernetes alapfogalmai, a WordPress és a MySQL alapszintű ismereteit.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI legújabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

> [!NOTE]
> Ha ebben a rövid útmutatóban helyileg futtatja a parancsokat (nem Azure Cloud Shell), akkor a parancsokat rendszergazdaként kell futtatnia.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Hozzunk létre egy *wordpress-project* erőforráscsoportot az *eastus* helyen található [az group create][az-group-create] paranccsal.

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> Az erőforráscsoport metaadatainak tárolása az erőforráscsoport helye. Akkor is itt futnak az erőforrások az Azure-ban, ha nem ad meg másik régiót az erőforrás létrehozása során.

Az alábbi példakimeneten a sikeresen létrehozott erőforráscsoport látható:

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

Használja az [az aks create](/cli/azure/aks#az-aks-create) parancsot egy AKS-fürt létrehozásához. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. Ez eltarthat néhány percig.

```azurecli-interactive
az aks create --resource-group wordpress-project --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Néhány perc múlva befejeződik a parancs, és visszaadja a fürttel kapcsolatos JSON-formátumú információkat.

> [!NOTE]
> AKS-fürt létrehozásakor a rendszer automatikusan létrehoz egy második erőforráscsoportot az AKS-erőforrások tárolására. Lásd: [Miért jön létre két erőforráscsoport az AKS-sel?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürtök kezeléséhez használja a [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), a Kubernetes parancssori ügyfelét. Ha az alkalmazást Azure Cloud Shell, `kubectl` a már telepítve van. Helyi `kubectl` telepítéshez használja az [az aks install-cli](/cli/azure/aks#az-aks-install-cli) parancsot:

```azurecli-interactive
az aks install-cli
```

Az [az aks get-credentials](/cli/azure/aks#az-aks-get-credentials) paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes parancssori felületét azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name myAKSCluster
```

> [!NOTE]
> A fenti parancs a [Kubernetes](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)konfigurációs fájljának alapértelmezett helyét használja, amely `~/.kube/config` . A --file használatával más helyet is megadhat a Kubernetes *konfigurációs fájlhoz.*

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

A következő példakimenet az előző lépésekben létrehozott csomópontot mutatja be. Győződjön meg arról, hogy a csomópont Ready (Kész) *állapotú:*

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Rugalmas Azure Database for MySQL létrehozása
Hozzon létre egy rugalmas kiszolgálót [az az mysql flexible-server create paranccsal.](/cli/azure/mysql/flexible-server) A következő parancs létrehoz egy kiszolgálót a szolgáltatás alapértelmezett értékeinek és értékeinek használatával az Azure CLI helyi környezetében:

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

A létrehozott kiszolgáló az alábbi attribútumokkal rendelkezik:
- A kiszolgáló első kiépítésekor létrejön egy új ```flexibleserverdb``` üres adatbázis. Ebben a rövid útmutatóban ezt az adatbázist fogjuk használni.
- Automatikusan létrehozott kiszolgálónév, rendszergazdai felhasználónév, rendszergazdai jelszó, erőforráscsoport neve (ha még nincs megadva a helyi környezetben), és ugyanazon a helyen, ahol az erőforráscsoport található
- Szolgáltatás alapértelmezett beállításai a fennmaradó kiszolgálói konfigurációkhoz: számítási szint (burstable), számítási méret/termékváltozat (B1MS), biztonsági másolatok megőrzési időtartama (7 nap) és MySQL-verzió (5.7)
- A nyilvános hozzáférésű argumentum használatával tűzfalszabályok által védett nyilvános hozzáférésű kiszolgálót hozhat létre. Adja meg az IP-címét a tűzfalszabály hozzáadásához, hogy engedélyezze a hozzáférést az ügyfélszámítógépről.
- Mivel a parancs helyi környezetet használ, a kiszolgálót az erőforráscsoportban és a ```wordpress-project``` régióban hozza ```eastus``` létre.


### <a name="build-your-wordpress-docker-image"></a>WordPress Docker-rendszerkép összeállítása

Töltse le [a WordPress legújabb](https://wordpress.org/download/) verzióját. Hozzon létre új ```my-wordpress-app``` könyvtárat a projekthez, és használja ezt az egyszerű mappastruktúrát

```
â””â”€â”€â”€my-wordpress-app
    â””â”€â”€â”€public
        â”œâ”€â”€â”€wp-admin
        â”‚   â”œâ”€â”€â”€css
        . . . . . . .
        â”œâ”€â”€â”€wp-content
        â”‚   â”œâ”€â”€â”€plugins
        . . . . . . .
        â””â”€â”€â”€wp-includes
        . . . . . . .
        â”œâ”€â”€â”€wp-config-sample.php
        â”œâ”€â”€â”€index.php
        . . . . . . .
    â””â”€â”€â”€ Dockerfile

```


Nevezze ```wp-config-sample.php```  át a 21–32. sort erre a kódrészletre, és cserélje le erre ```wp-config.php``` a kódrészletre. Az alábbi kódrészlet az adatbázisgazdát, a felhasználónevet és a jelszót olvassa be a Kubernetes-jegyzékfájlból.

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
Hozzon létre egy új Docker-fájl, és másolja ki ezt a kódrészletet. Ez a Docker-fájl az Apache-webkiszolgáló PHP-val való beállításában és a mysqli bővítmény engedélyezésében.

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>Docker-rendszerkép összeállítása
A paranccsal győződjön meg arról, hogy a terminál ```my-wordpress-app``` könyvtárában ```cd``` van. Futtassa a következő parancsot a rendszerkép felépítéséhez:

``` bash

docker build --tag myblog:latest .

```

A rendszerkép üzembe helyezése [a Docker Hubon vagy](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) az [Azure Container Registryben.](../../container-registry/container-registry-get-started-azure-cli.md)

> [!IMPORTANT]
>Ha Az Azure Container Regdistry (ACR) parancsot használja, futtassa az parancsot az ```az aks update``` ACR-fiók az AKS-fürthöz való csatolására.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Kubernetes-jegyzékfájl létrehozása

A Kubernetes-jegyzékfájl meghatározza a fürt célállapotát, például hogy milyen tárolólemezképeket kell futtatni. Hozzunk létre egy nevű jegyzékfájlt, és másolja be a következő `mywordpress.yaml` YAML-definíciót.

>[!IMPORTANT]
> - Cserélje ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` le a helyére a tényleges WordPress Docker-rendszerkép nevét és címkét, ```docker-hub-user/myblog:latest``` például: .
> - Frissítse az alábbi szakaszt a rugalmas ```env``` ```SERVERNAME``` ```YOUR-DATABASE-USERNAME``` ```YOUR-DATABASE-PASSWORD``` MySQL-kiszolgáló , , rel.

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

## <a name="deploy-wordpress-to-aks-cluster"></a>A WordPress üzembe helyezése AKS-fürtön
Telepítse az alkalmazást a [kubectl apply paranccsal,](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) és adja meg a YAML-jegyzék nevét:

```console
kubectl apply -f mywordpress.yaml
```

Az alábbi példakimeneten a sikeresen létrehozott üzemelő példányok és szolgáltatások láthatóak:

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes-szolgáltatás elérhetővé teszi az alkalmazás előtere számára az internetet. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot a `--watch` argumentummal.

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

Kezdetben a *wordpress-blog* szolgáltatás *EXTERNAL-IP-címe* *függőben van.*

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha *az EXTERNAL-IP*  cím függőben értékről tényleges nyilvános IP-címre változik, a használatával állítsa le a `CTRL-C` `kubectl` figyelés folyamatát. Az alábbi példakimenet egy érvényes, a szolgáltatáshoz rendelt nyilvános IP-címet mutat be:

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>WordPress tallózása

Nyisson meg egy webböngészőt a szolgáltatás külső IP-címére a WordPress telepítési oldalának megnyitásához.

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="A Wordpress telepítése sikeres a rugalmas AKS- és MySQL-kiszolgálón":::

>[!NOTE]
> - A WordPress webhely jelenleg nem használ HTTPS-t. Javasoljuk, hogy engedélyezze a [TLS-t a saját tanúsítványokkal.](../../aks/ingress-own-tls.md)
> - Engedélyezheti a [HTTP-útválasztást](../../aks/http-application-routing.md) a fürt számára.

## <a name="clean-up-the-resources"></a>Az erőforrások eltávolítása

Az Azure-díjak elkerülése érdekében érdemes felesleges erőforrásokat megtisztítani.  Ha a fürtre már nincs szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését](../../aks/kubernetes-service-principal.md#additional-considerations) ismertető cikket. Ha felügyelt identitást használt, az identitást a platform kezeli, és nem igényel eltávolítást.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan férhet hozzá az AKS-fürthöz használt [Kubernetes webes](../../aks/kubernetes-dashboard.md) irányítópultjához
- Ismerje meg, hogyan [skálázható a fürt](../../aks/tutorial-kubernetes-scale.md)
- Megtudhatja, hogyan kezelheti rugalmas [MySQL-kiszolgálóját](./quickstart-create-server-cli.md)
- Megtudhatja, [hogyan konfigurálhatja az adatbázis-kiszolgáló](./how-to-configure-server-parameters-cli.md) kiszolgálóparaméterét.

