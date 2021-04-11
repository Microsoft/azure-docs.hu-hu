---
title: Kapcsolódás Azure-beli virtuális gépekhez helyszíni feladatátvétel a Azure Site Recovery használatával
description: Útmutató Azure-beli virtuális gépekhez való kapcsolódáshoz a helyszíni rendszerről az Azure-ba történő feladatátvétel után Azure Site Recovery használatával
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: harshacs
ms.openlocfilehash: 4b88ed44632aa255837d8fb499782e11c716d443
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048187"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Kapcsolódás Azure-beli virtuális gépekhez a helyszíni feladatátvétel után 


Ez a cikk azt ismerteti, hogyan állíthatja be a kapcsolatot, hogy sikeresen tud-e csatlakozni az Azure-beli virtuális gépekhez a feladatátvételt követően.

Ha a helyszíni virtuális gépek (VM-EK) és a fizikai kiszolgálók Azure-ba történő helyreállítását állítja be, [Azure site Recovery](site-recovery-overview.md) elindítja a számítógépek replikálását az Azure-ba. Ezután, ha leállások történnek, a helyszíni helyről feladatátvételt végezhet az Azure-ba. Feladatátvétel esetén Site Recovery Azure-beli virtuális gépeket hoz létre a replikált helyszíni adatszolgáltatások használatával. A vész-helyreállítási tervezés részeként meg kell állapítani, hogyan csatlakozhat az Azure virtuális gépeken futó alkalmazásokhoz a feladatátvételt követően.

Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A helyszíni gépek előkészítése a feladatátvétel előtt.
> * Azure-beli virtuális gépek előkészítése feladatátvétel után. 
> * A feladatátvételt követően megőrzi az Azure-beli virtuális gépek IP-címeit.
> * A feladatátvételt követően rendeljen új IP-címeket az Azure-beli virtuális gépekhez.

## <a name="prepare-on-premises-machines"></a>Helyszíni gépek előkészítése

Az Azure-beli virtuális gépekhez való csatlakozás biztosításához a feladatátvétel előtt készítse elő a helyszíni gépeket.

### <a name="prepare-windows-machines"></a>Windows rendszerű gépek előkészítése

Helyszíni Windows rendszerű gépeken tegye a következőket:

1. Konfigurálja a Windows beállításait. Ilyenek például a statikus állandó útvonalak vagy a WinHTTP-proxyk eltávolítása, valamint a lemez TÁROLÓHÁLÓZATI házirend **OnlineAll** való beállítása. [Kövesse](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) ezeket az utasításokat.

2. Győződjön meg arról, hogy [ezek a szolgáltatások](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) futnak.

3. Engedélyezze a távoli asztal (RDP) szolgáltatást, hogy engedélyezze a távoli kapcsolatokat a helyszíni géppel. [Ismerje meg, hogyan](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) engedélyezheti az RDP-t a PowerShell használatával.

4. Ha egy Azure-beli virtuális gépet feladatátvétel után szeretne elérni az interneten keresztül, a helyszíni gépen lévő Windows tűzfalon engedélyezze a TCP és az UDP protokollt a nyilvános profilban, és állítsa be az RDP-t engedélyezett alkalmazásként az összes profilhoz.

5. Ha egy Azure-beli virtuális gépet egy helyek közötti VPN-kapcsolaton keresztül szeretne elérni a feladatátvétel után, a helyszíni gépen lévő Windows tűzfalon engedélyezze az RDP-t a tartomány és a privát profilok számára. [Megtudhatja](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) , hogyan engedélyezheti az RDP-forgalmat.
6. Feladatátvétel indításakor ellenőrizze, hogy a helyszíni virtuális gépen nincsenek-e függőben lévő Windows-frissítések. Ha vannak, előfordulhat, hogy a frissítések a feladatátvétel után megkezdik a telepítést az Azure virtuális gépen, és a frissítések befejezéséig nem tud majd bejelentkezni a virtuális gépre.

### <a name="prepare-linux-machines"></a>Linuxos gépek előkészítése

A helyszíni Linux rendszerű gépeken tegye a következőket:

1. Győződjön meg arról, hogy a Secure Shell szolgáltatás úgy van beállítva, hogy automatikusan induljon el a rendszerindításkor.
2. Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.


## <a name="configure-azure-vms-after-failover"></a>Azure-beli virtuális gépek konfigurálása feladatátvétel után

A feladatátvételt követően tegye a következőket a létrehozott Azure-beli virtuális gépeken.

1. Ha az interneten keresztül szeretne csatlakozni a virtuális géphez, rendeljen egy nyilvános IP-címet a virtuális géphez. Nem használhatja ugyanazt a nyilvános IP-címet a helyszíni géphez használt Azure-beli virtuális géphez. [További információ](../virtual-network/virtual-network-public-ip-address.md)
2. Győződjön meg arról, hogy a virtuális gép hálózati biztonsági csoport (NSG) szabályai engedélyezik a bejövő kapcsolatokat az RDP-vagy SSH-porton.
3. A virtuális gép megtekintéséhez tekintse meg a [rendszerindítási diagnosztikát](/troubleshoot/azure/virtual-machines/boot-diagnostics#enable-boot-diagnostics-on-existing-virtual-machine) .


> [!NOTE]
> Az Azure Bastion szolgáltatás privát RDP-és SSH-hozzáférést biztosít az Azure-beli virtuális gépekhez. [További](../bastion/bastion-overview.md) információ erről a szolgáltatásról.

## <a name="set-a-public-ip-address"></a>Nyilvános IP-cím beállítása

Ha egy nyilvános IP-címet manuálisan rendel egy Azure-beli virtuális géphez, a feladatátvételt hozzárendelheti egy Site Recovery [helyreállítási tervben](site-recovery-create-recovery-plans.md)lévő parancsfájl vagy Azure Automation runbook használatával, vagy beállíthatja a DNS-szintű útválasztást az Azure Traffic Manager használatával. [További](concepts-public-ip-address-with-site-recovery.md) információ a nyilvános címek beállításáról.


## <a name="assign-an-internal-address"></a>Belső címek kiosztása

Ha egy Azure-beli virtuális gép belső IP-címét feladatátvétel után szeretné beállítani, néhány lehetőség közül választhat:

- **Azonos IP-cím megőrzése**: az Azure-beli virtuális gépen ugyanazt az IP-címet használhatja, mint a helyszíni gép számára.
- **Eltérő IP-cím használata**: használhat másik IP-címet az Azure-beli virtuális géphez.


## <a name="retain-ip-addresses"></a>IP-címek megőrzése

Site Recovery lehetővé teszi, hogy ugyanazokat az IP-címeket őrizze meg, amikor feladatátvételt végez az Azure-ba. Ha megtartja ugyanazt az IP-címet, a feladatátvételt követően elkerüli a lehetséges hálózati problémákat, azonban némi bonyolultságot mutat be.

- Ha a célként megadott Azure-beli virtuális gép ugyanazt az IP-címet/alhálózatot használja, mint a helyszíni hely, akkor nem tud kapcsolatot létesíteni egymás között helyek közötti VPN-kapcsolattal vagy ExpressRoute, mert a cím átfedésben van. Az alhálózatoknak egyedinek kell lenniük.
- A feladatátvétel után a helyszíni és az Azure közötti kapcsolatban kell lennie, hogy az alkalmazások elérhetők legyenek az Azure-beli virtuális gépeken. Az Azure nem támogatja a kiterjesztett VLAN-okat, így ha meg szeretné őrizni az IP-címeket, a helyszíni gépen kívül a teljes alhálózat feladatátvétele érdekében az IP-címet át kell vennie az Azure-ba.
- Az alhálózat feladatátvétele biztosítja, hogy egy adott alhálózat ne legyen egyszerre a helyszínen és az Azure-ban.

Az IP-címek megtartásához a következő lépések szükségesek:

- A replikált elem számítási & hálózati tulajdonságainál állítsa be a hálózati és IP-címzést a cél Azure-beli virtuális gép számára a helyszíni beállítás tükrözéséhez.
- Az alhálózatokat a vész-helyreállítási folyamat részeként kell kezelni. Szüksége van egy Azure-VNet, amely megfelel a helyszíni hálózatnak, és a feladatátvételi hálózati útvonalakat úgy kell módosítani, hogy azt tükrözzék, hogy az alhálózat át lett helyezve az Azure-ba, és új IP-címek találhatók.  

### <a name="failover-example"></a>Példa feladatátvételre

Lássunk erre egy példát.

- A fiktív vállalati Woodgrove Bank a helyileg üzemeltetett üzleti alkalmazásokat üzemelteti a mobil alkalmazásaikat az Azure-ban.
- A helyek közötti VPN-kapcsolaton keresztül csatlakoznak a helyszínről az Azure-hoz. 
- A Woodgrove Site Recovery használatával replikálja a helyszíni gépeket az Azure-ba.
- A helyszíni alkalmazások nem kódolt IP-címeket használnak, ezért az Azure-ban ugyanazt az IP-címet szeretnék megőrizni.
- A helyszínen az alkalmazásokat futtató gépek három alhálózaton futnak:
    - 192.168.1.0/24
    - 192.168.2.0/24
    - 192.168.3.0/24
- Az Azure-ban futó alkalmazások két alhálózaton találhatók az Azure VNet **Azure-hálózatban** :
    - 172.16.1.0/24
    - 172.16.2.0/24

A címek megőrzése érdekében a következő műveleteket végzi el.

1. Amikor engedélyezik a replikálást, megadják, hogy a gépek replikálódnak az **Azure-hálózatra**.
2. **Helyreállítási hálózatot** hoznak létre az Azure-ban. Ez a VNet a 192.168.1.0/24 alhálózatot tükrözi a helyszíni hálózatában.
3. A Woodgrove létrehoz egy [VNet-VNet kapcsolatot](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) a két hálózat között. 

    > [!NOTE]
    > Az alkalmazás követelményeitől függően a VNet-VNet kapcsolatok a feladatátvétel előtt állíthatók be, manuális lépés/parancsfájl-lépés/Azure Automation-runbook egy Site Recovery [helyreállítási tervben](site-recovery-create-recovery-plans.md)vagy a feladatátvétel befejeződése után.

4. A feladatátvétel előtt a Site Recovery számítógép tulajdonságainál a célként megadott IP-címet a rendszer a helyszíni gép címére állítja be a következő eljárásban leírtak szerint.
5. A feladatátvételt követően az Azure-beli virtuális gépek ugyanazzal az IP-címmel jönnek létre. A Woodgrove az **Azure-hálózatról** a **helyreállítási hálózati** VNet csatlakoztatja a VNet-társítás használatával (az átviteli kapcsolat engedélyezve van).
6. A helyszíni Woodgrove hálózati módosításokat kell végeznie, beleértve az útvonalak módosítását is, hogy a 192.168.1.0/24 átkerüljön az Azure-ba.  

**Infrastruktúra a feladatátvétel előtt**

![Alhálózat feladatátvétele előtt](./media/site-recovery-network-design/network-design7.png)


**Infrastruktúra a feladatátvétel után**

![Alhálózat feladatátvétele után](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>A célként megadott hálózati beállítások megadása

A feladatátvétel előtt a célként megadott Azure-beli virtuális gép hálózati beállításait és IP-címét kell megadnia.

1.  A Recovery Services-tároló – > **replikált elemek** területen válassza a helyszíni gépet.
2. A gép **számítási és hálózati** lapján kattintson a **Szerkesztés** elemre a cél Azure-beli virtuális gép hálózati és adapter-beállításainak konfigurálásához.
3. A **hálózati tulajdonságok** területen válassza ki azt a célként megadott hálózatot, amelyben az Azure-beli virtuális gép a feladatátvételt követően jön létre.
4. A **hálózati** adapterek területen konfigurálja a hálózati adaptereket a célként megadott hálózatban. Alapértelmezés szerint Site Recovery az összes észlelt hálózati adaptert megjeleníti a helyszíni gépen.
    - A **célként megadott hálózati adapter típusa** beállításnál az egyes hálózati adapterek **elsődleges**, **másodlagos**, vagy **nem hozhatók létre** , ha nincs szüksége a célként megadott hálózati adapterre. Az egyik hálózati adaptert elsődlegesként kell beállítani a feladatátvételhez. Vegye figyelembe, hogy a célként megadott hálózat módosítása hatással van az Azure-beli virtuális gép összes hálózati adapterére.
    - Kattintson a hálózati adapter nevére annak az alhálózatnak a megadásához, amelyben az Azure-beli virtuális gép telepítve lesz.
    - Írja felül a **dinamikus** felülírást azzal a magánhálózati IP-címmel, amelyet hozzá szeretne rendelni a célként megadott Azure-beli virtuális géphez. Ha nincs megadva IP-cím, Site Recovery a következő elérhető IP-címet fogja hozzárendelni az alhálózatban lévő hálózati ADAPTERhez a feladatátvételkor.
    - [További](site-recovery-manage-network-interfaces-on-premises-to-azure.md) információ a hálózati adapterek helyszíni feladatátvétel az Azure-ba történő kezeléséről.


## <a name="get-new-ip-addresses"></a>Új IP-címek beolvasása

Ebben az esetben az Azure-beli virtuális gép új IP-címet kap a feladatátvétel után. A feladatátvételt követően létrehozott virtuális gép új IP-címének beállításához a következő lépéseket kell megadnia:

1. Nyissa meg a **replikált elemeket**.
2. Válassza ki a kívánt Azure-beli virtuális gépet.
3. Válassza a **számítás és hálózat** lehetőséget, majd válassza a **Szerkesztés** lehetőséget.

     ![A feladatátvételi hálózati konfigurációk testreszabása](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. A feladatátvételi hálózati beállítások frissítéséhez válassza a configure ( **Szerkesztés** ) lehetőséget a konfigurálni kívánt hálózati adapterhez. A megnyíló következő lapon adja meg a megfelelő előre létrehozott IP-címet a feladatátvételi teszt és a feladatátvételi hely között.

    ![A NIC-konfiguráció szerkesztése](media/azure-to-azure-customize-networking/nic-drilldown.png)

5. Válassza az **OK** lehetőséget.

A Site Recovery mostantól tiszteletben tartja ezeket a beállításokat, és gondoskodik arról, hogy a feladatátvételen a virtuális gép a megfelelő IP-címen keresztül kapcsolódjon a kiválasztott erőforráshoz, ha az elérhető a cél IP-tartományban. Ebben az esetben nincs szükség a teljes alhálózat feladatátvételére. DNS-frissítésre lesz szükség a sikertelenül átadott gép rekordjainak frissítéséhez, hogy a virtuális gép új IP-címére mutasson.

## <a name="next-steps"></a>Következő lépések
[Tudnivalók](site-recovery-active-directory.md) a helyszíni Active Directory és a DNS Azure-ba történő replikálásáról.