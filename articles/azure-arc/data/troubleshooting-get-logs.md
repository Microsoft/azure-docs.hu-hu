---
title: Naplók beszerzése az Azure arc-kompatibilis adatszolgáltatások hibakereséséhez
description: Ismerje meg, hogyan kérhet le naplófájlokat egy adatkezelőből az Azure arc-kompatibilis adatszolgáltatások hibakereséséhez.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0c4cff7583f08fe27649cee464fcef802cddd88f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93234043"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>Naplók beszerzése az Azure arc-kompatibilis adatszolgáltatások hibakereséséhez

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt a következőkre lesz szüksége:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. További információ: az [ügyféleszközök telepítése az Azure arc-adatszolgáltatások üzembe helyezéséhez és kezeléséhez](./install-client-tools.md).
* Rendszergazdai fiók az Azure arc-kompatibilis adatvezérlőbe való bejelentkezéshez.

## <a name="get-log-files"></a>Naplófájlok beolvasása

A szolgáltatási naplókat az összes hüvelybe vagy adott hüvelybe lekérheti hibaelhárítási célból. Az egyik módszer a szabványos Kubernetes-eszközök, például a `kubectl logs` parancs használata. Ebben a cikkben az eszközt fogja használni, amely megkönnyíti az [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] összes napló egyszeri beolvasását.

1. Jelentkezzen be az adatkezelőbe egy rendszergazdai fiókkal.

   ```console
   azdata login
   ```

2. A naplók kiírásához futtassa a következő parancsot:

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   Például:

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

Az adatkezelő létrehozza a naplófájlokat az aktuális munkakönyvtárban egy nevű alkönyvtárban `logs` . 

## <a name="options"></a>Beállítások

A `azdata arc dc debug copy-logs` parancs a következő beállításokat biztosítja a kimenet kezeléséhez:

* A naplófájlokat egy másik könyvtárba írja a `--target-folder` paraméter használatával.
* Tömörítse a fájlokat úgy, hogy kihagyja a `--skip-compress` paramétert.
* A memóriaképek kihagyása és a memóriaképek belefoglalása `--exclude-dumps` . Ezt a módszert csak akkor javasoljuk, ha Microsoft ügyfélszolgálata nem kérte a memóriaképek használatát. A memóriakép beszerzése megköveteli, hogy az adatvezérlő beállítása az adatkezelő létrehozásakor legyen `allowDumps` beállítva `true` .
* Szűrés a naplók összegyűjtéséhez csak egy adott Pod ( `--pod` ) vagy tároló ( `--container` ) néven.
* Egy adott egyéni erőforráshoz tartozó naplók gyűjtésének szűrése a és a paraméterek átadásával `--resource-kind` `--resource-name` . A `resource-kind` paraméter értékének az egyéni erőforrás-definíciós nevek egyikének kell lennie. Ezeket a neveket a parancs használatával kérheti le `kubectl get customresourcedefinition` .

Ezekkel a paraméterekkel a következő példában lecserélheti a t `<parameters>` : 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Például:

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

A következő mappa-hierarchia egy példa. Ez a pod name, a Container, majd a tárolón belüli címtár-hierarchia szerint van rendszerezve.

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>Következő lépések

[azdata arc DC hibakeresési naplók](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)
