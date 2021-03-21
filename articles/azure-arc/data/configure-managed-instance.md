---
title: Az Azure arc használatára képes SQL felügyelt példány konfigurálása
description: Az Azure arc használatára képes SQL felügyelt példány konfigurálása
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6e2443014f6788504a11784945078187a5a72de4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98985869"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Az Azure arc használatára képes SQL felügyelt példány konfigurálása

Ez a cikk azt ismerteti, hogyan konfigurálható az Azure arc használatára képes SQL felügyelt példány.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources"></a>Erőforrások konfigurálása

### <a name="configure-using-azure-data-cli-azdata"></a>Konfigurálás a használatával [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Az Azure arc használatára képes SQL felügyelt példányok konfigurációját szerkesztheti a használatával [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] . Az alábbi parancs futtatásával tekintheti meg a konfigurációs beállításokat. 

```
azdata arc sql mi edit --help
```

A következő példa a CPU Core-és a memória-kérelmeket és-korlátokat állítja be.

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

A felügyelt SQL-példányon végrehajtott módosítások megtekintéséhez az alábbi parancsokkal tekintheti meg a konfigurációs YAML fájlt:

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>Kiszolgáló beállításainak konfigurálása

A kiszolgáló konfigurációs beállításait az Azure arc használatára képes SQL felügyelt példányhoz is konfigurálhatja a létrehozási idő után. Ez a cikk azt ismerteti, hogyan konfigurálhat olyan beállításokat, mint például az MSSQL-ügynök engedélyezése vagy letiltása, a hibaelhárítási forgatókönyvek esetében adott nyomkövetési jelzők

Ezen beállítások bármelyikének módosításához kövesse az alábbi lépéseket:

1. Hozzon létre egy egyéni `mssql-custom.conf` fájlt, amely tartalmazza a célként megadott beállításokat. Az alábbi példa engedélyezi az SQL Agent használatát, és engedélyezi a 1204 nyomkövetési jelzőt:

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. Másolja a `mssql-custom.conf` fájlt a `/var/opt/mssql` `mssql-miaa` tárolóba a pod `master-0` -ban. Cserélje le `<namespaceName>` a nevet a Big Data-fürt nevére.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. Indítsa újra SQL Server példányt.  Cserélje le `<namespaceName>` a nevet a Big Data-fürt nevére.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**Ismert korlátozások**
- A fenti lépésekhez Kubernetes-fürt rendszergazdai engedélyei szükségesek
- Ez változhat az előzetes verzióban
