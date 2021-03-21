---
title: Biztonsági mentés és visszaállítás a rugalmas skálázású Azure Database for PostgreSQL-kiszolgálócsoportokhoz
description: Biztonsági mentés és visszaállítás a rugalmas skálázású Azure Database for PostgreSQL-kiszolgálócsoportokhoz
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 8b3304c673e8606667246a7d0df9ad8f3be11d9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686699"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoportok biztonsági mentése és visszaállítása

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Ha az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport biztonsági mentését vagy visszaállítását állítja be, a rendszer biztonsági másolatot készít az összes adatbázisról a kiszolgálócsoport összes PostgreSQL-csomópontján, és/vagy visszaállítja azt.

## <a name="take-a-manual-full-backup"></a>Manuális teljes biztonsági mentés készítése

A következő parancs futtatásával teljes biztonsági mentést készíthet a kiszolgálócsoport összes adat-és naplófájl-mappájáról:
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Ahol:
- a __név__ a biztonsági másolat nevét jelzi.
- a __kiszolgáló neve__ egy kiszolgálócsoport-csoportot jelöl
- a __No-WAIT__ érték azt jelzi, hogy a parancssor nem várja meg a biztonsági mentés befejeződését, hogy továbbra is használni tudja ezt a parancssori ablakot.

Ez a parancs egy elosztott teljes biztonsági mentést koordinál az összes olyan csomóponton, amely az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport részét képezi. Más szóval a koordinátor és a munkavégző csomópontok összes adatait biztonsági másolatot készít.

Például:

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

A biztonsági mentés befejezésekor a rendszer a biztonsági mentés AZONOSÍTÓját, nevét, méretét, állapotát és időbélyegét adja vissza. Például:
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` azt az időzónát jelzi, amikor a biztonsági mentés történt. Ebben a példában a "+ 00:00" kifejezés UTC-időt jelent (UTC + 00 óra 00 perc).

> [!NOTE]
> A következő lehetőségek még nem lehetségesek:
> - Automatikus biztonsági mentések ütemezése
> - A biztonsági mentés előrehaladásának megjelenítése a folyamat során

## <a name="list-backups"></a>Biztonsági másolatok listázása

A visszaállítani kívánt biztonsági másolatok listázásához futtassa a következő parancsot:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Például:

```console
azdata arc postgres backup list --server-name postgres01
```

A következőhöz hasonló kimenetet ad vissza:

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

Az időbélyeg oszlop azt az időpontot jelzi, amikor a biztonsági mentés történt.

## <a name="restore-a-backup"></a>Visszaállítás biztonsági másolatból
Ebben a szakaszban bemutatjuk, hogyan végezheti el a teljes visszaállítást vagy az időpontra történő visszaállítást. Teljes biztonsági mentés visszaállításakor a biztonsági mentés teljes tartalmát állítja vissza. Ha egy időponthoz tartozó visszaállítást végez, a rendszer a megadott időpontra állítja vissza. A rendszer nem állítja vissza az adott időponthoz képest később végrehajtott tranzakciókat.

### <a name="restore-a-full-backup"></a>Teljes biztonsági mentés visszaállítása
A biztonsági mentés teljes tartalmának visszaállításához futtassa a következő parancsot:
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

Ahol:
- a __Backup-ID__ a fent látható biztonsági mentési parancsban látható biztonsági másolat azonosítója.
Ez összehangolja az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport összes csomópontjának elosztott teljes visszaállítását. Más szóval a koordinátor és a munkavégző csomópontok összes adatait visszaállítja.

#### <a name="examples"></a>Angol nyelvű Példák:

__Állítsa vissza a kiszolgálói csoport postgres01 saját magára:__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Ez a művelet csak a PostgreSQL 12-es vagy újabb verziója esetén támogatott.

__Állítsa vissza a postgres01 egy másik kiszolgálói csoport postgres02:__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
Ezt a műveletet a PostgreSQL bármely olyan verziója támogatja, amely a 11-es verziót indítja. A cél-kiszolgáló csoportot a visszaállítási művelet előtt létre kell hozni, és ugyanazt a konfigurációt kell használnia, és ugyanazt a biztonsági mentési PVC-t kell használnia, mint a forráskiszolgáló-csoportnak.

A visszaállítási művelet befejezésekor a rendszer a parancssorhoz hasonló kimenetet ad vissza:

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> A következő lehetőségek még nem lehetségesek:
> - Biztonsági másolat visszaállítása a név megjelölésével
> - Visszaállítási művelet előrehaladásának megjelenítése


### <a name="do-a-point-in-time-restore"></a>Időpontra történő visszaállítás

A kiszolgálócsoport adott időpontra történő visszaállításához futtassa a következő parancsot:
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

A Restore parancs általános formátumának olvasásához futtassa a következőt: `azdata arc postgres backup restore --help` .

Hol `time` található a visszaállítás időpontja. Adjon meg egy időbélyeget vagy egy számot és egy utótagot ( `m` perc, `h` óra, `d` nap vagy `w` hetek). Például 90 percet vesz igénybe `1.5h` .

#### <a name="examples"></a>Angol nyelvű Példák:
__A kiszolgálócsoport postgres01-visszaállításának időpontját állítsa magára:__

Egy kiszolgálócsoport időpontra való visszaállítását még nem lehet elvégezni.

__A kiszolgálócsoport adott időpontra történő visszaállításával egy másik postgres01 postgres02 egy adott időbélyegre:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

Ez a példa a Server Group (postgres02) kiszolgáló csoportba való visszaállításával azt az állapotot állítja vissza, amely szerint a postgres01 kiszolgáló csoport a következő címen 2020 volt: 04:23:48.75 UTC. Vegye figyelembe, hogy a "+ 00" a megadott időponthoz tartozó időzónát adja meg. Ha nem jelöli meg az időzónát, a rendszer a visszaállítási műveletet futtató ügyfél időzónáját fogja használni.

Például:
- `2020-12-08 04:23:48.751326+00`UTC szerint értelmezve `2020-12-08 04:23:48.751326`
- Ha a csendes-óceáni téli időzónában (PST = UTC + 08) van, a `2020-12-08 04:23:48.751326` rendszer UTC szerint értelmezi ezt a műveletet, amely a `2020-12-08 12:23:48.751326` PostgreSQL 11-ös verziójától kezdődően minden verziójában támogatott. A cél-kiszolgáló csoportot a visszaállítási művelet előtt létre kell hozni, és ugyanazt a biztonsági mentési PVC-t kell használnia, mint a forráskiszolgáló-csoportnak.


__A postgres02 egy másik postgres01 való visszaállításának időpontját a múltban megadott időtartamra állítja be a rendszer:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

Ez a példa a (z) postgres02, amely azt az állapotot állítja vissza, amelyben a postgres01-csoport 20 perce volt.
Ezt a műveletet a PostgreSQL bármely olyan verziója támogatja, amely a 11-es verziót indítja. A cél-kiszolgáló csoportot a visszaállítási művelet előtt létre kell hozni, és ugyanazt a biztonsági mentési PVC-t kell használnia, mint a forráskiszolgáló-csoportnak.

> [!NOTE]
> A következő lehetőségek még nem lehetségesek:
> - Visszaállítási művelet előrehaladásának megjelenítése

## <a name="delete-backups"></a>Biztonsági másolatok törlése

A biztonsági mentés megőrzése nem állítható be előzetes verzióban. Azonban manuálisan is törölheti a nem szükséges biztonsági másolatokat.
A biztonsági másolatok törlésére szolgáló általános parancs a következő:

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

ahol:
- `--server-name` annak a csoportnak a neve, amelyből a felhasználó törölni kívánja a biztonsági mentést
- `--name` a törlendő biztonsági másolat neve
- `-id`a törlendő biztonsági másolat azonosítója

> [!NOTE]
> `--name` és `-id` kölcsönösen kizárják egymást.

Például:

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

A biztonsági másolatok nevének és AZONOSÍTÓjának lekéréséhez futtassa az előző bekezdésben ismertetett biztonsági mentési parancsot.

A DELETE paranccsal kapcsolatos további információkért futtassa a következőt:

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Következő lépések
- További információ a kiszolgálócsoport [kibővítéséről (munkavégző csomópontok hozzáadásáról)](scale-out-postgresql-hyperscale-server-group.md)
- További információ a kiszolgálócsoport [fel-vagy leskálázásáról (a memória/virtuális mag növelése/csökkentése)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
