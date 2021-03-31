---
title: SAP HANA magas rendelkezésre állása Azure-beli virtuális gépeken a SLES-on | Microsoft Docs
description: SAP HANA magas rendelkezésre állása Azure-beli virtuális gépeken SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: radeltch
ms.openlocfilehash: 42a4c4a41f6c8bdf9d4a8e78f634893722c8f389
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576397"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>SAP HANA magas rendelkezésre állása Azure-beli virtuális gépeken SUSE Linux Enterprise Server

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

A helyszíni fejlesztéshez a HANA rendszerreplikációt vagy a megosztott tárterületet használhatja a SAP HANA magas rendelkezésre állásának létrehozásához.
Az Azure Virtual Machines (VM) szolgáltatásban a HANA rendszerreplikáció az Azure-ban jelenleg az egyetlen támogatott magas rendelkezésre állási funkció. SAP HANA replikáció egy elsődleges csomópontból és legalább egy másodlagos csomópontból áll. Az elsődleges csomóponton lévő adatváltozások szinkron vagy aszinkron módon replikálódnak a másodlagos csomópontra.

Ez a cikk leírja, hogyan telepítheti és konfigurálhatja a virtuális gépeket, hogyan telepítheti a fürtöt, és hogyan telepítheti és konfigurálhatja SAP HANA rendszer-replikálást.
A példában a konfigurációk, a telepítési parancsok, a példányok száma **03** és a HANA rendszerazonosító **HN1** vannak használatban.

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* SAP-Megjegyzés [1928533], amely a következőket tartalmazta:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája.
  * Az Azure-beli virtuális gépek méretével kapcsolatos fontos kapacitási információk.
  * A támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk.
  * A Windows és a Linux szükséges SAP kernel-verziója Microsoft Azureon.
* Az SAP-Megjegyzés [2015553] az Azure-ban az SAP által támogatott SAP-szoftverek üzembe helyezésének előfeltételeit sorolja fel.
* A [2205917] -es SAP-Megjegyzés ajánlott operációsrendszer-beállításokkal SUSE Linux Enterprise Server SAP-alkalmazásokhoz.
* A [1944799] -es SAP-Megjegyzés SAP HANA irányelvek az SAP-alkalmazások SUSE Linux Enterprise Server.
* Az [2178632] -es SAP-Megjegyzés részletes információkkal szolgál az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az [1984787] -es SAP-Megjegyzés általános információkat tartalmaz a SUSE Linux Enterprise Server 12.
* Az SAP Megjegyzés [1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [401162] -es SAP-megjegyzés arról tájékoztat, hogy miként kerülheti el a "címek már használatban" beállítást a HANA rendszerreplikáció beállításakor.
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik az összes szükséges SAP-megjegyzéssel a Linux rendszerhez.
* [SAP HANA Certified IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Az Azure Virtual Machines tervezése és megvalósítása az SAP Linux rendszeren][planning-guide] című útmutatójában.
* [Azure Virtual Machines üzembe helyezés a Linuxon futó SAP][deployment-guide] -ben (ez a cikk).
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren –][dbms-guide] útmutató.
* [SUSE Linux Enterprise Server for SAP Applications 12 SP3 – ajánlott eljárások útmutatók][sles-for-sap-bp]
  * SAP HANA SR teljesítményre optimalizált infrastruktúra beállítása (SLES for SAP Applications 12 SP1). Az útmutató tartalmazza a SAP HANA rendszer-replikáció beállításához szükséges összes információt a helyszíni fejlesztéshez. Ez az útmutató alapkonfigurációként használható.
  * A SAP HANA SR-re optimalizált infrastruktúra beállítása (SLES for SAP Applications 12 SP1)

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás eléréséhez SAP HANA két virtuális gépre van telepítve. A rendszer a HANA rendszerreplikáció használatával replikálja az adatgyűjtést.

![SAP HANA magas rendelkezésre állás áttekintése](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA a rendszerreplikáció beállítása dedikált virtuális állomásnevet és virtuális IP-címeket használ. Az Azure-ban a virtuális IP-címek használatához terheléselosztó szükséges. A terheléselosztó konfigurációját a következő lista tartalmazza:

* Előtér-konfiguráció: IP-10.0.0.13 a hn1-db-hez
* Háttérbeli konfiguráció: a HANA rendszer-replikáció részét képező összes virtuális gép elsődleges hálózati adapteréhez csatlakozik
* Mintavételi port: 62503-es port
* Terheléselosztási szabályok: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Linux rendszeren való üzembe helyezés

Az SAP HANA erőforrás-ügynöke az SAP-alkalmazások SUSE Linux Enterprise Server részét képezi.
Az Azure Marketplace lemezképet tartalmaz a SUSE Linux Enterprise Server for SAP Applications for 12 szolgáltatáshoz, amely az új virtuális gépek üzembe helyezésére használható.

### <a name="deploy-with-a-template"></a>Üzembe helyezés sablon használatával

Az összes szükséges erőforrás üzembe helyezéséhez a GitHubon található egyik rövid útmutató-sablon is használható. A sablon üzembe helyezi a virtuális gépeket, a terheléselosztóot, a rendelkezésre állási készletet stb.
A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [adatbázis-sablont][template-multisid-db] vagy a [konvergens sablont][template-converged] a Azure Portal. 
    Az adatbázis-sablon csak adatbázishoz hoz létre terheléselosztási szabályokat. Az átszervezett sablon egy ASCS/SCS és ERS (csak Linux) példány terheléselosztási szabályait is létrehozza. Ha SAP NetWeaver-alapú rendszer telepítését tervezi, és ugyanazon a gépen szeretné telepíteni a ASCS/SCS-példányt, használja a [konvergens sablont][template-converged].

1. Adja meg a következő paramétereket:
    - **SAP-rendszerazonosító**: adja meg a TELEPÍTENI kívánt SAP-System azonosítót. A rendszer az azonosítót használja az üzembe helyezett erőforrások előtagjaként.
    - **Verem típusa**: (ez a paraméter csak akkor alkalmazható, ha a konvergens sablont használja.) Válassza ki az SAP NetWeaver stack-típust.
    - **Operációs rendszer típusa**: válasszon egyet a Linux-disztribúciók közül. Ebben a példában válassza a **SLES 12** lehetőséget.
    - **Adatbázis típusa**: válassza a **HANA** elemet.
    - **SAP-rendszer mérete**: Itt adhatja meg, hogy az új rendszer milyen típusú SAP-t fog biztosítani. Ha nem biztos benne, hogy hány SAP-rendszer szükséges, kérdezze meg az SAP-technológiai partnerét vagy rendszerintegrátorát.
    - **Rendszerszintű rendelkezésre állás**: válassza a **Ha** lehetőséget.
    - **Rendszergazdai Felhasználónév és rendszergazdai jelszó**: új felhasználó jön létre, amely a gépre való bejelentkezéshez használható.
    - **Új vagy meglévő alhálózat**: meghatározza, hogy létre kell-e hozni egy új virtuális hálózatot és alhálózatot, vagy egy meglévő alhálózatot. Ha már van olyan virtuális hálózata, amely a helyszíni hálózathoz van csatlakoztatva, válassza a **meglévő** lehetőséget.
    - **Alhálózati azonosító**: Ha a virtuális gépet egy olyan meglévő VNet szeretné telepíteni, amelyben egy alhálózat van megadva, a virtuális gépet hozzá kell rendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában úgy néz ki, mint a **/Subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /Subnets/ \<subnet name>**.

### <a name="manual-deployment"></a>Manuális üzembe helyezés

> [!IMPORTANT]
> Győződjön meg arról, hogy a kiválasztott operációs rendszer SAP-tanúsítvánnyal rendelkezik az adott virtuálisgép-típusok SAP HANAához. A SAP HANA Certified VM-típusok és operációsrendszer-kiadások listája [SAP HANA tanúsított IaaS platformokon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)kereshető. Ügyeljen rá, hogy a felsorolt virtuálisgép-típusok részletes listájának lekérése SAP HANA támogatott operációsrendszer-kiadások teljes listáját adja meg az adott virtuálisgép-típushoz
>  

1. Hozzon létre egy erőforráscsoportot.
1. Hozzon létre egy virtuális hálózatot.
1. Hozzon létre egy rendelkezésre állási készletet.
   - Állítsa be a maximális frissítési tartományt.
1. Hozzon létre egy Load balancert (belső). A [standard Load Balancer](../../../load-balancer/load-balancer-overview.md)használatát javasoljuk.
   - Válassza ki a 2. lépésben létrehozott virtuális hálózatot.
1. Hozzon létre egy 1. virtuális gépet.
   - Használjon olyan SLES4SAP-rendszerképet az Azure Galleryben, amelyet a kiválasztott VM-típus SAP HANA támogat.
   - Válassza ki a 3. lépésben létrehozott rendelkezésre állási készletet.
1. A 2. virtuális gép létrehozása
   - Használjon olyan SLES4SAP-rendszerképet az Azure Galleryben, amelyet a kiválasztott VM-típus SAP HANA támogat.
   - Válassza ki a 3. lépésben létrehozott rendelkezésre állási készletet. 
1. Adatlemezek hozzáadása.

> [!IMPORTANT]
> A lebegő IP-címek nem támogatottak a terheléselosztási helyzetekben a hálózati adapter másodlagos IP-konfigurációjában. További részletek: az [Azure Load Balancer korlátozásai](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Ha a virtuális gép további IP-címére van szüksége, helyezzen üzembe egy második hálózati adaptert.   

> [!Note]
> Ha a nyilvános IP-címek nélküli virtuális gépek a belső (nincs nyilvános IP-cím) standard Azure Load Balancer háttér-készletbe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurálást végeznek a nyilvános végpontok útválasztásának engedélyezéséhez. A kimenő kapcsolatok elérésével kapcsolatos részletekért lásd: [nyilvános végpontú kapcsolat Virtual Machines az Azure standard Load Balancer használata az SAP magas rendelkezésre állási helyzetekben](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

1. Ha standard Load balancert használ, kövesse az alábbi konfigurációs lépéseket:
   1. Először hozzon létre egy előtér-IP-címkészletet:
   
      1. Nyissa meg a terheléselosztó felületet, válassza a előtér **IP-készlet** lehetőséget, majd kattintson a **Hozzáadás** gombra.
      1. Adja meg az új előtér-IP-készlet nevét (például **Hana-frontend**).
      1. Állítsa a **hozzárendelést** **statikus** értékre, és adja meg az IP-címet (például **10.0.0.13**).
      1. Válassza az **OK** lehetőséget.
      1. Az új előtér-IP-készlet létrehozása után jegyezze fel a készlet IP-címét.
   
   1. Következő lépésként hozzon létre egy háttér-készletet:
   
      1. Nyissa meg a Load balancert, válassza a **háttérbeli készletek** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
      1. Adja meg az új háttér-készlet nevét (például **Hana-backend**).
      1. Válassza a **Virtual Network** lehetőséget.
      1. Válassza **a virtuális gép hozzáadása** lehetőséget.
      1. Válassza a * * virtuális gép * * elemet.
      1. Válassza ki a SAP HANA-fürthöz tartozó virtuális gépeket és azok IP-címeit.
      1. Válassza a **Hozzáadás** lehetőséget.
   
   1. Következő lépésként hozzon létre egy állapot-mintavételt:
   
      1. Nyissa meg a terheléselosztó-t, válassza az **állapot**-tesztek elemet, majd kattintson a **Hozzáadás** gombra.
      1. Adja meg az új állapot-mintavétel nevét (például **Hana-HP**).
      1. Válassza a **TCP** lehetőséget a protokoll és a **625-** es port. Tartsa meg az **intervallum** értékét 5-re, a nem kifogástalan **állapot küszöbértékének** értéke pedig 2.
      1. Válassza az **OK** lehetőséget.
   
   1. Ezután hozza létre a terheléselosztási szabályokat:
   
      1. Nyissa meg a terheléselosztó-t, válassza a terheléselosztási **szabályok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
      1. Adja meg az új terheléselosztó-szabály nevét (például **Hana-LB**).
      1. Válassza ki az előtér-IP-címet, a háttér-készletet és a korábban létrehozott állapot-mintavételt (például **Hana-frontend**, **Hana-backend** és **Hana-HP**).
      1. Válassza a **hektár portok** lehetőséget.
      1. Növelje az **üresjárati időkorlátot** 30 percre.
      1. Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.
      1. Válassza az **OK** lehetőséget.

1. Ha a forgatókönyv az alapszintű Load Balancer használatát is megköveteli, kövesse az alábbi konfigurációs lépéseket:
   1. Először hozzon létre egy előtér-IP-címkészletet:
   
      1. Nyissa meg a terheléselosztó felületet, válassza a előtér **IP-készlet** lehetőséget, majd kattintson a **Hozzáadás** gombra.
      1. Adja meg az új előtér-IP-készlet nevét (például **Hana-frontend**).
      1. Állítsa a **hozzárendelést** **statikus** értékre, és adja meg az IP-címet (például **10.0.0.13**).
      1. Válassza az **OK** lehetőséget.
      1. Az új előtér-IP-készlet létrehozása után jegyezze fel a készlet IP-címét.
   
   1. Következő lépésként hozzon létre egy háttér-készletet:
   
      1. Nyissa meg a Load balancert, válassza a **háttérbeli készletek** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
      1. Adja meg az új háttér-készlet nevét (például **Hana-backend**).
      1. Válassza **a virtuális gép hozzáadása** lehetőséget.
      1. Válassza ki a 3. lépésben létrehozott rendelkezésre állási készletet.
      1. Válassza ki a SAP HANA-fürthöz tartozó virtuális gépeket.
      1. Válassza az **OK** lehetőséget.
   
   1. Következő lépésként hozzon létre egy állapot-mintavételt:
   
      1. Nyissa meg a terheléselosztó-t, válassza az **állapot**-tesztek elemet, majd kattintson a **Hozzáadás** gombra.
      1. Adja meg az új állapot-mintavétel nevét (például **Hana-HP**).
      1. Válassza a **TCP** lehetőséget a protokoll és a **625-** es port. Tartsa meg az **intervallum** értékét 5-re, a nem kifogástalan **állapot küszöbértékének** értéke pedig 2.
      1. Válassza az **OK** lehetőséget.
   
   1. SAP HANA 1,0 esetében hozza létre a terheléselosztási szabályokat:
   
      1. Nyissa meg a terheléselosztó-t, válassza a terheléselosztási **szabályok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
      1. Adja meg az új terheléselosztó-szabály nevét (például Hana-LB-3 **03** 15).
      1. Válassza ki az előtér-IP-címet, a háttér-készletet és a korábban létrehozott állapot-mintavételt (például **Hana-frontend**).
      1. Tartsa a **protokollt** **TCP**-értékre, és írja be a 3 **03** 15 portot.
      1. Növelje az **üresjárati időkorlátot** 30 percre.
      1. Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.
      1. Válassza az **OK** lehetőséget.
      1. Ismételje meg ezeket a lépéseket a 3 **03** 17-ös porton.
   
   1. SAP HANA 2,0 esetében hozza létre a rendszeradatbázis terheléselosztási szabályait:
   
      1. Nyissa meg a terheléselosztó-t, válassza a terheléselosztási **szabályok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
      1. Adja meg az új terheléselosztó-szabály nevét (például Hana-LB-3 **03** 13).
      1. Válassza ki az előtér-IP-címet, a háttér-készletet és a korábban létrehozott állapot-mintavételt (például **Hana-frontend**).
      1. Tartsa a **protokollt** **TCP**-értékre, és írja be a 3 **03** 13 portot.
      1. Növelje az **üresjárati időkorlátot** 30 percre.
      1. Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.
      1. Válassza az **OK** lehetőséget.
      1. Ismételje meg ezeket a lépéseket a 3.**03**. porton.
   
   1. SAP HANA 2,0 esetében először hozza létre a bérlői adatbázishoz tartozó terheléselosztási szabályokat:
   
      1. Nyissa meg a terheléselosztó-t, válassza a terheléselosztási **szabályok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
      1. Adja meg az új terheléselosztó-szabály nevét (például Hana-LB-3 **03** 40).
      1. Válassza ki a korábban létrehozott előtérbeli IP-címet, a háttér-készletet és az állapot-mintavételt (például **Hana-frontend**).
      1. Tartsa a **protokollt** **TCP**-re, és írja be a 3 **03** 40 portot.
      1. Növelje az **üresjárati időkorlátot** 30 percre.
      1. Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.
      1. Válassza az **OK** lehetőséget.
      1. Ismételje meg ezeket a lépéseket a 3 **03** 41 és 3 **03** 42-es porton.

   A SAP HANA szükséges portokkal kapcsolatos további információkért olvassa el a [bérlői adatbázisok kapcsolatai](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) című részt a [SAP HANA bérlői adatbázisok](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) útmutatójában vagy az 2388694-es [SAP-megjegyzésben][2388694].

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket a Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapot-mintavételek meghibásodását eredményezi. Állítsa a paramétert a **0** értékre **net.IPv4.tcp_timestamps** . Részletekért lásd: [Load Balancer Health](../../../load-balancer/load-balancer-custom-probe-overview.md)-tesztek.
> Lásd még: SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Pacemaker-fürt létrehozása

Ha alapszintű pacemaker-fürtöt szeretne létrehozni ehhez a HANA-kiszolgálóhoz, kövesse az Azure-beli [SUSE Linux Enterprise Server pacemaker beállítása](high-availability-guide-suse-pacemaker.md) című témakör lépéseit. Ugyanazt a pacemaker-fürtöt használhatja a SAP HANA és az SAP NetWeaver (A) SCS-hez.

## <a name="install-sap-hana"></a>Az SAP HANA telepítése

Az ebben a szakaszban szereplő lépések az alábbi előtagokat használják:
- **[A]**: a lépés az összes csomópontra vonatkozik.
- **[1]**: a lépés csak az 1. csomópontra vonatkozik.
- **[2]**: a lépés csak a pacemaker-fürt 2-es csomópontjára vonatkozik.

1. **[A]** a lemez elrendezésének beállítása: **logikai kötet kezelője (LVM)**.

   Azt javasoljuk, hogy az LVM-t használja az adatfájlok és naplófájlok tárolására használt kötetekhez. Az alábbi példa azt feltételezi, hogy a virtuális gépekhez négy adatlemez van csatlakoztatva, amelyek két kötet létrehozásához használatosak.

   Az összes rendelkezésre álló lemez felsorolása:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Példa a kimenetre:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Fizikai kötetek létrehozása a használni kívánt összes lemezhez:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Hozzon létre egy kötet-csoportot az adatfájlokhoz. Használjon egy kötetet a naplófájlok számára, és egyet a SAP HANA megosztott könyvtárához:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Hozza létre a logikai köteteket. A kapcsoló használata nélkül jön létre lineáris kötet `lvcreate` `-i` . Javasoljuk, hogy hozzon létre egy csíkozott kötetet a jobb I/O-teljesítmény érdekében, és igazítsa a sávok méretét a SAP HANA virtuálisgép- [tároló konfigurációjában](./hana-vm-operations-storage.md)dokumentált értékekhez. Az `-i` argumentumnak a mögöttes fizikai kötetek számának kell lennie, az `-I` argumentum pedig a sáv mérete. Ebben a dokumentumban két fizikai kötet van használatban az adatkötethez, így a `-i` switch argumentum értéke **2**. Az adatkötet csíkozási mérete **256KiB**. A rendszer egy fizikai kötetet használ a naplózási kötethez, így `-i` `-I` a naplózási kötet parancsaihoz nem használhatók explicit módon a kapcsolók.  

   > [!IMPORTANT]
   > Használja a `-i` kapcsolót, és állítsa be a mögöttes fizikai kötet számára, ha több fizikai kötetet használ minden adathoz, naplóhoz vagy megosztott kötethez. `-I`Csíkozott kötet létrehozásakor használja a kapcsolót a sáv méretének megadásához.  
   > Lásd: SAP HANA virtuálisgép- [tárolási konfigurációk](./hana-vm-operations-storage.md) az ajánlott tárolási konfigurációkhoz, beleértve a sávok méretét és a lemezek számát.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>
  
   Hozza létre a csatlakoztatási könyvtárakat, és másolja a logikai kötetek UUID azonosítóját:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Hozzon létre `fstab` bejegyzéseket a három logikai kötethez:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Szúrja be a következő sort a `/etc/fstab` fájlba:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Az új kötetek csatlakoztatása:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** a lemez elrendezésének beállítása: **egyszerű lemezek**.

   A bemutató rendszerek esetében a HANA-adatait és a naplófájlokat egy lemezen helyezheti el. Hozzon létre egy partíciót a/dev/disk/Azure/scsi1/lun0, és formázza azt a XFS:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Szúrja be ezt a sort az/etc/fstab fájlba:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Hozza létre a cél könyvtárat, és csatlakoztassa a lemezt:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** állomásnév-feloldás beállítása az összes gazdagépen.

   Használhat DNS-kiszolgálót, vagy módosíthatja az/etc/hosts fájlt az összes csomóponton. Ez a példa a/etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat a/etc/hosts fájlba. Módosítsa az IP-címet és a gazdagépet úgy, hogy az megfeleljen a környezetének:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** telepítse a SAP HANA magas rendelkezésre állású csomagokat:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

SAP HANA rendszerreplikáció telepítéséhez kövesse az [SAP HANA SR teljesítményre optimalizált forgatókönyv útmutató](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/)4. fejezetét.

1. **[A]** futtassa a **HDBLCM** programot a HANA DVD-ről. Adja meg a következő értékeket a parancssorban:
   * A telepítés kiválasztása: adja meg az **1** értéket.
   * További összetevők kiválasztása a telepítéshez: **1**. Adjon meg egy értéket.
   * Adja meg a telepítési útvonalat [/Hana/Shared]: válassza az ENTER gombot.
   * Adja meg a helyi gazdagép nevét [..]: válassza az ENTER gombot.
   * További gazdagépeket szeretne hozzáadni a rendszeren? (i/n) [n]: válassza az ENTER gombot.
   * Adja meg SAP HANA rendszer AZONOSÍTÓját: adja meg a HANA biztonsági azonosítóját, például: **HN1**.
   * Adja meg a példány számát [00]: adja meg a HANA-példány számát. Adja meg a **03** értéket, ha az Azure-sablont használta, vagy követte a jelen cikk manuális üzembe helyezés szakaszát.
   * Válassza az adatbázis mód/index megadása [1] lehetőséget: válassza az ENTER gombot.
   * Válasszon rendszerhasználatot/adja meg a (z) [4] indexet: válassza ki a rendszerhasználati értéket.
   * Adja meg az adatkötetek helyét [/hana/data/HN1]: válassza az ENTER gombot.
   * Adja meg a naplózási kötetek helyét [/hana/log/HN1]: válassza az ENTER gombot.
   * Korlátozza a memória maximális kiosztását? [n]: válassza az ENTER gombot.
   * Adja meg a (z) "..." gazdagép nevét. [...]: Válassza az ENTER gombot.
   * Adja meg az SAP Host Agent User (sapadm) jelszót: adja meg a gazdagép-ügynök felhasználói jelszavát.
   * Az SAP Host Agent User (sapadm) jelszavának megerősítése: a megerősítéshez írja be a gazdagép-ügynök felhasználói jelszavát.
   * Adja meg a rendszergazda (hdbadm) jelszavát: adja meg a rendszergazdai jelszót.
   * Rendszergazda (hdbadm) jelszavának megerősítése: a megerősítéshez írja be újra a rendszergazda jelszavát.
   * Adja meg a rendszergazda kezdőkönyvtár [/usr/sap/HN1/home]: válassza az ENTER gombot.
   * Adja meg a rendszergazda bejelentkezési rendszerhéját [/bin/sh]: válassza az ENTER gombot.
   * Adja meg a rendszergazda felhasználói AZONOSÍTÓját [1001]: válassza az ENTER gombot.
   * Adja meg a felhasználói csoport AZONOSÍTÓját (sapsys) [79]: válassza az ENTER billentyűt.
   * Adja meg az adatbázis-felhasználó (rendszer) jelszavát: adja meg az adatbázis felhasználói jelszavát.
   * Adatbázis-felhasználó (rendszer) jelszavának megerősítése: írja be újra az adatbázis felhasználói jelszavát a megerősítéshez.
   * Újraindítja a rendszert a gép újraindítása után? [n]: válassza az ENTER gombot.
   * Folytatja? (i/n): az összefoglalás ellenőrzése. A folytatáshoz adja meg az **y** értéket.

1. **[A]** frissítse az SAP-gazdagép ügynököt.

   Töltse le a legújabb SAP Host Agent-archívumot az [SAP Software Center][sap-swcenter] webhelyről, és futtassa a következő parancsot az ügynök frissítéséhez. Cserélje le az Archívum elérési útját úgy, hogy az a letöltött fájlra mutasson:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2,0 rendszerreplikáció konfigurálása

Az ebben a szakaszban szereplő lépések az alábbi előtagokat használják:

* **[A]**: a lépés az összes csomópontra vonatkozik.
* **[1]**: a lépés csak az 1. csomópontra vonatkozik.
* **[2]**: a lépés csak a pacemaker-fürt 2-es csomópontjára vonatkozik.

1. **[1]** hozza létre a bérlői adatbázist.

   Ha SAP HANA 2,0-as vagy MDC-t használ, hozzon létre egy bérlői adatbázist az SAP NetWeaver rendszer számára. Cserélje le az **NW1** -t az SAP-rendszere SID-azonosítójával.

   Futtassa a következő parancsot <hanasid adm-ként \> :

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** a rendszerreplikáció konfigurálása az első csomóponton:

   Az adatbázisok biztonsági mentése <hanasid adm-ként \> :

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Másolja a System PKI-fájlokat a másodlagos helyre:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Hozza létre az elsődleges helyet:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** a rendszer replikációjának konfigurálása a második csomóponton:
    
   Regisztrálja a második csomópontot a rendszerreplikáció elindításához. Futtassa a következő parancsot <hanasid adm-ként \> :

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1,0 rendszerreplikáció konfigurálása

Az ebben a szakaszban szereplő lépések az alábbi előtagokat használják:

* **[A]**: a lépés az összes csomópontra vonatkozik.
* **[1]**: a lépés csak az 1. csomópontra vonatkozik.
* **[2]**: a lépés csak a pacemaker-fürt 2-es csomópontjára vonatkozik.

1. **[1]** hozza létre a szükséges felhasználókat.

   Futtassa a következő parancsot root-ként. Ügyeljen arra, hogy a félkövér sztringek (HANA rendszerazonosító **HN1** és a példány száma **03**) helyett a SAP HANA telepítésének értékeit adja meg:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** hozza létre a tároló bejegyzését.

   A következő parancs futtatásával hozzon létre egy új tároló-bejegyzést:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** az adatbázis biztonsági mentése.

   Az adatbázisok biztonsági mentése root-ként:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Ha több-bérlős telepítést használ, a bérlői adatbázis biztonsági mentését is elvégezheti:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** konfigurálja a rendszer replikálását az első csomóponton.

   Hozza létre az elsődleges helyet <hanasid \> adm-ként:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** a rendszer replikálásának konfigurálása a másodlagos csomóponton.

   Regisztrálja a másodlagos helyet <hanasid \> adm-ként:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA-fürt erőforrásainak létrehozása

Először hozza létre a HANA-topológiát. Futtassa az alábbi parancsokat a pacemaker-fürtcsomópontok egyik csomópontján:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Ezután hozza létre a HANA-erőforrásokat:

> [!IMPORTANT]
> A közelmúltbeli tesztelés feltárta a helyzeteket, ahol a netcat nem válaszol a várakozó kérelmekre, és csak egyetlen kapcsolat kezelésére vonatkozó korlátozásokat okoz. A netcat erőforrás nem figyeli az Azure Load Balancer kéréseit, és a lebegőpontos IP-cím elérhetetlenné válik.  
> A meglévő pacemaker-fürtök esetében a netcat és a socat helyett a múltban javasolt. Jelenleg a csomag erőforrás-ügynökök részét képező Azure-LB erőforrás-ügynök használatát javasoljuk a következő csomag-verzióra vonatkozó követelményekkel:
> - A SLES 12 SP4/SP5 esetében a verziónak legalább Resource-Agent-4.3.018. a7fb5035-3.30.1 kell lennie.  
> - A SLES 15/15 SP1 esetében a verziónak legalább Resource-Agent-4.3.0184.6 ee15eb2-4.13.1 kell lennie.  
>
> Vegye figyelembe, hogy a módosítás rövid állásidőt igényel.  
> Meglévő pacemaker-fürtök esetén, ha a konfigurációt már úgy módosították, hogy a socat használja az [azure Load-Balancer észlelésének megerősítése](https://www.suse.com/support/kb/doc/?id=7024128)című cikkben leírtak szerint, nem kell azonnal váltania az Azure-LB erőforrás-ügynökre.


> [!NOTE]
> Ez a cikk a " *Master* " és a " *Slave*" kifejezésekre mutató hivatkozásokat tartalmaz, amelyeket a Microsoft már nem használ. Ha eltávolítja ezeket a feltételeket a szoftverből, a rendszer eltávolítja őket ebből a cikkből.

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b> \
  meta resource-stickiness=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>A HANA aktív/olvasási engedélyezett rendszerreplikációjának konfigurálása a pacemaker-fürtben

A SAP HANA 2,0 SPS 01 SAP lehetővé teszi az aktív/olvasható beállítás használatát SAP HANA rendszer-replikáláshoz, ahol az SAP HANA rendszer-replikálás másodlagos rendszerei aktívan használhatók az olvasási és intenzív munkaterhelésekhez. Ha egy fürtön szeretné támogatni a telepítést, egy második virtuális IP-cím szükséges, amely lehetővé teszi, hogy az ügyfelek hozzáférhessenek a másodlagos, írásvédett SAP HANA adatbázishoz. Annak biztosítása érdekében, hogy a másodlagos replikálási hely továbbra is elérhető legyen egy átvétel után, a fürtnek át kell helyeznie a virtuális IP-címet a SAPHana-erőforrás másodlagos helyére.

Ez a szakasz azokat a további lépéseket ismerteti, amelyek szükségesek a HANA Active/Read enabled rendszer-replikáció kezeléséhez egy SUSE magas rendelkezésre állású fürtben a második virtuális IP-címmel.    
A folytatás előtt győződjön meg arról, hogy teljes körűen konfigurált SUSE magas rendelkezésre állású fürtöt felügyel SAP HANA-adatbázist a dokumentáció fenti részeiben leírtak szerint.  

![Magas rendelkezésre állás SAP HANA a másodlagos olvasási engedélyezve](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>További beállítás az Azure Load balancerben az aktív/írásvédett telepítéshez

A második virtuális IP-cím [kiépítésével](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#manual-deployment) kapcsolatos további lépések végrehajtásához ellenőrizze, hogy konfigurálta-e a Azure Load Balancer a manuális telepítés című szakaszban leírtak szerint.

1. A **standard** Load Balancer esetében kövesse az alábbi, a korábbi szakaszban létrehozott terheléselosztó további lépéseit.

   a. Második előtér-IP-címkészlet létrehozása: 

   - Nyissa meg a terheléselosztó felületet, válassza a előtér **IP-készlet** lehetőséget, majd kattintson a **Hozzáadás** gombra.
   - Adja meg a második előtér-IP-készlet nevét (például **Hana-secondaryIP**).
   - Állítsa a **hozzárendelést** **statikus** értékre, és adja meg az IP-címet (például **10.0.0.14**).
   - Válassza az **OK** lehetőséget.
   - Az új előtér-IP-készlet létrehozása után jegyezze fel az előtérbeli IP-címet.

   b. Következő lépésként hozzon létre egy állapot-mintavételt:

   - Nyissa meg a terheléselosztó-t, válassza az **állapot**-tesztek elemet, majd kattintson a **Hozzáadás** gombra.
   - Adja meg az új állapot-mintavétel nevét (például **Hana-secondaryhp**).
   - Válassza a **TCP** lehetőséget a protokoll és a **62603**-es port közül. Tartsa meg az **intervallum** értékét 5-re, a nem kifogástalan **állapot küszöbértékének** értéke pedig 2.
   - Válassza az **OK** lehetőséget.

   c. Ezután hozza létre a terheléselosztási szabályokat:

   - Nyissa meg a terheléselosztó-t, válassza a terheléselosztási **szabályok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
   - Adja meg az új terheléselosztó-szabály nevét (például **Hana-secondarylb**).
   - Válassza ki az előtér-IP-címet, a háttér-készletet és a korábban létrehozott állapot-mintavételt (például **Hana-secondaryIP**, **Hana-backend** és **Hana-secondaryhp**).
   - Válassza a **hektár portok** lehetőséget.
   - Növelje az **üresjárati időkorlátot** 30 percre.
   - Ügyeljen arra, hogy a **lebegő IP-címet engedélyezze**.
   - Válassza az **OK** lehetőséget.

### <a name="configure-hana-activeread-enabled-system-replication"></a>A HANA aktív/olvasási engedélyezett rendszerreplikációjának konfigurálása

A HANA rendszerreplikáció konfigurálásának lépéseit lásd: [SAP HANA 2,0 Rendszerreplikáció konfigurálása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#configure-sap-hana-20-system-replication) szakasz. Ha olvasási jogosultságú másodlagos forgatókönyvet telepít, miközben a második csomóponton konfigurálja a rendszerreplikációt, a következő parancsot futtassa a **hanasid** adm-ként:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Másodlagos virtuális IP-cím erőforrás hozzáadása aktív/írásvédett beállításhoz

A második virtuális IP-címet és a megfelelő elhelyezési korlátozást a következő parancsokkal lehet konfigurálni:

```
crm configure property maintenance-mode=true

crm configure primitive rsc_secip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
 meta target-role="Started" \
 operations \$id="rsc_secip_HN1_HDB03-operations" \
 op monitor interval="10s" timeout="20s" \
 params ip="10.0.0.14"

crm configure primitive rsc_secnc_HN1_HDB03 azure-lb port=62603 \
 meta resource-stickiness=0

crm configure group g_secip_HN1_HDB03 rsc_secip_HN1_HDB03 rsc_secnc_HN1_HDB03

crm configure colocation col_saphana_secip_HN1_HDB03 4000: g_secip_HN1_HDB03:Started \
 msl_SAPHana_HN1_HDB03:Slave 

crm configure property maintenance-mode=false
```
Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. A második virtuális IP-cím a másodlagos helyen, a SAPHana másodlagos erőforrással együtt fog futni.

```
sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
# Resource Group: g_secip_HN1_HDB03:
#     rsc_secip_HN1_HDB03       (ocf::heartbeat:IPaddr2):        Started hn1-db-1
#     rsc_secnc_HN1_HDB03       (ocf::heartbeat:azure-lb):       Started hn1-db-1

```

A következő szakaszban megtekintheti a végrehajtandó feladatátvételi tesztek jellemző készletét.

Vegye figyelembe a második virtuális IP-viselkedést, miközben az írásvédett másodlagos:

1. **SAPHana_HN1_HDB03** fürterőforrás **HN1-db-1-** re történő áttelepítésekor a második virtuális IP-cím átkerül a másik kiszolgálóra, **HN1-db-0-ra**. Ha konfigurálta AUTOMATED_REGISTER = "false" értéket, és a HANA rendszerreplikáció nincs automatikusan regisztrálva, akkor a második virtuális IP-cím a **hn1-db-0** kiszolgálón fut, mivel a kiszolgáló elérhető, és a fürtszolgáltatás online állapotban van.  

2. A kiszolgálók összeomlásának tesztelésekor a második virtuális IP-erőforrás (**rsc_secip_HN1_HDB03**) és az Azure Load Balancer port erőforrás (**rsc_secnc_HN1_HDB03**) az elsődleges kiszolgálón fog futni az elsődleges virtuális IP-erőforrások mellett. Amíg a másodlagos kiszolgáló nem működik, az olvasási jogosultsággal rendelkező HANA-adatbázishoz csatlakozó alkalmazások csatlakozni fognak az elsődleges HANA-adatbázishoz. A működés várható, mivel nem szeretné, hogy az olvasási jogosultsággal rendelkező HANA-adatbázishoz csatlakozó alkalmazások ne legyenek elérhetők, amíg a másodlagos kiszolgáló nem érhető el.
  
3. Ha a másodlagos kiszolgáló elérhető, és a fürtszolgáltatások online állapotban vannak, a második virtuális IP-cím és a port erőforrásai automatikusan átkerülnek a másodlagos kiszolgálóra, noha a HANA rendszerreplikáció nem lehet másodlagosként regisztrálva. Meg kell győződnie arról, hogy a másodlagos HANA-adatbázist olvasási engedélyezveként regisztrálja, mielőtt elindítja a fürtszolgáltatást a kiszolgálón. A HANA-példány fürtjének erőforrását beállíthatja úgy, hogy a (z) AUTOMATED_REGISTER = True paraméterrel automatikusan regisztrálja a másodlagos értéket.       

4. A feladatátvétel és a tartalék megoldás során az alkalmazások meglévő kapcsolatai megszakadnak a második virtuális IP-cím használatával a HANA-adatbázishoz való csatlakozáshoz.  

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

Ez a szakasz azt ismerteti, hogyan lehet tesztelni a telepítőt. Minden teszt feltételezi, hogy Ön a legfelső szintű, és a SAP HANA főkiszolgáló a **hn1-db-0** virtuális gépen fut.

### <a name="test-the-migration"></a>Az áttelepítés tesztelése

A teszt elkezdése előtt győződjön meg arról, hogy a pacemaker nem rendelkezik sikertelen művelettel (crm_mon-r-n keresztül), nincsenek váratlan helyekre vonatkozó korlátozások (például egy áttelepítési teszt maradékai), és hogy a HANA szinkronizált állapotú, például SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

A SAP HANA fő csomópontját a következő parancs végrehajtásával telepítheti át:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Ha be van állítva `AUTOMATED_REGISTER="false"` , a parancsok ezen sorozatának át kell telepítenie a SAP HANA fő csomópontot és a hn1-db-1 virtuális IP-címet tartalmazó csoportot.

Az áttelepítés elvégzése után a crm_mon-r kimenet így néz ki

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

A hn1-db-0 SAP HANA erőforrása nem indul el másodlagosként. Ebben az esetben konfigurálja a HANA-példányt másodlagosként a következő parancs végrehajtásával:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Az áttelepítés olyan helyekre vonatkozó korlátozásokat hoz létre, amelyeket újra törölni kell:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

A másodlagos csomópont erőforrásának állapotát is meg kell tisztítani:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

A HANA-erőforrás állapotának figyelése crm_mon-r használatával. Miután a HANA elindult a hn1-db-0-on, a kimenetnek a következőhöz hasonlóan kell kinéznie:

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Az Azure vívó-ügynök tesztelése (nem SBD)

Az Azure vívó-ügynök beállításának teszteléséhez tiltsa le a hálózati adaptert a hn1-db-0 csomóponton:

<pre><code>sudo ifdown eth0
</code></pre>

A fürt konfigurációjától függően a virtuális gépnek újra kell indítania vagy leállítania a szolgáltatást.
Ha a `stonith-action` beállítást kikapcsolva értékre állítja, a virtuális gép leáll, és a rendszer áttelepíti az erőforrásokat a futó virtuális gépre.

A virtuális gép újraindítása után a SAP HANA erőforrás nem indul el másodlagosként, ha be van állítva `AUTOMATED_REGISTER="false"` . Ebben az esetben konfigurálja a HANA-példányt másodlagosként a következő parancs végrehajtásával:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>SBD-kerítés tesztelése

A SBD beállítását az Inkvizítor folyamat leölésével ellenőrizheti.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

A hn1-db-0 csomópontot újra kell indítani. Előfordulhat, hogy a pacemaker szolgáltatás nem indul el később. Győződjön meg arról, hogy újra megkezdi a műveletet.

### <a name="test-a-manual-failover"></a>Manuális feladatátvétel tesztelése

A manuális feladatátvételt a `pacemaker` hn1-db-0 csomóponton található szolgáltatás leállításával tesztelheti:

<pre><code>service pacemaker stop
</code></pre>

A feladatátvételt követően újra elindíthatja a szolgáltatást. Ha be van állítva `AUTOMATED_REGISTER="false"` , a hn1-db-0 csomópont SAP HANA erőforrása nem indul el másodlagosként. Ebben az esetben konfigurálja a HANA-példányt másodlagosként a következő parancs végrehajtásával:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>SUSE-tesztek

> [!IMPORTANT]
> Győződjön meg arról, hogy a kiválasztott operációs rendszer SAP-tanúsítvánnyal rendelkezik az adott virtuálisgép-típusok SAP HANAához. A SAP HANA Certified VM-típusok és operációsrendszer-kiadások listája [SAP HANA tanúsított IaaS platformokon](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)kereshető. Ügyeljen rá, hogy a felsorolt virtuálisgép-típusok részletes listájának lekérése SAP HANA támogatott operációsrendszer-kiadások teljes listáját adja meg az adott virtuálisgép-típushoz

A használati esettől függően futtasson minden olyan tesztelési esetet, amely szerepel a SAP HANA SR teljesítményre optimalizált forgatókönyvben, vagy SAP HANA SR-re optimalizált forgatókönyvek útmutatója. Az útmutatókat az [SLES for SAP ajánlott eljárásokat tartalmazó oldalán][sles-for-sap-bp]találja.

Az alábbi tesztek a SAP HANA SR teljesítményre optimalizált forgatókönyvének tesztelési leírását ismertetik SUSE Linux Enterprise Server SAP-alkalmazások 12 SP1 útmutatójában. Naprakész verzió esetén mindig olvassa el az útmutatót is. Mindig ellenőrizze, hogy a HANA szinkronban van-e a teszt megkezdése előtt, és ellenőrizze, hogy helyes-e a pacemaker konfigurációja.

A következő tesztekben feltételezzük, hogy PREFER_SITE_TAKEOVER = "true" és AUTOMATED_REGISTER = "false".
Megjegyzés: az alábbi tesztek úgy lettek kialakítva, hogy sorban fussanak, és az előző tesztek kilépési állapotától függenek.

1. 1. TESZT: AZ ELSŐDLEGES ADATBÁZIS LEÁLLÍTÁSA AZ 1. CSOMÓPONTON

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa az alábbi parancsokat <hanasid adm-ként \> a hn1-db-0 csomóponton:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   A pacemakernek azonosítania kell a leállított HANA-példányt és a feladatátvételt a másik csomópontra. A feladatátvétel befejezése után a HANA-példány a hn1-db-0 csomóponton leáll, mert a pacemaker nem regisztrálja automatikusan a csomópontot HANA másodlagosként.

   Futtassa a következő parancsokat a csomópont hn1-db-0 másodlagosként való regisztrálásához és a sikertelen erőforrás törléséhez.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 2. TESZT: AZ ELSŐDLEGES ADATBÁZIS LEÁLLÍTÁSA A 2. CSOMÓPONTON

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Futtassa az alábbi parancsokat <hanasid \> adm-ként a következő csomóponton: hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   A pacemakernek azonosítania kell a leállított HANA-példányt és a feladatátvételt a másik csomópontra. A feladatátvételt követően a HANA-példány a (z) hn1-db-1 csomóponton leáll, mert a pacemaker nem regisztrálja automatikusan a csomópontot HANA másodlagosként.

   Futtassa a következő parancsokat a Node hn1-db-1 másodlagosként való regisztrálásához, és a sikertelen erőforrás törléséhez.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 3. TESZT: AZ ELSŐDLEGES ADATBÁZIS ÖSSZEOMLÁSA A CSOMÓPONTON

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa az alábbi parancsokat <hanasid adm-ként \> a hn1-db-0 csomóponton:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   A pacemakernek fel kell ismernie a meggyilkolt HANA-példányt és a feladatátvételt a másik csomópontra. A feladatátvétel befejezése után a HANA-példány a hn1-db-0 csomóponton leáll, mert a pacemaker nem regisztrálja automatikusan a csomópontot HANA másodlagosként.

   Futtassa a következő parancsokat a csomópont hn1-db-0 másodlagosként való regisztrálásához és a sikertelen erőforrás törléséhez.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 4. TESZT: AZ ELSŐDLEGES ADATBÁZIS ÖSSZEOMLÁSA A 2. CSOMÓPONTON

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Futtassa az alábbi parancsokat <hanasid \> adm-ként a következő csomóponton: hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   A pacemakernek fel kell ismernie a meggyilkolt HANA-példányt és a feladatátvételt a másik csomópontra. A feladatátvételt követően a HANA-példány a (z) hn1-db-1 csomóponton leáll, mert a pacemaker nem regisztrálja automatikusan a csomópontot HANA másodlagosként.

   Futtassa a következő parancsokat a Node hn1-db-1 másodlagosként való regisztrálásához, és a sikertelen erőforrás törléséhez.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 5. TESZT: ÖSSZEOMLÁS ELSŐDLEGES HELY CSOMÓPONTJA (1. CSOMÓPONT)

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat root-ként a hn1-db-0 csomóponton:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   A pacemakernek meg kell állapítania a meggyilkolt fürtcsomópont csomópontját A csomópont bekerítése után a pacemaker elindítja a HANA-példány átvételét. A bekerített csomópont újraindítása után a pacemaker nem indul el automatikusan.

   Futtassa az alábbi parancsokat a pacemaker elindításához, törölje a hn1-db-0 csomóponthoz tartozó SBD-üzeneteket, regisztrálja a Node hn1-db-0 értéket másodlagosként, és szüntesse meg a sikertelen erőforrás törlését.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. 6. TESZT: ÖSSZEOMLÁS MÁSODLAGOS HELY CSOMÓPONTJA (2. CSOMÓPONT)

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Futtassa a következő parancsokat root-ként a hn1-db-1 csomóponton:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   A pacemakernek meg kell állapítania a meggyilkolt fürtcsomópont csomópontját A csomópont bekerítése után a pacemaker elindítja a HANA-példány átvételét. A bekerített csomópont újraindítása után a pacemaker nem indul el automatikusan.

   Futtassa az alábbi parancsokat a pacemaker elindításához, törölje a hn1-db-1 csomóponthoz tartozó SBD-üzeneteket, regisztrálja a Node hn1-db-1-et másodlagosként, és törölje a sikertelen erőforrást.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 7. TESZT: A MÁSODLAGOS ADATBÁZIS LEÁLLÍTÁSA A 2. CSOMÓPONTON

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa az alábbi parancsokat <hanasid \> adm-ként a következő csomóponton: hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   A pacemaker felismeri a leállított HANA-példányt, és az erőforrást sikertelenként jelöli meg a (z) hn1-db-1 csomóponton. A pacemakernek automatikusan újra kell indítania a HANA-példányt. Futtassa a következő parancsot a sikertelen állapot tisztításához.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 8. TESZT: A MÁSODLAGOS ADATBÁZIS ÖSSZEOMLÁSA A 2. CSOMÓPONTON

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa az alábbi parancsokat <hanasid \> adm-ként a következő csomóponton: hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   A pacemaker felismeri a megölt HANA-példányt, és az erőforrást sikertelenként jelöli meg a (z) hn1-db-1 csomóponton. Futtassa a következő parancsot a sikertelen állapot tisztításához. A pacemakernek ezután automatikusan újra kell indítania a HANA-példányt.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. 9. TESZT: ÖSSZEOMLÁS MÁSODLAGOS HELY CSOMÓPONTJA (2. CSOMÓPONT) MÁSODLAGOS HANA-ADATBÁZIS FUTTATÁSA

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Futtassa a következő parancsokat root-ként a hn1-db-1 csomóponton:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   A pacemakernek meg kell állapítania a meggyilkolt fürtcsomópont csomópontját A bekerített csomópont újraindítása után a pacemaker nem indul el automatikusan.

   Futtassa az alábbi parancsokat a pacemaker elindításához, törölje a hn1-db-1 csomópont SBD-üzeneteit, és szüntesse meg a sikertelen erőforrás törlését.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Következő lépések

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
