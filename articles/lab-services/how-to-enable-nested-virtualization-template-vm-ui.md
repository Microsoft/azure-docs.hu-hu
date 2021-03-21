---
title: Beágyazott virtualizáció engedélyezése sablonbeli virtuális gépen Azure Lab Services (UI) | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre több virtuális géppel rendelkező sablonos virtuális gépet a rendszeren belül.  Más szóval engedélyezze a beágyazott virtualizációt a sablonban lévő virtuális gépen Azure Lab Servicesban.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: f8135e11fb7b7ddb588ab3a8ed01227712072fd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647919"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Beágyazott virtualizálás engedélyezése sablonbeli virtuális gépen Azure Lab Services manuálisan

A beágyazott virtualizálás lehetővé teszi, hogy több virtuális gépre kiterjedő környezetet hozzon létre a labor sablonjának virtuális gépén. A sablon közzétételével minden felhasználó számára elérhetővé válik a laborban egy virtuális gép, amelyben több virtuális gép van beállítva.  További információ a beágyazott virtualizálás és a Azure Lab Services használatáról: a [beágyazott virtualizálás engedélyezése sablonbeli virtuális gépen Azure Lab Servicesban](how-to-enable-nested-virtualization-template-vm.md).

Ez a cikk bemutatja, hogyan állíthatja be a beágyazott virtualizációt egy Azure Lab Services található sablonos gépen a Windows-szerepkörök és-eszközök közvetlen használatával.  Néhány dolog szükséges ahhoz, hogy egy osztály lehetővé váljon a beágyazott virtualizálás használata.  Az alábbi lépésekből megtudhatja, hogyan állíthat be manuálisan egy tesztkörnyezet-virtuálisgép-sablont a Hyper-V használatával.  A lépések a Windows Server 2016 vagy a Windows Server 2019 rendszerhez készültek.  

>[!IMPORTANT]
>A tesztkörnyezet létrehozásakor válassza a **nagyméretű (beágyazott virtualizálás)** vagy **közepes (beágyazott virtualizálás** ) lehetőséget a virtuális gép méretének kiválasztásához.  A beágyazott virtualizálás más módon nem működik.  

## <a name="enable-hyper-v-role"></a>Hyper-V szerepkör engedélyezése

A következő lépések azokat a műveleteket ismertetik, amelyek a Hyper-V Windows Serveren való engedélyezéséhez szükségesek a Kiszolgálókezelő használatával.  Ha a telepítés sikeres volt, a Hyper-V kezelője elérhetővé válik az ügyfél virtuális gépei hozzáadásához, módosításához és törléséhez.

1. A **Kiszolgálókezelő** irányítópult lapján kattintson a **szerepkörök és szolgáltatások hozzáadása** elemre.
2. Az **Alapismeretek** lapon kattintson a **Tovább** gombra.
3. A **telepítés típusának kiválasztása** lapon tartsa meg a szerepköralapú vagy a szolgáltatáson alapuló telepítés alapértelmezett beállítását, majd kattintson a **tovább** gombra.
4. A **célkiszolgáló kijelölése** lapon válassza a kiszolgáló kiválasztása a kiszolgáló készletből lehetőséget.   Az aktuális kiszolgáló már ki lesz választva.  Kattintson a Tovább gombra.
5. A **kiszolgálói szerepkörök kiválasztása** lapon válassza a **Hyper-V** lehetőséget.  
6. Ekkor megjelenik a **szerepkörök és szolgáltatások hozzáadása varázsló** előugró ablaka.  Válassza a **felügyeleti eszközök belefoglalása (ha alkalmazható)** lehetőséget.  Kattintson a **szolgáltatások hozzáadása** gombra.
7. A **Kiszolgálói szerepkörök kiválasztása** lapon kattintson a **Tovább** gombra.
8. A **szolgáltatások kiválasztása lapon** kattintson a **tovább** gombra.
9. A **Hyper-V** lapon kattintson a **Tovább** gombra.
10. A **virtuális kapcsolók létrehozása** lapon fogadja el az alapértelmezett beállításokat, majd kattintson a **tovább** gombra.
11. A **virtuális gép áttelepítése** lapon fogadja el az alapértelmezett beállításokat, majd kattintson a **tovább** gombra.
12. Az **alapértelmezett áruházak** lapon fogadja el az alapértelmezett beállításokat, majd kattintson a **tovább** gombra.
13. A **telepítendő összetevők megerősítése** lapon válassza a **célkiszolgáló automatikus újraindítása, ha szükséges** lehetőséget.
14. Amikor megjelenik a **szerepkörök és szolgáltatások hozzáadása varázsló** előugró ablaka, kattintson az **Igen** gombra.
15. Kattintson az **Install** (Telepítés) gombra.
16. Várjon, amíg a **telepítési folyamat** lap jelzi, hogy a Hyper-V szerepkör befejeződött.  Előfordulhat, hogy a gép a telepítés közepén újraindítható.
17. Kattintson a **Bezárás** gombra.

## <a name="enable-dhcp-role"></a>DHCP-szerepkör engedélyezése

Minden létrehozott Hyper-V-ügyfél virtuális gépnek szüksége van egy IP-címére a NAT-hálózaton.  Később létrehozjuk a NAT-hálózatot.  Az IP-címek hozzárendelésének egyik módja a gazdagép beállítása, ebben az esetben a labor virtuálisgép-sablon DHCP-kiszolgálóként.  Alább láthatók a DHCP-szerepkör engedélyezéséhez szükséges lépések.

1. A **Kiszolgálókezelő** **irányítópult** lapján kattintson a **szerepkörök és szolgáltatások hozzáadása** elemre.
2. Az **Alapismeretek** lapon kattintson a **Tovább** gombra.
3. A **telepítés típusának kiválasztása** lapon válassza a **szerepköralapú vagy a szolgáltatáson alapuló telepítés** lehetőséget, majd kattintson a **tovább** gombra.
4. A **célkiszolgáló kijelölése** lapon válassza ki az aktuális kiszolgálót a kiszolgáló készletből, majd kattintson a **tovább** gombra.
5. A **kiszolgálói szerepkörök kiválasztása** lapon válassza a **DHCP-kiszolgáló** lehetőséget.  
6. Ekkor megjelenik a **szerepkörök és szolgáltatások hozzáadása varázsló** előugró ablaka.  Válassza a **felügyeleti eszközök belefoglalása (ha alkalmazható)** lehetőséget.  Kattintson a **szolgáltatások hozzáadása** elemre.

    >[!NOTE]
    >Előfordulhat, hogy egy érvényesítési hiba jelenik meg, amely szerint nem található statikus IP-cím.  Ez a figyelmeztetés a forgatókönyv esetében figyelmen kívül hagyható.

7. A **Kiszolgálói szerepkörök kiválasztása** lapon kattintson a **Tovább** gombra.
8. A **szolgáltatások kiválasztása** lapon kattintson a **tovább** gombra.
9. A **DHCP-kiszolgáló** lapon kattintson a **tovább** gombra.
10. A **Telepítendő összetevők megerősítése** lapon kattintson a **Telepítés** parancsra.
11. Várjon, amíg a **telepítési folyamat lap** jelzi, hogy a DHCP-szerepkör befejeződött.
12. Kattintson a Bezárás gombra.

## <a name="enable-routing-and-remote-access-role"></a>Útválasztás és távelérés szerepkör engedélyezése

1. A **Kiszolgálókezelő** **irányítópult** lapján kattintson a **szerepkörök és szolgáltatások hozzáadása** elemre.
2. Az **Alapismeretek** lapon kattintson a **Tovább** gombra.
3. A **telepítés típusának kiválasztása** lapon válassza a **szerepköralapú vagy a szolgáltatáson alapuló telepítés** lehetőséget, majd kattintson a **tovább** gombra.
4. A **célkiszolgáló kijelölése** lapon válassza ki az aktuális kiszolgálót a kiszolgáló készletből, majd kattintson a **tovább** gombra.
5. A **kiszolgálói szerepkörök kiválasztása** lapon válassza a **távelérés** lehetőséget. Kattintson az **OK** gombra.
6. A **szolgáltatások kiválasztása** lapon kattintson a **tovább** gombra.
7. A **távelérés** lapon kattintson a **tovább** gombra.
8. A **szerepkör-szolgáltatások** lapon válassza az **Útválasztás** lehetőséget.
9. Ekkor megjelenik a **szerepkörök és szolgáltatások hozzáadása varázsló** előugró ablaka.  Válassza a **felügyeleti eszközök belefoglalása (ha alkalmazható)** lehetőséget.  Kattintson a **szolgáltatások hozzáadása** elemre.
10. Kattintson a **Tovább** gombra.
11. A **Webkiszolgálói szerepkör (IIS)** lapon kattintson a **Tovább** gombra.
12. A **Szerepkör-szolgáltatások kiválasztása** lapon kattintson a **Tovább** gombra.
13. A **Telepítendő összetevők megerősítése** lapon kattintson a **Telepítés** parancsra.
14. Várjon, amíg a **telepítési folyamat** lap jelzi, hogy a távelérési szerepkör befejeződött.  
15. Kattintson a **Bezárás** gombra.

## <a name="create-virtual-nat-network"></a>Virtuális NAT-hálózat létrehozása

Most, hogy az összes szükséges szerepkör telepítve van, ideje létrehozni a NAT-hálózatot.  A létrehozási folyamat magában foglalja a kapcsoló és a NAT-hálózat létrehozását is.  Egy NAT-(hálózati címfordítási) hálózat nyilvános IP-címet rendel a magánhálózaton lévő virtuális gépek egy csoportjához, hogy lehetővé tegye a kapcsolódást az internethez.  Ebben az esetben a privát virtuális gépek csoportja lesz a beágyazott virtuális gép.  A NAT-hálózat lehetővé teszi, hogy a beágyazott virtuális gépek kommunikálhassanak egymással. A kapcsoló egy hálózati eszköz, amely kezeli a hálózati forgalom fogadását és útválasztását.

### <a name="create-a-new-virtual-switch"></a>Új virtuális kapcsoló létrehozása

1. Nyissa meg a **Hyper-V kezelőjét** a Windows felügyeleti eszközökről.
2. Válassza ki az aktuális kiszolgálót a bal oldali navigációs menüben.
3. Kattintson a **virtuális kapcsoló kezelőjére...** a **Hyper-V kezelőjének** jobb oldalán található **műveletek** menüből.
4. A **virtuális kapcsoló kezelőjének** előugró ablakában válassza a **belső** lehetőséget a létrehozandó kapcsoló típusához.  Kattintson a **virtuális kapcsoló létrehozása** elemre.
5. Az újonnan létrehozott virtuális kapcsolóhoz állítsa a nevet valami emlékezetes névre.  Ebben a példában a "LabServicesSwitch"-t fogjuk használni.  Kattintson az **OK** gombra.
6. A rendszer új hálózati adaptert hoz létre.  A név a következőhöz hasonló lesz: "vEthernet (LabServicesSwitch)".  A **Vezérlőpult** megnyitásának ellenőrzéséhez kattintson a **hálózat és Internet** lehetőségre, majd a **hálózati állapot és feladatok megtekintése** elemre.  A bal oldalon kattintson az **adapter beállításainak módosítása** elemre.

### <a name="create-a-nat-network"></a>NAT-hálózat létrehozása

1. Nyissa meg az **Útválasztás és távelérés** eszközt a Windows felügyeleti eszközökről.
2. Válassza ki a helyi kiszolgálót a bal oldali navigációs oldalon.
3. Válassza   ->  **a művelet konfigurálás és az Útválasztás és távelérés engedélyezése** lehetőséget.
4. Amikor megjelenik az **Útválasztás és távelérési kiszolgáló telepítése varázsló** , kattintson a **tovább** gombra.
5. A **konfiguráció** lapon válassza a **hálózati címfordítás (NAT)** konfigurációja lehetőséget.  Kattintson a **Tovább** gombra.

    >[!WARNING]
    >Ne válassza a virtuális magánhálózati (VPN) hozzáférés és a NAT lehetőséget.

6. A **NAT-internetkapcsolat** lapon válassza az "Ethernet" lehetőséget.  Ne válassza a Hyper-V kezelőjében létrehozott "vEthernet (LabServicesSwitch)"-kapcsolatokat. Kattintson a **Tovább** gombra.
7. A varázsló utolsó oldalán kattintson a **Befejezés** gombra.
8. Amikor megjelenik a **szolgáltatás elindítása** párbeszédpanel, kattintson a **szolgáltatás indítása** lehetőségre.
9. Várjon, amíg a szolgáltatás elindult.

## <a name="update-network-adapter-settings"></a>Hálózati adapter beállításainak frissítése

A hálózati adapter társítva lesz a korábban létrehozott NAT-hálózat alapértelmezett átjárójának IP-címéhez használt IP-címhez.  Ebben a példában a 192.168.0.1 IP-címét létrehozjuk a 255.255.255.0 alhálózati maszkkal.  A korábban létrehozott virtuális kapcsolót fogjuk használni.

1. Nyissa meg a **Vezérlőpultot**, kattintson a **hálózat és Internet** lehetőségre, majd a **hálózati állapot és feladatok megtekintése** elemre.
2. A bal oldalon kattintson az **adapter beállításainak módosítása** elemre.  
3. A **hálózati kapcsolatok** ablakban kattintson duplán a "VEthernet (LabServicesSwitch)" elemre a **vEthernet (LabServicesSwitch) állapot** részletei párbeszédpanel megjelenítéséhez.
4. Kattintson a **Tulajdonságok** gombra.
5. Válassza ki **Internet Protocol 4-es verziójú (TCP/IPv4)** elemet, majd kattintson a **Tulajdonságok** gombra.
6. A **Internet Protocol 4-es verzió (TCP/IPv4) tulajdonságai** párbeszédpanelen válassza **a következő IP-cím használata** lehetőséget.  Az IP-cím mezőbe írja be a következőt: 192.168.0.1. Az alhálózati maszkhoz írja be a következőt: 255.255.255.0.  Hagyja üresen az alapértelmezett átjárót.  A DNS-kiszolgálókat is hagyja üresen.

    >[!NOTE]
    > A NAT-hálózat CIDR-jelölése 192.168.0.0/24 lesz.  Ez a 192.168.0.1 és 192.168.0.254 közötti felhasználható IP-címek tartományát hozza létre.  Konvenció szerint az átjárók az első IP-címmel rendelkeznek az alhálózat tartományában.

7. Kattintson az OK gombra.

## <a name="create-dhcp-scope"></a>DHCP-hatókör létrehozása

A DHCP-hatókör hozzáadásához a következő lépések utasításait kell megadnia.  Ebben a cikkben a NAT-hálózat 192.168.0.0/24 CIDR-jelöléssel van elfoglalva.  Ez a 192.168.0.1 és 192.168.0.254 közötti felhasználható IP-címek tartományát hozza létre.  A létrehozott hatókörnek a felhasználható címek tartományában kell lennie, kivéve a korábban már létrehozott IP-címet.

1. Nyissa meg a **felügyeleti eszközöket** , és nyissa meg a **DHCP** felügyeleti eszközt.
2. A **DHCP** -eszközben bontsa ki az aktuális kiszolgáló csomópontját, és válassza az **IPv4** elemet.
3. A művelet menüben válassza az **Új hatókör lehetőséget...**
4. Amikor megjelenik az **Új hatókör varázsló** , kattintson a **tovább** gombra **a kezdőlapon** .
5. A **hatókör neve** lapon adja meg a "LabServicesDhcpScope" nevet, vagy a név egy másik emlékezetét.  Kattintson a **Tovább** gombra.
6. Az **IP-címtartomány** lapon adja meg a következő értékeket.

    - a kezdő IP-cím 192.168.0.100
    - a záró IP-cím 192.168.0.200
    - 24 a hosszhoz
    - az alhálózati maszk 255.255.255.0

7. Kattintson a **Tovább** gombra.
8. A **kizárások és késleltetés hozzáadása** lapon kattintson a **tovább** gombra.
9. A **bérlet időtartama** lapon kattintson a **tovább** gombra.
10. A **DHCP-beállítások konfigurálása** lapon válassza az **Igen, most szeretném konfigurálni ezeket a beállításokat**. Kattintson a **Tovább** gombra.
11. Az **útválasztón (alapértelmezett átjáró)**
12. 192.168.0.1 hozzáadása, ha még nem tette meg. Kattintson a **Tovább** gombra.
13. A **tartománynév és DNS-kiszolgálók** lapon adja hozzá a 168.63.129.16 DNS-kiszolgáló IP-címéhez, ha az már nem lett végrehajtva.  a 168.63.129.16 egy Azure statikus DNS-kiszolgáló IP-címe. Kattintson a **Tovább** gombra.
14. A **WINS-kiszolgálók** lapon kattintson a **tovább** gombra.
15. A **Hatókör aktiválása** oldalon válassza az **Igen, most szeretném aktiválni ezt a hatókört** lehetőséget.  Kattintson a **Tovább** gombra.
16. Az **Új hatókör varázsló befejezése** lapon kattintson a **Befejezés** gombra.

## <a name="conclusion"></a>Összegzés

Most a sablon-számítógép készen áll a Hyper-V virtuális gépek létrehozására.   A Hyper-V virtuális gépek létrehozásával kapcsolatos utasításokért tekintse meg [a virtuális gép létrehozása a Hyper-v-ben](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) című témakört.  Tekintse meg a [Microsoft próbaverzióját](https://www.microsoft.com/evalcenter/) is a rendelkezésre álló operációs rendszerek és szoftverek ellenőrzéséhez.

## <a name="next-steps"></a>Következő lépések

A következő lépések közösek a laborok beállításához.

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail-regisztrációs hivatkozások a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users)