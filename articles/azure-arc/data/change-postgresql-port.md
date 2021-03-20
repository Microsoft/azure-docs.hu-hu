---
title: A PostgreSQL-port módosítása
description: Módosítsa azt a portot, amelyen az Azure arc engedélyezve van a PostgreSQL nagy kapacitású-kiszolgálócsoport figyelése.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/02/2020
ms.topic: how-to
ms.openlocfilehash: 45424408c790e4921be48464818c55fe74313fd3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93328717"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>A kiszolgáló csoport figyelési portjának módosítása 

A port módosítása a kiszolgálócsoport szabványos szerkesztési művelete. A port módosításához futtassa a következő parancsot:
```console
 azdata arc postgres server edit -n <server group name> --port <desired port number>
```

Tegyük fel például, hogy a kiszolgálócsoport neve _postgres01_ , és a _866_-es porton szeretné figyelni. A következő parancsot kell futtatnia:
```console
 azdata arc postgres server edit -n postgres01 --port 866
```

## <a name="verify-that-the-port-was-changed"></a>Annak ellenőrzése, hogy a port módosult-e

A port módosításának ellenőrzéséhez futtassa a következő parancsot a kiszolgálócsoport konfigurációjának megjelenítéséhez:
```console
azdata arc postgres server show -n <server group name>
```

A parancs kimenetében tekintse meg a kiszolgálócsoport specifikációinak "szolgáltatás" szakaszában megjelenő portszámot a "Port" elemnél.
Azt is megteheti, hogy a externalEndpoint az állapot szakaszának állapota részében ellenőrizheti, hogy az IP-címet a konfigurált portszám követi-e.

Példaként, ha folytatjuk a fenti példát, futtassa a következő parancsot:
```console
azdata arc postgres server show -n postgres01
```

és a 866-as port a következőre hivatkozik:

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
és itt.

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>Következő lépések
- További információ a [kiszolgálói csoporthoz való kapcsolódásról](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
- Tekintse át, hogyan konfigurálhatja a kiszolgálócsoport egyéb szempontjait a dokumentáció How-to\Manage\Configure & skálázás szakaszában.
