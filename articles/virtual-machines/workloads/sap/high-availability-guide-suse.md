---
title: Azure-beli virtuális gépek magas rendelkezésre állása SAP NetWeaver-hez a SLES-on | Microsoft Docs
description: Magas rendelkezésre állási útmutató SAP NetWeaver-hez SUSE Linux Enterprise Server SAP-alkalmazásokhoz
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/22/2020
ms.author: radeltch
ms.openlocfilehash: e33b514f61aec69c566eae455d2e59b1a66813f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673812"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver számára SUSE Linux Enterprise Server SAP-alkalmazásokhoz

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
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Ez a cikk leírja, hogyan telepítheti a virtuális gépeket, konfigurálhatja a virtuális gépeket, telepítheti a fürtöt, és telepítheti a kiválóan elérhető SAP NetWeaver 7,50 rendszerét.
A példában szereplő konfigurációk, telepítési parancsok stb. A ASCS példányok száma 00, az ERS-példányok száma 02, a rendszer pedig az SAP rendszerazonosító NW1 használja. A példában szereplő erőforrások (például virtuális gépek, virtuális hálózatok) nevei feltételezik, hogy az átszervezett [sablont][template-converged] az SAP System ID NW1 használta az erőforrások létrehozásához.

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat

* SAP-Megjegyzés [1928533][1928533], amely a következőket tartalmazta:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája
  * Fontos kapacitási információk Azure-beli virtuális gépek méreteihez
  * Támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk
  * A Windows és a Linux rendszerhez szükséges SAP kernel verziója Microsoft Azure

* Az SAP Note [2015553][2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Megjegyzés [2205917][2205917] ajánlott operációsrendszer-beállításokkal SUSE Linux Enterprise Server SAP-alkalmazásokhoz
* A [1944799][1944799] -es SAP-Megjegyzés SAP HANA irányelvek az SAP-alkalmazásokhoz SUSE Linux Enterprise Server
* Az [2178632][2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [2191498][2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692][2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az [1984787][1984787] -es SAP-Megjegyzés általános információkat tartalmaz a SUSE Linux Enterprise Server 12.
* Az SAP Megjegyzés [1999351][1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [SUSE SAP ha ajánlott eljárási útmutatók][suse-ha-guide] Az útmutatók az összes szükséges információt tartalmazzák, amelyekkel beállítható a NetWeaver HA és SAP HANA rendszer-replikáció a helyszínen. Ezeket az útmutatókat általános alaptervként használhatja. Sokkal részletesebb információkat biztosítanak.
* [SUSE magas rendelkezésre állású bővítmény – 12 SP3 kibocsátási megjegyzések][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás eléréséhez az SAP NetWeaver használatához NFS-kiszolgáló szükséges. Az NFS-kiszolgáló külön fürtben van konfigurálva, és több SAP-rendszer is használható.

![SAP NetWeaver – magas rendelkezésre állás – áttekintés](./media/high-availability-guide-suse/ha-suse.png)

Az NFS-kiszolgáló, az SAP NetWeaver ASCS, az SAP NetWeaver SCS, az SAP NetWeaver ERS és a SAP HANA adatbázis virtuális gazdagépeket és virtuális IP-címeket használ. Az Azure-ban a virtuális IP-címek használatához terheléselosztó szükséges. A [standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)használatát javasoljuk. Az alábbi lista az (A) SCS és az ERS Load Balancer konfigurációját mutatja be.

### <a name="ascs"></a>Egy SCS

* Előtér-konfiguráció
  * IP-10.0.0.7
* Mintavételi port
  * Port 620<strong> &lt; nr &gt; </strong>
* Terheléselosztási szabályok
  * Ha standard Load Balancer használ, válassza a **hektár portok** elemet.
  * Ha alapszintű Load Balancer használ, hozzon létre terheléselosztási szabályokat a következő portokhoz
    * 32<strong> &lt; nr &gt; </strong> TCP
    * 36<strong> &lt; nr &gt; </strong> TCP
    * 39<strong> &lt; nr &gt; </strong> TCP
    * 81<strong> &lt; nr &gt; </strong> TCP
    * 5<strong> &lt; nr &gt; </strong>13 TCP
    * 5<strong> &lt; nr &gt; </strong>14 TCP
    * 5<strong> &lt; nr &gt; </strong>16 TCP

### <a name="ers"></a>ERS

* Előtér-konfiguráció
  * IP-10.0.0.8
* Mintavételi port
  * Port 621<strong> &lt; nr &gt; </strong>
* Terheléselosztási szabályok
  * Ha standard Load Balancer használ, válassza a **hektár portok** elemet.
  * Ha alapszintű Load Balancer használ, hozzon létre terheléselosztási szabályokat a következő portokhoz
    * 32<strong> &lt; nr &gt; </strong> TCP
    * 33<strong> &lt; nr &gt; </strong> TCP
    * 5<strong> &lt; nr &gt; </strong>13 TCP
    * 5<strong> &lt; nr &gt; </strong>14 TCP
    * 5<strong> &lt; nr &gt; </strong>16 TCP

* Háttér-konfiguráció
  * Az (A) SCS/ERS-fürt részét képező összes virtuális gép elsődleges hálózati adapteréhez csatlakozik


## <a name="setting-up-a-highly-available-nfs-server"></a>Egy magasan elérhető NFS-kiszolgáló beállítása

Az SAP NetWeaver megosztott tárterületet igényel az átvitelhez és a profilhoz. Olvassa el az [NFS magas rendelkezésre állását az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server][nfs-ha] on az NFS-kiszolgáló beállítása az SAP NetWeaver-hez című témakört.

## <a name="setting-up-ascs"></a>Az (A) SCS beállítása

A GitHubról származó Azure-sablonok használatával üzembe helyezheti az összes szükséges Azure-erőforrást, beleértve a virtuális gépeket, a rendelkezésre állási készletet és a terheléselosztó-t, vagy manuálisan is üzembe helyezheti az erőforrásokat.

### <a name="deploy-linux-via-azure-template"></a>Linux telepítése Azure-sablon használatával

Az Azure Marketplace lemezképet tartalmaz a SUSE Linux Enterprise Server for SAP Applications for 12 szolgáltatáshoz, amely az új virtuális gépek üzembe helyezésére használható. A Piactéri rendszerkép tartalmazza az SAP NetWeaver erőforrás-ügynökét.

Az összes szükséges erőforrás üzembe helyezéséhez használhatja a GitHubon a rövid útmutató sablonjait. A sablon üzembe helyezi a virtuális gépeket, a terheléselosztó-t, a rendelkezésre állási készletet stb. A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [ASCS/SCS multi SID sablont][template-multisid-xscs] vagy az átszervezett [sablont][template-converged] a Azure Portal. 
   A ASCS/SCS sablon csak az SAP NetWeaver ASCS/SCS és ERS (csak Linux) példányok terheléselosztási szabályait hozza létre, míg az átszervezett sablon egy adatbázis terheléselosztási szabályait is létrehozza (például Microsoft SQL Server vagy SAP HANA). Ha SAP NetWeaver-alapú rendszer telepítését tervezi, és az adatbázist ugyanarra a gépre szeretné telepíteni, használja a [konvergens sablont][template-converged].
1. Adja meg a következő paramétereket
   1. Erőforrás-előtag (csak ASCS/SCS multi SID sablon)  
      Adja meg a használni kívánt előtagot. Az értéket a rendszer az üzembe helyezett erőforrások előtagjaként használja.
   3. SAP rendszerazonosító (csak konvergált sablon)  
      Adja meg a telepíteni kívánt SAP-System AZONOSÍTÓját. A rendszer az azonosítót használja az üzembe helyezett erőforrások előtagjaként.
   4. Verem típusa  
      Válassza ki az SAP NetWeaver stack típusát
   5. Operációs rendszer típusa  
      Válassza ki a Linux-disztribúciók egyikét. Ebben a példában válassza a SLES 12 BYOS elemet.
   6. Adatbázis típusa  
      HANA kiválasztása
   7. Az SAP-rendszerméret.  
      Az új rendszerek által biztosított SAP mennyisége. Ha nem biztos benne, hogy a rendszer hány SAP-t igényel, kérdezze meg az SAP-technológiai partnerét vagy a rendszerintegrátort
   8. A rendszerek rendelkezésre állása  
      Válassza a HA lehetőséget
   9. Rendszergazdai Felhasználónév és rendszergazdai jelszó  
      Létrejön egy új felhasználó, amely használható a gépre való bejelentkezéshez.
   10. Alhálózat azonosítója  
   Ha a virtuális gépet egy olyan meglévő VNet szeretné telepíteni, amelyben a virtuális gépet definiáló alhálózat van, akkor nevezze el az adott alhálózat AZONOSÍTÓját. Az azonosító általában úgy néz ki, mint az/Subscriptions/**&lt; előfizetés &gt; -azonosítója**/ResourceGroups/**&lt; &gt;****&lt; &gt;** nevű erőforráscsoport neve/Providers/Microsoft.Network/virtualNetworks/virtuális hálózat neve/Subnets/**&lt; alhálózat neve &gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>A Linux telepítése manuálisan Azure Portal használatával

Először létre kell hoznia a virtuális gépeket ehhez az NFS-fürthöz. Ezt követően hozzon létre egy terheléselosztó-t, és használja a virtuális gépeket a háttér-készletben.

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Rendelkezésre állási csoport létrehozása  
   Maximális frissítési tartomány beállítása
1. 1. virtuális gép létrehozása  
   Használjon legalább SLES4SAP 12 SP1-et, ebben a példában a SLES4SAP 12 SP1 rendszerképet https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES for SAP Applications 12 SP1 használata  
   Válassza ki a korábban létrehozott rendelkezésre állási készletet  
1. 2. virtuális gép létrehozása  
   Használjon legalább SLES4SAP 12 SP1-et, ebben a példában a SLES4SAP 12 SP1 rendszerképet https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES for SAP Applications 12 SP1 használata  
   Válassza ki a korábban létrehozott rendelkezésre állási készletet  
1. Adjon hozzá legalább egy adatlemezt mindkét virtuális géphez  
   Az adatlemezek a/usr/SAP/ `<SAPSID`> könyvtárához használatosak.
1. Load Balancer létrehozása (belső, standard):  
   1. Az előtérbeli IP-címek létrehozása
      1. A ASCS IP-10.0.0.7
         1. Nyissa meg a terheléselosztó-t, válassza a előtéri IP-készlet lehetőséget, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új előtér-IP-készlet nevét (például **NW1-ASCs-frontend**)
         1. Állítsa a hozzárendelést statikus értékre, és adja meg az IP-címet (például **10.0.0.7**).
         1. Kattintson az OK gombra
      1. A ASCS IP-10.0.0.8
         * A fenti lépések megismétlésével hozzon létre egy IP-címet az ERS számára (például **10.0.0.8** és **NW1-AERS-backend**)
   1. A háttérkészlet létrehozása
      1. Nyissa meg a Load balancert, válassza a háttérbeli készletek elemet, majd kattintson a Hozzáadás gombra.
      1. Adja meg az új háttérbeli készlet nevét (például **NW1-backend**)
      1. Kattintson a virtuális gép hozzáadása elemre.
      1. Virtuális gép kiválasztása
      1. Válassza ki az (A) SCS-fürthöz tartozó virtuális gépeket és azok IP-címeit.
      1. Kattintson az Add (Hozzáadás) parancsra
   1. Az állapot-mintavételek létrehozása
      1. A ASCS 620 **00** portja
         1. Nyissa meg a terheléselosztó-t, válassza az állapot-tesztek elemet, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új állapot-mintavétel nevét (például **NW1-ASCs-HP**)
         1. Válassza a TCP protokollt, a 620 **00** portot, az 5. időközt és a nem megfelelő állapotú küszöbértéket 2
         1. Kattintson az OK gombra
      1. Port 621 **02** ASCS-esekhöz
         * A fenti lépések megismétlésével hozzon létre egy állapot-mintavételt az ERS számára (például 621 **02** és **NW1-AERS-HP**)
   1. Terheléselosztási szabályok
      1. Terheléselosztási szabályok ASCS
         1. Nyissa meg a Load balancert, válassza a terheléselosztási szabályok elemet, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új Load Balancer-szabály nevét (például **NW1-LB-ASCs**)
         1. Válassza ki a korábban létrehozott előtérbeli IP-címet, háttér-készletet és állapot-mintavételt (például **NW1-ASCs-frontend**, **NW1-backend** és **NW1-ASCs-HP**)
         1. **Ha portok** kiválasztása
         1. Üresjárati időkorlát 30 percre növelve
         1. **Ügyeljen arra, hogy a lebegő IP-címet engedélyezze**
         1. Kattintson az OK gombra
         * A fenti lépések megismétlésével hozzon létre terheléselosztási szabályokat az ERS számára (például **NW1-LB-ERS**)
1. Ha a forgatókönyvben alapszintű terheléselosztó (belső) szükséges, kövesse az alábbi lépéseket:  
   1. Az előtérbeli IP-címek létrehozása
      1. A ASCS IP-10.0.0.7
         1. Nyissa meg a terheléselosztó-t, válassza a előtéri IP-készlet lehetőséget, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új előtér-IP-készlet nevét (például **NW1-ASCs-frontend**)
         1. Állítsa a hozzárendelést statikus értékre, és adja meg az IP-címet (például **10.0.0.7**).
         1. Kattintson az OK gombra
      1. A ASCS IP-10.0.0.8
         * A fenti lépések megismétlésével hozzon létre egy IP-címet az ERS számára (például **10.0.0.8** és **NW1-AERS-frontend**)
   1. A háttérkészlet létrehozása
      1. Nyissa meg a Load balancert, válassza a háttérbeli készletek elemet, majd kattintson a Hozzáadás gombra.
      1. Adja meg az új háttérbeli készlet nevét (például **NW1-backend**)
      1. Kattintson a virtuális gép hozzáadása elemre.
      1. Válassza ki a korábban létrehozott rendelkezésre állási készletet
      1. Válassza ki az (A) SCS-fürthöz tartozó virtuális gépeket.
      1. Kattintson az OK gombra
   1. Az állapot-mintavételek létrehozása
      1. A ASCS 620 **00** portja
         1. Nyissa meg a terheléselosztó-t, válassza az állapot-tesztek elemet, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új állapot-mintavétel nevét (például **NW1-ASCs-HP**)
         1. Válassza a TCP protokollt, a 620 **00** portot, az 5. időközt és a nem megfelelő állapotú küszöbértéket 2
         1. Kattintson az OK gombra
      1. Port 621 **02** ASCS-esekhöz
         * A fenti lépések megismétlésével hozzon létre egy állapot-mintavételt az ERS számára (például 621 **02** és **NW1-AERS-HP**)
   1. Terheléselosztási szabályok
      1. 32 **00** TCP a ASCS
         1. Nyissa meg a Load balancert, válassza a terheléselosztási szabályok elemet, majd kattintson a Hozzáadás gombra.
         1. Adja meg az új terheléselosztó-szabály nevét (például **NW1-LB-3200**)
         1. Válassza ki a korábban létrehozott előtérbeli IP-címet, háttér-készletet és állapot-mintavételt (például **NW1-ASCs-frontend**)
         1. Tartsa meg a protokoll **TCP**-t, írja be a **3200** portot
         1. Üresjárati időkorlát 30 percre növelve
         1. **Ügyeljen arra, hogy a lebegő IP-címet engedélyezze**
         1. Kattintson az OK gombra
      1. További portok a ASCS
         * Ismételje meg a fenti lépéseket a **36 00**,**39 00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 és TCP ASCS
      1. További portok a ASCS-ESEK számára
         * Ismételje meg a fenti lépéseket a 33 **02**, 5 **02** 13, 5 **02** 14, 5 **02** 16 és TCP ASCS-eseknél.

> [!IMPORTANT]
> A lebegő IP-címek nem támogatottak a terheléselosztási helyzetekben a hálózati adapter másodlagos IP-konfigurációjában. További részletek: az [Azure Load Balancer korlátozásai](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Ha a virtuális gép további IP-címére van szüksége, helyezzen üzembe egy második hálózati adaptert.  

> [!Note]
> Ha a nyilvános IP-címek nélküli virtuális gépek a belső (nincs nyilvános IP-cím) standard Azure Load Balancer háttér-készletbe kerülnek, nem lesz kimenő internetkapcsolat, kivéve, ha további konfigurálást végeznek a nyilvános végpontok útválasztásának engedélyezéséhez. A kimenő kapcsolatok elérésével kapcsolatos részletekért lásd: [nyilvános végpontú kapcsolat Virtual Machines az Azure standard Load Balancer használata az SAP magas rendelkezésre állási helyzetekben](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Ne engedélyezze a TCP-időbélyegeket a Azure Load Balancer mögött elhelyezett Azure-beli virtuális gépeken. A TCP-időbélyegek engedélyezése az állapot-mintavételek meghibásodását eredményezi. Állítsa a paramétert a **0** értékre **net.IPv4.tcp_timestamps** . Részletekért lásd: [Load Balancer Health](../../../load-balancer/load-balancer-custom-probe-overview.md)-tesztek.

### <a name="create-pacemaker-cluster"></a>Pacemaker-fürt létrehozása

Kövesse a [pacemaker beállítása SUSE Linux Enterprise Server az Azure-ban](high-availability-guide-suse-pacemaker.md) című témakör lépéseit egy alapszintű pacemaker-fürt létrehozásához ehhez A (a) SCS-kiszolgálóhoz.

### <a name="installation"></a>Telepítés

A következő elemek a **[a]** előtaggal vannak ellátva, amelyek az összes csomópontra érvényesek, **[1]** – csak az 1. vagy **[2]** csomópontra érvényesek, csak a 2. csomópontra.

1. **[A]** SUSE-összekötő telepítése

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > A gazdagép nevében található kötőjel használatával kapcsolatos ismert probléma az **SAP-SUSE-cluster-Connector** csomag **3.1.1** -es verziójában van meghatározva. Győződjön meg arról, hogy a csomag SAP-SUSE-cluster-Connector legalább 3.1.1-es verzióját használja, ha a fürt csomópontjait használja az állomásnévben. Ellenkező esetben a fürt nem fog működni. 

   Győződjön meg arról, hogy az SAP SUSE-fürt összekötő új verzióját telepítette. A régit hívták sap_suse_cluster_connector és az újat **SAP-SUSE-cluster-Connector** néven nevezzük.

   ```
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   ```

1. **[A]** SAP-erőforrás-ügynökök frissítése  
   
   Az ebben a cikkben ismertetett új konfiguráció használatához az erőforrás-ügynökök csomagra vonatkozó javításra van szükség. A következő paranccsal ellenőrizhető, hogy a javítás már telepítve van-e

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   A kimenetnek a következőhöz hasonlónak kell lennie

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Ha a GREP parancs nem találja a IS_ERS paramétert, telepítenie kell a [SUSE letöltési oldalán](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents) található javítást.

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]** telepítési állomásnév feloldása

   Használhat DNS-kiszolgálót, vagy módosíthatja a/etc/hosts az összes csomóponton. Ez a példa a/etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat a/etc/hosts. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően   

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Felkészülés az SAP NetWeaver telepítésére

1. **[A]** a megosztott könyvtárak létrehozása

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>
   
   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** AutoFS konfigurálása

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Hozzon létre egy fájlt

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   </code></pre>

   A AutoFS újraindítása az új megosztások csatlakoztatásához

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A] A** swap-fájl konfigurálása

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Az ügynök újraindítása a módosítás aktiválásához

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Az SAP NetWeaver ASCS/ERS telepítése

1. **[1]** virtuális IP-erőforrás és állapot-mintavétel létrehozása a ASCS-példányhoz

   > [!IMPORTANT]
   > A közelmúltbeli tesztelés feltárta a helyzeteket, ahol a netcat nem válaszol a várakozó kérelmekre, és csak egyetlen kapcsolat kezelésére vonatkozó korlátozásokat okoz. A netcat erőforrás nem figyeli az Azure Load Balancer kéréseit, és a lebegőpontos IP-cím elérhetetlenné válik.  
   > A meglévő pacemaker-fürtök esetében a netcat és a socat helyett a múltban javasolt. Jelenleg a csomag erőforrás-ügynökök részét képező Azure-LB erőforrás-ügynök használatát javasoljuk a következő csomag-verzióra vonatkozó követelményekkel:
   > - A SLES 12 SP4/SP5 esetében a verziónak legalább Resource-Agent-4.3.018. a7fb5035-3.30.1 kell lennie.  
   > - A SLES 15/15 SP1 esetében a verziónak legalább Resource-Agent-4.3.0184.6 ee15eb2-4.13.1 kell lennie.  
   >
   > Vegye figyelembe, hogy a módosítás rövid állásidőt igényel.  
   > Meglévő pacemaker-fürtök esetén, ha a konfigurációt már úgy módosították, hogy a socat használja az [azure Load-Balancer észlelésének megerősítése](https://www.suse.com/support/kb/doc/?id=7024128)című cikkben leírtak szerint, nem kell azonnal váltania az Azure-LB erőforrás-ügynökre.

   <pre><code>sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ASCS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCS' directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>NW1</b>_ASCS fs_<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

   <pre><code>sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** az SAP NetWeaver ASCS telepítése  

   Telepítse az SAP NetWeaver ASCS-t root-ként az első csomóponton egy olyan virtuális állomásnév használatával, amely a ASCS terheléselosztó felületi konfigurációjának IP-címére mutat, például: <b>NW1-ASCS</b>, <b>10.0.0.7</b> és a terheléselosztó mintavételéhez használt példány száma, például <b>00</b>.

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Ha a telepítés során nem sikerül almappát létrehozni a/usr/SAP/**NW1**/ASCS **00**-ben, próbálja meg beállítani a ASCS **00** mappa tulajdonosát és csoportját, és próbálkozzon újra.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** virtuális IP-erőforrás és állapot-mintavétel létrehozása az ERS-példányhoz

   <pre><code>sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ERS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS' directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS azure-lb port=621<b>02</b>
   
   sudo crm configure group g-<b>NW1</b>_ERS fs_<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

   <pre><code>sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** SAP NETWEAVER-ERS telepítése

   Telepítse az SAP NetWeaver-ket root-ként a második csomóponton egy olyan virtuális állomásnév használatával, amely leképezi a terheléselosztó előtér-konfigurációjának IP-címét, például: <b>NW1-AERS</b>, <b>10.0.0.8</b> és a terheléselosztó mintavételéhez használt példány számát, például: <b>02</b>.

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Használja az SWPM SP 20 PL 05-es vagy újabb verzióját. Az alacsonyabb verziók nem tudják megfelelően beállítani az engedélyeket, és a telepítés sikertelen lesz.

   Ha a telepítés során nem sikerül almappát létrehozni a/usr/SAP/**NW1**/ERS **02**-ben, próbálja meg beállítani az ERS **02** mappa tulajdonosát és csoportját, és próbálkozzon újra.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>


1. **[1]** a ASCS/SCS és az ERS instance-profilok átalakítása
 
   * ASCS/SCS-profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   A ENSA1 és a ENSA2 esetében ügyeljen arra, hogy az `keepalive` operációs rendszer paramétereinek beállítása a [1410736](https://launchpad.support.sap.com/#/notes/1410736)-es SAP-megjegyzésben leírtak szerint történjen.    

   * ERS-profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

1. **[A] A** Keep Alive konfigurálása

   Az SAP NetWeaver Application Server és a ASCS/SCS közötti kommunikáció egy szoftveres terheléselosztó használatával irányítható át. A terheléselosztó konfigurálható időtúllépés után leválasztja az inaktív kapcsolatokat. Ennek megelőzése érdekében be kell állítania egy paramétert az SAP NetWeaver ASCS/SCS profilban, ha a ENSA1-t használja, és minden SAP-kiszolgálón módosítania kell a Linux rendszer `keepalive` beállításait mind a ENSA1-, mind a ENSA2-hez. További információért olvassa el az [SAP megjegyzés 1410736][1410736] .

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

1. **[A]** a telepítés után KONFIGURÁLJA az SAP-felhasználókat

   <pre><code># Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm
   </code></pre>

1. **[1]** adja hozzá a ASCS és a ERS SAP-szolgáltatásokat a sapservice-fájlhoz

   Adja hozzá a ASCS-szolgáltatási bejegyzést a második csomóponthoz, és másolja az ERS szolgáltatási bejegyzést az első csomópontra.

   <pre><code>cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** az SAP-fürt erőforrásainak létrehozása

Ha a sorba helyezni Server 1 architektúráját (ENSA1) használja, az erőforrásokat az alábbiak szerint határozza meg:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Az SAP bevezette a 2. sorba helyezni-kiszolgáló, beleértve a replikálást, az SAP NW 7,52-támogatását. A ABAP platform 1809-től kezdődően a sorba helyezni Server 2 alapértelmezés szerint telepítve van. Lásd: SAP-Megjegyzés [2630416](https://launchpad.support.sap.com/#/notes/2630416) a sorba helyezni Server 2 támogatásához.
Ha a sorba helyezni Server 2 architektúráját ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) használja, adja meg az erőforrásokat az alábbiak szerint:

<pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true 
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Ha egy régebbi verzióról frissít, és átvált a 2. sorba helyezni-kiszolgálóra, tekintse meg a következőt: SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.


   <pre><code>sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Az SAP NetWeaver Application Server előkészítése

Egyes adatbázisokhoz szükséges, hogy az adatbázis példányának telepítése egy alkalmazáskiszolgáló legyen végrehajtva. Készítse elő az alkalmazáskiszolgáló virtuális gépeket, hogy azok használni tudják őket ezekben az esetekben.

Az ordító lépések azt feltételezik, hogy az alkalmazáskiszolgáló a ASCS/SCS és HANA kiszolgálóktól eltérő kiszolgálóra van telepítve. Ellenkező esetben az alábbi lépések (például az állomásnév-feloldás konfigurálása) nem szükségesek.

1. Operációs rendszer konfigurálása

   Csökkentse a piszkos gyorsítótár méretét. További információ: [alacsony írási teljesítmény a SLES 11/12-kiszolgálókon nagyméretű RAM-mal](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. Állomásnév feloldásának beállítása

   Használhat DNS-kiszolgálót, vagy módosíthatja a/etc/hosts az összes csomóponton. Ez a példa a/etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra

   ```bash
   sudo vi /etc/hosts
   ```

   Szúrja be a következő sorokat a/etc/hosts. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. A sapmnt könyvtár létrehozása

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. AutoFS konfigurálása

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Hozzon létre egy új fájlt a

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   A AutoFS újraindítása az új megosztások csatlakoztatásához

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Lapozófájl konfigurálása

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Az ügynök újraindítása a módosítás aktiválásához

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Az adatbázis telepítése

Ebben a példában az SAP NetWeaver SAP HANAra van telepítve. A telepítéshez minden támogatott adatbázist használhat. A SAP HANA Azure-ban való telepítésével kapcsolatos további információkért lásd: [SAP HANA magas rendelkezésre állása Azure-beli Virtual Machines (VM)][sap-hana-ha]. A támogatott adatbázisok listáját lásd: SAP- [megjegyzés 1928533][1928533].

1. Az SAP Database-példány telepítésének futtatása

   Telepítse az SAP NetWeaver Database-példányt root-ként egy olyan virtuális állomásnév használatával, amely az adatbázishoz tartozó terheléselosztó előtér-konfigurációjának IP-címére van leképezve, például <b>NW1-db</b> és <b>10.0.0.13</b>.

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver alkalmazáskiszolgáló telepítése

Az SAP-alkalmazáskiszolgáló telepítéséhez kövesse az alábbi lépéseket.

1. Alkalmazáskiszolgáló előkészítése

   Az alkalmazáskiszolgáló előkészítéséhez kövesse az alábbi, az [SAP NetWeaver Application Server-előkészítés](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) című fejezet lépéseit.

1. Az SAP NetWeaver Application Server telepítése

   Telepítsen egy elsődleges vagy további SAP NetWeaver Application Servert.

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. SAP HANA Biztonságos tár frissítése

   Frissítse a SAP HANA biztonságos tárolót, hogy az SAP HANA rendszerreplikáció beállításának virtuális nevére mutasson.

   A következő parancs futtatásával sorolja fel a bejegyzéseket
   <pre><code>hdbuserstore List
   </code></pre>

   Ennek az összes bejegyzést fel kell sorolnia, és a következőhöz hasonlóan kell kinéznie
   <pre><code>DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>HN1</b>
   </code></pre>

   A kimenet azt mutatja, hogy az alapértelmezett bejegyzés IP-címe a virtuális gépre mutat, nem pedig a terheléselosztó IP-címére. Ezt a bejegyzést úgy kell módosítani, hogy a terheléselosztó virtuális állomásneve mutasson. Ügyeljen arra, hogy ugyanazt a portot használja (a fenti kimenetben **30313** ) és az adatbázis nevét (a fenti kimenet **HN1** )!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db:30313@HN1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

A következő tesztek a tesztelési esetek egy példányát jelentik a SUSE ajánlott eljárásokat ismertető útmutatójában. Ezeket a rendszer átmásolja az Ön kényelme érdekében. Mindig olvassa el az ajánlott eljárásokat ismertető útmutatót, és végezze el az esetleg hozzáadott további teszteket is.

1. HAGetFailoverConfig, HACheckConfig és HACheckFailoverConfig tesztelése

   Futtassa az alábbi parancsokat adm-ként \<sapsid> azon a csomóponton, amelyen a ASCS-példány jelenleg fut. Ha a parancsok meghiúsulnak: nincs elég memória, a gazdagép kötőjelei okozhatja. Ez egy ismert probléma, amelyet a SUSE az SAP-SUSE-cluster-Connector csomagban fog megállapítani.

   <pre><code>nw1-cl-0:nw1adm 54> sapcontrol -nr <b>00</b> -function HAGetFailoverConfig
   
   # 15.08.2018 13:50:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: Toolchain Module
   # HASAPInterfaceVersion: Toolchain Module (sap_suse_cluster_connector 3.0.1)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode:
   # HANodes: nw1-cl-0, nw1-cl-1
   
   nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -function HACheckConfig
   
   # 15.08.2018 14:00:04
   # HACheckConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   # SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   # SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   # SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   # SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   # SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   # SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   # SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   # SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   # SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (nw1-ascs_NW1_00), SAPInstance includes is-ers patch
   # SUCCESS, SAP CONFIGURATION, Enqueue replication (nw1-ascs_NW1_00), Enqueue replication enabled
   # SUCCESS, SAP STATE, Enqueue replication state (nw1-ascs_NW1_00), Enqueue replication active
   
   nw1-cl-0:nw1adm 56> sapcontrol -nr 00 -function HACheckFailoverConfig
   
   # 15.08.2018 14:04:08
   # HACheckFailoverConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

1. A ASCS-példány manuális áttelepíteni

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa a következő parancsokat root-ként a ASCS-példány áttelepíteni.

   <pre><code>nw1-cl-0:~ # crm resource migrate rsc_sap_NW1_ASCS00 force
   # INFO: Move constraint created for rsc_sap_NW1_ASCS00
   
   nw1-cl-0:~ # crm resource unmigrate rsc_sap_NW1_ASCS00
   # INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. HAFailoverToNode tesztelése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Futtassa az alábbi parancsokat az \<sapsid> adm-ként a ASCS-példány áttelepíteni.

   <pre><code>nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -host nw1-ascs -user nw1adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   # Remove migration constraints
   nw1-cl-0:~ # crm resource clear rsc_sap_NW1_ASCS00
   #INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Csomópont összeomlásának szimulálása

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa a következő parancsot gyökérként azon a csomóponton, amelyen a ASCS-példány fut

   <pre><code>nw1-cl-0:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Ha SBD használ, a pacemaker nem kezdődhet automatikusan a megölt csomóponton. A csomópont újraindítása utáni állapotnak így kell kinéznie.

   <pre><code>Online: [ nw1-cl-1 ]
   OFFLINE: [ nw1-cl-0 ]
   
   Full list of resources:
   
   stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-1 'not running' (7): call=219, status=complete, exitreason='none',
       last-rc-change='Wed Aug 15 14:38:38 2018', queued=0ms, exec=0ms
   </code></pre>

   Az alábbi parancsokkal elindíthatja a pacemakert a megölt csomóponton, megtisztíthatja a SBD-üzeneteket, és megtisztíthatja a sikertelen erőforrásokat.

   <pre><code># run as root
   # list the SBD device(s)
   nw1-cl-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   nw1-cl-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message nw1-cl-0 clear
   
   nw1-cl-0:~ # systemctl start pacemaker
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. A ASCS-példány manuális újraindításának tesztelése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Hozzon létre egy sorba helyezni zárolást, például szerkesszen egy felhasználót a tranzakció su01. Futtassa az alábbi parancsokat adm-ként \<sapsid> azon a csomóponton, amelyen a ASCS-példány fut. A parancsok leállítják a ASCS-példányt, és újra elindítják. Ha a sorba helyezni Server 1 architektúrát használja, a sorba helyezni zárolás várhatóan el fog veszni ebben a tesztben. Ha a sorba helyezni Server 2 architektúrát használja, a rendszer megőrzi a sorba helyezni. 

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   A ASCS-példánynak mostantól le kell tiltania a Pacemakerben

   <pre><code>rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Indítsa el újra a ASCS példányt ugyanazon a csomóponton.

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   A tranzakciós su01 sorba helyezni-zárolását el kell veszíteni, és a háttérnek alaphelyzetbe kell állítani. Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Az üzenetküldési kiszolgáló folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Futtassa az alábbi parancsokat root-ként az üzenet-kiszolgáló folyamatának azonosításához és a megöléséhez.

   <pre><code>nw1-cl-1:~ # pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Ha csak egyszer fogja megölni az üzenetet, a sapstart újraindítja a kiszolgálót. Ha elég gyakran megölni, a pacemaker végül áthelyezi a ASCS-példányt a másik csomópontra. A teszt után futtassa a következő parancsokat root-ként a ASCS és az ERS-példány erőforrás-állapotának tisztításához.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Sorba helyezni-kiszolgáló folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa a következő parancsokat gyökérként azon a csomóponton, amelyen a ASCS-példány fut, hogy megöli a sorba helyezni-kiszolgálót.

   <pre><code>nw1-cl-0:~ # pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   A ASCS-példánynak azonnal át kell vennie a feladatátvételt a másik csomópontra. Az ERS-példánynak a ASCS-példány elindítása után is feladatátvételt kell tennie. A teszt után futtassa a következő parancsokat root-ként a ASCS és az ERS-példány erőforrás-állapotának tisztításához.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Sorba helyezni-replikációs kiszolgáló folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Futtassa a következő parancsot gyökérként azon a csomóponton, amelyen az ERS-példány fut, hogy megöli a sorba helyezni replikációs kiszolgáló folyamatát.

   <pre><code>nw1-cl-0:~ # pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Ha csak egyszer futtatja a parancsot, a sapstart újraindítja a folyamatot. Ha elég gyakran futtatja, a sapstart nem indítja újra a folyamatot, és az erőforrás leállított állapotba kerül. A teszt után futtassa a következő parancsokat root-ként az ERS-példány erőforrás-állapotának tisztításához.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Sorba helyezni sapstartsrv folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Futtassa a következő parancsokat gyökérként azon a csomóponton, amelyen a ASCS fut.

   <pre><code>nw1-cl-1:~ # pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   nw1-cl-1:~ # kill -9 59545
   </code></pre>

   A sapstartsrv folyamatot mindig újra kell indítani a pacemaker erőforrás-ügynökkel. Erőforrás állapota a teszt után:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

## <a name="next-steps"></a>Következő lépések

* [HA SAP NW Azure-beli virtuális gépeken, SLES for SAP Applications multi-SID útmutató](./high-availability-guide-suse-multi-sid.md)
* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez lásd: [Az Azure-beli SAP HANA magas rendelkezésre állása Virtual Machines (VM)][sap-hana-ha]