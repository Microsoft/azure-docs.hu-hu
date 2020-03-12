---
title: A rendelkezésre állási csoport figyelője a RHEL virtuális gépek Azure-ban való SQL Server konfigurálása – Linux Virtual Machines | Microsoft Docs
description: Tudnivalók a rendelkezésre állási csoport figyelője beállításáról az Azure-beli RHEL Virtual Machines szolgáltatásban SQL Server
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 80557eb3776ba17a4922d1fc384b87419ffbd67e
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096591"
---
# <a name="tutorial-configure-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Oktatóanyag: a rendelkezésre állási csoport figyelő konfigurálása az Azure-beli RHEL virtuális gépeken való SQL Server

> [!NOTE]
> A bemutatott oktatóanyag **nyilvános előzetes**verzióban érhető el. 
>
> Ebben az oktatóanyagban a SQL Server 2017-es RHEL 7,6-et használjuk, de a SQL Server 2019 a RHEL 7 vagy a RHEL 8 használatával konfigurálható. A rendelkezésre állási csoport erőforrásainak konfigurálására vonatkozó parancsok megváltoztak a RHEL 8-ban, és érdemes megtekinteni a [rendelkezésre állási csoport erőforrásának](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) és a RHEL 8 erőforrásoknak a megfelelő parancsokkal kapcsolatos további információkért.

Ez az oktatóanyag bemutatja, hogyan hozhat létre rendelkezésre állási csoportot figyelőt az Azure-beli RHEL virtuális gépeken futó SQL-kiszolgálókhoz. Az alábbiakat fogja elsajátítani:

> [!div class="checklist"]
> - Terheléselosztó létrehozása a Azure Portalban
> - A terheléselosztó háttérbeli készletének konfigurálása
> - Mintavétel létrehozása a terheléselosztó számára
> - Terheléselosztási szabályok beállítása
> - A terheléselosztó erőforrás létrehozása a fürtben
> - A rendelkezésre állási csoport figyelő létrehozása
> - A figyelőhöz való csatlakozás tesztelése
> - Feladatátvétel tesztelése

## <a name="prerequisite"></a>Előfeltételek

Elkészült [ **oktatóanyag: a rendelkezésre állási csoportok konfigurálása az Azure-beli virtuális gépek RHEL SQL Server**](sql-server-linux-rhel-ha-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>A terheléselosztó létrehozása a Azure Portal

Az alábbi utasítások végigvezetik az 1 – 4. lépésen a Load Balancer [létrehozása és konfigurálása](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal) a terheléselosztó Azure Portal szakaszában, a terheléselosztó [-Azure Portal](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md) cikkben.

### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása

1. A Azure Portal nyissa meg azt az erőforráscsoportot, amely a SQL Server virtuális gépeket tartalmazza. 

2. Az erőforrás csoportban kattintson a **Hozzáadás**gombra.

3. Keressen rá a **Load Balancer** kifejezésre, majd a keresési eredmények között válassza a **Load Balancer**lehetőséget, amelyet a **Microsoft**tesz közzé.

4. A **Load Balancer** panelen kattintson a **Létrehozás**gombra.

5. A terheléselosztó **létrehozása** párbeszédpanelen konfigurálja a terheléselosztó a következőképpen:

   | Beállítás | Érték |
   | --- | --- |
   | **Name (Név)** |A terheléselosztó nevét jelölő szöveges név. Például: **sqlLB**. |
   | **Típus** |**Belső** |
   | **Virtuális hálózat** |A létrehozott alapértelmezett VNet **VM1VNET**kell lennie. |
   | **Alhálózat** |Válassza ki azt az alhálózatot, amelyhez a SQL Server példányok tartoznak. Az alapértelmezett értéknek **VM1Subnet**kell lennie.|
   | **IP-cím hozzárendelése** |**Statikus** |
   | **Magánhálózati IP-cím** |Használja a fürtben létrehozott `virtualip` IP-címet. |
   | **Előfizetés** |Használja az erőforráscsoporthoz használt előfizetést. |
   | **Erőforráscsoport** |Válassza ki azt az erőforráscsoportot, amelybe a SQL Server példányok tartoznak. |
   | **Hely** |Válassza ki azt az Azure-helyet, amelyen a SQL Server példányok szerepelnek. |

### <a name="configure-the-back-end-pool"></a>A háttér-készlet konfigurálása
Az Azure meghívja a háttérbeli címkészlet *háttér-készletét*. Ebben az esetben a háttér-készlet a rendelkezésre állási csoportban lévő három SQL Server példány címe. 

1. Az erőforráscsoporthoz kattintson a létrehozott terheléselosztó elemre. 

2. A **Beállítások**területen kattintson a **háttér-készletek**elemre.

3. Háttérbeli címkészlet létrehozásához kattintson a **Hozzáadás** elemre a **háttérrendszer**-készletekben. 

4. A **háttérbeli készlet hozzáadása**területen a **név**mezőben adja meg a háttér-készlet nevét.

5. A **társított a**következőhöz területen válassza a **virtuális gép**lehetőséget. 

6. Válassza ki az egyes virtuális gépeket a környezetben, és rendelje hozzá a megfelelő IP-címet az egyes kijelölésekhez.

    :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-backend-pool.png" alt-text="Háttér-készlet hozzáadása":::

7. Kattintson az **Hozzáadás** parancsra. 

### <a name="create-a-probe"></a>Mintavétel létrehozása

A mintavétel határozza meg, hogy az Azure hogyan ellenőrzi, hogy a SQL Server példányok melyike jelenleg rendelkezik a rendelkezésre állási csoport figyelője szolgáltatásával. Az Azure a mintavétel létrehozásakor meghatározott porton alapuló IP-cím alapján ellenőrzi a szolgáltatást.

1. A terheléselosztó **beállításai** panelen kattintson az **állapot**-mintavételek elemre. 

2. Az **állapot** -mintavételek panelen kattintson a **Hozzáadás**gombra.

3. Konfigurálja a mintavételt a mintavétel **hozzáadása** panelen. A mintavétel konfigurálásához használja a következő értékeket:

   | Beállítás | Érték |
   | --- | --- |
   | **Name (Név)** |A mintavételt jelölő szöveges név. Például: **SQLAlwaysOnEndPointProbe**. |
   | **Protocol (Protokoll)** |**TCP** |
   | **Port** |Bármely elérhető portot használhat. Például *59999*. |
   | **Intervallum** |*5* |
   | **Nem Kifogástalan állapot küszöbértéke** |*2* |

4.  Kattintson az **OK** gombra. 

5. Jelentkezzen be az összes virtuális gépre, és nyissa meg a mintavételi portot a következő parancsokkal:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Az Azure létrehozza a mintavételt, majd a használatával teszteli, hogy melyik SQL Server-példány rendelkezik a rendelkezésre állási csoport figyelővel.

### <a name="set-the-load-balancing-rules"></a>Terheléselosztási szabályok beállítása

A terheléselosztási szabályok azt konfigurálhatják, hogy a terheléselosztó hogyan irányítja át a forgalmat a SQL Server példányokra. Ennél a terheléselosztónál engedélyezi a közvetlen kiszolgáló visszaküldését, mert a három SQL Server-példány közül csak az egyik a rendelkezésre állási csoport figyelő erőforrásának egyszerre van tulajdonosa.

1. A terheléselosztó **beállításai** panelen kattintson a terheléselosztási **szabályok**elemre. 

2. A **terheléselosztási szabályok** panelen kattintson a **Hozzáadás**gombra.

3. A **terheléselosztási szabályok hozzáadása** panelen konfigurálja a terheléselosztási szabályt. Használja az alábbi beállításokat: 

   | Beállítás | Érték |
   | --- | --- |
   | **Name (Név)** |A terheléselosztási szabályokat jelképező szöveges név. Például: **SQLAlwaysOnEndPointListener**. |
   | **Protocol (Protokoll)** |**TCP** |
   | **Port** |*1433* |
   | **Háttér-port** |*1433*. Ez az érték figyelmen kívül lesz hagyva, mert ez a szabály **lebegőpontos IP-címet használ (a közvetlen kiszolgáló visszatérése)** . |
   | **Mintavételi** |Használja a terheléselosztó számára létrehozott mintavétel nevét. |
   | **Munkamenetek megőrzése** |**NEz egy** |
   | **Üresjárati időkorlát (perc)** |*4* |
   | **Lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása)** |**Engedélyezve** |

   :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-load-balancing-rule.png" alt-text="Terheléselosztási szabály hozzáadása":::

4. Kattintson az **OK** gombra. 
5. Az Azure konfigurálja a terheléselosztási szabályt. A terheléselosztó most úgy van konfigurálva, hogy átirányítsa a forgalmat a rendelkezésre állási csoport figyelőjét futtató SQL Server-példányra. 

Ezen a ponton az erőforráscsoport egy terheléselosztó, amely az összes SQL Server géphez csatlakozik. A terheléselosztó IP-címet is tartalmaz a SQL Server always on rendelkezésre állási csoport figyelője számára, így bármelyik gép válaszolhat a rendelkezésre állási csoportok kéréseire.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>A terheléselosztó erőforrás létrehozása a fürtben

1. Jelentkezzen be az elsődleges virtuális gépre. Létre kell hoznia az erőforrást, amely lehetővé teszi az Azure Load Balancer mintavételi port (59999) használatát a példánkban. Futtassa az alábbi parancsot:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Hozzon létre egy csoportot, amely tartalmazza a `virtualip` és `azure_load_balancer` erőforrást:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Megkötések hozzáadása

1. A közös elhelyezési korlátozást úgy kell konfigurálni, hogy az Azure Load Balancer IP-címe és az AG-erőforrás ugyanazon a csomóponton fusson. Futtassa az alábbi parancsot:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Hozzon létre egy megrendelési korlátozást, amely biztosítja, hogy az AG-erőforrás az Azure Load Balancer IP-címének megkezdése előtt fusson. Míg a elhelyezési megkötés egy megrendelés megkötését jelenti, ez kikényszeríti azt.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. A megkötések ellenőrzéséhez futtassa a következő parancsot:

    ```bash
    sudo pcs constraint list --full
    ```

    A következő kimenetnek kell megjelennie:

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>A rendelkezésre állási csoport figyelő létrehozása

1. Az elsődleges csomóponton futtassa az alábbi parancsot a SQLCMD vagy a SSMS:

    - Cserélje le az alább használt IP-címet a `virtualip` IP-címére.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Jelentkezzen be az egyes VM-csomópontokra. A következő parancs használatával nyissa meg a Hosts fájlt, és állítson be állomásnév-feloldást a `ag1-listener` minden gépen.

    ```
    sudo vi /etc/hosts
    ```

    A **VI** szerkesztőben írja be a `i` szöveget, és egy üres sorban adja hozzá a `ag1-listener`IP-címét. Ezután adja hozzá `ag1-listener` az IP-cím melletti terület után.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    A **VI** -szerkesztőből való kilépéshez először nyomja meg az **ESC** billentyűt, majd írja be a `:wq` parancsot a fájl írásához és a kilépéshez. Ezt minden csomóponton végezze el.

## <a name="test-the-listener-and-a-failover"></a>A figyelő és a feladatátvétel tesztelése

### <a name="test-logging-into-sql-server-using-the-availability-group-listener"></a>A SQL Server a rendelkezésre állási csoport figyelője használatával történő bejelentkezés tesztelése

1. A SQLCMD használatával jelentkezzen be SQL Server elsődleges csomópontjára a rendelkezésre állási csoport figyelője nevével:

    - Használjon egy korábban létrehozott bejelentkezési azonosítót, és cserélje le a `<YourPassword>`t a megfelelő jelszóval. Az alábbi példa a SQL Server használatával létrehozott `sa`-bejelentkezést használja.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Keresse meg annak a kiszolgálónak a nevét, amelyhez csatlakozik. Futtassa a következő parancsot a SQLCMD-ben:

    ```sql
    SELECT @@SERVERNAME
    ```

    A kimenetnek az aktuális elsődleges csomópontot kell megjelenítenie. Ezt akkor kell `VM1`, ha még soha nem tesztelt feladatátvételt.

    Az `exit` parancs beírásával lépjen ki az SQL-munkamenetből.

### <a name="test-a-failover"></a>Feladatátvétel tesztelése

1. Futtassa az alábbi parancsot az elsődleges replika manuális feladatátvételéhez `<VM2>` vagy egy másik replikára. Cserélje le a `<VM2>` értéket a kiszolgálónév értékére.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Ha megtekinti a korlátozásokat, látni fogja, hogy egy másik korlátozás lett hozzáadva a manuális feladatátvétel miatt:

    ```bash
    sudo pcs constraint list --full
    ```

    Látni fogja, hogy egy `cli-prefer-ag_cluster-master` AZONOSÍTÓJÚ megkötés lett hozzáadva.

1. Távolítsa el a (z) AZONOSÍTÓJÚ korlátozást a következő parancs használatával `cli-prefer-ag_cluster-master`:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Ellenőrizze a fürt erőforrásait a `sudo pcs resource`parancs használatával, és látnia kell, hogy az elsődleges példány most már `<VM2>`.

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. A SQLCMD használatával jelentkezzen be az elsődleges replikába a figyelő nevével:

    - Használjon egy korábban létrehozott bejelentkezési azonosítót, és cserélje le a `<YourPassword>`t a megfelelő jelszóval. Az alábbi példa a SQL Server használatával létrehozott `sa`-bejelentkezést használja.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Keresse meg azt a kiszolgálót, amelyhez csatlakozik. Futtassa a következő parancsot a SQLCMD-ben:

    ```sql
    SELECT @@SERVERNAME
    ```

    Látnia kell, hogy most már csatlakozik ahhoz a virtuális géphez, amelyre a feladatátvételt elvégezte.

## <a name="next-steps"></a>Következő lépések

További információ az Azure-beli terheléselosztó használatáról:

> [!div class="nextstepaction"]
> [Terheléselosztó konfigurálása egy rendelkezésre állási csoport számára az Azure SQL Server virtuális gépeken](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)
