---
title: Hálózati kommunikáció monitorozása – oktatóanyag – Azure Portal | Microsoft Docs
description: Megismerheti, hogyan monitorozhatja a két virtuális gép közötti hálózati kommunikációt az Azure Network Watcher kapcsolatfigyelő funkciójával.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 5cac4a46fb35ef955903018028abbe7588c94dc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66233887"
---
# <a name="tutorial-monitor-network-communication-between-two-virtual-machines-using-the-azure-portal"></a>Oktatóanyag: Az Azure portal használatával, két virtuális gép közötti hálózati kommunikáció monitorozása

A virtuális gépek és a végpontok (pl. egy másik virtuális gép) közötti sikeres kommunikáció kulcsfontosságú lehet vállalata számára. Olykor előfordulhat, hogy egyes konfigurációs módosítások megszakítják a kommunikációt. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Két virtuális gép létrehozása
> * Virtuális gépek közötti kommunikáció monitorozása a Network Watcher kapcsolatfigyelő funkciójával
> * Riasztások létrehozása a kapcsolatfigyelő metrikáin
> * Virtuális gépek közötti kommunikációs probléma diagnosztizálása és a megoldás megismerése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-vms"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. Az Azure Portal bal felső sarkában kattintson az **+ Erőforrás létrehozása** gombra.
2. Válassza a **Számítás** lehetőséget, majd válasszon egy operációs rendszert. Ebben az oktatóanyagban **Windows Server 2016 Datacenter** rendszert használunk.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|myVm1|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza az **Új létrehozása** elemet, és adja meg a **myResourceGroup** nevet.|
    |Location egység| Válassza az **USA keleti régiója** lehetőséget.|

4. Válassza ki a virtuális gép méretét, majd kattintson a **Kiválasztás** gombra.
5. A **BEÁLLÍTÁSOK** területen válassza a **Bővítmények** elemet. Válassza a **Bővítmény hozzáadása**, majd a **Windowshoz készült Network Watcher-ügynök** lehetőséget, ahogyan az az alábbi képen is látható:

    ![Network Watcher-ügynök bővítmény](./media/connection-monitor/nw-agent-extension.png)

6. A **Windowshoz készült Network Watcher-ügynök** területen válassza a **Létrehozás** elemet, a **Bővítmény telepítése** területen válassza az **OK**, majd a **Bővítmények** területen válassza az **OK** lehetőséget.
7. Fogadja el az alapértelmezett értékeket a többi **beállításnál**, és válassza az **OK** lehetőséget.
8. Az **Összegzés** lap **Létrehozás** területén kattintson a **Létrehozás** elemre a virtuális gép üzembe helyezésének megkezdéséhez.

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Végezze el ismét [Az első virtuális gép létrehozása](#create-the-first-vm) szakaszban leírt lépéseket az alábbi módosításokkal:

|Lépés|Beállítás|Érték|
|---|---|---|
| 1. | Válassza ki a verziót **Ubuntu Server** |                                                                         |
| 3 | Name (Név)                                  | myVm2                                                                   |
| 3 | Hitelesítés típusa                   | Illessze be SSH nyilvános kulcsát, vagy válassza a **Jelszó** lehetőséget és adjon meg egy jelszót. |
| 3 | Erőforráscsoport                        | Válassza a **Meglévő használata** lehetőséget, majd a **myResourceGroup** elemet.                 |
| 6 | Kiterjesztések                            | **Network Watcher-ügynök Linux rendszeren**                                             |

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Várjon, amíg a virtuális gép üzembe helyezése befejeződik, mielőtt folytatná a további lépésekkel.

## <a name="create-a-connection-monitor"></a>Kapcsolatfigyelő létrehozása

Hozzon létre egy kapcsolatfigyelőt a *myVm1* és a *myVm2* virtuális gép között a 22-es TCP-porton keresztüli kommunikáció monitorozásához.

1. Válassza a portál bal oldalán található **Minden szolgáltatás** lehetőséget.
2. Kezdje el begépelni a *Network Watcher* kifejezést a **Szűrő** mezőbe. Amikor a **Network Watcher** elem megjelenik a keresési eredmények között, válassza ki.
3. A **FIGYELÉS** területen válassza a **Kapcsolatfigyelő** lehetőséget.
4. Válassza a **+ Hozzáadás** lehetőséget.
5. Írja be vagy válassza ki a monitorozni kívánt kapcsolat adatait, majd válassza a **Hozzáadás** lehetőséget. Az alábbi képen látható példában a *myVm1* virtuális géptől a *myVm2* virtuális gépre irányuló, a 22-es porton áthaladó kommunikáció van monitorozva:

    | Beállítás                  | Érték               |
    | ---------                | ---------           |
    | Name (Név)                     | myVm1-myVm2(22)     |
    | Source                   |                     |
    | Virtuális gép          | myVm1               |
    | Célhely              |                     |
    | Virtuális gép kiválasztása |                     |
    | Virtuális gép          | myVm2               |
    | Port                     | 22                  |

    ![Kapcsolatfigyelő hozzáadása](./media/connection-monitor/add-connection-monitor.png)

## <a name="view-a-connection-monitor"></a>Kapcsolatfigyelő megtekintése

1. Hajtsa végre a [Kapcsolatfigyelő létrehozása](#create-a-connection-monitor) szakaszban leírt 1–3. lépést a kapcsolatfigyelő megtekintéséhez. Megjelenik a meglévő kapcsolatfigyelők listája, ahogyan az az alábbi képen is látható:

    ![Kapcsolatfigyelők](./media/connection-monitor/connection-monitors.png)

2. Válassza ki a **myVm1-myVm2(22)** nevű figyelőt a fenti képen látható módon, és tekintse meg a figyelő részleteit az alábbi képen látható módon:

    ![A figyelő részletei](./media/connection-monitor/vm-monitor.png)

    Tekintse meg az alábbi információkat:

    | Elem                     | Érték                      | Részletek                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Állapot                   | Elérhető                  | Jelzi, hogy a végpont elérhető-e.|
    | ÁTL. ADATVÁLTÁS          | A kapcsolat adatváltási idejét jelzi ezredmásodpercben. A kapcsolatfigyelő 60 másodpercenként teszteli a kapcsolatot, így a késést az idő függvényében is monitorozhatja.                                         |
    | Ugrások                     | A kapcsolatfigyelő jelzi a két végpont közötti ugrásokat. Eben a példában a kapcsolat két, azonos virtuális hálózaton található virtuális gép között jött létre, ezért csak egy ugrásra van szükség a 10.0.0.5 IP-címre. Ha bármilyen meglévő rendszer vagy egyéni útvonal átirányítja a forgalmat a virtuális gépek között egy VPN-átjárón vagy egy hálózati virtuális berendezésen keresztül, további ugrásokra lesz szükség.                                                                                                                         |
    | ÁLLAPOT                   | A végpontok melletti zöld pipák jelzik, hogy a végpontok kifogástalan állapotúak.    ||

## <a name="generate-alerts"></a>Riasztások létrehozása

A riasztásokat riasztási szabályok hozzák létre az Azure Monitorban, és rendszeres időközönként automatikusan mentett lekérdezéseket vagy egyéni naplókereséseket futtathatnak. A létrehozott riasztások automatikusan futtathatnak egy vagy több műveletet, például értesíthetnek valakit, vagy elindíthatnak egy másik folyamatot. Riasztási szabály beállításakor a megcélzott erőforrás határozza meg a riasztások létrehozásához használható elérhető metrikák listáját.

1. Az Azure Portalon válassza a **Monitor** szolgáltatást, majd válassza a **Riasztások** > **Új riasztási szabály** elemet.
2. Kattintson a **Cél kiválasztása** elemre, majd válassza ki a megcélozni kívánt erőforrásokat. Válassza ki az **előfizetést**, majd adja meg az **erőforrás típusát** a használni kívánt Kapcsolatfigyelő kiszűréséhez.

    ![riasztási képernyő a kiválasztott céllal](./media/connection-monitor/set-alert-rule.png)
1. A megcélozni kívánt erőforrás kiválasztása után válassza a **Feltételek hozzáadása** elemet. A Network Watcher olyan [metrikákkal rendelkezik, amelyeken riasztásokat hozhat létre](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#metrics-and-dimensions-supported). Állítsa be az **elérhető jeleket** a ProbesFailedPercent és az AverageRoundtripMs metrikáin:

    ![riasztás oldal kiválasztott jelekkel](./media/connection-monitor/set-alert-signals.png)
1. Adja meg a riasztás részleteit, például a riasztási szabály nevét, a leírását és a súlyosságát. Egy műveletcsoportot is hozzáadhat a riasztáshoz a riasztás válaszának automatizálása és testreszabása érdekében.

## <a name="view-a-problem"></a>Probléma megtekintése

Alapértelmezés szerint az Azure minden porton engedélyezi a kommunikációt két, azonos virtuális hálózaton található virtuális gép között. Idővel azonban Ön, vagy valaki a vállalatnál felülírhatja az Azure alapértelmezett szabályait, ezzel véletlenül a kommunikáció megszakadását okozva. Az alábbi lépések végrehajtásával idézzen elő kommunikációs problémát, majd tekintse meg ismét a kapcsolatfigyelőt:

1. Írja be a *myResourceGroup* nevet a portál tetején lévő keresőmezőbe. Amikor a **myResourceGroup** erőforráscsoport megjelenik a keresési eredmények között, válassza ki.
2. Válassza ki a **myVm2-nsg** hálózati biztonsági csoportot.
3. Válassza a **Bejövő biztonsági szabályok**, majd a **Hozzáadás** elemet a következő képen látható módon:

    ![Bejövő biztonsági szabály](./media/connection-monitor/inbound-security-rules.png)

4. Az alapértelmezett szabály, amely engedélyezi a kommunikációt az azonos virtuális hálózaton található virtuális gépek között, a következő: **AllowVnetInBound**. Hozzon létre egy szabályt az **AllowVnetInBound** szabálynál magasabb prioritással (alacsonyabb számmal), amely elutasítja a 22-es porton keresztül érkező bejövő kommunikációt. Válassza ki vagy írja be a következő adatokat, fogadja el a fennmaradó alapértelmezett adatokat, majd válassza a **Hozzáadás** elemet:

    | Beállítás                 | Érték          |
    | ---                     | ---            |
    | Célporttartományok | 22             |
    | Műveletek                  | Megtagadás           |
    | Prioritás                | 100            |
    | Name (Név)                    | DenySshInbound |

5. Mivel a kapcsolatfigyelő 60 másodpercenként vesz mintát, várjon néhány percet, és a portál bal oldalán válassza a **Network Watcher**, majd a **Kapcsolatfigyelő** elemet, és válassza ki ismét a **myVm1-myVm2(22)** figyelőt. Az eredmények most eltérőek lesznek, ahogyan az a következő képen látható:

    ![Részletek monitorozásával kapcsolatos hiba](./media/connection-monitor/vm-monitor-fault.png)

    Egy piros felkiáltójel ikon látható a **myvm2529** hálózati adapter állapotoszlopában.

6. Az állapotváltozás okának megismeréséhez válassza ki az előző képen látható 10.0.0.5-ös IP-címet. A kapcsolatfigyelő tájékoztatja, hogy a kommunikációs hiba oka: *Forgalomleállás miatt a következő hálózati biztonsági csoportra vonatkozó szabályt: UserRule_DenySshInbound*.

    Ha nem tudott róla, hogy valaki megvalósította a 4. lépésben létrehozott biztonsági szabályt, a kapcsolatfigyelőből megtudhatta, hogy ez a szabály okozza a kommunikációs problémát. Ezután módosíthatja, felülírhatja vagy eltávolíthatja a szabályt, és visszaállíthatja a virtuális gépek közötti kommunikációt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan monitorozhatja a két virtuális gép közötti kommunikációt. Megtudta, hogy egy hálózati biztonsági csoport szabály akadályozta meg a virtuális géppel való kommunikációt. A kapcsolatfigyelő által visszaadható válaszok megismeréséhez tekintse meg a [választípusokra](network-watcher-connectivity-overview.md#response) vonatkozó részt. Lehetősége van egy virtuális gép, egy teljes tartománynév, egy URI vagy egy IP-cím közötti kommunikáció monitorozására is.

Előfordulhat, hogy egy virtuális hálózat erőforrásai nem képesek kommunikálni más, Azure virtuális hálózati átjáróval csatlakoztatott hálózatok erőforrásaival. A következő oktatóanyag a virtuális hálózati átjárókkal kapcsolatos problémák diagnosztizálását mutatja be.

> [!div class="nextstepaction"]
> [Hálózatok közötti kommunikációs problémák diagnosztizálása](diagnose-communication-problem-between-networks.md)
