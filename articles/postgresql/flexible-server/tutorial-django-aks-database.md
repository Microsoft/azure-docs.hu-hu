---
title: 'Oktatóanyag: Django üzembe helyezése az AK-fürtön a PostgreSQL rugalmas kiszolgálóval az Azure CLI használatával'
description: Megtudhatja, hogyan hozhat létre és helyezhet üzembe Django az AK-ban Azure Database for PostgreSQL-rugalmas kiszolgálóval.
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc
ms.openlocfilehash: 8768f7f4ca898f3f1f81cdd2611679e62d999ef8
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645195"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>Oktatóanyag: Django-alkalmazás üzembe helyezése az AK-ban Azure Database for PostgreSQL-rugalmas kiszolgálóval

Ebben a rövid útmutatóban egy Django-alkalmazást helyez üzembe az Azure Kubernetes Service (ak) fürtön Azure Database for PostgreSQL-rugalmas kiszolgálóval (előzetes verzió) az Azure CLI használatával.

Az **[AK](../../aks/intro-kubernetes.md)** egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. **[Azure Database for PostgreSQL – a rugalmas kiszolgáló (előzetes verzió)](overview.md)** egy teljes körűen felügyelt adatbázis-szolgáltatás, amely részletesebb szabályozást és rugalmasságot biztosít az adatbázis-felügyeleti funkciók és a konfigurációs beállítások használatával.

> [!NOTE]
> - Azure Database for PostgreSQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el
> - Ez a rövid útmutató feltételezi, hogy a Kubernetes fogalmak, a Django és a PostgreSQL alapszintű ismeretekkel rendelkezik.

## <a name="pre-requisites"></a>Előfeltételek
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- Használja az [Azure Cloud Shellt](../../cloud-shell/quickstart.md) a Bash-környezet használatával.

   [![Indítás beágyazása](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell elindítása")](https://shell.azure.com)  
- Ha szeretné, [telepítse](/cli/azure/install-azure-cli) az Azure CLI-t a CLI-hivatkozások parancsainak futtatásához.
  - Ha helyi telepítést használ, jelentkezzen be az Azure CLI-vel az [az login](/cli/azure/reference-index#az-login) parancs futtatásával.  A hitelesítési folyamat befejezéséhez kövesse a terminálon megjelenő lépéseket.  További bejelentkezési lehetőségek megismeréséhez tekintse meg a [Bejelentkezés az Azure CLI használatával](/cli/azure/authenticate-azure-cli) című szakaszt.
  - Ha a rendszer kéri, az első használatkor telepítse az Azure CLI-bővítményeket.  További információ a bővítményekről: [Bővítmények használata az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview).
  - Futtassa az [az version](/cli/azure/reference-index?#az_version) parancsot a telepített verzió és a függő kódtárak megkereséséhez. A legújabb verzióra az [az upgrade](/cli/azure/reference-index?#az_upgrade) paranccsal frissíthet. Ehhez a cikkhez az Azure CLI legújabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

> [!NOTE]
> Ha a rövid útmutatóban helyileg futtatja a parancsokat (Azure Cloud Shell helyett), győződjön meg arról, hogy rendszergazdaként futtatja a parancsokat.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Hozzon létre egy erőforráscsoportot, a *Django-Project* használatával az [az Group Create] [az-Group-Create] parancsot az *eastus* helyen.

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> Az erőforráscsoport helye az erőforráscsoport metaadatait tárolja. Emellett az erőforrások az Azure-ban futnak, ha nem ad meg egy másik régiót az erőforrás létrehozásakor.

A következő példa kimenete azt mutatja, hogy az erőforráscsoport sikeresen létrejött:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>AKS-fürt létrehozása

Használja az [az aks create](/cli/azure/aks#az-aks-create) parancsot egy AKS-fürt létrehozásához. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. A művelet végrehajtása több percet is igénybe vehet.

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
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
az aks get-credentials --resource-group django-project --name djangoappcluster
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

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL rugalmas kiszolgáló létrehozása
Hozzon létre egy rugalmas kiszolgálót az az [postgreSQL flexibilis-Server Create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create)paranccsal. A következő parancs egy kiszolgálót hoz létre az Azure CLI helyi környezetében lévő szolgáltatás-alapértékek és-értékek használatával:

```azurecli-interactive
az postgres flexible-server create --public-access <YOUR-IP-ADDRESS>
```

A létrehozott kiszolgáló az alábbi attribútumokkal rendelkezik:
- Egy új üres adatbázis ```postgres``` jön létre, amikor a kiszolgálót először kiépítik. Ebben a rövid útmutatóban ezt az adatbázist fogjuk használni.
- Automatikusan létrehozott kiszolgálónév, rendszergazdai Felhasználónév, rendszergazdai jelszó, erőforráscsoport neve (ha még nincs meghatározva helyi környezetben), és az erőforráscsoport megegyező helyén
- Szolgáltatás alapértelmezett értékei a fennmaradó kiszolgálói konfigurációkhoz: számítási szintek (általános célú), számítási méret/SKU (2vCores használó Standard_D2s_v3), biztonsági mentési megőrzési időtartam (7 nap) és PostgreSQL-verzió (12)
- A nyilvános hozzáférés argumentum lehetővé teszi, hogy olyan kiszolgálót hozzon létre, amely a tűzfalszabályok által védett nyilvános hozzáféréssel rendelkezik. Azáltal, hogy az IP-címet adja hozzá a tűzfalszabály hozzáadásához az ügyfélszámítógépről való hozzáférés engedélyezéséhez.
- Mivel a parancs helyi környezetet használ, létrehozza a kiszolgálót az erőforráscsoporthoz ```django-project``` és a régióban ```eastus``` .


## <a name="build-your-django-docker-image"></a>Django Docker-rendszerkép összeállítása

Hozzon létre egy új [Django-alkalmazást](https://docs.djangoproject.com/en/3.1/intro/) , vagy használja a meglévő Django-projektet. Győződjön meg arról, hogy a kód ebben a mappában van.

```
└───my-djangoapp
    └───views.py
    └───models.py
    └───forms.py
    ├───templates
        . . . . . . .
    ├───static
        . . . . . . .
└───my-django-project
    └───settings.py
    └───urls.py
    └───wsgi.py
        . . . . . . .
    └─── Dockerfile
    └─── requirements.txt
    └─── manage.py
    
```
A alkalmazásban frissítse a ```ALLOWED_HOSTS``` ```settings.py``` Django alkalmazást a kubernetes-alkalmazáshoz hozzárendelt külső IP-címmel.

```python
ALLOWED_HOSTS = ['*']
```

```DATABASES={ }```Szakasz frissítése a ```settings.py``` fájlban. Az alábbi kódrészlet a Kubernetes manifest-fájlból olvassa be az adatbázis-gazdagépet, a felhasználónevet és a jelszót.

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>requirements.txt fájl létrehozása
Hozzon létre egy ```requirements.txt``` fájlt a Django alkalmazás függőségeinek listázásához. Íme egy példa ```requirements.txt``` fájl. A használatával létrehozhat [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/) egy requirements.txt fájlt a meglévő alkalmazáshoz.

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>Dockerfile létrehozása
Hozzon létre egy nevű új fájlt ```Dockerfile``` , és másolja az alábbi kódrészletet. Ez a Docker a Python 3,8 beállítását és a requirements.txt fájlban felsorolt összes követelmény telepítését ismerteti.

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>Rendszerkép létrehozása
Győződjön meg arról, hogy a ```my-django-app``` parancs használatával a terminálban van a könyvtárban ```cd``` . Futtassa a következő parancsot a hirdetőtábla rendszerképének összeállításához:

``` bash

docker build --tag myblog:latest .

```

Telepítse a lemezképet a [Docker hub](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) vagy az [Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md)szolgáltatásba.

> [!IMPORTANT]
>Ha az Azure Container regdistry (ACR) használja, futtassa a parancsot az ```az aks update``` ACR-fiók az AK-fürthöz való csatolásához.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Kubernetes manifest-fájl létrehozása

A Kubernetes jegyzékfájl a fürt kívánt állapotát határozza meg, például a tároló lemezképeit. Hozzon létre egy nevű manifest ```djangoapp.yaml``` -fájlt, és másolja a következő YAML-definícióba.

>[!IMPORTANT]
> - Cserélje le a helyére a ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` tényleges Django Docker-rendszerkép nevét és címkéjét, például: ```docker-hub-user/myblog:latest``` .
> - Frissítse az ```env``` alábbi szakaszt a saját ```SERVERNAME``` , a ```YOUR-DATABASE-USERNAME``` ```YOUR-DATABASE-PASSWORD``` postgres rugalmas kiszolgálóval.


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>Django üzembe helyezése az AK-fürtön
Telepítse az alkalmazást a [kubectl Apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f djangoapp.yaml
```

A következő példa kimenete a sikeresen létrehozott központi telepítéseket és szolgáltatásokat mutatja:

```output
deployment "django-app" created
service "python-svc" created
```

A központi telepítés ```django-app``` lehetővé teszi, hogy leírja a telepítés részleteit, például az alkalmazáshoz használt képeket, a hüvelyek és a pod-konfiguráció számát. A szolgáltatás úgy ```python-svc``` jön létre, hogy külső IP-címen teszi elérhetővé az alkalmazást.

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes szolgáltatás az előtér-alkalmazást az internethez teszi elérhetővé. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot a `--watch` argumentummal.

```azurecli-interactive
kubectl get service django-app --watch
```

Kezdetben a *Django-app* szolgáltatás *külső IP-címe* *függőben* jelenik meg.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha a *külső IP-* cím *függőben* ÁLLAPOTRÓL tényleges nyilvános IP-címről változik, akkor a `CTRL-C` figyelési folyamat leállításához használja a következőt: `kubectl` . A következő példa kimenete a szolgáltatáshoz hozzárendelt érvényes nyilvános IP-címet jeleníti meg:

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Most nyisson meg egy webböngészőt a szolgáltatás külső IP-címére, és tekintse meg a Django alkalmazást.  

>[!NOTE]
> - A Django-hely jelenleg nem HTTPS protokollt használ. Javasoljuk, hogy [a TLS-t a saját tanúsítványainak használatával engedélyezze](../../aks/ingress-own-tls.md).
> - A fürthöz [http-útválasztást](../../aks/http-application-routing.md) is engedélyezhet. Ha engedélyezve van a http-útválasztás, a rendszer egy bejövő vezérlőt konfigurál az AK-fürtben. Az alkalmazások telepítésekor a megoldás nyilvánosan elérhető DNS-neveket is létrehoz az alkalmazás-végpontokhoz.

## <a name="run-database-migrations"></a>Adatbázis-áttelepítés futtatása

Bármely Django-alkalmazás esetében futtatnia kell az adatbázis-áttelepítést, vagy statikus fájlokat kell gyűjtenie. Ezeket a Django rendszerhéj-parancsokat a használatával futtathatja ```$ kubectl exec <pod-name> -- [COMMAND]``` .  A parancs futtatása előtt meg kell keresnie a pod nevet a használatával ```kubectl get pods``` . 

```bash
$ kubectl get pods
```

Ehhez hasonló kimenet jelenik meg
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

Miután megtalálta a pod nevét, futtathatja a Django-adatbázis áttelepítését a paranccsal ```$ kubectl exec <pod-name> -- [COMMAND]``` . Vegye figyelembe, hogy a ```/code/``` projekt munkakönyvtára a ```Dockerfile``` fentiekben definiálva van.

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

A kimenet így néz ki: 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

Ha problémákba ütközik, futtassa a parancsot ```kubectl logs <pod-name>```  az alkalmazás által kiváltott kivétel megtekintéséhez. Ha az alkalmazás sikeresen működik, a futtatásakor egy kimenet jelenik meg ```kubectl logs``` .

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>Az erőforrások eltávolítása

Az Azure-költségek elkerülése érdekében törölje a szükségtelen erőforrásokat.  Ha a fürtre már nincs szükség, az [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete) paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését](../../aks/kubernetes-service-principal.md#additional-considerations) ismertető cikket. Felügyelt identitás használata esetén az identitást a platform felügyeli, és nem szükséges az eltávolítás.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [érheti el a Kubernetes webes irányítópultját](../../aks/kubernetes-dashboard.md) az AK-fürthöz
- Ismerje meg, hogyan [engedélyezheti a folyamatos üzembe helyezést](../../aks/deployment-center-launcher.md)
- Ismerje meg, hogyan [méretezheti a fürtöt](../../aks/tutorial-kubernetes-scale.md)
- Ismerje meg, hogyan kezelheti [postgres rugalmas kiszolgálóját](./quickstart-create-server-cli.md)
- Ismerje meg, hogyan konfigurálhatja az adatbázis-kiszolgáló [kiszolgálói paramétereit](./howto-configure-server-parameters-using-cli.md) .
