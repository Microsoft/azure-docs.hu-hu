---
title: Beágyazott virtualizálás engedélyezése az Azure-ban Virtual Machines
description: Beágyazott virtualizálás engedélyezése az Azure-ban Virtual Machines
author: cynthn
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: how-to
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.openlocfilehash: e85ac58c80e1fd695938bf09b6435dba1f4ee083
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100091346"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Beágyazott virtualizálás engedélyezése Azure-beli virtuális gépen

A beágyazott virtualizálás számos Azure-beli virtuálisgép-családban támogatott. Ez a képesség nagy rugalmasságot biztosít az olyan forgatókönyvek támogatásához, mint a fejlesztés, a tesztelés, a képzés és a demonstrációs környezetek.   

Ez a cikk a Hyper-V Azure-beli virtuális gépen való engedélyezésének, valamint az adott vendég virtuális géphez való internetkapcsolat konfigurálásának lépéseit ismerteti.

## <a name="create-a-nesting-capable-azure-vm"></a>Beágyazási képességgel rendelkező Azure-beli virtuális gép létrehozása

Hozzon létre egy új Windows Server 2016 Azure-beli virtuális gépet. A beágyazást támogató virtuálisgép-méretek teljes listájáért tekintse meg az [Azure számítási egységét ismertető cikket](../acu.md).

Ne feledje, hogy a virtuális gép méretét elég nagy méretűre kell kiválasztania ahhoz, hogy támogassa a vendég virtuális gépek igényeit. Ebben a példában egy D4_v3 méretű Azure-beli virtuális gépet használunk. 

[Itt](https://azure.microsoft.com/regions/services/)megtekintheti a Dv3 vagy Ev3 sorozatú virtuális gépek regionális elérhetőségét.

>[!NOTE]
>
>Az új virtuális gépek létrehozásával kapcsolatos részletes utasításokért lásd: [Windows rendszerű virtuális gépek létrehozása és kezelése a Azure PowerShell modullal](./tutorial-manage-vm.md)
    
## <a name="connect-to-your-azure-vm"></a>Kapcsolódás Azure-beli virtuális géphez

Hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

1. Kattintson a **Csatlakozás** gombra a virtuális gép tulajdonságai között. A rendszer létrehoz és letölt egy Remote Desktop Protocol-fájlt (.rdp-fájlt).

2. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, akkor kattintson a **Csatlakozás** gombra. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12).

3. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót, majd kattintson az **OK** gombra.

4. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>A Hyper-V funkció engedélyezése az Azure-beli virtuális gépen
Ezeket a beállításokat manuálisan is konfigurálhatja, vagy egy PowerShell-szkriptet kaptunk a konfiguráció automatizálásához.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>1. lehetőség: a beágyazott virtualizálás konfigurálása PowerShell-parancsfájl használatával
A [githubon](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested)elérhető egy PowerShell-parancsfájl, amely lehetővé teszi a beágyazott virtualizációt egy Windows Server 2016-gazdagépen. A parancsfájl ellenőrzi az előfeltételeket, majd konfigurálja a beágyazott virtualizálás szolgáltatást az Azure-beli virtuális gépen. A konfiguráció befejezéséhez újra kell indítani az Azure-beli virtuális gépet. Ez a szkript más környezetekben is működhet, de nem garantált. Tekintse meg az Azure blogbejegyzését az Azure-on futó beágyazott virtualizálás élő videó bemutatóján. https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>2. lehetőség: a beágyazott virtualizálás manuális konfigurálása

1. Az Azure-beli virtuális gépen nyissa meg a PowerShellt rendszergazdaként. 

2. Engedélyezze a Hyper-V szolgáltatást és a felügyeleti eszközöket.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Ez a parancs újraindítja az Azure-beli virtuális gépet. Az újraindítási folyamat során a rendszer elveszíti RDP-kapcsolatát.
    
3. Az Azure-beli virtuális gép újraindítása után RDP használatával csatlakozzon újra a virtuális géphez.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Internetkapcsolat beállítása a vendég virtuális géphez
Hozzon létre egy új virtuális hálózati adaptert a vendég virtuális géphez, és konfigurálja a NAT-átjárót az internetkapcsolat engedélyezéséhez.

### <a name="create-a-nat-virtual-network-switch"></a>NAT virtuális hálózati kapcsoló létrehozása

1. Az Azure-beli virtuális gépen nyissa meg a PowerShellt rendszergazdaként.
   
2. Hozzon létre egy belső kapcsolót.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Tekintse meg a kapcsoló tulajdonságait, és jegyezze fel az új adapter Előtérillesztő ifindex.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Jegyezze fel az imént létrehozott virtuális kapcsoló "Előtérillesztő ifindex".
    
4. Hozzon létre egy IP-címet a NAT-átjáró számára.
    
Az átjáró konfigurálásához néhány információra van szüksége a hálózatról:    
  * IP-cím – a NAT-átjáró IP-címe határozza meg a virtuális hálózati alhálózat alapértelmezett átjárójának címeként használandó IPv4-vagy IPv6-címet. Az általános űrlap az a. b. c. 1 (például "192.168.0.1"). A végső pozíciónak nem kell 1, általában az előtag hosszán alapul. Általában az RFC 1918 magánhálózati hálózati címtartomány használatát kell használnia. 
  * PrefixLength – az alhálózat előtagjának hossza határozza meg a helyi alhálózat méretét (alhálózati maszk). Az alhálózat előtagjának hossza 0 és 32 közötti egész szám lehet. 0 a teljes internetet leképezi, 32 csak egy leképezett IP-címet engedélyezhet. Az általános értékek 24 és 12 között változnak attól függően, hogy hány IP-címet kell csatlakoztatni a NAT-hoz. Az általános PrefixLength 24 – ez a 255.255.255.0 alhálózati maszkja.
  * Az kapcsolatindex- **előtérillesztő ifindex** az előző lépésben létrehozott virtuális kapcsoló illesztőfelület-indexe. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>NAT-hálózat létrehozása

Az átjáró konfigurálásához meg kell adnia a hálózattal és a NAT-átjáróval kapcsolatos információkat:
  * Név – ez a NAT-hálózat neve. 
  * InternalIPInterfaceAddressPrefix – a NAT-alhálózat előtagja a NAT-átjáróhoz tartozó IP-előtagot, valamint a NAT-alhálózat előtagjának hosszát ismerteti. Az általános űrlap az a. b. c. 0/NAT alhálózat előtagjának hossza. 

A PowerShellben hozzon létre egy új NAT-hálózatot.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>A vendég virtuális gép létrehozása

>[!IMPORTANT] 
>
>Az Azure Guest Agent nem támogatott beágyazott virtuális gépeken, és problémákat okozhat a gazdagépen és a beágyazott virtuális gépeken is. Ne telepítse az Azure-ügynököt a beágyazott virtuális gépekre, és ne használjon rendszerképet azon beágyazott virtuális gépek létrehozásához, amelyeken már telepítve van az Azure Guest Agent.

1. Nyissa meg a Hyper-V kezelőjét, és hozzon létre egy új virtuális gépet. Konfigurálja úgy a virtuális gépet, hogy a létrehozott új belső hálózatot használja.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Operációs rendszer telepítése a vendég virtuális gépre.
    
    >[!NOTE] 
    >
    >A virtuális gépre telepítendő operációs rendszer telepítéséhez telepíteni kell a telepítési adathordozót. Ebben az esetben a Windows 10 Enterprise rendszert használjuk.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>IP-cím kiosztása a vendég virtuális géphez

Ha IP-címet szeretne hozzárendelni a vendég virtuális géphez, manuálisan állítson be egy statikus IP-címet a vendég virtuális gépen, vagy konfigurálja a DHCP-t az Azure-beli virtuális gépen, hogy az IP-cím dinamikusan legyen hozzárendelve.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>1. lehetőség: konfigurálja a DHCP-t, hogy dinamikusan rendeljen hozzá IP-címet a vendég virtuális géphez
Az alábbi lépések végrehajtásával konfigurálja a DHCP-t a gazdagép virtuális gépén a dinamikus címek kiosztásához.

#### <a name="install-dhcp-server-on-the-azure-vm"></a>DHCP-kiszolgáló telepítése az Azure-beli virtuális gépen

1. Nyissa meg a Kiszolgálókezelőt. Az irányítópulton kattintson a **szerepkörök és szolgáltatások hozzáadása** elemre. A Szerepkörök és szolgáltatások hozzáadása varázsló jelenik meg.
  
2. A varázslóban kattintson a **tovább** gombra a kiszolgálói szerepkörök lapra.
  
3. Jelölje be a **DHCP-kiszolgáló** jelölőnégyzetet, kattintson a **szolgáltatások hozzáadása** elemre, majd kattintson a **tovább** gombra a varázsló befejezéséhez.
  
4. Kattintson az **Install** (Telepítés) gombra.

#### <a name="configure-a-new-dhcp-scope"></a>Új DHCP-hatókör konfigurálása

1. Nyissa meg a DHCP-kezelőt.
  
2. A navigációs ablaktáblán bontsa ki a kiszolgáló nevét, kattintson a jobb gombbal az **IPv4** elemre, majd kattintson az **Új hatókör** elemre. Megjelenik az új hatókör varázsló, és kattintson a **tovább** gombra.
  
3. Adja meg a hatókör nevét és leírását, majd kattintson a **tovább** gombra.
  
4. Adja meg a DHCP-kiszolgáló IP-tartományát (például 192.168.0.100 – 192.168.0.200).
  
5. Kattintson a **tovább** gombra az alapértelmezett átjáró lapon. Adja meg a korábban létrehozott IP-címet (például 192.168.0.1) az alapértelmezett átjáróként, majd kattintson a **Hozzáadás** gombra.
  
6. Kattintson a **tovább** gombra, amíg a varázsló befejeződik, elhagyja az összes alapértelmezett értéket, majd kattintson a **Befejezés** gombra.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>2. lehetőség: statikus IP-cím manuális beállítása a vendég virtuális gépen
Ha nem konfigurálta a DHCP-t úgy, hogy dinamikusan rendeljen hozzá IP-címet a vendég virtuális géphez, kövesse az alábbi lépéseket egy statikus IP-cím beállításához.

1. Az Azure-beli virtuális gépen nyissa meg a PowerShellt rendszergazdaként.

2. Kattintson a jobb gombbal a vendég virtuális gépre, majd kattintson a kapcsolat lehetőségre.

3. Jelentkezzen be a vendég virtuális gépre.

4. A vendég virtuális gépen nyissa meg a hálózati és megosztási központot.

5. Konfigurálja a hálózati adaptert az előző szakaszban létrehozott NAT-hálózat tartományán belüli címekre.

Ebben a példában egy 192.168.0.0/24 tartományban található címeket fog használni.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Kapcsolat tesztelése a vendég virtuális gépen

A vendég virtuális gépen nyissa meg a böngészőt, és navigáljon egy weblapra.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

A vendég virtuális gépek és az Azure-beli virtuális gépek közötti transzparens kapcsolat engedélyezésével kapcsolatos utasításokért tekintse meg [ezt a dokumentumot](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization).
