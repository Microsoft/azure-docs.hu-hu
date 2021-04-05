---
title: A OpenFaaS használata az Azure Kubernetes szolgáltatással (ak)
description: Ismerje meg, hogyan helyezhet üzembe és használhat OpenFaaS egy Azure Kubernetes Service-(ak-) fürtön kiszolgáló nélküli függvények létrehozásához tárolókkal.
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 319107127b79383fc3b49f0eeb856a0e6c5b09f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92747763"
---
# <a name="using-openfaas-on-aks"></a>OpenFaaS használata az AK-on

A [OpenFaaS][open-faas] a kiszolgáló nélküli függvények a tárolók használatával történő létrehozásához használható keretrendszer. Nyílt forráskódú projektként nagy léptékű bevezetést szerzett a Közösségen belül. Ez a dokumentum részletesen ismerteti a OpenFaas telepítését és használatát egy Azure Kubernetes Service-(ak-) fürtön.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához a következőkre lesz szüksége.

* A Kubernetes alapszintű ismerete.
* Az Azure Kubernetes Service (ak) fürt és a fejlesztési rendszeren konfigurált AK-beli hitelesítő adatok.
* Az Azure CLI telepítve van a fejlesztői rendszeren.
* A rendszerre telepített git parancssori eszközök.

## <a name="add-the-openfaas-helm-chart-repo"></a>A OpenFaaS Helm chart-adattár hozzáadása

[https://shell.azure.com](https://shell.azure.com)Nyissa meg a Azure Cloud shellt a böngészőben.

A OpenFaaS megtartja a saját Helm-diagramjait, hogy naprakészek legyenek a legújabb módosításokkal.

```console
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>OpenFaaS üzembe helyezése

Helyes gyakorlat, hogy a OpenFaaS és a OpenFaaS függvényt a saját Kubernetes-névterében kell tárolni.

Hozzon létre egy névteret a OpenFaaS rendszer és a függvények számára:

```console
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

Jelszó létrehozása a OpenFaaS felhasználói felületi portálhoz és REST API:

```console
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

A titok értékét lekérheti a paranccsal `echo $PASSWORD` .

Az itt létrehozott jelszót a Helm diagram fogja használni az alapszintű hitelesítés engedélyezéséhez a OpenFaaS-átjárón, amely egy felhőalapú terheléselosztó keresztül elérhető az interneten.

A klónozott tárház tartalmazza a OpenFaaS Helm diagramját. Ezzel a diagrammal telepíthet OpenFaaS az AK-fürtbe.

```console
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

Kimenet:

```output
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

```console
kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

A rendszer létrehoz egy nyilvános IP-címet a OpenFaaS-átjáró eléréséhez. Az IP-cím lekéréséhez használja a [kubectl Get Service][kubectl-get] parancsot. Egy percet is igénybe vehet, hogy az IP-cím hozzá legyen rendelve a szolgáltatáshoz.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Kimeneti.

```output
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

A OpenFaaS rendszer teszteléséhez keresse meg a külső IP-címet a 8080-as porton `http://52.186.64.52:8080` ebben a példában. A rendszer kérni fogja, hogy jelentkezzen be. A jelszó beolvasásához írja be a következőt: `echo $PASSWORD` .

![OpenFaaS felhasználói felület](media/container-service-serverless/openfaas.png)

Végül telepítse a OpenFaaS CLI-t. Ez a példa a brewt használta, további lehetőségekért tekintse meg a [OPENFAAS CLI dokumentációját][open-faas-cli] .

```console
brew install faas-cli
```

Állítsa be `$OPENFAAS_URL` a fentiekben található nyilvános IP-címet.

Jelentkezzen be az Azure CLI-vel:

```console
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>Első függvény létrehozása

Most, hogy a OpenFaaS működőképes, hozzon létre egy függvényt a OpenFaas-portál használatával.

Kattintson az **új funkció telepítése** és a **Figlet** keresése elemre. Válassza ki a Figlet függvényt, majd kattintson a **telepítés** elemre.

![Képernyőfelvétel: az új függvény üzembe helyezése párbeszédpanel a keresési sorban lévő szöveg figlet.](media/container-service-serverless/figlet.png)

Használja a curl-t a függvény meghívásához. Cserélje le az IP-címet a következő példában az OpenFaas-átjáróval.

```console
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Kimenet:

```output
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Második függvény létrehozása

Most hozzon létre egy második függvényt. Ez a példa a OpenFaaS CLI használatával lesz üzembe helyezve, és tartalmaz egy egyéni tároló rendszerképet, és beolvassa az adatok egy Cosmos DB. A függvény létrehozása előtt több elemet is konfigurálni kell.

Először hozzon létre egy új erőforráscsoportot a Cosmos DB számára.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Helyezzen üzembe egy CosmosDB-példányt `MongoDB` . A példánynak egyedi névnek kell lennie, és frissítenie `openfaas-cosmos` kell valami egyedire a környezetében.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Szerezze be a Cosmos Database-beli kapcsolatok karakterláncát, és tárolja egy változóban.

Frissítse az `--resource-group` argumentum értékét az erőforráscsoport nevére, és az `--name` argumentumot a Cosmos db nevére.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Most töltse ki a Cosmos DBt a tesztelési adatokkal. Hozzon létre egy nevű fájlt `plans.json` , és másolja a következő JSON-t.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

A *mongoimport* eszköz használatával töltse be a CosmosDB-példányt az adattal.

Ha szükséges, telepítse a MongoDB-eszközöket. Az alábbi példa a Brew használatával telepíti ezeket az eszközöket, további lehetőségekért tekintse meg a [MongoDB dokumentációját][install-mongo] .

```console
brew install mongodb
```

Töltse be az adatbázist az adatbázisba.

```console
mongoimport --uri=$COSMOS -c plans < plans.json
```

Kimenet:

```output
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Futtassa a következő parancsot a függvény létrehozásához. Frissítse az argumentum értékét a `-g` OpenFaaS-átjáró címeként.

```console
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Az üzembe helyezést követően az újonnan létrehozott OpenFaaS-végpontot kell látnia a függvényhez.

```output
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Tesztelje a függvényt a curl használatával. Frissítse az IP-címet az OpenFaaS-átjáró címével.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Kimenet:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

A függvényt a OpenFaaS felhasználói felületén is tesztelheti.

![helyettesítő szöveg](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Következő lépések

Továbbra is megismerheti a OpenFaaS workshopot olyan gyakorlati laborok segítségével, amelyek olyan témaköröket ölelnek fel, mint például a saját GitHub-robot létrehozása, a titkok felhasználása, a metrikák megtekintése és az automatikus skálázás.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
