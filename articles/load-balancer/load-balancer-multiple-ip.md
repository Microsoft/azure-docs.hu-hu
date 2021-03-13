---
title: Terheléselosztás több IP-konfiguráción – Azure Portal
titleSuffix: Azure Load Balancer
description: Ebben a cikkben megismerheti az elsődleges és másodlagos IP-konfigurációk terheléselosztását a Azure Portal használatával.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 3d28946aad263af635a0139e68d424a77a1eab25
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417824"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Terheléselosztás több IP-konfiguráción a Azure Portal használatával

> [!div class="op_single_selector"]
> * [Portál](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [Parancssori felület](load-balancer-multiple-ip-cli.md)

Ebben a cikkben bemutatjuk, hogyan használhatók a Azure Load Balancer több IP-címmel egy másodlagos hálózati adapteren (NIC). A következő ábra szemlélteti a forgatókönyvet:

![Terheléselosztói forgatókönyv](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

A forgatókönyvben a következő konfigurációt használjuk:

- Két virtuális gép (VM), amely Windows rendszert futtat.
- Minden virtuális gép rendelkezik egy elsődleges és egy másodlagos hálózati adapterrel.
- Minden másodlagos NIC két IP-konfigurációval rendelkezik.
- Mindegyik virtuális gép két webhelyet üzemeltet: contoso.com és fabrikam.com.
- Minden webhely egy IP-konfigurációhoz van kötve a másodlagos hálózati adapteren.
- A Azure Load Balancer két előtér-IP-címet tesz elérhetővé, egyet az egyes webhelyekhez. Az előtérbeli címek a forgalom elosztására szolgálnak az egyes webhelyek megfelelő IP-konfigurációjához.
- Ugyanazt a portszámot használja mind az előtér-, mind a háttér-készlet IP-címeihez.

## <a name="prerequisites"></a>Előfeltételek

A példában feltételezzük, hogy van egy **contosofabrikam** nevű erőforráscsoport, amely a következőképpen van konfigurálva:

- Az erőforráscsoport tartalmaz egy **myVNet** nevű virtuális hálózatot.
- A **myVNet** hálózat két, **VM1** és **VM2** nevű virtuális gépet tartalmaz.
- A VM1 és a VM2 ugyanazon rendelkezésre állási csoportba tartoznak, **myavailset elemet** néven. 
- A VM1 és a VM2 mindegyikének van egy **VM1NIC1** és **VM2NIC1** nevű elsődleges hálózati adaptere. 
- A VM1 és a VM2 egy **VM1NIC2** és **VM2NIC2** nevű másodlagos hálózati adapterrel rendelkeznek.

A virtuális gépek több hálózati adapterrel történő létrehozásával kapcsolatos további információkért lásd: [virtuális gép létrehozása több hálózati adapterrel a PowerShell használatával](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Terheléselosztás végrehajtása több IP-konfiguráción

A jelen cikkben ismertetett forgatókönyv megvalósításához hajtsa végre az alábbi lépéseket.

### <a name="step-1-configure-the-secondary-nics"></a>1. lépés: a másodlagos hálózati adapterek konfigurálása

A virtuális hálózat minden egyes virtuális gépén adja hozzá a másodlagos hálózati adapter IP-konfigurációját:  

1. Keresse meg a Azure Portal: https://portal.azure.com . Jelentkezzen be az Azure-fiókjával.

2. A képernyő bal felső részén válassza ki az **erőforráscsoport** ikont. Ezután válassza ki azt az erőforráscsoportot, amelyben a virtuális gépek találhatók (például **contosofabrikam**). Az **erőforráscsoportok** ablaktábla megjeleníti a virtuális gépek összes erőforrását és hálózati adapterét.

3. Az egyes virtuális gépek másodlagos hálózati adapteréhez adja hozzá az IP-konfigurációt:

    1. Válassza ki a konfigurálni kívánt másodlagos hálózati adaptert.
    
    2. Válassza az **IP-konfigurációk** lehetőséget. A következő ablaktáblán a felső részen válassza a **Hozzáadás** lehetőséget.

    3. Az **IP-konfigurációk hozzáadása** területen adjon hozzá egy második IP-konfigurációt a hálózati adapterhez: 

        1. Adja meg a másodlagos IP-konfiguráció nevét. (A VM1 és a VM2 esetében például a **VM1NIC2-ipconfig2** és a **VM2NIC2-ipconfig2** IP-konfiguráció nevet adja meg.)

        2. A **magánhálózati IP-cím**, **kiosztási** beállítás területen válassza a **statikus** lehetőséget.

        3. Válassza az **OK** lehetőséget.

A másodlagos hálózati adapter második IP-konfigurációjának befejeződése után megjelenik az adott hálózati adapter **IP-konfigurációk** beállításai között.

### <a name="step-2-create-the-load-balancer"></a>2. lépés: A terheléselosztó létrehozása

A terheléselosztó létrehozása a konfigurációhoz:

1. Keresse meg a Azure Portal: https://portal.azure.com . Jelentkezzen be az Azure-fiókjával.

2. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Load Balancer** elemet. Ezután válassza a **Létrehozás** lehetőséget.

3. A terheléselosztó **létrehozása** területen adja meg a terheléselosztó nevét. Ebben a forgatókönyvben a **mylb** nevet használjuk.

4. A **nyilvános IP-cím** területen hozzon létre egy új, **PublicIP1** nevű nyilvános IP-címet.

5. Az **erőforráscsoport** területen válassza ki a virtuális gépekhez tartozó meglévő erőforráscsoportot (például **contosofabrikam**). Válassza ki a terheléselosztó üzembe helyezésének helyét, majd kattintson **az OK gombra**.

A terheléselosztó megkezdi a telepítést. Az üzembe helyezés eltarthat néhány percig, hogy sikeresen befejeződjön. Az üzembe helyezés befejezése után a terheléselosztó erőforrásként jelenik meg az erőforráscsoporthoz.

### <a name="step-3-configure-the-front-end-ip-pool"></a>3. lépés: az előtér-IP-készlet konfigurálása

Az egyes webhelyekhez (contoso.com és fabrikam.com) konfigurálja az előtér-IP-készletet a terheléselosztó számára:

1. A portálon válassza a **További szolgáltatások** lehetőséget. A szűrő mezőbe írja be a **nyilvános IP-címet** , majd válassza a **nyilvános IP-címek** lehetőséget. A következő ablaktáblán a felső részen válassza a **Hozzáadás** lehetőséget.

2. Két nyilvános IP-cím (**PublicIP1** és **PublicIP2**) konfigurálása mindkét webhelyhez (contoso.com és fabrikam.com):

   1. Adja meg az előtér-IP-cím nevét.

   2. Az **erőforráscsoport** mezőben válassza ki a virtuális gépekhez tartozó meglévő erőforráscsoportot (például **contosofabrikam**).

   3. A **hely** mezőben válassza ki a virtuális gépekkel megegyező helyet.

   4. Válassza az **OK** lehetőséget.

      A nyilvános IP-címek létrehozása után azok a **nyilvános IP-** címek alatt jelennek meg.

3. <a name="step3-3"></a>A portálon válassza a **További szolgáltatások** lehetőséget. A szűrő mezőbe írja be a **Load balancert** , majd válassza a **Load Balancer** lehetőséget. 

4. Válassza ki azt a terheléselosztó (**mylb**), amelyhez hozzá szeretné adni az ELŐTÉR-IP-készletet.

5. A **Beállítások** területen válassza a előtér **IP-konfiguráció** elemet. A következő ablaktáblán a felső részen válassza a **Hozzáadás** lehetőséget.

6. Adja meg az előtér-IP-cím nevét (például **contosofe** vagy **fabrikamfe**).

7. <a name="step3-7"></a>Válassza ki az **IP-címet**. A **nyilvános IP-cím választása** területen válassza ki az előtér IP-címeit (**PublicIP1** vagy **PublicIP2**).

8. Hozza létre a második előtér-IP-címet a <a href="#step3-3">3</a> . lépés a jelen szakasz <a href="#step3-7">7. lépésével</a> megismételve.

Az előtér-készlet konfigurálása után az IP-címek a terheléselosztó előtérbeli **IP-konfigurációs** beállításai alatt jelennek meg. 
    
### <a name="step-4-configure-the-back-end-pool"></a>4. lépés: a háttér-készlet konfigurálása

Minden webhelyhez (contoso.com és fabrikam.com) konfigurálja a háttérbeli címkészletet a terheléselosztó számára:
        
1. A portálon válassza a **További szolgáltatások** lehetőséget. A szűrő mezőbe írja be a **Load balancert** , majd válassza a **Load Balancer** lehetőséget.

2. Válassza ki azt a terheléselosztó (**mylb**), amelyhez hozzá szeretné adni a háttér-készletet.

3. A **Beállítások** területen válassza a **háttér-készletek** elemet. Adja meg a háttér-készlet nevét (például **contosopool** vagy **fabrikampool**). A következő ablaktáblán a felső részen válassza a **Hozzáadás** lehetőséget. 

4. A **társított** elemnél válassza a **rendelkezésre állási csoport** lehetőséget.

5. A **rendelkezésre állási csoport beállításnál** válassza a **myavailset elemet** lehetőséget.

6. Adja hozzá a célként megadott hálózati IP-konfigurációkat mindkét virtuális géphez: 

    ![Háttérbeli készletek konfigurálása a terheléselosztó számára](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. A **cél virtuális gép** beállításnál válassza ki a háttér-készlethez hozzáadni kívánt virtuális gépet (például **VM1** vagy **VM2**).

    2. A **hálózati IP-konfiguráció** területen válassza ki az előző lépésben kiválasztott virtuális gép másodlagos hálózati ADAPTERÉNEK IP-konfigurációját (például **VM1NIC2-ipconfig2** vagy **VM2NIC2-ipconfig2**).

7. Válassza az **OK** lehetőséget.

Miután konfigurálta a háttér-készletet, a címek a terheléselosztó **háttér-készletének** beállításai alatt jelennek meg.

### <a name="step-5-configure-the-health-probe"></a>5. lépés: az állapot-mintavétel konfigurálása

Állapot-mintavétel konfigurálása a terheléselosztó számára:

1. A portálon válassza a **További szolgáltatások** lehetőséget. A szűrő mezőbe írja be a **Load balancert** , majd válassza a **Load Balancer** lehetőséget.

2. Válassza ki azt a terheléselosztó (**mylb**), amelyhez hozzá szeretné adni az állapot-mintavételt.

3. A **Beállítások** területen válassza az **állapot** mintavétel elemet. A következő ablaktáblán a felső részen válassza a **Hozzáadás** lehetőséget. 

4. Adja meg az állapot-mintavétel nevét (például **http**). Válassza az **OK** lehetőséget.

### <a name="step-6-configure-load-balancing-rules"></a>6. lépés: terheléselosztási szabályok konfigurálása

Minden webhelyhez (contoso.com és fabrikam.com) konfigurálja a terheléselosztási szabályokat:
    
1. <a name="step6-1"></a>A **Beállítások** területen válassza a terheléselosztási **szabályok** elemet. A következő ablaktáblán a felső részen válassza a **Hozzáadás** lehetőséget. 

2. A **Name (név**) mezőbe írja be a terheléselosztási szabály nevét (például **HTTPc** for contoso.com vagy **HTTPf** for fabrikam.com).

3. A **felületi IP-cím** mezőben válassza ki a korábban létrehozott ELŐTÉR-IP-címet (például **contosofe** vagy **fabrikamfe**).

4. A **port** -és **háttér-portok** esetében tartsa meg az alapértelmezett **80**-as értéket.

5. A **lebegőpontos IP-cím (közvetlen kiszolgáló visszatérése)** beállításnál válassza a **Letiltva** lehetőséget.

6. <a name="step6-6"></a>Kattintson **az OK gombra**.

7. Hozza létre a második terheléselosztó-szabályt az <a href="#step6-1">1. lépés</a> megismétlésével a jelen szakasz <a href="#step6-6">6. lépésével</a> .

A szabályok konfigurálása után ezek megjelennek a terheléselosztó terheléselosztási **szabályok** beállításai között.

### <a name="step-7-configure-dns-records"></a>7. lépés: DNS-rekordok konfigurálása

Az utolsó lépésként konfigurálja a DNS-erőforrásrekordokat úgy, hogy az a terheléselosztó megfelelő előtér-IP-címeire mutasson. A tartományokat Azure DNS tárolhatja. A Azure DNS és a Load Balancer használatával kapcsolatos további információkért lásd: a [Azure DNS használata más Azure-szolgáltatásokkal](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Következő lépések
- További információ az Azure terheléselosztási szolgáltatásainak az Azure-beli [terheléselosztási szolgáltatások használatával](../traffic-manager/traffic-manager-load-balancing-azure.md)történő összevonásáról.
- Megtudhatja, hogyan használhatja a különböző típusú naplókat a Load Balancer kezeléséhez és a [Azure Load Balancer Azure monitor-naplókban](../load-balancer/load-balancer-monitor-log.md)való hibakereséséhez.
