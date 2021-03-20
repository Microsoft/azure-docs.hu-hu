---
title: Azure Data Studio használata a PostgreSQL-példány kezeléséhez
description: Azure Data Studio használata a PostgreSQL-példány kezeléséhez
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7dcc0f916a15598060e034dcf62536ee13e2672e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92320238"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>A Azure Data Studio használata az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport kezelésére


Ez a cikk a következőket ismerteti:
- PostgreSQL-példányok kezelése az irányítópult-nézetekkel, például az Áttekintés, a kapcsolatok karakterláncai, a tulajdonságok, Resource Health...
- az adatai és a séma használata

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

- [A azdata, a Azure Data Studio és az Azure CLI telepítése](install-client-tools.md)
- Telepítés Azure Data Studio a **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** és az **Azure arc** -és **PostgreSQL** -bővítmények
- Az [Azure arc-adatkezelő](create-data-controller-using-azdata.md) létrehozása
- Azure Data Studio elindítása

## <a name="connect-to-the-azure-arc-data-controller"></a>Kapcsolódás az Azure arc-adatkezelőhöz

A Azure Data Studio bontsa ki a csomópont **Azure arc-vezérlőket** , és válassza a **vezérlő összekapcsolása** gombot:

Adja meg az Azure-adatkezelőhöz tartozó kapcsolódási adatokat:

- **Vezérlő URL-címe:**

    A vezérlőhöz a Kubernetes-ben való kapcsolódáshoz használandó URL-cím. Például a következő formában van megadva `https://<IP_address_of_the_controller>:<Kubernetes_port.` :

    ```console
    https://12.345.67.890:30080
    ```
- **Felhasználónév:**

    A vezérlőhöz való kapcsolódáshoz használt felhasználói fiók neve. Használja azt a nevet, amelyet általában a futtatásakor használ `azdata login` . Nem a PostgreSQL-adatbázismotor számára a psql-ből való kapcsolódáshoz használt PostgreSQL-felhasználó neve.
- **Jelszó:** A vezérlőhöz való kapcsolódáshoz használt felhasználói fiók jelszava


Az Azure-beli adatstúdió megjeleníti az ív adatkezelőjét. Bontsa ki a csomópontot, és megjeleníti az általa kezelt PostgreSQL-példányok listáját.

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoportok kezelése

Kattintson a jobb gombbal a kezelni kívánt PostgreSQL-példányra, majd válassza a [kezelés] lehetőséget.

A PostgreSQL irányítópult nézete:

Ez az ablaktábla bal oldalán több irányítópultot is tartalmaz:

- **Áttekintés:** Megjeleníti a példány összegző információit, például a nevet, a PostgreSQL rendszergazdai felhasználónevét, az Azure-előfizetés AZONOSÍTÓját, a konfigurációt, az adatbázismotor verzióját, a Grafana és a Kibana tartozó végpontokat...
- **Kapcsolatok karakterláncai:** Különböző kapcsolati karakterláncokat jelenít meg, amelyekhez szükség lehet a PostgreSQL-példányhoz való kapcsolódásra, például a psql, a Node.js, a PHP, a Ruby...
- **Problémák diagnosztizálása és megoldása:** A kezdőlapon számos olyan erőforrást talál, amely segít a példányok hibaelhárításában, amikor kibővítjük a hibaelhárító jegyzetfüzeteket
- **Új támogatási kérelem:** Az a Kezdőlap, amelyből a terméktámogatási szolgálattól kérhet segítséget a nyilvános előzetes bejelentés megkezdése után.

## <a name="work-with-your-data-and-schema"></a>Az adatai és a séma használata

A Azure Data Studio ablak bal oldalán bontsa ki a csomópont- **kiszolgálókat**:

Válassza a [kapcsolat hozzáadása] lehetőséget, majd adja meg a kapcsolati adatokat a PostgreSQL-példányban:
- **Kapcsolattípus:** PostgreSQL
- **Kiszolgáló neve:** adja meg a PostgreSQL-példány nevét. Például: postgres01
- **Hitelesítés típusa:** Jelszó
- **Felhasználónév:** használhatja például a standard/default PostgreSQL rendszergazdai felhasználónevet. Megjegyzés: Ez a mező megkülönbözteti a kis-és nagybetűket.
- **Password (jelszó):** a PostgreSQL-Felhasználónév jelszava a psql-kapcsolatok karakterláncában található a parancs kimenetében. `azdata postgres server endpoint -n postgres01`
- **Adatbázis neve:** állítsa be annak az adatbázisnak a nevét, amelyhez csatlakozni szeretne. Beállíthatja, hogy az __alapértelmezett__ értékre legyen állítva
- **Kiszolgálócsoport:** beállíthatja, hogy az __alapértelmezett__ értékre legyen állítva
- **Név (nem kötelező):** ezt a mezőt hagyja üresen
- **Speciális**
    - **Gazdagép IP-címe:** a Kubernetes-fürt nyilvános IP-címe
    - **Port:** az a port, amelyen a PostgreSQL-példány figyel. Ez a port a parancs kimenetében található psql-kapcsolatok karakterláncának végére mutat `azdata postgres server endpoint -n postgres01` . Nem a 30080-es port, amelyen a Kubernetes figyeli, és a Azure Data Studio Azure-adatvezérlőjéhez való csatlakozáskor adta meg.
    - **Egyéb paraméterek:** Önexplicitnek kell lenniük, ha az alapértelmezett/üres értékeket is meg szeretné jeleníteni.

A kiszolgálóhoz való kapcsolódáshoz válassza **az [ok] és a [kapcsolódás]** lehetőséget.

A csatlakozást követően számos élmény érhető el:
- **Új lekérdezés**
- **Új jegyzetfüzet**
- **Kiterjesztheti a kiszolgáló megjelenítését, és böngészhet/dolgozhat az adatbázisban található objektumokon**
- **...**

## <a name="next-step"></a>Következő lépés
[A kiszolgálócsoport figyelése](monitor-grafana-kibana.md)
