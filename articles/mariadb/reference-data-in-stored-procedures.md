---
title: Azure Database for MariaDB-adatok a replikálási tárolt eljárások
description: Ez a cikk az adatok a replikációhoz használt összes tárolt eljárások be.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: reference
ms.date: 09/24/2018
ms.openlocfilehash: 75dc10ba3d95fd12ea99e10d321237560ee28171
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53535352"
---
# <a name="azure-database-for-mariadb-data-in-replication-stored-procedures"></a>Azure Database for MariaDB-adatok a replikálási tárolt eljárások

Adatok a replikáció lehetővé teszi, hogy szinkronizálja az adatokat a helyszínen futó virtuális gépeket vagy adatbázis-szolgáltatások be az Azure Database for MariaDB szolgáltatás egyéb felhőszolgáltatók által üzemeltetett MariaDB-kiszolgálóról.

A következő tárolt eljárások segítségével állítsa be, vagy távolítsa el az adatok a replikáció a master és a replika között.

|**Tárolt eljárás neve**|**Bemeneti paraméterek**|**A kimeneti paraméterek**|**Használati Megjegyzés**|
|-----|-----|-----|-----|
|*MySQL.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|–|Adatok áthelyezése az SSL-mód, át kell adnia a hitelesítésszolgáltató tanúsítványát a környezetben be master_ssl_ca paraméter. </br><br>SSL nélküli adatátvitelhez, adja át egy üres karakterlánc master_ssl_ca paraméter be.|
|*MySQL.az_replication _indítása*|–|–|Elindítja a replikáció.|
|*MySQL.az_replication _stop*|–|–|Replikáció leállítása.|
|*MySQL.az_replication _remove_master*|–|–|Eltávolítja a replikációs kapcsolatot a master és a replika között.|
|*MySQL.az_replication_skip_counter*|–|–|Egy replikációs hiba kihagyja.|

Adatok a közötti replikáció egy fő- és a egy replikát, az Azure Database for MariaDB beállításával kapcsolatban tekintse meg [adatok a replikáció konfigurálása](howto-data-in-replication.md).
