---
title: Naplók és metrikák megtekintése a Kibana és a Grafana használatával
description: Naplók és metrikák megtekintése a Kibana és a Grafana használatával
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/08/2020
ms.topic: how-to
ms.openlocfilehash: cb53aba300b933c78d9ac2f5fc5cf8054f3413e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670001"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Naplók és metrikák megtekintése a Kibana és a Grafana használatával

A Kibana és a Grafana webes irányítópultok megállapításokat és átláthatóságot biztosítanak az Azure Arc-kompatibilis adatszolgáltatások által használt Kubernetes-névterekben.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Az Azure SQL felügyelt példányainak figyelése az Azure arc szolgáltatásban

A következő CLI-parancs futtatásával érheti el a naplókat és a figyelési irányítópultokat az ív használatára képes SQL felügyelt példányhoz. `azdata`

```bash

azdata arc sql endpoint list -n <name of SQL instance>

```
A megfelelő Grafana-irányítópultok a következők:

* "Az Azure SQL felügyelt példány Metrikái"
* "Gazdagép-csomópont Metrikái"
* "Gazda hüvely mérőszámai"


> [!NOTE]
>  Amikor a rendszer a Felhasználónév és a jelszó megadását kéri, adja meg az Azure arc-adatkezelő létrehozásakor megadott felhasználónevet és jelszót.

> [!NOTE]
>  A rendszer figyelmeztetést küld a tanúsítványra, mert az előzetes verzióban használt tanúsítványok önaláírt tanúsítványok.


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>Az Azure arc Azure Database for PostgreSQL nagy kapacitású figyelése

A PostgreSQL nagy kapacitású tartozó naplók és monitorozási irányítópultok eléréséhez futtassa az alábbi `azdata` CLI-parancsot

```bash

azdata arc postgres endpoint list -n <name of postgreSQL instance>

```

A kapcsolódó postgreSQL-irányítópultok a következők:

* "Postgres mérőszámok"
* "Postgres táblázat Metrikái"
* "Gazdagép-csomópont Metrikái"
* "Gazda hüvely mérőszámai"


## <a name="additional-firewall-configuration"></a>További tűzfal-konfiguráció

Attól függően, hogy hol telepítette az adatvezérlőt, előfordulhat, hogy meg kell nyitnia a portokat a tűzfalon a Kibana és Grafana végpontok eléréséhez.

Az alábbi példa bemutatja, hogyan teheti ezt meg egy Azure-beli virtuális gépen. Ezt akkor kell megtennie, ha a szkript használatával telepítette a Kubernetes.

Az alábbi lépések kiemelik, hogyan hozhat létre NSG-szabályt a Kibana és a Grafana végpontokhoz:

### <a name="find-the-name-of-the-nsg"></a>Az NSG-név megkeresése

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>A NSG-szabály hozzáadása

Ha megvan a NSG neve, hozzáadhat egy szabályt a következő parancs használatával:

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```


## <a name="next-steps"></a>Következő lépések
- [A metrikák és naplók feltöltésének](upload-metrics-and-logs-to-azure-monitor.md) kipróbálása Azure monitor
- További információ a Grafana:
   - [Bevezetés](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Grafana alapjai](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Grafana oktatóanyagok](https://grafana.com/tutorials/grafana-fundamentals/#1)
- További információ a Kibana
   - [Bevezetés](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana útmutató](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Bevezetés az irányítópult szövegéig az adatvizualizációkkal a Kibana-ben](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Kibana-irányítópultok létrehozása](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

