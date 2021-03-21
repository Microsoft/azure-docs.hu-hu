---
title: Adatvezérlő létrehozása a Kubernetes-eszközök használatával
description: Adatvezérlő létrehozása a Kubernetes-eszközök használatával
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 500587dc6564aa55eb430365eb67bb958bbd2482
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519979"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Azure arc-adatkezelő létrehozása a Kubernetes-eszközökkel

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Tekintse át a következő témakört: [Az Azure arc-adatkezelő létrehozása](create-data-controller.md) az áttekintési információkhoz.

Az Azure arc-adatkezelő Kubernetes eszközökkel történő létrehozásához telepítenie kell a Kubernetes-eszközöket.  Az ebben a cikkben szereplő példák az alábbi módon használhatók `kubectl` , de más Kubernetes-eszközökkel, például a Kubernetes-irányítópulttal is használhatók, `oc` vagy `helm` Ha ismeri ezeket az eszközöket és a Kubernetes YAML/JSON-t.

[A kubectl eszköz telepítése](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> A lenti Azure arc-adatkezelő létrehozásához szükséges néhány lépés a Kubernetes-fürt rendszergazdai engedélyeinek megkövetelése.  Ha Ön nem Kubernetes-Fürtfelügyelő, akkor a Kubernetes-fürt rendszergazdájának kell elvégeznie ezeket a lépéseket az Ön nevében.

### <a name="cleanup-from-past-installations"></a>Korábbi telepítések karbantartása

Ha korábban már telepítette az Azure arc-adatkezelőt, ugyanazon a fürtön, és törölte az Azure arc-adatkezelőt a `azdata arc dc delete` paranccsal, előfordulhat, hogy bizonyos fürtözött objektumokat is törölni kell. Futtassa az alábbi parancsokat az Azure arc-adatkezelő fürt szintjének törléséhez:

```console
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
```

## <a name="overview"></a>Áttekintés

Az Azure arc-adatkezelő létrehozása a következő magas szintű lépésekkel rendelkezik:
1. Hozza létre az egyéni erőforrás-definíciókat az ív adatvezérlőhöz, az Azure SQL felügyelt példányához és a PostgreSQL nagy kapacitású. **[A Kubernetes-fürt rendszergazdai engedélyei szükségesek]**
2. Hozzon létre egy névteret, amelyben az adatvezérlő létre lesz hozva. **[A Kubernetes-fürt rendszergazdai engedélyei szükségesek]**
3. Hozza létre a bootstrapper szolgáltatást, beleértve a replikakészlet, a szolgáltatásfiók, a szerepkör és a szerepkör kötését.
4. Hozzon létre egy titkos kulcsot az adatkezelő rendszergazdai felhasználónevéhez és jelszavához.
5. Hozza létre az adatkezelőt.
   
## <a name="create-the-custom-resource-definitions"></a>Egyéni erőforrás-definíciók létrehozása

Futtassa az alábbi parancsot az egyéni erőforrás-definíciók létrehozásához.  **[A Kubernetes-fürt rendszergazdai engedélyei szükségesek]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>Hozzon létre egy névteret, amelyben az adatkezelő létrehozva lesz

Az alábbihoz hasonló parancs futtatásával hozzon létre egy új, dedikált névteret, amelyben az adatkezelő létrejön.  Ebben a példában és a jelen cikkben szereplő példák további részében a rendszer a névtér nevét `arc` fogja használni. Ha más nevet szeretne használni, használja az egész nevet.

```console
kubectl create namespace arc
```

Ha más személyek ezt a névteret fogják használni, amely nem a fürt rendszergazdája, javasoljuk, hogy hozzon létre egy névtér-rendszergazdai szerepkört, és adja meg a szerepkört a felhasználóknak egy szerepkör-kötésen keresztül.  A névtér rendszergazdájának teljes körű engedélyekkel kell rendelkeznie a névtérben.  További információt a felhasználók számára részletesebb szerepköralapú hozzáférés biztosítása című témakörben talál.

## <a name="create-the-bootstrapper-service"></a>A bootstrapper szolgáltatás létrehozása

A bootstrapper szolgáltatás kezeli a bejövő kérelmeket olyan egyéni erőforrások létrehozásához, szerkesztéséhez és törléséhez, mint például az adatkezelő, az SQL felügyelt példány vagy a PostgreSQL nagy kapacitású-kiszolgálócsoport.

A következő parancs futtatásával hozzon létre egy bootstrapper-szolgáltatást, egy szolgáltatásfiókot a bootstrapper szolgáltatáshoz, valamint egy szerepkör-és szerepkör-kötést a bootstrapper szolgáltatás fiókhoz.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/bootstrapper.yaml
```

Ellenőrizze, hogy fut-e az bootstrapper Pod a következő paranccsal.  Előfordulhat, hogy néhány alkalommal futtatnia kell, amíg az állapot meg nem változik `Running` .

```console
kubectl get pod --namespace arc
```

A bootstrapper. YAML sablonfájl alapértelmezett értéke a bootstrapper-tároló rendszerképének lekérése a Microsoft Container Registryból (MCR).  Ha a környezet nem fér hozzá közvetlenül a Microsoft Container Registryhoz, a következőket teheti:
- Kövesse az alábbi lépéseket a [tároló lemezképének lekéréséhez a Microsoft Container Registry, és küldje el őket egy privát tároló-beállításjegyzékbe](offline-deployment.md).
- [Hozzon létre egy rendszerkép-lekérési titkot](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) a privát tároló beállításjegyzékében.
- Vegyen fel egy rendszerképet, amely titkos kulcsot hoz létre a bootstrapper-tárolóhoz. Lásd az alábbi példát.
- Módosítsa a bootstrapper rendszerképének helyét. Lásd az alábbi példát.

Az alábbi példa azt feltételezi, hogy létrehozott egy rendszerkép-lekérési titkos nevet `regcred` a Kubernetes dokumentációjában jelzett módon.

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-dec-2020  <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>Titkos kód létrehozása az adatkezelő rendszergazdája számára

Az adatkezelő rendszergazdájának felhasználónevét és jelszavát a rendszer az adatkezelő API-hoz történő hitelesítésre használja a rendszergazdai függvények végrehajtásához.  Válassza ki a biztonságos jelszót, és ossza meg csak azokkal, akiknek szükségük van a fürt rendszergazdai jogosultságára.

A titkos Kubernetes a rendszer Base64 kódolású karakterláncként tárolja – egyet a felhasználónévhez és egyet a jelszóhoz.

Az online eszközzel Base64-kódolást használhat a kívánt felhasználónévvel és jelszóval, vagy a platformtól függően beépített CLI-eszközöket is használhat.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

A Felhasználónév és a jelszó kódolása után létrehozhat egy fájlt a [sablonfájl](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/controller-login-secret.yaml) alapján, és lecserélheti a felhasználónevet és a jelszót.

Ezután futtassa a következő parancsot a titkos kulcs létrehozásához.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>Az adatvezérlő létrehozása

Most már készen áll az adatvezérlő létrehozására.

Először hozzon létre egy másolatot a [sablonból](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/data-controller.yaml) a számítógépen, hogy az egyes beállítások módosíthatók legyenek.

Szükség szerint szerkessze a következőket:

**SZÜKSÉGES**
- **hely**: módosítsa ezt úgy, hogy az az Azure-beli hely legyen, ahol a rendszer az adatvezérlő _metaadatait_ tárolja.  Az elérhető Azure-helyszínek listáját az [adatkezelő létrehozása – áttekintés](create-data-controller.md) című cikkben tekintheti meg.
- **resourceGroup**: az Azure-erőforráscsoport, amelyben létre szeretné hozni az adatkezelő Azure-erőforrását Azure Resource Manager.  Ez az erőforráscsoport általában már létezik, de az adatok Azure-ba történő feltöltésekor nem szükséges.
- **előfizetés**: annak az előfizetésnek az Azure-előfizetésének GUID azonosítója, amelyhez az Azure-erőforrásokat létre kívánja hozni.

**AJÁNLOTT ÁTTEKINTENI ÉS MÓDOSÍTANI AZ ALAPÉRTELMEZETT ÉRTÉKEKET**
- **tárterület.. Osztálynév**: az adatkezelő és a naplófájlok számára használandó tárolási osztály.  Ha nem biztos abban, hogy a rendelkezésre álló tárolási osztályok a Kubernetes-fürtben találhatók, futtassa a következő parancsot: `kubectl get storageclass` .  Az alapértelmezett érték azt `default` feltételezi, hogy a tárolási osztály létezik, és a neve `default` nem az alapértelmezett tárolási osztály.   Megjegyzés: két osztálynév-beállítást kell beállítani a kívánt tárolási osztályra – egyet az adathalmazra, egyet pedig a naplókhoz.
- **ServiceType**: módosítsa a szolgáltatás típusát arra az értékre, `NodePort` Ha nem használ terheléselosztó.  Megjegyzés: két serviceType-beállítást kell módosítani.
- Az Azure Red Hat OpenShift vagy a Red Hat OpenShift Container platformon az adatkezelő létrehozása előtt a biztonsági környezeti korlátozást kell alkalmaznia. Kövesse a [biztonsági környezeti korlátozás alkalmazása az Azure arc-kompatibilis adatszolgáltatásokra a OpenShift-on](how-to-apply-security-context-constraint.md)című témakör utasításait.
- **Biztonság** Az Azure Red Hat OpenShift vagy a Red Hat OpenShift Container platform esetében cserélje le a `security:` beállításokat a következő értékekre az adatvezérlő YAML fájljában. 

```yml
  security:
    allowDumps: true
    allowNodeMetricsCollection: false
    allowPodMetricsCollection: false
    allowRunAsRoot: false
```

**VÁLASZTHATÓ**
- **Name (név**): az adatvezérlő alapértelmezett neve `arc` , de szükség esetén módosíthatja.
- **DisplayName**: ezt a tulajdonságot a fájl tetején található name attribútummal megegyező értékre kell beállítani.
- **beállításjegyzék**: a Microsoft Container Registry az alapértelmezett.  Ha a képeket a Microsoft Container Registryból húzza át, és [egy privát tároló-beállításjegyzékbe szeretné őket](offline-deployment.md)leküldeni, itt adja meg a beállításjegyzék IP-címét vagy DNS-nevét.
- **dockerRegistry**: a kép lekérési titka, hogy szükség esetén lekérje a képeket egy privát tároló-beállításjegyzékből.
- **adattár**: a Microsoft Container Registry alapértelmezett tárháza `arcdata` .  Ha privát tároló-beállításjegyzéket használ, adja meg az Azure arc-kompatibilis adatszolgáltatások tárolójának rendszerképeit tartalmazó mappa/tárház elérési útját.
- **imageTag**: a sablon aktuális legfrissebb címkéje alapértelmezés szerint a sablonban van, de ha régebbi verziót szeretne használni, akkor módosíthatja azt.

A következő példa egy befejezett adatkezelő YAML-fájlt mutat be. Frissítse a környezet példáját a követelmények és a fenti információk alapján.

```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: arc-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-dec-2020 
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

Mentse a szerkesztett fájlt a helyi számítógépre, és futtassa a következő parancsot az adatvezérlő létrehozásához:

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>A létrehozási állapot figyelése

A vezérlő létrehozása több percet is igénybe vehet. A következő parancsokkal figyelheti a folyamatot egy másik terminál ablakban:

> [!NOTE]
>  Az alábbi parancsok azt feltételezik, hogy létrehozott egy adatkezelőt és egy Kubernetes-névteret a névvel `arc` .  Ha más névtér-vagy adatvezérlő-nevet használt, akkor a nevet lecserélheti `arc` a nevét.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Egy adott Pod létrehozási állapotát is megtekintheti egy, az alábbihoz hasonló parancs futtatásával.  Ez különösen hasznos az esetleges problémák elhárításához.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

A Azure Data Studio készült Azure arc-bővítmény egy jegyzetfüzetet biztosít, amellyel megtudhatja, hogyan állíthatja be az Azure arc-kompatibilis Kubernetes, és hogyan konfigurálhatja egy olyan git-tárház figyelésére, amely egy minta SQL-alapú felügyelt példány YAML-fájlt tartalmaz. Ha minden csatlakoztatva van, a rendszer egy új SQL felügyelt példányt helyez üzembe a Kubernetes-fürtön.

Lásd: az **SQL felügyelt példányának üzembe helyezése** az Azure arc-kompatibilis Kubernetes és a Flux notebook használatával a Azure Data Studio Azure arc-bővítményében.

## <a name="troubleshooting-creation-problems"></a>A létrehozási problémák elhárítása

Ha bármilyen problémába ütközik a létrehozással kapcsolatban, tekintse meg a [hibaelhárítási útmutatót](troubleshoot-guide.md).

## <a name="next-steps"></a>Következő lépések

- [SQL felügyelt példány létrehozása a Kubernetes-natív eszközök használatával](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása a Kubernetes natív eszközeivel](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
