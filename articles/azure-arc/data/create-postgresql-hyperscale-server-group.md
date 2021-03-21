---
title: Azure Arc-kompatibilis, rugalmas skálázású PostgreSQL-kiszolgálócsoport létrehozása
description: Azure Arc-kompatibilis, rugalmas skálázású PostgreSQL-kiszolgálócsoport létrehozása
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 046f9d80c034e1ac1f2e7ffe144b4f389861b043
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687940"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc-kompatibilis, rugalmas skálázású PostgreSQL-kiszolgálócsoport létrehozása

Ez a dokumentum a PostgreSQL nagy kapacitású-kiszolgálócsoport Azure arc-beli létrehozásának lépéseit ismerteti.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Első lépések
Ha már ismeri az alábbi témaköröket, kihagyhatja ezt a bekezdést.
Fontos témaköröket érdemes elolvasni a létrehozás folytatása előtt:
- [Az Azure arc-kompatibilis adatszolgáltatások áttekintése](overview.md)
- [Csatlakozási módok és követelmények](connectivity.md)
- [Tárolási konfiguráció és Kubernetes-tárolási fogalmak](storage-configuration.md)
- [Kubernetes erőforrás-modell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Ha szeretné kipróbálni a teljes környezetet anélkül, hogy saját maga is kipróbálja magát, gyorsan megkezdheti az [Azure arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) az Azure Kubernetes szolgáltatásban (ak), az AWS rugalmas Kubernetes szolgáltatás (EKS), a Google Cloud Kubernetes Engine (GKE) vagy egy Azure-beli virtuális gépen.


## <a name="login-to-the-azure-arc-data-controller"></a>Bejelentkezés az Azure arc-adatkezelőbe

A példány létrehozása előtt először be kell jelentkeznie az Azure arc-adatvezérlőbe. Ha már bejelentkezett az adatkezelőbe, kihagyhatja ezt a lépést.

```console
azdata login
```

Ekkor a rendszer kérni fogja a felhasználónevet, a jelszót és a rendszernévtért.  

> Ha a parancsfájlt használta az adatvezérlő létrehozásához, akkor a névtérnek **ív** típusúnak kell lennie

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Előzetes és ideiglenes lépés csak a OpenShift-felhasználók számára
A következő lépésre való áttérés előtt implementálja ezt a lépést. Ha a PostgreSQL nagy kapacitású-kiszolgáló csoportját nem az alapértelmezett értékre szeretné telepíteni a Red Hat OpenShift, az alábbi parancsokat kell futtatnia a fürtön a biztonsági megkötések frissítéséhez. Ez a parancs megadja a szükséges jogosultságokat a PostgreSQL nagy kapacitású-kiszolgálói csoportot futtató szolgáltatásfiókok számára. Az Azure arc-adatkezelő üzembe helyezése során felvette a biztonsági környezeti megkötés (SCC) ív-adathalmazát.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**A kiszolgáló-csoport neve a következő lépésben létrehozandó kiszolgálócsoport neve.**

A OpenShift SCCs kapcsolatos további információkért tekintse meg a [OpenShift dokumentációját](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html). Most már megvalósíthatja a következő lépést.


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc-kompatibilis, rugalmas skálázású PostgreSQL-kiszolgálócsoport létrehozása

Az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozásához használja az parancsot, `azdata arc postgres server create` amelybe több paramétert kell átadnia.

A létrehozás időpontjában beállítható paraméterekkel kapcsolatos részletekért tekintse át a parancs kimenetét:
```console
azdata arc postgres server create --help
```

A fő paramétereknek a következőket kell figyelembe venniük:
- a telepíteni kívánt **kiszolgálócsoport neve** . Adja `--name` meg vagy `-n` utána egy olyan nevet, amelynek hossza nem haladhatja meg a 11 karaktert.

- a telepíteni kívánt **PostgreSQL-motor verziója** : alapértelmezés szerint a 12-es verzió. A 12-es verzió üzembe helyezéséhez kihagyhatja ezt a paramétert, vagy átadhatja a következő paraméterek egyikét: `--engine-version 12` vagy `-ev 12` . A 11-es verzió üzembe helyezéséhez jelezze a következőt: `--engine-version 11` vagy `-ev 11` .

- a felskálázáshoz telepíteni kívánt **munkavégző csomópontok száma és a** jobb teljesítmény elérése. A továbblépés előtt olvassa el a [postgres nagy kapacitású kapcsolatos fogalmakat](concepts-distributed-postgres-hyperscale.md). A telepítendő munkavégző csomópontok számának jelzéséhez használja a (z) paramétert, `--workers` vagy pedig `-w` egy 2 értéknél nagyobb vagy azzal egyenlő egész számot. Ha például egy 2 feldolgozó csomóponttal rendelkező kiszolgálói csoportot szeretne üzembe helyezni, jelezze vagy a következőt: `--workers 2` `-w 2` . Ez három hüvelyt hoz létre, egyet a koordinátori csomóponthoz/példányhoz, és kettőt a munkavégző csomópontok/példányok számára (egyet az egyes munkavállalók számára).

- **azok a tárolási osztályok** , amelyeket használni szeretne a kiszolgálócsoport számára. Fontos, hogy a tárolási osztályt akkor állítsa be, amikor egy kiszolgálócsoport központi telepítését végzi, mivel ez a telepítés után nem módosítható. Ha a telepítés után módosítani szeretné a tárolási osztályt, ki kell bontania az adatgyűjtést, törölnie kell a kiszolgálói csoportot, létre kell hoznia egy új számítógépcsoportot, és importálnia kell az adatait. Megadhatja az adatokat, naplókat és biztonsági másolatokat használó tárolási osztályokat is. Alapértelmezés szerint, ha nem jelöli meg a tárolási osztályokat, a rendszer az adatvezérlő tárolási osztályait fogja használni.
    - az adattárolási osztály beállításához adja meg a paramétert, `--storage-class-data` vagy `-scd` utána a tárolási osztály nevét.
    - a naplók tárolási osztályának beállításához adja meg a paramétert, `--storage-class-logs` vagy a `-scl` tárolási osztály neve után.
    - a biztonsági másolatok tárolási osztályának beállítása: ebben az előzetes verzióban az Azure arc engedélyezve PostgreSQL nagy kapacitású kétféleképpen állíthatja be a tárolási osztályokat attól függően, hogy milyen típusú biztonsági mentési/visszaállítási műveleteket szeretne végezni. Dolgozunk a tapasztalatok egyszerűsítésén. Egy tárolási osztályt vagy egy mennyiségi jogcím csatlakoztatását kell megjelölnie. A kötet jogcímek csatlakoztatása egy meglévő állandó mennyiségi jogcím (ugyanabban a névtérben) és a kötet típusa (és a kötet típusától függően választható metaadatok) pár, kettősponttal elválasztva. A rendszer az állandó kötetet minden Pod-ban csatlakoztatja a PostgreSQL-kiszolgáló csoport számára.
        - Ha azt szeretné, hogy csak a teljes adatbázis-visszaállítást tervezze meg, állítsa be a paramétert, vagy adja meg a `--storage-class-backups` `-scb` tárolási osztály nevét.
        - Ha azt tervezi, hogy a teljes adatbázis-visszaállítást és az időpontot is visszaállítja, állítsa be a paramétert, vagy adja meg a `--volume-claim-mounts` `-vcm` kötet jogcíme és a kötet típusa nevét.

Vegye figyelembe, hogy a Create parancs végrehajtásakor a rendszer kérni fogja az alapértelmezett rendszergazda felhasználó jelszavának megadását `postgres` . Az adott felhasználó neve nem módosítható ebben az előzetes verzióban. Az interaktív kérdés kihagyható úgy, hogy a `AZDATA_PASSWORD` create parancs futtatása előtt beállítja a munkamenet környezeti változóját.

### <a name="examples"></a>Példák

**Ha a postgres01 nevű postgres-verzióhoz tartozó, 2 feldolgozó csomóponttal rendelkező kiszolgálót kíván üzembe helyezni az adatkezelővel megegyező tárolási osztályokat használva, futtassa a következő parancsot:**
```console
azdata arc postgres server create -n postgres01 --workers 2
```

**Ha egy postgres01 nevű postgres-verzióhoz tartozó, 2 feldolgozó csomóponttal rendelkező kiszolgálót szeretne üzembe helyezni, amely ugyanazokat a tárolási osztályokat használja, mint az adat-és naplófájlok adatkezelője, de az adott tárolási osztálya a teljes visszatárolást és az időpontot állítja vissza, használja a következő lépéseket:**

 Ez a példa azt feltételezi, hogy a kiszolgálócsoport egy Azure Kubernetes Service (ak) fürtben üzemel. Ez a példa a azurefile-prémium szintű tárolási osztály nevét használja. Az alábbi példát úgy állíthatja be, hogy az megfeleljen a környezetnek. Vegye figyelembe, hogy ehhez a konfigurációhoz **AccessModes ReadWriteMany szükséges** .  

Először hozzon létre egy YAML-fájlt, amely tartalmazza a PVC biztonsági mentésének lenti leírását, és nevezze el CreateBackupPVC. YML például:
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

Ezután hozzon létre egy PVC-t a YAML fájlban tárolt definíció használatával:

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

Ezután hozza létre a kiszolgálói csoportot:

```console
azdata arc postgres server create -n postgres01 --workers 2 -vcm backup-pvc:backup
```

> [!IMPORTANT]
> - Olvassa el a [biztonsági mentéssel és visszaállítással kapcsolatos jelenlegi korlátozásokat](limitations-postgresql-hyperscale.md#backup-and-restore).


> [!NOTE]  
> - Ha az adatvezérlőt `AZDATA_USERNAME` `AZDATA_PASSWORD` a és a munkamenet-környezeti változók használatával telepítette ugyanabban a terminál-munkamenetben, akkor a `AZDATA_PASSWORD` rendszer a PostgreSQL nagy kapacitású-kiszolgálócsoport üzembe helyezéséhez használja az értékeket. Ha inkább egy másik jelszót szeretne használni, vagy (1) frissítse a vagy a `AZDATA_PASSWORD` (2) értéket, törölje a `AZDATA_PASSWORD` környezeti változót, vagy (3) törölje annak értékét, hogy a rendszer a kiszolgáló létrehozásakor interaktív módon adja meg a jelszót.
> - A PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása nem fogja azonnal regisztrálni az erőforrásokat az Azure-ban. Az [erőforrás-leltár](upload-metrics-and-logs-to-azure-monitor.md)  vagy a [használati adatok](view-billing-data-in-azure.md) Azure-ba való feltöltése során az erőforrások az Azure-ban lesznek létrehozva, és az erőforrások megjelennek a Azure Portalban.



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Az ív-adatvezérlőben üzembe helyezett PostgreSQL nagy kapacitású-kiszolgálócsoportok listázása

Az ív-adatvezérlőben üzembe helyezett PostgreSQL nagy kapacitású-kiszolgálócsoportok listázásához futtassa a következő parancsot:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Végpontok beszerzése az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálói csoportokhoz való kapcsolódáshoz

A PostgreSQL-kiszolgálócsoport végpontjait a következő parancs futtatásával tekintheti meg:

```console
azdata arc postgres endpoint list -n <server group name>
```
Például:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

A PostgreSQL-példány végpontjának használatával kapcsolódhat a PostgreSQL nagy kapacitású-kiszolgáló csoportjához a kedvenc eszközéről:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [Pgcli](https://www.pgcli.com/) psql, pgAdmin stb. Ha így tesz, csatlakozik a koordinátor csomóponthoz/példányhoz, amely gondoskodik a lekérdezés átirányításáról a megfelelő munkavégző csomópontokra/példányokra, ha elosztott táblákat hozott létre. További részletekért olvassa el az [Azure arc-kompatibilis PostgreSQL-nagy kapacitású kapcsolatos fogalmakat](concepts-distributed-postgres-hyperscale.md).

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Speciális Megjegyzés az Azure-beli virtuális gépek üzembe helyezéséről

Ha Azure-beli virtuális gépet használ, a végpont IP-címe nem fogja megjeleníteni a _nyilvános_ IP-címet. A nyilvános IP-cím megkereséséhez használja a következő parancsot:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Ezután összekapcsolhatja a nyilvános IP-címet a porttal a csatlakozás elvégzéséhez.

Előfordulhat, hogy a hálózati biztonsági átjárón (NSG) keresztül fel kell tenni a PostgreSQL nagy kapacitású-kiszolgáló csoportjának portját is. A (NSG) szolgáltatáson keresztüli adatforgalom engedélyezéséhez hozzá kell adnia egy olyan szabályt, amely a következő paranccsal végezhető el:

Egy szabály beállításához ismernie kell a NSG nevét. A NSG az alábbi parancs használatával határozhatja meg:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Ha megkapta a NSG nevét, a következő parancs használatával adhat hozzá tűzfalszabály-szabályt. A példában szereplő értékek egy NSG-szabályt hoznak létre a 30655-es porthoz, és lehetővé teszik a csatlakozást **bármely** forrás IP-címről.  Ez nem az ajánlott biztonsági eljárás!  Az ügyfél IP-címére vagy a csoport vagy a szervezet IP-címeire jellemző IP-címtartomány megadásával jobban lezárhatja a dolgokat.

Cserélje le a--Destination-port-Ranges paraméter értékét a lenti "azdata arc postgres Server List" parancsból kapott portszámra.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Csatlakozás az Azure Data Studióval

Nyissa meg Azure Data Studiot, és kapcsolódjon a példányhoz a fenti külső végpont IP-címével és portszámával, valamint a példány létrehozásakor megadott jelszóval.  Ha a PostgreSQL nem érhető el a *kapcsolattípus* legördülő listájában, akkor a PostgreSQL-bővítményt a Bővítmények lapon megkeresve keresse meg a PostgreSQL-t.

> [!NOTE]
> A port számának megadásához a kapcsolódási panelen kattintson a [speciális] gombra.

Ne feledje, hogy ha Azure-beli virtuális gépet használ, szüksége lesz a _nyilvános_ IP-címére, amely a következő parancs segítségével érhető el:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Kapcsolat a psql

A PostgreSQL nagy kapacitású-kiszolgáló csoport eléréséhez adja át a fentiekben lekért PostgreSQL nagy kapacitású-kiszolgálócsoport külső végpontját:

Most már csatlakozhat bármelyik psql:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Következő lépések

- Olvassa el a Azure Database for PostgreSQL nagy kapacitású kapcsolatos fogalmakat és útmutatókat az adatok több PostgreSQL nagy kapacitású-csomóponton való terjesztéséhez, valamint a jobb teljesítmény kihasználása érdekében:
    * [Csomópontok és táblák](../../postgresql/concepts-hyperscale-nodes.md)
    * [Alkalmazás típusának meghatározása](../../postgresql/concepts-hyperscale-app-type.md)
    * [Elosztási oszlop kiválasztása](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tábla közös elhelyezése](../../postgresql/concepts-hyperscale-colocation.md)
    * [Táblák elosztása és módosítása](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Több-bérlős adatbázis tervezése](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Valós idejű elemzési irányítópult kialakítása](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* A fenti dokumentumokban hagyja ki a **bejelentkezett szakaszt a Azure Portalba**, & **hozzon létre egy Azure Database for PostgreSQL-nagy kapacitású (Citus)**. Implementálja az Azure arc üzembe helyezésének hátralévő lépéseit. Ezek a részek Azure Database for PostgreSQL az Azure-felhőben nagy kapacitású (Citus) jellemzőek, de a dokumentumok egyéb részei közvetlenül alkalmazhatók az Azure arc-kompatibilis PostgreSQL-nagy kapacitású.

- [Az Azure-ív vertikális felskálázása a PostgreSQL nagy kapacitású-kiszolgálócsoport számára](scale-out-postgresql-hyperscale-server-group.md)
- [Tárolási konfiguráció és Kubernetes-tárolási fogalmak](storage-configuration.md)
- [Állandó mennyiségi jogcímek kiterjesztése](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes erőforrás-modell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
