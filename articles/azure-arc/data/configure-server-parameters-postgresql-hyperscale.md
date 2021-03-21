---
title: Postgres-motor kiszolgálói paramétereinek konfigurálása a PostgreSQL nagy kapacitású-kiszolgálócsoport számára az Azure arc-ban
titleSuffix: Azure Arc enabled data services
description: Postgres-motor kiszolgálói paramétereinek konfigurálása a PostgreSQL nagy kapacitású-kiszolgálócsoport számára az Azure arc-ban
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: cdbddfc84b3f71576cfd0299f2babec859b4ef1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92311063"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>Az Azure Arc-kompatibilis, rugalmas skálázású PostgreSQL adatbázismotor-beállításainak megadása

Ez a dokumentum a rugalmas skálázású PostgreSQL-kiszolgálócsoport adatbázismotorjának egyéni (nem alapértelmezett) értékre való beállításának lépéseit ismerteti. Az adatbázismotor paramétereinek beállításával és az alapértelmezett értékkel kapcsolatos részletekért tekintse meg a PostgreSQL dokumentációját [itt](https://www.postgresql.org/docs/current/runtime-config.html).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> Az előzetes verzió nem támogatja a következő paraméterek beállítását: 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>Syntax

Az adatbázismotor-beállítások konfigurálására szolgáló parancs általános formátuma a következő:

```console
azdata arc postgres server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-engine-settings, --re}] {'<parameter name>=<parameter value>, ...'}
```

## <a name="show-current-custom-values"></a>Aktuális egyéni értékek megjelenítése

### <a name="with-azure-data-cli-azdata-command"></a>[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]Paranccsal

```console
azdata arc postgres server show -n <server group name>
```

Például:

```console
azdata arc postgres server show -n postgres01
```

Ez a parancs Visszaadja annak a csoportnak a specifikációját, amelyben a beállított paramétereket látni fogja. Ha nincs engine\settings szakasz, az azt jelenti, hogy az összes paraméter az alapértelmezett értéken fut:

```console
 "
...
engine": {
      "settings": {
        "default": {
          "autovacuum_vacuum_threshold": "65"
        }
      }
    },
...
```

### <a name="with-kubectl-command"></a>A kubectl paranccsal

Kövesse az alábbi lépéseket.

1. A kiszolgálócsoport egyéni erőforrás-definíciójának beolvasása

   Futtassa a következőt:

   ```console
   azdata arc postgres server show -n <server group name>
   ```

   Például:

   ```console
   azdata arc postgres server show -n postgres01
   ```

   Ez a parancs Visszaadja annak a csoportnak a specifikációját, amelyben a beállított paramétereket látni fogja. Ha nincs engine\settings szakasz, az azt jelenti, hogy az összes paraméter az alapértelmezett értéken fut:

   ```output
   > {
     >"apiVersion": "arcdata.microsoft.com/v1alpha1",
     >"**kind**": "**postgresql-12**",
     >"metadata": {
       >"creationTimestamp": "2020-08-25T14:32:23Z",
       >"generation": 1,
       >"name": "postgres01",
       >"namespace": "arc",  
   ```

   A kimeneti eredmények között keresse meg a mezőt, `kind` és állítsa félre az értéket, például: `postgresql-12` .

2. A Kubernetes tartozó egyéni erőforrás leírása 

   A parancs általános formátuma:

   ```console
   kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
   ```

   Például:

   ```console
   kubectl describe postgresql-12 postgres01
   ```

   Ha a motor beállításaihoz egyéni értékek vannak beállítva, akkor azokat visszaadja. Például:

   ```output
   Engine:
   ...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
   ```

   Ha nem állított be egyéni értékeket a motor beállításaihoz, a motor beállításai szakasza `resultset` üres lesz, például:

   ```output
   Engine:
   ...
       Settings:
         Default:
   ```

## <a name="set-custom-values-for-engine-settings"></a>A motor beállításainak egyéni értékének beállítása

Az alábbi parancsok a koordinátor csomópont paramétereit és a PostgreSQL-nagy kapacitású tartozó munkavégző csomópontokat azonos értékekre állítja be. A kiszolgálói csoportban nem lehet paramétereket beállítani a szerepkörökhöz. Ez azt jelenti, hogy egy adott paraméter nem konfigurálható a koordinátor és a munkavégző csomópontok egy másik értékére.

### <a name="set-a-single-parameter"></a>Egyetlen paraméter beállítása

```console
azdata arc server edit -n <server group name> -e <parameter name>=<parameter value>
```

Például:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=8MB
```

### <a name="set-multiple-parameters-with-a-single-command"></a>Több paraméter beállítása egyetlen paranccsal

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>=<parameter value>, <parameter name>=<parameter value>,...'
```

Például:

```console
azdata arc postgres server edit -n postgres01 -e 'shared_buffers=8MB, max_connections=50'
```

### <a name="reset-a-parameter-to-its-default-value"></a>Paraméter visszaállítása az alapértelmezett értékre

Ha egy paramétert az alapértelmezett értékre szeretne visszaállítani, állítsa be úgy, hogy ne jelezzen értéket. 

Például:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=
```

### <a name="reset-all-parameters-to-their-default-values"></a>Az összes paraméter visszaállítása az alapértelmezett értékekre

```console
azdata arc postgres server edit -n <server group name> -e '' -re
```

Például:

```console
azdata arc postgres server edit -n postgres01 -e '' -re
```

## <a name="special-considerations"></a>Különleges szempontok

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>Adja meg azt a paramétert, amelynek értéke vesszőt, szóközt vagy speciális karaktert tartalmaz

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>="<parameter value>"'
```

Például:

```console
azdata arc postgres server edit -n postgres01 -e 'custom_variable_classes = "plpgsql,plperl"'
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>Környezeti változó átadása egy paraméter értékében

A környezeti változót a "" "belül kell becsomagolni, hogy az ne legyen megoldva a beállítása előtt.

Például: 

```console
azdata arc postgres server edit -n postgres01 -e 'search_path = "$user"'
```

## <a name="next-steps"></a>Következő lépések
- További információ a kiszolgálócsoport [kibővítéséről (munkavégző csomópontok hozzáadásáról)](scale-out-postgresql-hyperscale-server-group.md)
- További információ a kiszolgálócsoport [fel-vagy leskálázásáról (a memória/virtuális mag növelése/csökkentése)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
