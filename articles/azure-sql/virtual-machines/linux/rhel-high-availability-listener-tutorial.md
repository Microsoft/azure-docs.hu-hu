---
title: Rendelkezésre állási csoport figyelője SQL Server RHEL virtuális gépeken az Azure-ban – Linux Virtual machines | Microsoft Docs
description: Tudnivalók a rendelkezésre állási csoport figyelője beállításáról az Azure-beli RHEL Virtual Machines szolgáltatásban SQL Server
ms.service: virtual-machines-linux
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 83fde9e957cb2011ce585603e51d331be171bc08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586227"
---
# <a name="tutorial-configure-an-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Oktatóanyag: rendelkezésre állási csoport figyelője SQL Server RHEL virtuális gépek Azure-ban való konfigurálásához
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> A bemutatott oktatóanyag **nyilvános előzetes** verzióban érhető el. 
>
> Ebben az oktatóanyagban a SQL Server 2017-es RHEL 7,6-et használjuk, de a magas rendelkezésre állás konfigurálásához a SQL Server 2019 a RHEL 7 vagy a RHEL 8 használatával lehetséges. A rendelkezésre állási csoport erőforrásainak konfigurálására szolgáló parancsok megváltoztak a RHEL 8-ban, és a megfelelő parancsokról további információért tekintse meg a [rendelkezésre állási csoport erőforrásának létrehozása](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) és a RHEL 8 erőforrásai című cikket.

Ez az oktatóanyag a RHEL Virtual Machines (VM) Azure-beli virtuális gépeken futó SQL Server-alapú rendelkezésre állási csoport-figyelő létrehozásához nyújt útmutatást. Az alábbiakat fogja elsajátítani:

> [!div class="checklist"]
> - Terheléselosztó létrehozása a Azure Portalban
> - A terheléselosztó háttérbeli készletének konfigurálása
> - Mintavétel létrehozása a terheléselosztó számára
> - Terheléselosztási szabályok beállítása
> - A terheléselosztó erőforrás létrehozása a fürtben
> - A rendelkezésre állási csoport figyelő létrehozása
> - A figyelőhöz való csatlakozás tesztelése
> - Feladatátvétel tesztelése

## <a name="prerequisite"></a>Előfeltétel

Elkészült [oktatóanyag: a rendelkezésre állási csoportok konfigurálása az Azure-beli virtuális gépek RHEL SQL Server](rhel-high-availability-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>A terheléselosztó létrehozása a Azure Portal

Az alábbi utasítások végigvezetik az 1 – 4. lépésen a Load Balancer [létrehozása és konfigurálása](../windows/availability-group-load-balancer-portal-configure.md#create--configure-load-balancer) a terheléselosztó Azure Portal szakaszában, a terheléselosztó [-Azure Portal](../windows/availability-group-load-balancer-portal-configure.md) cikkben.

### <a name="create-the-load-balancer"></a>A terheléselosztó létrehozása

1. A Azure Portal nyissa meg azt az erőforráscsoportot, amely a SQL Server virtuális gépeket tartalmazza. 

2. Az erőforrás csoportban kattintson a **Hozzáadás** gombra.

3. Keressen rá a **Load Balancer** kifejezésre, majd a keresési eredmények között válassza a **Load Balancer** lehetőséget, amelyet a **Microsoft** tesz közzé.

4. A **Load Balancer** panelen kattintson a **Létrehozás** gombra.

5. A terheléselosztó **létrehozása** párbeszédpanelen konfigurálja a terheléselosztó a következőképpen:

   | Beállítás | Érték |
   | --- | --- |
   | **Név** |A terheléselosztó nevét jelölő szöveges név. Például: **sqlLB**. |
   | **Típus** |**Belső** |
   | **Virtuális hálózat** |A létrehozott alapértelmezett virtuális hálózatnak a **VM1VNET** nevűnek kell lennie. |
   | **Alhálózat** |Válassza ki azt az alhálózatot, amelyhez a SQL Server példányok tartoznak. Az alapértelmezett értéknek **VM1Subnet** kell lennie.|
   | **IP-cím hozzárendelése** |**Statikus** |
   | **Magánhálózati IP-cím** |Használja a `virtualip` fürtben létrehozott IP-címet. |
   | **Előfizetés** |Használja az erőforráscsoporthoz használt előfizetést. |
   | **Erőforráscsoport** |Válassza ki azt az erőforráscsoportot, amelybe a SQL Server példányok tartoznak. |
   | **Hely** |Válassza ki azt az Azure-helyet, amelyen a SQL Server példányok szerepelnek. |

### <a name="configure-the-back-end-pool"></a>A háttér-készlet konfigurálása
Az Azure meghívja a háttérbeli címkészlet *háttér-készletét*. Ebben az esetben a háttér-készlet a rendelkezésre állási csoportban lévő három SQL Server példány címe. 

1. Az erőforráscsoporthoz kattintson a létrehozott terheléselosztó elemre. 

2. A **Beállítások** területen kattintson a **háttér-készletek** elemre.

3. Háttérbeli címkészlet létrehozásához kattintson a **Hozzáadás** elemre a **háttérrendszer**-készletekben. 

4. A **háttérbeli készlet hozzáadása** területen a **név** mezőben adja meg a háttér-készlet nevét.

5. A **társított a** következőhöz területen válassza a **virtuális gép** lehetőséget. 

6. Válassza ki az egyes virtuális gépeket a környezetben, és rendelje hozzá a megfelelő IP-címet az egyes kijelölésekhez.

    :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-backend-pool.png" alt-text="Háttér-készlet hozzáadása":::

7. Kattintson a **Hozzáadás** parancsra. 

### <a name="create-a-probe"></a>Mintavétel létrehozása

A mintavétel határozza meg, hogy az Azure hogyan ellenőrzi, hogy a SQL Server példányok melyike jelenleg rendelkezik a rendelkezésre állási csoport figyelője szolgáltatásával. Az Azure a mintavétel létrehozásakor meghatározott porton alapuló IP-cím alapján ellenőrzi a szolgáltatást.

1. A terheléselosztó **beállításai** panelen kattintson az **állapot**-mintavételek elemre. 

2. Az **állapot** -mintavételek panelen kattintson a **Hozzáadás** gombra.

3. Konfigurálja a mintavételt a mintavétel **hozzáadása** panelen. A mintavétel konfigurálásához használja a következő értékeket:

   | Beállítás | Érték |
   | --- | --- |
   | **Név** |A mintavételt jelölő szöveges név. Például: **SQLAlwaysOnEndPointProbe**. |
   | **Protokoll** |**TCP** |
   | **Port** |Bármely elérhető portot használhat. Például *59999*. |
   | **Intervallum** |*5* |
   | **Nem kifogástalan állapot küszöbértéke** |*2* |

4.  Kattintson az **OK** gombra. 

5. Jelentkezzen be az összes virtuális gépre, és nyissa meg a mintavételi portot a következő parancsokkal:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Az Azure létrehozza a mintavételt, majd a használatával teszteli, hogy melyik SQL Server-példány rendelkezik a rendelkezésre állási csoport figyelővel.

### <a name="set-the-load-balancing-rules"></a>Terheléselosztási szabályok beállítása

A terheléselosztási szabályok azt konfigurálhatják, hogy a terheléselosztó hogyan irányítja át a forgalmat a SQL Server példányokra. Ennél a terheléselosztónál engedélyezi a közvetlen kiszolgáló visszaküldését, mert a három SQL Server-példány közül csak az egyik a rendelkezésre állási csoport figyelő erőforrásának egyszerre van tulajdonosa.

1. A terheléselosztó **beállításai** panelen kattintson a terheléselosztási **szabályok** elemre. 

2. A **terheléselosztási szabályok** panelen kattintson a **Hozzáadás** gombra.

3. A **terheléselosztási szabályok hozzáadása** panelen konfigurálja a terheléselosztási szabályt. Használja a következő beállításokat: 

   | Beállítás | Érték |
   | --- | --- |
   | **Név** |A terheléselosztási szabályokat jelképező szöveges név. Például: **SQLAlwaysOnEndPointListener**. |
   | **Protokoll** |**TCP** |
   | **Port** |*1433* |
   | **Háttérport** |*1433*. Ez az érték figyelmen kívül lesz hagyva, mert ez a szabály **lebegőpontos IP-címet használ (a közvetlen kiszolgáló visszatérése)**. |
   | **Mintavétel** |Használja a terheléselosztó számára létrehozott mintavétel nevét. |
   | **Munkamenet-állandóság** |**Nincs** |
   | **Üresjárati időkorlát (perc)** |*4* |
   | **Lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása)** |**Engedélyezve** |

   :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-load-balancing-rule.png" alt-text="Terheléselosztási szabály hozzáadása":::

4. Kattintson az **OK** gombra. 
5. Az Azure konfigurálja a terheléselosztási szabályt. A terheléselosztó most úgy van konfigurálva, hogy átirányítsa a forgalmat a rendelkezésre állási csoport figyelőjét futtató SQL Server-példányra. 

Ezen a ponton az erőforráscsoport egy terheléselosztó, amely az összes SQL Server géphez csatlakozik. A terheléselosztó IP-címet is tartalmaz a SQL Server always on rendelkezésre állási csoport figyelője számára, így bármelyik gép válaszolhat a rendelkezésre állási csoportok kéréseire.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>A terheléselosztó erőforrás létrehozása a fürtben

1. Jelentkezzen be az elsődleges virtuális gépre. Létre kell hoznia az erőforrást, amely lehetővé teszi az Azure Load Balancer mintavételi port (59999) használatát a példánkban. Futtassa az alábbi parancsot:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Hozzon létre egy csoportot, amely tartalmazza a `virtualip` és az `azure_load_balancer` erőforrást:

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

    - Cserélje le az alább használt IP-címet az `virtualip` IP-címére.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Jelentkezzen be az egyes VM-csomópontokra. A következő parancs használatával nyissa meg a Hosts fájlt, és állítsa be az állomásnév feloldását `ag1-listener` minden egyes gépen.

    ```
    sudo vi /etc/hosts
    ```

    A **VI** szerkesztőben írja be a `i` szöveget, és egy üres sorban adja hozzá a (z) IP-címét `ag1-listener` . Ezután adja hozzá `ag1-listener` az IP-cím melletti szóközt.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    A **VI** -szerkesztőből való kilépéshez először nyomja meg az **ESC** billentyűt, majd írja be a parancsot a `:wq` fájl írásához és a kilépéshez. Ezt minden csomóponton végezze el.

## <a name="test-the-listener-and-a-failover"></a>A figyelő és a feladatátvétel tesztelése

### <a name="test-logging-in-to-sql-server-using-the-availability-group-listener"></a>Bejelentkezés SQL Server a rendelkezésre állási csoport figyelője használatával

1. A SQLCMD használatával jelentkezzen be SQL Server elsődleges csomópontjára a rendelkezésre állási csoport figyelője nevével:

    - Használjon egy korábban létrehozott bejelentkezési azonosítót, és cserélje le `<YourPassword>` a megfelelő jelszóval. Az alábbi példa a `sa` SQL Server használatával létrehozott bejelentkezést használja.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Keresse meg annak a kiszolgálónak a nevét, amelyhez csatlakozik. Futtassa a következő parancsot a SQLCMD-ben:

    ```sql
    SELECT @@SERVERNAME
    ```

    A kimenetnek az aktuális elsődleges csomópontot kell megjelenítenie. Ezt akkor kell megadnia `VM1` , ha még soha nem tesztelt feladatátvételt.

    A parancs beírásával lépjen ki a SQL Server-munkamenetből `exit` .

### <a name="test-a-failover"></a>Feladatátvétel tesztelése

1. Futtassa a következő parancsot az elsődleges replika manuális feladatátvételéhez `<VM2>` vagy egy másik replikához. Cserélje le a `<VM2>` nevet a kiszolgálónév értékére.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Ha megtekinti a korlátozásokat, látni fogja, hogy egy másik korlátozás lett hozzáadva a manuális feladatátvétel miatt:

    ```bash
    sudo pcs constraint list --full
    ```

    Látni fogja, hogy az AZONOSÍTÓval rendelkező megkötés `cli-prefer-ag_cluster-master` hozzá lett adva.

1. Távolítsa el a megkötést AZONOSÍTÓval a `cli-prefer-ag_cluster-master` következő parancs használatával:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Ellenőrizze a fürt erőforrásait a parancs használatával `sudo pcs resource` , és láthatja, hogy az elsődleges példány most már `<VM2>` .

    > [!NOTE]
    > Ez a cikk a Slave kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.


    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. A SQLCMD használatával jelentkezzen be az elsődleges replikára a figyelő nevével:

    - Használjon egy korábban létrehozott bejelentkezési azonosítót, és cserélje le `<YourPassword>` a megfelelő jelszóval. Az alábbi példa a `sa` SQL Server használatával létrehozott bejelentkezést használja.

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
> [A rendelkezésre állási csoport terheléselosztásának konfigurálása SQL Server Azure-beli virtuális gépeken](../windows/availability-group-load-balancer-portal-configure.md)
