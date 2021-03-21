---
title: A pacemaker beállítása a RHEL az Azure-ban | Microsoft Docs
description: A pacemaker beállítása Red Hat Enterprise Linux az Azure-ban
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2021
ms.author: radeltch
ms.openlocfilehash: af8523486b42af8c0722a56bdd813d6449692c14
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676891"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>A pacemaker beállítása Red Hat Enterprise Linux az Azure-ban

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* SAP-Megjegyzés [1928533], amely a következőket tartalmazta:
  * Az SAP-szoftverek üzembe helyezéséhez támogatott Azure-beli virtuálisgép-méretek listája.
  * Az Azure-beli virtuális gépek méretével kapcsolatos fontos kapacitási információk.
  * A támogatott SAP-szoftverek és operációs rendszerek (OS) és adatbázis-kombinációk.
  * A Windows és a Linux szükséges SAP kernel-verziója Microsoft Azureon.
* Az SAP Note [2015553] az SAP által támogatott SAP-szoftverek Azure-beli üzembe helyezésének előfeltételeit sorolja fel.
* Az SAP Megjegyzés [2002167] ajánlott operációsrendszer-beállításokkal Red Hat Enterprise Linux
* A [2009879] -es SAP-Megjegyzés SAP HANA irányelvek a Red Hat Enterprise Linux
* Az [2178632] -es SAP-Megjegyzés részletes információkat tartalmaz az Azure-beli SAP-ban jelentett összes figyelési mérőszámról.
* A [2191498] -es SAP-Megjegyzés a szükséges SAP-gazdagép ügynökének verziója az Azure-ban linuxos.
* Az [2243692] -es SAP-Megjegyzés az Azure-beli Linuxon futó SAP-licenceléssel kapcsolatos információkat tartalmaz.
* Az SAP Megjegyzés [1999351] további hibaelhárítási információkat tartalmaz az SAP-hez készült Azure Enhanced monitoring bővítménnyel kapcsolatban.
* Az [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik minden szükséges SAP-megjegyzéssel a Linux rendszerhez.
* [Azure Virtual Machines tervezése és implementálása Linux rendszeren az SAP-ban][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-hez Linux rendszeren (ez a cikk)][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hez Linux rendszeren][dbms-guide]
* [Rendszerreplikáció SAP HANA a pacemaker-fürtben](https://access.redhat.com/articles/3004101)
* Általános RHEL dokumentáció
  * [Magas rendelkezésre állású Add-On áttekintése](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Magas rendelkezésre állású Add-On felügyelet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Magas rendelkezésre állású Add-On referenciája](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [A magas rendelkezésre állású fürtök RHEL vonatkozó támogatási szabályzatok – SBD és fence_sbd](https://access.redhat.com/articles/2800691)
* Az Azure-specifikus RHEL dokumentációja:
  * [A RHEL magas rendelkezésre állású fürtökre vonatkozó támogatási szabályzatok – Microsoft Azure Virtual Machines a fürt tagjai](https://access.redhat.com/articles/3131341)
  * [Red Hat Enterprise Linux 7,4 (és újabb) High-Availability fürt telepítése és konfigurálása Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [A RHEL 8 – magas rendelkezésre állás és fürtök bevezetésének szempontjai](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/considerations_in_adopting_rhel_8/high-availability-and-clusters_considerations-in-adopting-rhel-8)
  * [Az SAP S/4HANA ASCS/ERS konfigurálása önálló sorba helyezni-kiszolgáló 2 (ENSA2) segítségével a pacemaker on RHEL 7,6](https://access.redhat.com/articles/3974941)
  * [Az Azure-beli SAP-ajánlatok RHEL](https://access.redhat.com/articles/5456301)

## <a name="cluster-installation"></a>Fürt telepítése

![Pacemaker on RHEL – áttekintés](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> A Red Hat nem támogatja a szoftveresen emulált watchdog használatát. A Red Hat nem támogatja a SBD a felhőalapú platformokon. Részletekért lásd: [a RHEL magas rendelkezésre állású fürtökhöz kapcsolódó támogatási szabályzatai – SBD és fence_sbd](https://access.redhat.com/articles/2800691).
> Az egyetlen támogatott kerítési mechanizmus a pacemaker Red Hat Enterprise Linux-fürtökhöz az Azure-ban, az Azure kerítés ügynöke.  

A következő elemek a **[a]** előtaggal vannak ellátva, amelyek az összes csomópontra érvényesek, **[1]** – csak az 1. vagy **[2]** csomópontra érvényesek, csak a 2. csomópontra.

1. **[A]** regisztráljon. Ez a lépés nem szükséges, ha a RHEL SAP HA-kompatibilis lemezképeket használ.  

   Regisztrálja a virtuális gépeket, és csatolja azt egy készlethez, amely a RHEL 7 tárházait tartalmazza.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Ha készletet csatlakoztat egy Azure Marketplace-TB RHEL-rendszerképhez, a RHEL-használat után gyakorlatilag egyszer kell fizetnie, és egyszer kell megadnia a RHEL jogosultságot a csatolni kívánt készletben. Ennek enyhítése érdekében az Azure mostantól BYOS RHEL-lemezképeket biztosít. További információt [itt](../redhat/byos.md) talál.  

1. **[A]** engedélyezze a RHEL az SAP-repók számára. Ez a lépés nem szükséges, ha a RHEL SAP HA-kompatibilis lemezképeket használ.  

   A szükséges csomagok telepítéséhez engedélyezze a következő adattárakat.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** telepítse a RHEL ha Add-On

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > A következő Azure kerítés-ügynök (vagy újabb verziók) használatát javasoljuk az ügyfelek számára a gyorsabb feladatátvételi idő kihasználása érdekében, ha egy erőforrás leáll, vagy ha a fürtcsomópontok nem tudnak kommunikálni egymással:  
   > A RHEL 7,7 vagy újabb verziója a kerítés-ügynökök csomag legújabb elérhető verzióját használja  
   > RHEL 7,6: kerítés-ügynökök-4.2.1-11.el7_6.8  
   > RHEL 7,5: kerítés-ügynökök-4.0.11-86.el7_5.8  
   > RHEL 7,4: kerítés-ügynökök-4.0.11-66.el7_4.12  
   > További információkért tekintse meg a [magas rendelkezésre állású RHEL futó Azure-beli virtuális gépeket, ha a virtuális gép leáll, vagy a kerítés sikertelen/időtúllépést](https://access.redhat.com/solutions/3408711)okoz.

   Keresse meg az Azure kerítés ügynökének verzióját. Ha szükséges, frissítse a fent megadott vagy újabb verzióra.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Ha frissítenie kell az Azure kerítés-ügynököt, és ha egyéni szerepkört használ, ügyeljen arra, hogy frissítse az egyéni szerepkört a **következő művelettel**:. További részletekért lásd: [Egyéni szerepkör létrehozása a kerítési ügynökhöz](#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]** telepítési állomásnév feloldása

   Használhat DNS-kiszolgálót, vagy módosíthatja a/etc/hosts az összes csomóponton. Ez a példa a/etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és a gazdagépet a következő parancsokra.  

   >[!IMPORTANT]
   > Ha a fürt konfigurációjában állomásnévket használ, elengedhetetlen, hogy megbízható legyen az állomásnév feloldása. A fürt kommunikációja sikertelen lesz, ha a nevek nem érhetők el, és ez a fürt feladatátvételi késéséhez vezethet.
   > A/etc/hosts használatának előnye, hogy a fürt független lesz a DNS-től, ami egyetlen meghibásodási pont lehet.  

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat a/etc/hosts. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** hacluster jelszavának módosítása ugyanarra a jelszóra

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** tűzfalszabályok hozzáadása a pacemakerhez

   Adja hozzá a következő tűzfalszabályok a fürt csomópontjai közötti összes fürt kommunikációhoz.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** alapszintű fürtszolgáltatások engedélyezése

   Futtassa a következő parancsokat a pacemaker szolgáltatás engedélyezéséhez, és indítsa el.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** pacemaker-fürt létrehozása

   Futtassa a következő parancsokat a csomópontok hitelesítéséhez és a fürt létrehozásához. Állítsa a tokent 30000-re, hogy a memóriát megőrizve karbantartást engedélyezzen. További információkért tekintse meg [ezt a cikket a Linux rendszerhez][virtual-machines-linux-maintenance].  
   
   Ha a **RHEL 7. x verzióban** hoz létre fürtöt, használja a következő parancsokat:  
   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all
   </code></pre>

   Ha a **RHEL 8. X verzióban** hoz létre fürtöt, használja a következő parancsokat:  
   <pre><code>sudo pcs host auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> totem token=30000
   sudo pcs cluster start --all
   </code></pre>

   A fürt állapotának ellenőrzéséhez hajtsa végre a következő parancsot:  
   <pre><code> # Run the following command until the status of both nodes is online
   sudo pcs status
   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A] A** várt szavazatok beállítása. 
   
   <pre><code># Check the quorum votes 
    pcs quorum status
    # If the quorum votes are not set to 2, execute the next command
    sudo pcs quorum expected-votes 2
   </code></pre>

   >[!TIP]
   > Ha több csomópontot tartalmazó fürtöt hoz létre, a kettőnél több csomóponttal rendelkező fürt esetén a szavazatok nem állíthatók be 2 értékre.    

1. **[1]** egyidejű kerítés-műveletek engedélyezése

   <pre><code>sudo pcs property set concurrent-fencing=true
   </code></pre>

## <a name="create-stonith-device"></a>STONITH-eszköz létrehozása

A STONITH-eszköz egy egyszerű szolgáltatásnév használatával engedélyezi a Microsoft Azure. Egy egyszerű szolgáltatásnév létrehozásához kövesse az alábbi lépéseket.

1. Nyissa meg a következőt: <https://portal.azure.com>
1. A Azure Active Directory panel megnyitása  
   Válassza a tulajdonságok lehetőséget, és jegyezze fel a könyvtár AZONOSÍTÓját. Ez a **bérlő azonosítója**.
1. Kattintson Alkalmazásregisztrációk
1. Kattintson az új regisztráció elemre.
1. Adjon meg egy nevet, válassza a "fiókok ebben a szervezeti címtárban" lehetőséget. 
2. Válassza az alkalmazás típusa "web" lehetőséget, adjon meg egy bejelentkezési URL-címet (például http: \/ /localhost), és kattintson a Hozzáadás gombra.  
   A bejelentkezési URL-cím nincs használatban, és bármely érvényes URL-cím lehet
1. Válassza a tanúsítványok és titkos kulcsok lehetőséget, majd kattintson az új ügyfél titka elemre.
1. Adja meg az új kulcs leírását, válassza a "soha nem jár le" lehetőséget, majd kattintson a Hozzáadás gombra.
1. Csomópont létrehozása az értékkel. Az egyszerű szolgáltatás **jelszavaként** van használatban
1. Válassza az Áttekintés lehetőséget. Jegyezze fel az alkalmazás AZONOSÍTÓját. A szolgáltatás felhasználóneveként (**Bejelentkezési azonosítóként** az alábbi lépésekben) használatos az egyszerű szolgáltatásnév számára

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** egyéni szerepkör létrehozása a kerítés ügynökéhez

Az egyszerű szolgáltatás alapértelmezés szerint nem rendelkezik az Azure-erőforrások eléréséhez szükséges engedélyekkel. Meg kell adnia a szolgáltatásnév számára a fürt összes virtuális gépe elindításához és leállításához (kikapcsolásához) szükséges engedélyeket. Ha még nem tette meg az egyéni szerepkört, akkor a [PowerShell](../../../role-based-access-control/role-assignments-powershell.md) vagy az [Azure CLI](../../../role-based-access-control/role-assignments-cli.md) használatával hozhatja létre

Használja az alábbi tartalmat a bemeneti fájlhoz. A tartalmat az előfizetéséhez kell igazítania, a c276fc76-9cd4-44c9-99a7-4fd71546436e és a e91d47c4-76f3-4271-a796-21b4ecfe3624 helyére az előfizetés azonosítóit kell cserélnie. Ha csak egy előfizetéssel rendelkezik, távolítsa el a második bejegyzést a AssignableScopes-ben.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** az egyéni szerepkör társítása az egyszerű szolgáltatáshoz

Rendelje hozzá az előző fejezetben az egyszerű szolgáltatásnév számára létrehozott "Linux kerítési ügynök szerepkör" egyéni szerepkört. Ne használja többé a tulajdonosi szerepkört!

1. Nyissa meg a következőt: https://portal.azure.com
1. Nyissa meg az összes erőforrás panelt
1. Válassza ki az első fürtcsomópont virtuális gépét.
1. Kattintson a hozzáférés-vezérlés (IAM) elemre.
1. Kattintson a szerepkör-hozzárendelés hozzáadása elemre.
1. Válassza ki a "Linux kerítési ügynök szerepkör" szerepkört
1. Adja meg a fent létrehozott alkalmazás nevét
1. Kattintson a Save (Mentés) gombra.

Ismételje meg a fenti lépéseket a második fürtcsomóponton.

### <a name="1-create-the-stonith-devices"></a>**[1]** a STONITH-eszközök létrehozása

A virtuális gépek engedélyeinek szerkesztése után a fürtben konfigurálhatja a STONITH-eszközöket.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

> [!NOTE]
> A (z) "pcmk_host_map" kapcsoló csak akkor szükséges a parancsban, ha a RHEL és az Azure-beli virtuális gépek nevei nem egyeznek. Adja meg a leképezést a **hostname: VM-Name** formátumban.
> Tekintse meg az parancs félkövér szakaszát. További információ: [milyen formátumot érdemes használni a csomópont-hozzárendelések megadásához az eszközök stonith pcmk_host_map](https://access.redhat.com/solutions/2619961)

A **7. X** RHEL használja a következő parancsot a kerítés eszköz konfigurálásához:    
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

A **8. X** RHEL használja a következő parancsot a kerítés eszköz konfigurálásához:  
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm username="<b>login ID</b>" password="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

> [!IMPORTANT]
> A figyelési és a kerítési műveletek deszerializáltak. Ennek eredményeképpen, ha már futó figyelési művelet és egyidejű kerítési esemény van, akkor a fürt feladatátvétele a már futó figyelési művelet miatt nem jár késéssel.  

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** STONITH-eszköz használatának engedélyezése

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

> [!TIP]
>Az Azure kerítés ügynöke a nyilvános végponti pontokhoz kapcsolódóan dokumentált kimenő kapcsolatot igényel, valamint a lehetséges megoldásokkal együtt a [standard szintű ILB használó virtuális gépek nyilvános végponti kapcsolatát](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

## <a name="next-steps"></a>Következő lépések

* [Azure Virtual Machines az SAP tervezéséhez és megvalósításához][planning-guide]
* [Azure Virtual Machines üzembe helyezés az SAP-ban][deployment-guide]
* [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz][dbms-guide]
* A magas rendelkezésre állás és a SAP HANA Azure-beli virtuális gépeken történő vész-helyreállítási tervének megismeréséhez lásd: [Az Azure-beli SAP HANA magas rendelkezésre állása Virtual Machines (VM)][sap-hana-ha]
