---
title: Azure-beli virtuális gépek magas rendelkezésre állása SAP NW-hez a RHEL-on | Microsoft Docs
description: Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver-on Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/11/2021
ms.author: radeltch
ms.openlocfilehash: 9c712e1e7536c35a937349aedef3065e1ad34ccc
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097058"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver-on Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Ez a cikk leírja, hogyan telepítheti a virtuális gépeket, konfigurálhatja a virtuális gépeket, telepítheti a fürtöt, és telepítheti a kiválóan elérhető SAP NetWeaver 7,50 rendszerét.
A példában szereplő konfigurációk, telepítési parancsok stb. A ASCS példányok száma 00, az ERS-példányok száma 02, a rendszer pedig az SAP rendszerazonosító NW1 használja. A példában szereplő erőforrások (például virtuális gépek, virtuális hálózatok) nevei feltételezik, hogy a [ASCS/SCS sablont][template-multisid-xscs] használta az erőforrás-előtag NW1 az erőforrások létrehozásához.

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat

* SAP-Megjegyzés [1928533], amely a következőket tartalmazta:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája
  * Fontos kapacitási információk Azure-beli virtuális gépek méreteihez
  * Támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk
  * A Windows és a Linux rendszerhez szükséges SAP kernel verziója Microsoft Azure

* Az SAP Note [2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Megjegyzés [2002167] ajánlott operációsrendszer-beállításokkal Red Hat Enterprise Linux
* A [2009879] -es SAP-Megjegyzés SAP HANA irányelvek a Red Hat Enterprise Linux
* Az [2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az SAP Megjegyzés [1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [A Red Hat Gluster Storage termék dokumentációja](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [SAP NetWeaver a pacemaker-fürtben](https://access.redhat.com/articles/3150081)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású Add-On áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású Add-On felügyelet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású Add-On referenciája](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Az SAP NetWeaver ASCS/ERS konfigurálása önálló erőforrásokkal a RHEL 7,5-ben](https://access.redhat.com/articles/3569681)
  * [Az SAP S/4HANA ASCS/ERS konfigurálása önálló sorba helyezni Server 2 (ENSA2) segítségével a RHEL-beli Pacemakerben ](https://access.redhat.com/articles/3974941)
* Az Azure-specifikus RHEL dokumentációja:
  * [A RHEL magas rendelkezésre állású fürtökre vonatkozó támogatási szabályzatok – Microsoft Azure Virtual Machines a fürt tagjai](https://access.redhat.com/articles/3131341)
  * [Red Hat Enterprise Linux 7,4 (és újabb) High-Availability fürt telepítése és konfigurálása Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Áttekintés

A magas rendelkezésre állás elérése érdekében az SAP NetWeaver megosztott tárterületet igényel. A GlusterFS külön fürtben van konfigurálva, és több SAP-rendszer is használható.

![SAP NetWeaver – magas rendelkezésre állás – áttekintés](./media/high-availability-guide-rhel/ha-rhel.png)

Az SAP NetWeaver ASCS, az SAP NetWeaver SCS, az SAP NetWeaver ERS és a SAP HANA adatbázis virtuális gazdagépeket és virtuális IP-címeket használ. Az Azure-ban a virtuális IP-címek használatához terheléselosztó szükséges. A [standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)használatát javasoljuk. Az alábbi lista az (A) SCS és az ERS Load Balancer konfigurációját mutatja be.

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

## <a name="setting-up-glusterfs"></a>GlusterFS beállítása

Az SAP NetWeaver megosztott tárterületet igényel az átvitelhez és a profilhoz. GlusterFS olvasása Azure-beli [virtuális gépeken Red Hat Enterprise Linux for SAP NetWeaver][glusterfs-ha] az SAP NetWeaver GlusterFS beállításához.

## <a name="setting-up-ascs"></a>Az (A) SCS beállítása

A GitHubról származó Azure-sablonok használatával üzembe helyezheti az összes szükséges Azure-erőforrást, beleértve a virtuális gépeket, a rendelkezésre állási készletet és a terheléselosztó-t, vagy manuálisan is üzembe helyezheti az erőforrásokat.

### <a name="deploy-linux-via-azure-template"></a>Linux telepítése Azure-sablon használatával

Az Azure Marketplace egy Red Hat Enterprise Linux rendszerképet tartalmaz, amely az új virtuális gépek üzembe helyezéséhez használható. Az összes szükséges erőforrás üzembe helyezéséhez használhatja a GitHubon a rövid útmutató sablonjait. A sablon üzembe helyezi a virtuális gépeket, a terheléselosztó-t, a rendelkezésre állási készletet stb. A sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [ASCS/SCS sablont][template-multisid-xscs] a Azure Portal  
1. Adja meg a következő paramétereket
   1. Erőforrás-előtag  
      Adja meg a használni kívánt előtagot. Az értéket a rendszer az üzembe helyezett erőforrások előtagjaként használja.
   1. Verem típusa  
      Válassza ki az SAP NetWeaver stack típusát
   1. Operációs rendszer típusa  
      Válassza ki a Linux-disztribúciók egyikét. Ebben a példában válassza a RHEL 7 elemet.
   1. Adatbázis típusa  
      HANA kiválasztása
   1. SAP-rendszerek száma  
      Az ebben a fürtben futó SAP-rendszer száma. Válassza az 1 lehetőséget.
   1. A rendszerek rendelkezésre állása  
      Válassza a HA lehetőséget
   1. Rendszergazdai Felhasználónév, rendszergazdai jelszó vagy SSH-kulcs  
      Létrejön egy új felhasználó, amely a gépre való bejelentkezéshez használható.
   1. Alhálózat azonosítója  
   Ha a virtuális gépet egy olyan meglévő VNet szeretné telepíteni, amelyben a virtuális gépet definiáló alhálózat van, akkor nevezze el az adott alhálózat AZONOSÍTÓját. Az azonosító általában úgy néz ki, mint az/Subscriptions/**&lt; előfizetés &gt; -azonosítója**/ResourceGroups/**&lt; &gt;****&lt; &gt;** nevű erőforráscsoport neve/Providers/Microsoft.Network/virtualNetworks/virtuális hálózat neve/Subnets/**&lt; alhálózat neve &gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>A Linux telepítése manuálisan Azure Portal használatával

Először létre kell hoznia a fürthöz tartozó virtuális gépeket. Ezt követően hozzon létre egy terheléselosztó-t, és használja a virtuális gépeket a háttér-készletben.

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Rendelkezésre állási csoport létrehozása  
   Maximális frissítési tartomány beállítása
1. 1. virtuális gép létrehozása  
   Használjon legalább RHEL 7, ebben a példában a Red Hat Enterprise Linux 7,4 rendszerképet <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Válassza ki a korábban létrehozott rendelkezésre állási készletet  
1. 2. virtuális gép létrehozása  
   Használjon legalább RHEL 7, ebben a példában a Red Hat Enterprise Linux 7,4 rendszerképet <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
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
         * A fenti lépések megismétlésével hozzon létre egy IP-címet az ERS számára (például **10.0.0.8** és **NW1-AERS-frontend**)
   1. A háttérkészlet létrehozása
      1. Nyissa meg a Load balancert, válassza a háttérbeli készletek elemet, majd kattintson a Hozzáadás gombra.
      1. Adja meg az új háttérbeli készlet nevét (például **NW1-backend**)
      1. Kattintson a virtuális gép hozzáadása elemre.
      1. Válassza a Virtuális gép lehetőséget.
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

Kövesse a [pacemaker beállítása Red Hat Enterprise Linux az Azure-ban](high-availability-guide-rhel-pacemaker.md) című témakör lépéseit egy alapszintű pacemaker-fürt létrehozásához ehhez A (a) SCS-kiszolgálóhoz.

### <a name="prepare-for-sap-netweaver-installation"></a>Felkészülés az SAP NetWeaver telepítésére

A következő elemek a **[a]** előtaggal vannak ellátva, amelyek az összes csomópontra érvényesek, **[1]** – csak az 1. vagy **[2]** csomópontra érvényesek, csak a 2. csomópontra.

1. **[A]** telepítési állomásnév feloldása

   Használhat DNS-kiszolgálót, vagy módosíthatja a/etc/hosts az összes csomóponton. Ez a példa a/etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat a/etc/hosts. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

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

1. **[A]** GlusterFS-ügyfél és egyéb követelmények telepítése

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]** az erőforrás-ügynökök verziójának ellenõrzése – SAP

   Győződjön meg arról, hogy a telepített erőforrás-ügynökök-SAP-csomag verziószáma legalább 3.9.5 -124. el7
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]** csatlakoztatási bejegyzések hozzáadása

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Az új megosztások csatlakoztatása

   <pre><code>sudo mount -a
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

1. **[A]** RHEL-konfiguráció

   Konfigurálja a RHEL az SAP Note [2002167] -es verziójában leírtak szerint

### <a name="installing-sap-netweaver-ascsers"></a>Az SAP NetWeaver ASCS/ERS telepítése

1. **[1]** virtuális IP-erőforrás és állapot-mintavétel létrehozása a ASCS-példányhoz

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** az SAP NetWeaver ASCS telepítése  

   Telepítse az SAP NetWeaver ASCS-t root-ként az első csomóponton egy olyan virtuális állomásnév használatával, amely a ASCS terheléselosztó felületi konfigurációjának IP-címére mutat, például: <b>NW1-ASCS</b>, <b>10.0.0.7</b> és a terheléselosztó mintavételéhez használt példány száma, például <b>00</b>.

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Ha a telepítés során nem sikerül almappát létrehozni a/usr/SAP/**NW1**/ASCS **00**-ben, próbálja meg beállítani a ASCS **00** mappa tulajdonosát és csoportját, és próbálkozzon újra.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** virtuális IP-erőforrás és állapot-mintavétel létrehozása az ERS-példányhoz

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]** SAP NETWEAVER-ERS telepítése  

   Telepítse az SAP NetWeaver-ket root-ként a második csomóponton egy olyan virtuális állomásnév használatával, amely leképezi a terheléselosztó előtér-konfigurációjának IP-címét, például: <b>NW1-AERS</b>, <b>10.0.0.8</b> és a terheléselosztó mintavételéhez használt példány számát, például: <b>02</b>.

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Ha a telepítés során nem sikerül almappát létrehozni a/usr/SAP/**NW1**/ERS **02**-ben, próbálja meg beállítani az ERS **02** mappa tulajdonosát és csoportját, és próbálkozzon újra.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]** a ASCS/SCS és az ERS instance-profilok átalakítása

   * ASCS/SCS-profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   A ENSA1 és a ENSA2 esetében ügyeljen arra, hogy az `keepalive` operációs rendszer paramétereinek beállítása a [1410736](https://launchpad.support.sap.com/#/notes/1410736)-es SAP-megjegyzésben leírtak szerint történjen.    

   * ERS-profil

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A] A** Keep Alive konfigurálása

   Az SAP NetWeaver Application Server és a ASCS/SCS közötti kommunikáció egy szoftveres terheléselosztó használatával irányítható át. A terheléselosztó konfigurálható időtúllépés után leválasztja az inaktív kapcsolatokat. Ennek megelőzése érdekében be kell állítania egy paramétert az SAP NetWeaver ASCS/SCS profilban, ha a ENSA1-t használja, és minden SAP-kiszolgálón módosítania kell a Linux rendszer `keepalive` beállításait mind a ENSA1-, mind a ENSA2-hez. További információért olvassa el az [SAP megjegyzés 1410736][1410736] .

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

1. **[A]** a/usr/SAP/sapservices fájl frissítése

   Ha meg szeretné akadályozni, hogy a sapinit indítási parancsfájlja elindítsa a példányokat, a pacemaker által kezelt összes példányt ki kell kommentálni a/usr/SAP/sapservices-fájlból. Ne tegye megjegyzésbe a SAP HANA példányt, ha a HANA SR-vel fog használni.

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]** az SAP-fürt erőforrásainak létrehozása

  Ha a sorba helyezni Server 1 architektúráját (ENSA1) használja, az erőforrásokat az alábbiak szerint határozza meg:

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Az SAP bevezette a 2. sorba helyezni-kiszolgáló, beleértve a replikálást, az SAP NW 7,52-támogatását. A ABAP platform 1809-től kezdődően a sorba helyezni Server 2 alapértelmezés szerint telepítve van. Lásd: SAP-Megjegyzés [2630416](https://launchpad.support.sap.com/#/notes/2630416) a sorba helyezni Server 2 támogatásához.
   Ha a sorba helyezni Server 2 architektúráját ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) használja, telepítse az erőforrás-ügynök erőforrás-ügynökök-SAP-4.1.1-12.el7.x86_64 vagy újabb verzióját, és adja meg az erőforrásokat az alábbiak szerint:

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Ha egy régebbi verzióról frissít, és átvált a 2. sorba helyezni-kiszolgálóra, tekintse meg a következőt: SAP Note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > A fenti konfiguráció időtúllépései csak példák, és előfordulhat, hogy az adott SAP-beállításhoz kell igazítani őket. 

   Győződjön meg arról, hogy a fürt állapota ok, és hogy az összes erőforrás el van indítva. Nem fontos, hogy az erőforrások melyik csomóponton futnak.

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]** tűzfalszabályok hozzáadása a ASCS és a ERS-hoz mindkét csomóponton

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Az SAP NetWeaver Application Server előkészítése

Egyes adatbázisokhoz szükséges, hogy az adatbázis példányának telepítése egy alkalmazáskiszolgáló legyen végrehajtva. Készítse elő az alkalmazáskiszolgáló virtuális gépeket, hogy azok használni tudják őket ezekben az esetekben.

Az ordító lépések azt feltételezik, hogy az alkalmazáskiszolgáló a ASCS/SCS és HANA kiszolgálóktól eltérő kiszolgálóra van telepítve. Ellenkező esetben az alábbi lépések (például az állomásnév-feloldás konfigurálása) nem szükségesek.

1. Állomásnév feloldásának beállítása

   Használhat DNS-kiszolgálót, vagy módosíthatja a/etc/hosts az összes csomóponton. Ez a példa a/etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat a/etc/hosts. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

1. A sapmnt könyvtár létrehozása

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. A GlusterFS-ügyfél és egyéb követelmények telepítése

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Csatlakoztatási bejegyzések hozzáadása

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Az új megosztások csatlakoztatása

   <pre><code>sudo mount -a
   </code></pre>

1. Lapozófájl konfigurálása
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Az ügynök újraindítása a módosítás aktiválásához

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Az adatbázis telepítése

Ebben a példában az SAP NetWeaver SAP HANAra van telepítve. A telepítéshez minden támogatott adatbázist használhat. A SAP HANA Azure-beli telepítésével kapcsolatos további információkért lásd: a [SAP HANA magas rendelkezésre állása az Azure-beli virtuális gépeken Red Hat Enterprise Linuxon][sap-hana-ha] . For a list of supported databases, see [SAP Note 1928533][1928533] .

1. Az SAP Database-példány telepítésének futtatása

   Telepítse az SAP NetWeaver Database-példányt root-ként egy olyan virtuális állomásnév használatával, amely az adatbázishoz tartozó terheléselosztó előtér-konfigurációjának IP-címére van leképezve, például <b>NW1-db</b> és <b>10.0.0.13</b>.

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver alkalmazáskiszolgáló telepítése

Az SAP-alkalmazáskiszolgáló telepítéséhez kövesse az alábbi lépéseket.

1. Alkalmazáskiszolgáló előkészítése

   Az alkalmazáskiszolgáló előkészítéséhez kövesse az alábbi, az [SAP NetWeaver Application Server-előkészítés](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) című fejezet lépéseit.

1. Az SAP NetWeaver Application Server telepítése

   Telepítsen egy elsődleges vagy további SAP NetWeaver Application Servert.

   A sapinst paraméterrel SAPINST_REMOTE_ACCESS_USER engedélyezheti, hogy a nem root felhasználó csatlakozhasson a sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. SAP HANA Biztonságos tár frissítése

   Frissítse a SAP HANA biztonságos tárolót, hogy az SAP HANA rendszerreplikáció beállításának virtuális nevére mutasson.

   Futtassa a következő parancsot a bejegyzések adm-ként való listázásához. \<sapsid>

   <pre><code>hdbuserstore List
   </code></pre>

   Ennek az összes bejegyzést fel kell sorolnia, és a következőhöz hasonlóan kell kinéznie
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   A kimenet azt mutatja, hogy az alapértelmezett bejegyzés IP-címe a virtuális gépre mutat, nem pedig a terheléselosztó IP-címére. Ezt a bejegyzést úgy kell módosítani, hogy a terheléselosztó virtuális állomásneve mutasson. Ügyeljen arra, hogy ugyanazt a portot használja (a fenti kimenetben **30313** ) és az adatbázis nevét (a fenti kimenet **HN1** )!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>A fürt beállításának tesztelése

1. A ASCS-példány manuális áttelepíteni

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa a következő parancsokat root-ként a ASCS-példány áttelepíteni.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Csomópont összeomlásának szimulálása

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Futtassa a következő parancsot gyökérként azon a csomóponton, amelyen a ASCS-példány fut

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   A csomópont újraindítása utáni állapotnak így kell kinéznie.

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   A sikertelen erőforrások tisztításához használja az alábbi parancsot.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Az üzenetküldési kiszolgáló folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa az alábbi parancsokat root-ként az üzenet-kiszolgáló folyamatának azonosításához és a megöléséhez.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -f ms.sapNW1 | xargs kill -9
   </code></pre>

   Ha csak egyszer fogja megölni az üzenetet kiszolgálóját, a rendszer újraindítja `sapstart` . Ha elég gyakran megölni, a pacemaker végül áthelyezi a ASCS-példányt a másik csomópontra. A teszt után futtassa a következő parancsokat root-ként a ASCS és az ERS-példány erőforrás-állapotának tisztításához.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Sorba helyezni-kiszolgáló folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Futtassa a következő parancsokat gyökérként azon a csomóponton, amelyen a ASCS-példány fut, hogy megöli a sorba helyezni-kiszolgálót.

   <pre><code>
    #If using ENSA1 
    [root@nw1-cl-1 ~]# pgrep -f en.sapNW1 | xargs kill -9
    #If using ENSA2
    [root@nw1-cl-1 ~]# pgrep -f enq.sapNW1 | xargs kill -9
   </code></pre>

   A ASCS-példánynak azonnal át kell vennie a feladatátvételt a másik csomópontra. Az ERS-példánynak a ASCS-példány elindítása után is feladatátvételt kell tennie. A teszt után futtassa a következő parancsokat root-ként a ASCS és az ERS-példány erőforrás-állapotának tisztításához.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Sorba helyezni-replikációs kiszolgáló folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa a következő parancsot gyökérként azon a csomóponton, amelyen az ERS-példány fut, hogy megöli a sorba helyezni replikációs kiszolgáló folyamatát.

   <pre><code>
    #If using ENSA1
    [root@nw1-cl-1 ~]# pgrep -f er.sapNW1 | xargs kill -9
    #If using ENSA2
    [root@nw1-cl-1 ~]# pgrep -f enqr.sapNW1 | xargs kill -9
   </code></pre>

   Ha csak egyszer futtatja a parancsot, `sapstart` a automatikusan újraindítja a folyamatot. Ha elég gyakran fut, a `sapstart` nem indítja újra a folyamatot, és az erőforrás leállított állapotba kerül. A teszt után futtassa a következő parancsokat root-ként az ERS-példány erőforrás-állapotának tisztításához.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Erőforrás állapota a teszt után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Sorba helyezni sapstartsrv folyamatának leölése

   Erőforrás állapota a teszt elindítása előtt:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Futtassa a következő parancsokat gyökérként azon a csomóponton, amelyen a ASCS fut.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   A sapstartsrv folyamatot mindig újra kell indítani a pacemaker erőforrás-ügynöknek a figyelés részeként. Erőforrás állapota a teszt után:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>Következő lépések

* [HA SAP NW Azure-beli virtuális gépeken, RHEL for SAP Applications multi-SID útmutató](./high-availability-guide-rhel-multi-sid.md)
* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és hogyan tervezheti meg az Azure-beli SAP HANA vész-helyreállítását (nagyméretű példányok), tekintse meg [a SAP HANA (nagyméretű példányok) magas rendelkezésre állását és a](hana-overview-high-availability-disaster-recovery.md)
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez lásd: [Az Azure-beli SAP HANA magas rendelkezésre állása Virtual Machines (VM)][sap-hana-ha]