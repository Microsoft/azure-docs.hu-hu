---
title: Azure arc-kompatibilis PostgreSQL-nagy kapacitású létrehozása Azure Data Studio használatával
description: Azure arc-kompatibilis PostgreSQL-nagy kapacitású létrehozása Azure Data Studio használatával
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e2007d8f0c558d35c0507b6e12bce6d6777fad52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92310901"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Azure arc-kompatibilis PostgreSQL-nagy kapacitású létrehozása Azure Data Studio használatával

Ez a dokumentum végigvezeti a Azure Data Studio használatának lépésein az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoportok kiépítéséhez.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>Kapcsolódás az Azure arc-adatkezelőhöz

Példány létrehozása előtt jelentkezzen be az Azure arc-adatkezelőbe, ha még nincs bejelentkezve.

```console
azdata login
```

Ekkor a rendszer kérni fogja a névteret, amelyben az adatvezérlőt létrehozták, a felhasználónevet és a jelszót, hogy bejelentkezzen a vezérlőbe.

> Ha ellenőriznie kell a névteret, a futtatásával ```kubectl get pods -A``` lekérheti a fürtben található összes névtér listáját.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Előzetes és ideiglenes lépés csak a OpenShift-felhasználók számára

A következő lépésre való áttérés előtt implementálja ezt a lépést. Ha a PostgreSQL nagy kapacitású-kiszolgáló csoportját nem az alapértelmezett értékre szeretné telepíteni a Red Hat OpenShift, az alábbi parancsokat kell futtatnia a fürtön a biztonsági megkötések frissítéséhez. Ez a parancs megadja a szükséges jogosultságokat a PostgreSQL nagy kapacitású-kiszolgálói csoportot futtató szolgáltatásfiókok számára. Az Azure arc-adatkezelő üzembe helyezése során felvette a biztonsági környezeti megkötés (SCC) **_ív-adathalmazát_** .

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_A **kiszolgáló-csoport neve** a következő lépésben telepítendő kiszolgálócsoport neve._
   
A OpenShift SCCs kapcsolatos további információkért tekintse meg a [OpenShift dokumentációját](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Most már megvalósíthatja a következő lépést.

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc-kompatibilis, rugalmas skálázású PostgreSQL-kiszolgálócsoport létrehozása

1. Azure Data Studio elindítása
1. A kapcsolatok lapon kattintson a bal felső sarokban található három pontra, és válassza az "új központi telepítés" lehetőséget.
1. A központi telepítési lehetőségek közül válassza a **PostgreSQL nagy kapacitású Server Group-Azure arc** lehetőséget.
    >[!NOTE]
    > Előfordulhat, hogy a rendszer arra kéri, hogy telepítse az [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] itt, ha jelenleg nincs telepítve.
1. Fogadja el az adatvédelmi és a licencfeltételeket, majd kattintson a lenti **kijelölés** elemre.
1. A PostgreSQL nagy kapacitású-kiszolgálócsoport üzembe helyezése – Azure arc panelen adja meg a következő adatokat:
   - Adja meg a kiszolgálócsoport nevét
   - Adja meg és erősítse meg a _postgres_ rendszergazda felhasználójának jelszavát.
   - Válassza ki az adattárolási osztályt a megfelelő értékként
   - A naplókhoz megfelelő tárolási osztály kiválasztása
   - A biztonsági másolatok megfelelő tárolási osztályának kiválasztása
   - Válassza ki a kiépíteni kívánt munkavégző csomópontok számát
1. Kattintson a **telepítés** gombra

Ezzel elindítja az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozását az adatkezelőn.

Néhány percen belül sikeresen befejeződött a létrehozás.

## <a name="next-steps"></a>Következő lépések
- [Kiszolgálócsoport kezelése Azure Data Studio használatával](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [A kiszolgálócsoport figyelése](monitor-grafana-kibana.md)
- Olvassa el a Azure Database for PostgreSQL nagy kapacitású kapcsolatos fogalmakat és útmutatókat az adatok több PostgreSQL nagy kapacitású-csomóponton keresztüli terjesztéséhez, valamint az Azure Database postgres nagy kapacitású nyújtotta előnyök kihasználása érdekében. :
    * [Csomópontok és táblák](../../postgresql/concepts-hyperscale-nodes.md)
    * [Alkalmazás típusának meghatározása](../../postgresql/concepts-hyperscale-app-type.md)
    * [Elosztási oszlop kiválasztása](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tábla közös elhelyezése](../../postgresql/concepts-hyperscale-colocation.md)
    * [Táblák elosztása és módosítása](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Több-bérlős adatbázis tervezése](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Valós idejű elemzési irányítópult kialakítása](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* A fenti dokumentumokban hagyja ki a **bejelentkezett szakaszt a Azure Portalba**, & **hozzon létre egy Azure Database for PostgreSQL-nagy kapacitású (Citus)**. Implementálja az Azure arc üzembe helyezésének hátralévő lépéseit. Ezek a részek Azure Database for PostgreSQL az Azure-felhőben nagy kapacitású (Citus) jellemzőek, de a dokumentumok egyéb részei közvetlenül alkalmazhatók az Azure arc-kompatibilis PostgreSQL-nagy kapacitású.

- [A rugalmas skálázású Azure Database for PostgreSQL-kiszolgálócsoport horizontális felskálázása](scale-out-postgresql-hyperscale-server-group.md)
- [Tárolási konfiguráció és Kubernetes-tárolási fogalmak](storage-configuration.md)
- [Kubernetes erőforrás-modell](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

