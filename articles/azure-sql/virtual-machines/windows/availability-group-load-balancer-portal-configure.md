---
title: Rendelkezésre állási csoport figyelők konfigurálása & Load Balancer (Azure Portal)
description: Részletes útmutató figyelő létrehozásához az Azure Virtual Machines szolgáltatásban SQL Server az Always On rendelkezésre állási csoport számára
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e53a6a4875b3dde55d1822daa342d6cde536d1c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100096429"
---
# <a name="configure-a-load-balancer-for-a-sql-server-always-on-availability-group-in-azure-virtual-machines"></a>Terheléselosztó konfigurálása SQL Server always on rendelkezésre állási csoporthoz az Azure-ban Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Ez a cikk azt ismerteti, hogyan hozható létre terheléselosztó egy SQL Server always on rendelkezésre állási csoport számára az Azure-beli Virtual Machines, amely a Azure Resource Manager használatával fut. A rendelkezésre állási csoportoknak Load balancerre van szükségük, ha a SQL Server példányok az Azure Virtual Machines. A terheléselosztó tárolja az IP-címet a rendelkezésre állási csoport figyelője számára. Ha egy rendelkezésre állási csoport több régióra is kiterjed, az egyes régiókban terheléselosztó szükséges.

A feladat végrehajtásához rendelkeznie kell egy, a Resource Managerrel futó Azure-beli virtuális gépeken üzembe helyezett SQL Server always on rendelkezésre állási csoporttal. Mindkét SQL Server virtuális gépnek ugyanahhoz a rendelkezésre állási csoporthoz kell tartoznia. A rendelkezésre állási csoport automatikus létrehozásához használhatja a [Microsoft-sablont](./availability-group-quickstart-template-configure.md) a Resource Managerben. Ez a sablon automatikusan létrehoz egy belső terheléselosztó-t. 

Ha szeretné, [manuálisan is konfigurálhatja a rendelkezésre állási csoportokat](availability-group-manually-configure-tutorial.md).

Ez a cikk megköveteli, hogy a rendelkezésre állási csoportok már konfigurálva legyenek.  

Kapcsolódó cikkek megtekintése:

* [AlwaysOn rendelkezésre állási csoportok konfigurálása az Azure-beli virtuális gépen (GUI)](availability-group-manually-configure-tutorial.md)   
* [Virtuális hálózatok közötti kapcsolat létrehozása az Azure Resource Manager és a PowerShell használatával](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Ennek a cikknek a bejárásával létrehozhatja és konfigurálhatja a Load balancert a Azure Portal. A folyamat befejezése után konfigurálja a fürtöt úgy, hogy a terheléselosztó IP-címét használja a rendelkezésre állási csoport figyelője számára.

## <a name="create--configure-load-balancer"></a>A Load Balancer létrehozása & konfigurálása 

A feladat ezen részében hajtsa végre a következő lépéseket:

1. A Azure Portal hozza létre a terheléselosztó és konfigurálja az IP-címet.
2. Konfigurálja a háttér-készletet.
3. Hozza létre a mintavételt. 
4. Állítsa be a terheléselosztási szabályokat.

> [!NOTE]
> Ha a SQL Server példányok több erőforráscsoport és régió között találhatók, minden egyes erőforráscsoport esetében végezze el az egyes lépéseket kétszer.
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>1. lépés: a terheléselosztó létrehozása és az IP-cím konfigurálása

Először hozza létre a Load balancert. 

1. A Azure Portal nyissa meg azt az erőforráscsoportot, amely a SQL Server virtuális gépeket tartalmazza. 

2. Az erőforráscsoport területen válassza a **Hozzáadás** lehetőséget.

3. Keresse meg a **Load balancert**. A keresési eredmények között válassza a **Load Balancer** (a **Microsoft** által közzétett) lehetőséget.

4. A **Load Balancer** panelen válassza a **Létrehozás** lehetőséget.

5. A terheléselosztó **létrehozása** párbeszédpanelen konfigurálja a terheléselosztó a következőképpen:

   | Beállítás | Érték |
   | --- | --- |
   | **Név** |A terheléselosztó nevét jelölő szöveges név. Például: **sqlLB**. |
   | **Típus** |**Belső**: a legtöbb implementáció belső Load balancert használ, amely lehetővé teszi, hogy az ugyanazon a virtuális hálózaton lévő alkalmazások csatlakozzanak a rendelkezésre állási csoporthoz.  </br> **Külső**: lehetővé teszi, hogy az alkalmazások nyilvános internetkapcsolaton keresztül csatlakozzanak a rendelkezésre állási csoporthoz. |
   | **Termékváltozat** |**Alapszintű**: alapértelmezett beállítás. Csak akkor érvényes, ha SQL Server példányok ugyanahhoz a rendelkezésre állási csoporthoz tartoznak. </br> **Standard**: előnyben részesített. Érvényes, ha SQL Server példányok ugyanahhoz a rendelkezésre állási csoporthoz tartoznak. Kötelező, ha a SQL Server példányai eltérő rendelkezésre állási zónákban találhatók. |
   | **Virtuális hálózat** |Válassza ki azt a virtuális hálózatot, amelyre a SQL Server példányok tartoznak. |
   | **Alhálózat** |Válassza ki azt az alhálózatot, amelyhez a SQL Server példányok tartoznak. |
   | **IP-cím hozzárendelése** |**Statikus** |
   | **Magánhálózati IP-cím** |Válasszon ki egy elérhető IP-címet az alhálózatból. Akkor használja ezt az IP-címet, ha egy figyelőt hoz létre a fürtön. A jelen cikk későbbi részében található PowerShell-szkriptben használja ezt a címeket a `$ListenerILBIP` változóhoz. |
   | **Előfizetés** |Ha több előfizetéssel rendelkezik, ez a mező jelenhet meg. Válassza ki azt az előfizetést, amelyet hozzá szeretne rendelni ehhez az erőforráshoz. Általában ugyanazt az előfizetést, mint a rendelkezésre állási csoport összes erőforrását. |
   | **Erőforráscsoport** |Válassza ki azt az erőforráscsoportot, amelybe a SQL Server példányok tartoznak. |
   | **Hely** |Válassza ki azt az Azure-helyet, amelyen a SQL Server példányok szerepelnek. |

6. Válassza a **Létrehozás** lehetőséget. 

Az Azure létrehozza a Load balancert. A terheléselosztó egy adott hálózathoz, alhálózathoz, erőforráscsoporthoz és helyhez tartozik. Miután az Azure befejezte a feladatot, ellenőrizze a terheléselosztó beállításait az Azure-ban. 

### <a name="step-2-configure-the-back-end-pool"></a>2. lépés: a háttér-készlet konfigurálása

Az Azure meghívja a háttérbeli címkészlet *háttér-készletét*. Ebben az esetben a háttér-készlet a rendelkezésre állási csoportban lévő két SQL Server példány címe. 

1. Az erőforráscsoport területen válassza ki a létrehozott terheléselosztó. 

2. A **Beállítások** területen válassza a **háttér-készletek** elemet.

3. **Háttérbeli** címkészlet létrehozásához kattintson a **Hozzáadás** elemre. 

4. A **háttérbeli készlet hozzáadása** területen a **név** mezőben adja meg a háttér-készlet nevét.

5. A **virtuális gépek** területen válassza **a virtuális gép hozzáadása** elemet. 

6. A **virtuális gépek kiválasztása** területen válassza a **rendelkezésre állási csoport kiválasztása** lehetőséget, majd adja meg azt a rendelkezésre állási készletet, amelyhez a SQL Server virtuális gépek tartoznak.

7. Miután kiválasztotta a rendelkezésre állási csoportot, válassza a **virtuális gépek** kiválasztása lehetőséget, válassza ki a két virtuális gépet, amelyek a rendelkezésre állási csoportban lévő SQL Server példányokat futtatják, majd válassza a **kiválasztás** lehetőséget. 

8. Kattintson **az OK** gombra a **virtuális gépek kiválasztásához** és a **háttér-készlet hozzáadásához**. 

Az Azure frissíti a háttér-címkészlet beállításait. A rendelkezésre állási csoportnak most két SQL Server példánya van.

### <a name="step-3-create-a-probe"></a>3. lépés: mintavétel létrehozása

A mintavétel határozza meg, hogy az Azure hogyan ellenőrzi, hogy a SQL Server példányok melyike jelenleg rendelkezik a rendelkezésre állási csoport figyelője szolgáltatásával. Az Azure a mintavétel létrehozásakor meghatározott porton alapuló IP-cím alapján ellenőrzi a szolgáltatást.

1. A terheléselosztó **beállításai** panelen válassza az **állapot**-mintavétel lehetőséget. 

2. Az állapotfigyelő **Szondák** panelen válassza a **Hozzáadás** lehetőséget.

3. Konfigurálja a mintavételt a mintavétel **hozzáadása** panelen. A mintavétel konfigurálásához használja a következő értékeket:

   | Beállítás | Érték |
   | --- | --- |
   | **Név** |A mintavételt jelölő szöveges név. Például: **SQLAlwaysOnEndPointProbe**. |
   | **Protokoll** |**TCP** |
   | **Port** |Bármely elérhető portot használhat. Például *59999*. |
   | **Intervallum** |*5* |
   | **Nem kifogástalan állapot küszöbértéke** |*2* |

4.  Válassza az **OK** lehetőséget. 

> [!NOTE]
> Győződjön meg arról, hogy a megadott port meg van nyitva a tűzfalon mindkét SQL Server példányon. Mindkét példányhoz szükség van egy bejövő szabályra a használt TCP-porthoz. További információ: [Tűzfalszabály hozzáadása vagy szerkesztése](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)). 
> 

Az Azure létrehozza a mintavételt, majd a használatával teszteli, hogy melyik SQL Server-példány rendelkezik a rendelkezésre állási csoport figyelővel.

### <a name="step-4-set-the-load-balancing-rules"></a>4. lépés: a terheléselosztási szabályok beállítása

A terheléselosztási szabályok azt konfigurálhatják, hogy a terheléselosztó hogyan irányítja át a forgalmat a SQL Server példányokra. Ennél a terheléselosztónál engedélyezi a közvetlen kiszolgáló visszaküldését, mert a két SQL Server példány közül csak az egyik rendelkezik a rendelkezésre állási csoport figyelő erőforrásával.

1. A terheléselosztó **beállításai** panelen válassza a terheléselosztási **szabályok** lehetőséget. 

2. A **terheléselosztási szabályok** panelen válassza a **Hozzáadás** lehetőséget.

3. A **terheléselosztási szabályok hozzáadása** panelen konfigurálja a terheléselosztási szabályt. Használja a következő beállításokat: 

   | Beállítás | Érték |
   | --- | --- |
   | **Név** |A terheléselosztási szabályokat jelképező szöveges név. Például: **SQLAlwaysOnEndPointListener**. |
   | **Protokoll** |**TCP** |
   | **Port** |*1433* |
   | **Háttér-port** |*1433*. Ez az érték figyelmen kívül lesz hagyva, mert ez a szabály **lebegőpontos IP-címet használ (a közvetlen kiszolgáló visszatérése)**. |
   | **Mintavétel** |Használja a terheléselosztó számára létrehozott mintavétel nevét. |
   | **Munkamenet-állandóság** |**Nincs** |
   | **Üresjárati időkorlát (perc)** |*4* |
   | **Lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása)** |**Engedélyezve** |

   > [!NOTE]
   > Előfordulhat, hogy az összes beállítás megtekintéséhez le kell görgetni a panelt.
   > 

4. Válassza az **OK** lehetőséget. 

5. Az Azure konfigurálja a terheléselosztási szabályt. A terheléselosztó most úgy van konfigurálva, hogy átirányítsa a forgalmat a rendelkezésre állási csoport figyelőjét futtató SQL Server-példányra. 

Ezen a ponton az erőforráscsoport egy terheléselosztó, amely mindkét SQL Server géphez csatlakozik. A terheléselosztó IP-címet is tartalmaz a SQL Server always on rendelkezésre állási csoport figyelőhöz, így bármelyik gép válaszolhat a rendelkezésre állási csoportok kéréseire.

> [!NOTE]
> Ha a SQL Server példányai két különálló régióban találhatók, ismételje meg a másik régió lépéseit. Az egyes régiókban terheléselosztó szükséges. 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>A fürt konfigurálása a terheléselosztó IP-címének használatára

A következő lépés a figyelő konfigurálása a fürtön, és a figyelő online állapotba helyezése. Hajtsa végre a következő lépéseket: 

1. Hozza létre a rendelkezésre állási csoport figyelőjét a feladatátvevő fürtön. 

2. A figyelő online állapotba helyezése.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>5. lépés: a rendelkezésre állási csoport figyelő létrehozása a feladatátvevő fürtön

Ebben a lépésben manuálisan hozza létre a rendelkezésre állási csoport figyelőjét Feladatátvevőfürt-kezelő és SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>A figyelő konfigurációjának ellenőrzése

Ha a fürt erőforrásai és függőségei megfelelően vannak konfigurálva, a figyelőt SQL Server Management Studioban tekintheti meg. A figyelő portjának beállításához hajtsa végre a következő lépéseket:

1. Indítsa el SQL Server Management Studio, majd kapcsolódjon az elsődleges replikához.

2. Nyissa meg a **AlwaysOn magas rendelkezésre** állási  >  **csoportok**  >  **rendelkezésre állási csoportjának figyelőit**.  

    Ekkor megjelenik a Feladatátvevőfürt-kezelőban létrehozott figyelő neve. 

3. Kattintson a jobb gombbal a figyelő nevére, majd válassza a **Tulajdonságok parancsot**.

4. A **port** mezőben adja meg a rendelkezésre állási csoport figyelő portszámát a korábban használt $EndpointPort használatával (1433 volt az alapértelmezett), majd kattintson az **OK gombra**.

Most már rendelkezik egy rendelkezésre állási csoporttal az Azure-beli virtuális gépeken, amelyek Resource Manager módban futnak. 

## <a name="test-the-connection-to-the-listener"></a>A figyelővel létesített kapcsolatok tesztelése

Tesztelje a kapcsolatokat a következő lépések végrehajtásával:

1. A Remote Desktop Protocol (RDP) használatával olyan SQL Server-példányhoz csatlakozhat, amely ugyanabban a virtuális hálózatban található, de nem tulajdonosa a replikának. Ez a kiszolgáló lehet a fürt másik SQL Server példánya is.

2. A **Sqlcmd** segédprogram használatával tesztelheti a kapcsolódást. Az alábbi szkript például egy **Sqlcmd** kapcsolatot létesít az elsődleges replikával a figyelőn keresztül a Windows-hitelesítéssel:

    ```console
    sqlcmd -S <listenerName> -E
    ```

Az SQLCMD-kapcsolat automatikusan csatlakozik az elsődleges replikát futtató SQL Server-példányhoz. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Új rendelkezésre állási csoport IP-címének létrehozása

Minden rendelkezésre állási csoport külön figyelőt használ. Minden figyelő saját IP-címmel rendelkezik. A további figyelők IP-címének tárolására ugyanazt a terheléselosztót használja. Miután létrehozta a rendelkezésre állási csoportot, adja hozzá az IP-címet a terheléselosztó számára, majd konfigurálja a figyelőt.

Ha IP-címet szeretne hozzáadni egy terheléselosztó számára a Azure Portal, hajtsa végre a következő lépéseket:

1. A Azure Portal nyissa meg a terheléselosztót tartalmazó erőforráscsoportot, majd válassza ki a Load balancert. 

2. A **Beállítások** területen válassza ki a ELŐTÉRI **IP-készlet** elemet, majd kattintson a **Hozzáadás** gombra. 

3. Az előtérbeli **IP-cím hozzáadása** területen rendeljen hozzá egy nevet az előtérhöz. 

4. Ellenőrizze, hogy a **virtuális hálózat** és az **alhálózat** megegyezik-e a SQL Server példányokkal.

5. Állítsa be a figyelő IP-címét. 
   
   >[!TIP]
   >Az IP-címet statikusra állíthatja, és olyan címet adhat meg, amely jelenleg nem használatos az alhálózatban. Másik lehetőségként beállíthatja az IP-címet a dinamikus értékre, és mentheti az új előtér-IP-címkészletet. Ha így tesz, a Azure Portal automatikusan hozzárendel egy elérhető IP-címet a készlethez. Ezután újra megnyithatja az előtér-IP-készletet, és megváltoztathatja a hozzárendelést statikusra. 

6. Mentse a figyelő IP-címét. 

7. Adja hozzá az állapot-mintavételt a következő beállítások használatával:

   |Beállítás |Érték
   |:-----|:----
   |**Név** |A mintavételt azonosító név.
   |**Protokoll** |TCP
   |**Port** |Egy nem használt TCP-port, amelynek minden virtuális gépen elérhetőnek kell lennie. Más célra nem használható. Két figyelő nem használhatja ugyanazt a mintavételi portot. 
   |**Intervallum** |A mintavételi kísérletek közötti idő. Használja az alapértelmezett (5) értéket.
   |**Nem kifogástalan állapot küszöbértéke** |Azon egymást követő küszöbértékek száma, amelyeknek sikertelennek kell lennie a virtuális gép nem megfelelő állapotának számítása előtt.

8. A mintavétel mentéséhez kattintson **az OK gombra** . 

9. Hozzon létre egy terheléselosztási szabályt. Válassza a **terheléselosztási szabályok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.

10. Konfigurálja az új terheléselosztási szabályt a következő beállítások használatával:

    |Beállítás |Érték
    |:-----|:----
    |**Név** |A terheléselosztási szabályt azonosító név. 
    |**Előtérbeli IP-cím** |Válassza ki a létrehozott IP-címet. 
    |**Protokoll** |TCP
    |**Port** |Használja az SQL Server-példányok által használt portot. Az alapértelmezett példány az 1433-es portot használja, hacsak nem módosította. 
    |**Háttérport** |Használja ugyanazt az értéket, mint a **port**.
    |**Háttérkészlet** |Az SQL Server példányokkal rendelkező virtuális gépeket tartalmazó készlet. 
    |**Állapotteszt** |Válassza ki a létrehozott mintavételt.
    |**Munkamenet-állandóság** |Nincsenek
    |**Üresjárati időkorlát (perc)** |Alapértelmezett (4)
    |**Lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása)** | Engedélyezve

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>A rendelkezésre állási csoport konfigurálása az új IP-cím használatára

A fürt konfigurálásának befejezéséhez ismételje meg az első rendelkezésre állási csoport létrehozásakor követett lépéseket. Vagyis konfigurálja úgy a [fürtöt, hogy az új IP-címet használja](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Miután hozzáadott egy IP-címet a figyelőhöz, konfigurálja a további rendelkezésre állási csoportot a következő lépések végrehajtásával: 

1. Győződjön meg arról, hogy az új IP-címhez tartozó mintavételi port mindkét SQL Server virtuális gépen meg van nyitva. 

2. [A Fürtfelügyelőben adja hozzá az ügyfél-hozzáférési pontot](#addcap).

3. [Konfigurálja az IP-erőforrást a rendelkezésre állási csoport számára](#congroup).

   >[!IMPORTANT]
   >Az IP-cím létrehozásakor használja a terheléselosztó számára hozzáadott IP-címet.  

4. [Az SQL Server rendelkezésre állási csoport erőforrásának kihasználása az ügyfél-hozzáférési ponttól függ](#dependencyGroup).

5. [Az ügyfél-hozzáférési pont erőforrásának kihasználása az IP-címről függ](#listname).
 
6. [A fürt paramétereinek beállítása a PowerShellben](#setparam).

Miután konfigurálta a rendelkezésre állási csoportot az új IP-cím használatára, konfigurálja a figyelőhöz való kapcsolódást. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Elosztott rendelkezésre állási csoport terheléselosztási szabályának hozzáadása

Ha egy rendelkezésre állási csoport részt vesz egy elosztott rendelkezésre állási csoportban, a terheléselosztó további szabályra van szüksége. Ez a szabály az elosztott rendelkezésre állási csoport figyelője által használt portot tárolja.

>[!IMPORTANT]
>Ez a lépés csak akkor érvényes, ha a rendelkezésre állási csoport részt vesz egy [elosztott rendelkezésre állási csoportban](/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Minden olyan kiszolgálón, amely részt vesz az elosztott rendelkezésre állási csoportban, hozzon létre egy bejövő szabályt az elosztott rendelkezésre állási csoport figyelő TCP-portján. A dokumentáció számos példát használ a 5022-es verzióra. 

1. A Azure Portal válassza a terheléselosztó lehetőséget, majd válassza a terheléselosztási **szabályok** lehetőséget, majd válassza a **+ Hozzáadás** lehetőséget. 

1. Hozza létre a terheléselosztási szabályt a következő beállításokkal:

   |Beállítás |Érték
   |:-----|:----
   |**Név** |Az elosztott rendelkezésre állási csoport terheléselosztási szabályát azonosító név. 
   |**Előtérbeli IP-cím** |Használja ugyanazt a frontend IP-címet, mint a rendelkezésre állási csoportot.
   |**Protokoll** |TCP
   |**Port** |5022 – az [elosztott rendelkezésre állási csoport végpont-figyelője](/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups)portja.</br> Bármely elérhető port lehet.  
   |**Háttérport** | 5022 – ugyanazt az értéket használja, mint a **port**.
   |**Háttérkészlet** |Az SQL Server példányokkal rendelkező virtuális gépeket tartalmazó készlet. 
   |**Állapotteszt** |Válassza ki a létrehozott mintavételt.
   |**Munkamenet-állandóság** |Nincsenek
   |**Üresjárati időkorlát (perc)** |Alapértelmezett (4)
   |**Lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása)** | Engedélyezve

Ismételje meg ezeket a lépéseket a terheléselosztó számára az elosztott rendelkezésre állási csoportokban részt vevő többi rendelkezésre állási csoportban.

Ha egy Azure hálózati biztonsági csoporttal korlátozza a hozzáférést, győződjön meg arról, hogy az engedélyezési szabályok a következők:
- A háttér-SQL Server VM IP-címei
- A Load Balancer az AG-figyelőhöz tartozó lebegőpontos IP-címei
- A fürt alapvető IP-címe, ha van ilyen.

## <a name="next-steps"></a>Következő lépések

- [SQL Server always on rendelkezésre állási csoport konfigurálása különböző régiókban található Azure-beli virtuális gépeken](availability-group-manually-configure-multiple-regions.md)
