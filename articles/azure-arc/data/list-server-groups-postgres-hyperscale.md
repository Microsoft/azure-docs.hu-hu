---
title: Egy Azure-beli ív-adatvezérlőben létrehozott, Azure arc-alapú PostgreSQL nagy kapacitású-kiszolgálócsoportok listázása
description: Egy Azure-beli ív-adatvezérlőben létrehozott, Azure arc-alapú PostgreSQL nagy kapacitású-kiszolgálócsoportok listázása
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90940788"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Egy Azure-beli ív-adatvezérlőben létrehozott, Azure arc-alapú PostgreSQL nagy kapacitású-kiszolgálócsoportok listázása

Ez a cikk azt ismerteti, hogyan kérhető le az ív-adatkezelőben létrehozott kiszolgálócsoportok listája.

A lista lekéréséhez használja az alábbi módszerek valamelyikét, ha csatlakozott az ív adatvezérlőhöz:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>A CLI és a azdata használatával
A parancs általános formátuma:
```console
azdata arc postgres server list
```

A következőhöz hasonló kimenetet ad vissza:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
A parancshoz elérhető paraméterekkel kapcsolatos további információkért futtassa a következőt:
```console
azdata arc postgres server list --help
```

## <a name="from-cli-with-kubectl"></a>A CLI és a kubectl használatával
Futtassa a következő parancsok egyikét.

**A postgres-verziótól függetlenül a kiszolgálói csoportok listázásához futtassa a következőt:**
```console
kubectl get postgresqls
```
A következőhöz hasonló kimenetet ad vissza:
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**A postgres adott verziójához tartozó kiszolgálócsoportok listázásához futtassa a következőt:**
```console
kubectl get postgresql-12
```

A postgres 11-es verzióját futtató kiszolgálócsoportok listázásához cserélje le a PostgreSQL- _12_ -et a _PostgreSQL-11_ értékre.

## <a name="next-steps"></a>Következő lépések:

* [Olvassa el a kapcsolati végpontok beszerzéséről és a kapcsolati karakterláncok létrehozásáról szóló cikket a kiszolgálói csoporthoz való kapcsolódáshoz](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Olvassa el az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport konfigurációjának bemutatását ismertető cikket](show-configuration-postgresql-hyperscale-server-group.md)
