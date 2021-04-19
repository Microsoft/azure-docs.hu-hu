---
title: A magas skálázású PostgreSQL-kiszolgálócsoportok hibaelhárítása
description: Magas skálázású PostgreSQL-kiszolgálócsoportok hibaelhárítása Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: caaab07200a8631935a2b5d5368a0c16ea9a60c5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "92320216"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>A magas skálázású PostgreSQL-kiszolgálócsoportok hibaelhárítása
Ez a cikk a kiszolgálócsoport hibaelhárítására használható néhány technikát ismertet. A cikken kívül elolvashatja, hogyan kereshet a naplókban a [Kibana](monitor-grafana-kibana.md) használatával, vagy hogyan vizualizálhatja a kiszolgálócsoport metrikákat a [Grafana](monitor-grafana-kibana.md) használatával. 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>További részletek az azdata parancs végrehajtásával kapcsolatban
A **--debug** paramétert bármely futtatott azdata parancshoz hozzáadhatja. Ez a parancs végrehajtásával kapcsolatos további információkat jelenít meg a konzolon. Hasznosnak kell lennie, ha részleteket keres, amelyek segítenek megérteni a parancs viselkedését.
Futtathatja például a következőt:
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

vagy
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

Emellett használhatja a --help paramétert bármely azdata parancshoz, hogy megjelenítsen néhány súgót, egy adott parancs paramétereinek listáját. Például:
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>Az adatkezelő és a kiszolgálócsoportok naplóinak gyűjtése
Olvassa el a naplók [lekért Azure Arc-kompatibilis adatszolgáltatások](troubleshooting-get-logs.md)



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Interaktív hibaelhárítás Jupyter-notebookokkal a Azure Data Studio
Jegyzetfüzetek használatával eljárásokat is dokumentálhat a teendőket/utasításokat ismertető Markdown-tartalmak belefoglalásával. Végrehajtható kódot is megadhat az eljárások automatizálásához.  Ez a minta az általános üzemeltetési eljárásoktól kezdve a hibaelhárítási útmutatókig jól használható.

Tegyük fel például, hogy egy olyan, nagy skálázású PostgreSQL-kiszolgálócsoport hibaelhárításával van probléma, amely problémákba Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>Eszközök telepítése

Telepítse Azure Data Studio, majd a notebook futtatásához használt ügyfélszámítógépre a `kubectl` [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Azure Data Studio. Ehhez kövesse az Ügyféleszközök [telepítése útmutatót.](install-client-tools.md)

### <a name="update-the-path-environment-variable"></a>A PATH környezeti változó frissítése

Győződjön meg arról, hogy ezek az eszközök bárhonnan meghívhatóak ezen az ügyfélszámítógépen. Windows rendszerű ügyfélszámítógépen például frissítse a PATH rendszerkörnyezeti változót, és adja hozzá azt a mappát, amelybe a kubectl-t telepítette.

### <a name="sign-in-with-azure-data-cli-azdata"></a>Bejelentkezés a következővel: [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Jelentkezzen be az Arc-adatkezelőbe erről az ügyfélszámítógépről, és mielőtt elindítja a Azure Data Studio. Ehhez futtatassa a következő parancsot:

```console
azdata login --endpoint https://<IP address>:<port>
```

Cserélje `<IP address>` le a helyére a Kubernetes-fürt IP-címét, valamint azt a portot, amelyen a `<port>` Kubernetes figyel. A rendszer kérni fogja a felhasználónevet és a jelszót. További részletekért futtassa a következőt: _

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Jelentkezzen be a Kubernetes-fürtbe a kubectl-sel

Ehhez használhatja a blogbejegyzésben található [példaparancsokat.](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/)
A következő parancsokat kell futtatnia:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>A hibaelhárítási jegyzetfüzet

Indítsa Azure Data Studio és nyissa meg a hibaelhárítási jegyzetfüzetet. 

Hajtsa végre a [](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) 033-manage-Postgres-with-AzureDataStudio.md lépéseit:

1. Csatlakozás az Arc-adatkezelőhöz
2. Válassza ki a jobb oldalon a Postgres-példányt, majd válassza a **[Kezelés] lehetőséget**
3. Válassza a **[Problémák diagnosztizálása és megoldása] irányítópultot**
4. Válassza a **[Hibaelhárítás] hivatkozást**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio – A PostgreSQL hibaelhárítási jegyzetfüzetének megnyitása":::

Megnyílik a **TSG100 – A Azure Arc-kompatibilis PostgreSQL – rugalmas skálázás hibaelhárító jegyzetfüzete:** :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio – PostgreSQL"::: hibaelhárítási jegyzetfüzet használata

#### <a name="run-the-scripts"></a>A szkriptek futtatása
A felül található "Összes futtatása" gombra kattintva egyszerre futtathatja a jegyzetfüzetet, vagy egyesére is végrehajthatja az egyes kódcellák lépéseit.

Tekintse meg a kódcellák végrehajtásának kimenetét az esetleges problémák megtekintéséhez.

Idővel további részleteket is hozzáadunk a jegyzetfüzethez a gyakori problémák felismerésének és megoldásának részleteivel kapcsolatban.

## <a name="next-step"></a>Következő lépés
- További információ [a naplók lekért Azure Arc-kompatibilis adatszolgáltatások](troubleshooting-get-logs.md)
- További információ a [naplók Kibanával való keresésről](monitor-grafana-kibana.md)
- További információ a [Grafanával való monitorozásról](monitor-grafana-kibana.md)
- Saját jegyzetfüzetek létrehozása
