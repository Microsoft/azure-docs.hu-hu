---
title: A PostgreSQL nagy kapacitású-kiszolgálócsoportok hibáinak megoldása
description: A PostgreSQL nagy kapacitású-kiszolgálócsoportok hibáinak megoldása Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: caaab07200a8631935a2b5d5368a0c16ea9a60c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92320216"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>A PostgreSQL nagy kapacitású-kiszolgálócsoportok hibaelhárítása
Ez a cikk néhány olyan technikát ismertet, amelyet a kiszolgálói csoport hibaelhárításához használhat. A cikk mellett érdemes elolvasnia, hogyan használhatja a [Kibana](monitor-grafana-kibana.md) a naplók keresésére vagy a [Grafana](monitor-grafana-kibana.md) használatával megjelenítheti a kiszolgálói csoport metrikáit. 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>További részletek az azdata-parancsok végrehajtásáról
A **--Debug** paramétert hozzáadhatja bármilyen azdata-parancshoz, amelyet végrehajt. Ekkor a konzolon további információk jelennek meg a parancs végrehajtásáról. Hasznos lehet az adott parancs működésének megértéséhez szükséges részletek beolvasása.
Futtathatja például a következőt:
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

vagy
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

Emellett a--help parancsot is használhatja bármely azdata-parancshoz egy adott parancs paramétereinek megjelenítéséhez. Például:
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>Adatkezelő és a kiszolgálócsoportok naplóinak összegyűjtése
Olvassa el a [naplók Azure arc-kompatibilis adatszolgáltatásokhoz való](troubleshooting-get-logs.md) beolvasásáról szóló cikket.



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Interaktív hibaelhárítás a Azure Data Studio Jupyter notebookokkal
Jegyzetfüzetek használatával eljárásokat is dokumentálhat a teendőket/utasításokat ismertető Markdown-tartalmak belefoglalásával. Végrehajtható kódot is megadhat az eljárások automatizálásához.  Ez a minta az általános üzemeltetési eljárásoktól kezdve a hibaelhárítási útmutatókig jól használható.

Tegyük fel például, hogy a PostgreSQL nagy kapacitású-kiszolgálócsoport hibaelhárítása során problémák merülhetnek fel Azure Data Studio használatával.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>Eszközök telepítése

Telepítse a Azure Data Studiot, `kubectl` és azon [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] az ügyfélszámítógépen, amelyet a jegyzetfüzetnek a Azure Data Studio-ben való futtatásához használ. Ehhez kövesse az [ügyféleszközök telepítése](install-client-tools.md) című témakör útmutatását.

### <a name="update-the-path-environment-variable"></a>A PATH környezeti változó frissítése

Győződjön meg arról, hogy ezek az eszközök bárhonnan meghívhatók ezen az ügyfélszámítógépen. Például egy Windows-ügyfélgépen frissítse a PATH rendszerkörnyezeti változót, és adja hozzá azt a mappát, amelyben a kubectl telepítette.

### <a name="sign-in-with-azure-data-cli-azdata"></a>Bejelentkezés a [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Jelentkezzen be az ív-adatkezelőből erről az ügyfélszámítógépről, és a Azure Data Studio elindítása előtt. Ehhez a következőhöz hasonló parancsot kell futtatnia:

```console
azdata login --endpoint https://<IP address>:<port>
```

A helyére írja `<IP address>` be a Kubernetes-fürt IP-címét, valamint `<port>` azt a portot, amelyen a Kubernetes figyel. A rendszer kérni fogja a Felhasználónév és a jelszó megadását. További részletekért futtassa a következőt: _

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Jelentkezzen be a Kubernetes-fürtbe a kubectl

Ehhez érdemes lehet az [ebben](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) a blogbejegyzésben megadott példa parancsokat használni.
A következőhöz hasonló parancsokat kell futtatnia:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>A hibaelhárítási jegyzetfüzet

Indítsa el Azure Data Studio, és nyissa meg a hibaelhárítási jegyzetfüzetet. 

Hajtsa végre a következő témakörben ismertetett lépéseket:  [033-Manage-postgres-with-AzureDataStudio.MD](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) :

1. Kapcsolódás az ív-adatkezelőhöz
2. Kattintson a jobb gombbal a postgres-példányra, és válassza a **[kezelés]** lehetőséget.
3. Válassza a **[problémák diagnosztizálása és megoldása] irányítópultot** .
4. Válassza a **[hibakeresés] hivatkozást**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio – a PostgreSQL hibaelhárítási Jegyzetfüzetének megnyitása":::

A **TSG100 – az Azure arc-kompatibilis PostgreSQL nagy kapacitású hibaelhárító jegyzetfüzete** a következőket nyitja meg: :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio – a PostgreSQL hibaelhárítási notebookjának használata":::

#### <a name="run-the-scripts"></a>Parancsfájlok futtatása
Válassza az összes futtatása gombot a lap tetején, hogy egyszerre hajtsa végre a jegyzetfüzetet, vagy az egyes kódrészleteket egyenként is végrehajthatja.

Megtekintheti a programkódok végrehajtásának kimenetét az esetleges problémákhoz.

A jegyzetfüzettel kapcsolatban a gyakori problémák felismerésével és megoldásával kapcsolatos további részleteket is felvesszük.

## <a name="next-step"></a>Következő lépés
- További információ [Az Azure arc-kompatibilis adatszolgáltatások naplófájljainak beszerzéséről](troubleshooting-get-logs.md)
- További információ a [Kibana](monitor-grafana-kibana.md)
- További információ a Grafana-mel végzett [figyelésről](monitor-grafana-kibana.md)
- Saját jegyzetfüzetek létrehozása
