---
title: 'Oktatóanyag: Django üzembe helyezése AKS-fürtön rugalmas PostgreSQL-kiszolgálóval az Azure CLI használatával'
description: Megtudhatja, hogyan építhet ki és helyezhet üzembe gyorsan Django-t az AKS-Azure Database for PostgreSQL rugalmas kiszolgálóval.
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: b79b470a25a63c0a46ddef94ee65f47f37c560cb
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477808"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>Oktatóanyag: Django-alkalmazás üzembe helyezése az AKS-Azure Database for PostgreSQL rugalmas kiszolgálóval

Ebben a rövid útmutatóban egy Django-alkalmazást fog üzembe helyezni egy Azure Kubernetes Service-fürtön egy rugalmas Azure Database for PostgreSQL (előzetes verzió) kiszolgálóval az Azure CLI használatával.

**[Az AKS](../../aks/intro-kubernetes.md)** egy felügyelt Kubernetes-szolgáltatás, amely lehetővé teszi a fürtök gyors üzembe helyezését és kezelését. Azure Database for PostgreSQL – A rugalmas kiszolgáló **[(előzetes verzió)](overview.md)** egy teljes körűen felügyelt adatbázis-szolgáltatás, amely részletesebb vezérlést és rugalmasságot biztosít az adatbázis-kezelési funkciókhoz és a konfigurációs beállításokhoz.

> [!NOTE]
> - Azure Database for PostgreSQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el
> - Ez a rövid útmutató feltételezi, hogy alapszintű ismereteket biztosít a Kubernetes, a Django és a PostgreSQL fogalmairól.

## <a name="pre-requisites"></a>Előfeltételek
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- Használja az [Azure Cloud Shellt](../../cloud-shell/quickstart.md) a Bash-környezet használatával.

   [![Indítás beágyazása](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell elindítása")](https://shell.azure.com)  
- Ha szeretné, telepítse [az](/cli/azure/install-azure-cli) Azure CLI-t a CLI-referenciaparancsok futtatásához.
  - Ha helyi telepítést használ, jelentkezzen be az Azure CLI-vel az [az login](/cli/azure/reference-index#az-login) parancs futtatásával.  A hitelesítési folyamat befejezéséhez kövesse a terminálon megjelenő lépéseket.  További bejelentkezési lehetőségek megismeréséhez tekintse meg a [Bejelentkezés az Azure CLI használatával](/cli/azure/authenticate-azure-cli) című szakaszt.
  - Ha a rendszer kéri, az első használatkor telepítse az Azure CLI-bővítményeket.  További információ a bővítményekről: [Bővítmények használata az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview).
  - Futtassa az [az version](/cli/azure/reference-index?#az_version) parancsot a telepített verzió és a függő kódtárak megkereséséhez. A legújabb verzióra az [az upgrade](/cli/azure/reference-index?#az_upgrade) paranccsal frissíthet. Ehhez a cikkhez az Azure CLI legújabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

> [!NOTE]
> Ha ebben a rövid útmutatóban helyileg futtatja a parancsokat (nem Azure Cloud Shell), akkor a parancsokat rendszergazdaként kell futtatnia.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Hozzunk létre egy *django-project* erőforráscsoportot az [az group create][az-group-create] paranccsal az *eastus* helyen.

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> Az erőforráscsoport metaadatainak tárolása az erőforráscsoport helye. Akkor is itt futnak az erőforrások az Azure-ban, ha nem ad meg másik régiót az erőforrás létrehozása során.

Az alábbi példakimeneten a sikeresen létrehozott erőforráscsoport látható:

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

Használja az [az aks create](/cli/azure/aks#az-aks-create) parancsot egy AKS-fürt létrehozásához. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. Ez eltarthat néhány percig.

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
```

Néhány perc múlva befejeződik a parancs, és visszaadja a fürttel kapcsolatos JSON-formátumú információkat.

> [!NOTE]
> AKS-fürt létrehozásakor a rendszer automatikusan létrehoz egy második erőforráscsoportot az AKS-erőforrások tárolására. Lásd: [Miért jön létre két erőforráscsoport az AKS-sel?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürtök kezeléséhez használja a [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), a Kubernetes parancssori ügyfelét. Ha az alkalmazást Azure Cloud Shell, `kubectl` a már telepítve van. A helyi `kubectl` telepítéshez használja az [az aks install-cli](/cli/azure/aks#az-aks-install-cli) parancsot:

```azurecli-interactive
az aks install-cli
```

Az [az aks get-credentials](/cli/azure/aks#az-aks-get-credentials) paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes parancssori felületét azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group django-project --name djangoappcluster
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

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Rugalmas Azure Database for PostgreSQL létrehozása
Hozzon létre egy rugalmas kiszolgálót [az az postgreSQL flexible-server create paranccsal.](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) A következő parancs létrehoz egy kiszolgálót a szolgáltatás alapértelmezett értékeinek és értékeinek használatával az Azure CLI helyi környezetében:

```azurecli-interactive
az postgres flexible-server create --public-access <YOUR-IP-ADDRESS>
```

A létrehozott kiszolgáló az alábbi attribútumokkal rendelkezik:
- A kiszolgáló első kiépítésekor létrejön egy új ```postgres``` üres adatbázis. Ebben a rövid útmutatóban ezt az adatbázist fogjuk használni.
- Automatikusan létrehozott kiszolgálónév, rendszergazdai felhasználónév, rendszergazdai jelszó, erőforráscsoport neve (ha még nincs megadva a helyi környezetben), és ugyanazon a helyen, ahol az erőforráscsoport található
- Szolgáltatás alapértelmezései a fennmaradó kiszolgálói konfigurációkhoz: számítási szint (általános célú), számítási méret/termékváltozat (Standard_D2s_v3, amely 2 virtuális magot használ), biztonsági másolatok megőrzési időtartama (7 nap) és PostgreSQL-verzió (12)
- A nyilvános hozzáférésű argumentum használatával tűzfalszabályok által védett nyilvános hozzáférésű kiszolgálót hozhat létre. Adja meg az IP-címét a tűzfalszabály hozzáadásához, hogy engedélyezze a hozzáférést az ügyfélszámítógépről.
- Mivel a parancs helyi környezetet használ, a kiszolgálót az erőforráscsoportban és a ```django-project``` régióban hozza ```eastus``` létre.


## <a name="build-your-django-docker-image"></a>A Django Docker-rendszerkép összeállítása

Hozzon létre egy [új Django-alkalmazást,](https://docs.djangoproject.com/en/3.1/intro/) vagy használja a meglévő Django-projektet. Győződjön meg arról, hogy a kód ebben a mappastruktúrában található.

```
â””â”€â”€â”€my-djangoapp
    â””â”€â”€â”€views.py
    â””â”€â”€â”€models.py
    â””â”€â”€â”€forms.py
    â”œâ”€â”€â”€templates
          . . . . . . .
    â”œâ”€â”€â”€static
         . . . . . . .
â””â”€â”€â”€my-django-project
    â””â”€â”€â”€settings.py
    â””â”€â”€â”€urls.py
    â””â”€â”€â”€wsgi.py
        . . . . . . .
    â””â”€â”€â”€ Dockerfile
    â””â”€â”€â”€ requirements.txt
    â””â”€â”€â”€ manage.py
    
```
Frissítse ```ALLOWED_HOSTS``` az -t, hogy a ```settings.py``` Django-alkalmazás a kubernetes-alkalmazáshoz hozzárendelt külső IP-címet használja.

```python
ALLOWED_HOSTS = ['*']
```

Frissítse ```DATABASES={ }``` a fájl ```settings.py```  szakaszát. Az alábbi kódrészlet az adatbázisgazdát, a felhasználónevet és a jelszót olvassa be a Kubernetes-jegyzékfájlból.

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

### <a name="generate-a-requirementstxt-file"></a>Új requirements.txt létrehozása
Hozzon ```requirements.txt``` létre egy fájlt a Django-alkalmazás függőségeinek felsorolásához. 1. ```requirements.txt``` Példafájl. A használatával [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/) létrehozhat egy requirements.txt fájlt a meglévő alkalmazáshoz.

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>Dockerfile létrehozása
Hozzon létre egy nevű új fájlt, és másolja az ```Dockerfile``` alábbi kódrészletet. Ez a Docker-fájl a Python 3.8 beállításában és a fájlban felsorolt requirements.txt telepítésében.

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

### <a name="build-your-image"></a>A rendszerkép összeállítása
A paranccsal győződjön meg arról, hogy a terminál ```my-django-app``` könyvtárában ```cd``` van. Futtassa a következő parancsot a hirdetőtábla rendszerképének felépítéséhez:

``` bash

docker build --tag myblog:latest .

```

A rendszerkép üzembe helyezése [a Docker Hubon vagy](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) az [Azure Container Registryben.](../../container-registry/container-registry-get-started-azure-cli.md)

> [!IMPORTANT]
>Ha Az Azure Container Regdistry (ACR) parancsot használja, futtassa az parancsot az ```az aks update``` ACR-fiók az AKS-fürthöz való csatolására.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Kubernetes-jegyzékfájl létrehozása

A Kubernetes-jegyzékfájl meghatározza a fürt célállapotát, például hogy milyen tárolólemezképeket kell futtatni. Hozzunk létre egy nevű jegyzékfájlt, és másolja be a következő ```djangoapp.yaml``` YAML-definíciót.

>[!IMPORTANT]
> - Cserélje ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` le a helyére a tényleges Django Docker-rendszerkép nevét és címkét, ```docker-hub-user/myblog:latest``` például: .
> - Az alábbi szakaszt frissítse a rugalmas ```env``` ```SERVERNAME``` ```YOUR-DATABASE-USERNAME``` ```YOUR-DATABASE-PASSWORD``` Postgres-kiszolgáló , , és útjára.


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

## <a name="deploy-django-to-aks-cluster"></a>Django üzembe helyezése AKS-fürtön
Telepítse az alkalmazást a [kubectl apply paranccsal,](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) és adja meg a YAML-jegyzék nevét:

```console
kubectl apply -f djangoapp.yaml
```

Az alábbi példakimenet a sikeresen létrehozott üzemelő példányokat és szolgáltatásokat mutatja be:

```output
deployment "django-app" created
service "python-svc" created
```

Az üzemelő példányok lehetővé teszik az üzemelő példány részleteinek, például az alkalmazáshoz használt rendszerképek, a podok számának és a ```django-app``` podkonfigurációnak a részleteit. Létrejön egy ```python-svc``` szolgáltatás, amely egy külső IP-címmel teszi elérhetővé az alkalmazást.

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor egy Kubernetes-szolgáltatás teszi elérhetővé az alkalmazás előtere számára az internetet. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot a `--watch` argumentummal.

```azurecli-interactive
kubectl get service django-app --watch
```

Kezdetben a *django-app* szolgáltatás *EXTERNAL-IP-címe* *függőben van.*

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha *az EXTERNAL-IP*  cím függőben értékről tényleges nyilvános IP-címre változik, a használatával állítsa le a `CTRL-C` `kubectl` figyelés folyamatát. Az alábbi példakimenet egy érvényes, a szolgáltatáshoz rendelt nyilvános IP-címet mutat be:

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Most nyisson meg egy webböngészőt a szolgáltatás külső IP-címére, és tekintse meg a Django-alkalmazást.  

>[!NOTE]
> - A Django-webhely jelenleg nem használ HTTPS-t. Javasoljuk, hogy engedélyezze a [TLS-t a saját tanúsítványokkal.](../../aks/ingress-own-tls.md)
> - Engedélyezheti a [HTTP-útválasztást](../../aks/http-application-routing.md) a fürt számára. Ha a HTTP-útválasztás engedélyezve van, konfigurál egy bejövő forgalomvezérlőt az AKS-fürtben. Az alkalmazások üzembe helyezésekor a megoldás nyilvánosan elérhető DNS-neveket is létrehoz az alkalmazásvégponthoz.

## <a name="run-database-migrations"></a>Adatbázis-áttelepítések futtatása

Minden django-alkalmazás esetén adatbázis-migrálást kell futtatnia, vagy statikus fájlokat kell gyűjtenie. Ezeket a django rendszerhéjparancsokat a paranccsal ```$ kubectl exec <pod-name> -- [COMMAND]``` futtathatja.  A parancs futtatása előtt meg kell találnia a pod nevét a ```kubectl get pods``` használatával. 

```bash
$ kubectl get pods
```

Ehhez hasonló kimenetet fog látni
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

Ha megtalálta a pod nevét, a paranccsal futtathatja a django-adatbázis migrálását. ```$ kubectl exec <pod-name> -- [COMMAND]``` Megjegyzés: ```/code/``` a fentiben definiált projekt munkakönyvtára. ```Dockerfile```

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

A kimenet így nézne ki: 
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

Ha problémákba fog belefutni, futtassa a következőt: , hogy ```kubectl logs <pod-name>```  lássa, milyen kivételt okozott az alkalmazás. Ha az alkalmazás sikeresen működik, a futtatásakor ehhez hasonló kimenetet fog ```kubectl logs``` látni.

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

Az Azure-díjak elkerülése érdekében el kell tisztítania a felesleges erőforrásokat.  Ha a fürtre már nincs szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését](../../aks/kubernetes-service-principal.md#additional-considerations) ismertető cikket. Ha felügyelt identitást használt, az identitást a platform kezeli, és nem igényel eltávolítást.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [férhet hozzá a Kubernetes webes irányítópultjához](../../aks/kubernetes-dashboard.md) az AKS-fürthöz
- Útmutató a folyamatos [üzembe helyezés engedélyezéséhez](../../aks/deployment-center-launcher.md)
- Ismerje meg, hogyan [skálázható a fürt](../../aks/tutorial-kubernetes-scale.md)
- Megtudhatja, hogyan kezelheti rugalmas [Postgres-kiszolgálóját](./quickstart-create-server-cli.md)
- Megtudhatja, [hogyan konfigurálhatja az adatbázis-kiszolgáló](./howto-configure-server-parameters-using-cli.md) kiszolgálóparaméterét.
