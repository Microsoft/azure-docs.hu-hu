---
title: Irányítópultok Azure Data Studio
description: Irányítópultok Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a96be6d4da3d292b2e9881652aad28f318ccee8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92107572"
---
# <a name="azure-data-studio-dashboards"></a>Irányítópultok Azure Data Studio

A [Azure Data Studio](/sql/azure-data-studio/what-is) az Azure arc erőforrásaival kapcsolatos információk megtekintéséhez hasonló élményt nyújt a Azure Portalhoz.  Ezeket a nézeteket **irányítópultoknak** nevezzük, és olyan elrendezéssel és lehetőségekkel rendelkeznek, amelyek a Azure Portal adott erőforrásával kapcsolatban látszanak, de rugalmasságot biztosítanak az információk helyi környezetben való megjelenítéséhez olyan esetekben, amikor nem áll rendelkezésre az Azure-hoz elérhető kapcsolódás.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connecting-to-a-data-controller"></a>Csatlakozás adatvezérlőhöz

### <a name="prerequisites"></a>Előfeltételek

- [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) letöltése
- Az Azure arc bővítmény telepítve van

### <a name="determine-the-data-controller-server-api-endpoint-url"></a>Az adatvezérlő kiszolgáló API-végpontjának URL-címének meghatározása

Először kapcsolódnia kell Azure Data Studio az adatkezelő szolgáltatás API-végpontjának URL-címéhez.

A végpont beszerzéséhez futtassa a következő parancsot:

```console
kubectl get svc/controller-svc-external -n <namespace name>

#Example:
kubectl get svc/controller-svc-external -n arc
```

Ekkor az alábbihoz hasonló kimenet jelenik meg:

```console
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                       AGE
controller-svc-external   LoadBalancer   10.0.175.137   52.154.152.24    30080:32192/TCP                               22h
```

Ha terheléselosztó-típust használ, a külső IP-címet és portszámot is át szeretné másolni. A NodePort használata esetén a Kubernetes API-kiszolgáló IP-címét és a PORT (ok) oszlopban felsorolt portszámot kell használnia.

Most egy URL-címet kell létrehoznia a végponthoz az alábbi információk kombinálásával:

```console
https://<ip address>:<port>

Example:
https://52.154.152.24:30080
```

Jegyezze fel az IP-címét, ahogy a következő lépésben használni fogja.

### <a name="connect"></a>Csatlakozás

1. Azure Data Studio megnyitása

1. Válassza a bal oldali **kapcsolatok** fület

Az oldal alján bontsa ki az **Azure arc-vezérlők** nevű panelt.

Kattintson a + ikonra egy új adatvezérlő-kapcsolatok hozzáadásához.

A Command paletta képernyő felső részén adja meg az 1. lépésben létrehozott URL-címet, majd kattintson az ENTER gombra.
Adja meg az adatvezérlő felhasználónevét.  Ez volt a username érték, amelyet az adatvezérlő üzembe helyezésekor adott meg.  Kattintson az ENTER gombra.
Adja meg az adatvezérlő jelszavát.  Ez volt a jelszó értéke, amelyet a rendszer az adatvezérlő üzembe helyezése során adott át. Kattintson az ENTER gombra.

Most, hogy csatlakozott egy adatkezelőhöz, megtekintheti az adatvezérlő irányítópultját és az SQL által felügyelt példányokat vagy a PostgreSQL nagy kapacitású-kiszolgáló csoportjának erőforrásait.

## <a name="view-the-data-controller-dashboard"></a>Az adatvezérlő irányítópultjának megtekintése

Kattintson a jobb gombbal az adatvezérlőre az **ív-vezérlők** kibontható paneljének kapcsolatok paneljén, és válassza a **kezelés** lehetőséget.

Itt megtekintheti az adatkezelő erőforrás részleteit, például a nevet, a régiót, a kapcsolat módját, az erőforráscsoportot, az előfizetést, a vezérlő végpontját és a névteret.  Megtekintheti az adatkezelő által kezelt összes felügyelt adatbázis-erőforrás listáját is.

Megfigyelheti, hogy az elrendezés hasonló ahhoz, amit a Azure Portal tartalmaz.

Kényelmesen elindíthat egy SQL felügyelt példány vagy PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozását az + új példány gombra kattintva.

A Azure Portal az adatvezérlő kontextusában is megnyithatja a Megnyitás Azure Portal gombra kattintva.

## <a name="view-the-sql-managed-instance-dashboards"></a>Az SQL felügyelt példány irányítópultjának megtekintése

Ha létrehozta az SQL által felügyelt példányokat, az Azure-adatkezelők kibontható panelének kapcsolatok paneljén láthatja őket, az azokat kezelő adatkezelő alatt.

Egy adott példányhoz tartozó SQL felügyelt példány irányítópultjának megtekintéséhez kattintson a jobb gombbal a példányra, és válassza a kezelés lehetőséget.

A kapcsolat panel a jobb oldalon jelenik meg, és rákérdez az SQL-példányhoz való kapcsolódásra a bejelentkezéshez/jelszóhoz. Ha ismeri a kapcsolati adatokat, megadhatja azt, és kattintson a csatlakoztatás gombra.  Ha nem tudja, kattintson a Mégse gombra.  Mindkét esetben a kapcsolódási panel bezárásakor az irányítópultra kerül.

Az Áttekintés lapon megtekintheti az SQL által felügyelt példány részleteit, például az erőforráscsoportot, az adatkezelőt, az előfizetés AZONOSÍTÓját, az állapotot, a régiót és egyebeket.  Azt is megtekintheti, hogy az adott SQL-példányhoz tartozó Grafana-vagy Kibana-irányítópultok a hivatkozással is megtekinthetők.

Ha tud csatlakozni az SQL-kezelő példányához, további információt itt talál.

Az SQL felügyelt példányát törölheti innen, vagy megnyithatja a Azure Portal az SQL felügyelt példányának megtekintéséhez a Azure Portal.

Ha a bal oldalon a kapcsolódási karakterláncok lapra kattint, megtekintheti az SQL felügyelt példányának előre összeállított kapcsolódási karakterláncait, így egyszerűen másolhatja és beillesztheti a különböző alkalmazásokat vagy kódokat.

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>A PostgreSQL nagy kapacitású Server Group-irányítópultok megtekintése

Ha létrehozta a PostgreSQL nagy kapacitású-kiszolgálócsoportok listáját, az Azure-adatkezelők kibontható panelének kapcsolatok paneljén láthatja őket.

Ha meg szeretné tekinteni a PostgreSQL nagy kapacitású-kiszolgáló csoportjának irányítópultját egy adott kiszolgálócsoport esetében, kattintson a jobb gombbal a kiszolgáló csoportra, és válassza a kezelés lehetőséget.

Az Áttekintés lapon megtekintheti a kiszolgálócsoport részleteit, például az erőforráscsoportot, az adatkezelőt, az előfizetés AZONOSÍTÓját, az állapotot, a régiót és egyebeket.  Azt is megtekintheti, hogy a hivatkozás, amelyre kattintva a Grafana vagy a Kibana irányítópultok beléphetnek az adott kiszolgálócsoport kontextusában.

A kiszolgálói csoportot törölheti innen, vagy megnyithatja a Azure Portalt a kiszolgálócsoport megtekintéséhez a Azure Portal.

Ha a bal oldalon a kapcsolódási karakterláncok lapra kattint, megtekintheti az adott kiszolgálócsoport előre összeállított kapcsolódási karakterláncait, így egyszerűen másolhatja és beillesztheti a különböző alkalmazásokat vagy kódokat.

Ha a bal oldali tulajdonságok lapra kattint, további részleteket láthat.

Ha a bal oldali erőforrás állapota lapra kattint, megtekintheti az adott kiszolgálócsoport aktuálisan magas szintű állapotát.

Ha a bal oldali diagnosztizálás és megoldás problémák lapra kattint, elindíthatja a PostgreSQL hibaelhárítási jegyzetfüzetjét.

Ha a bal oldalon a New support Request (új támogatási kérés) lapra kattint, akkor a Azure Portalt a kiszolgálói csoport számára is elindíthatja, és létrehozhat egy Azure-támogatási kérést.