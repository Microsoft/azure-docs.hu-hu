---
title: Gépek előkészítése a Azure Migrate való áttelepítéshez
description: Megtudhatja, hogyan készítheti elő a helyszíni gépeket a Azure Migrate való áttelepítéshez.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: 8083b9edd49f65f29fe9c9b2cfa30edfacf89507
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614887"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Helyszíni gépek előkészítése az Azure-ba való áttelepítésre

Ez a cikk azt ismerteti, hogyan készítse elő a helyszíni gépeket az Azure-ba való Migrálás előtt a [Azure Migrate: Server áttelepítési](migrate-services-overview.md#azure-migrate-server-migration-tool) eszköz használatával.

Ebben a cikkben:
> [!div class="checklist"]
> * Tekintse át az áttelepítési korlátozásokat.
> * Válassza ki a VMware virtuális gépek áttelepítésének módszerét
> * A Hypervisort és az operációs rendszerre vonatkozó követelményeket a migrálni kívánt gépeknél tekintheti meg.
> * Tekintse át az áttelepíteni kívánt gépek URL-címét és porthoz való hozzáférését.
> * Tekintse át az áttelepítés megkezdése előtt szükségessé vált módosításokat.
> * Az áttelepített gépekre vonatkozó Azure-beli virtuális gépekre vonatkozó követelmények keresése
> * Készítse elő a gépeket, hogy az áttelepítés után csatlakozni lehessen az Azure virtuális gépekhez.



## <a name="verify-migration-limitations"></a>Áttelepítési korlátozások ellenőrzése

A táblázat összefoglalja a Azure Migrate felderítési, felmérési és áttelepítési korlátait. Javasoljuk, hogy az áttelepítés előtt mérje fel a gépeket, de nem kell.

**Forgatókönyv** | **Projekt** | **Felderítés/Értékelés** | **Migrálás**
--- | --- | --- | ---
**VMware virtuális gépek** | Egy Azure Migrate-projektben akár 35 000 virtuális gépet is felderítheti és felbecsülheti. | Akár 10 000 VMware virtuális gépet is felfedezheti egyetlen [Azure Migrate berendezéssel](common-questions-appliance.md) a VMware-hez. | **Ügynök nélküli áttelepítés**: egyszerre legfeljebb 500 virtuális gépet lehet replikálni az egyes vCenter Serverokból. **Ügynök-alapú áttelepítés**: a [replikációs berendezést](migrate-replication-appliance.md) nagy számú virtuális gép replikálására is [felskálázással](./agent-based-migration-architecture.md#performance-and-scaling) végezheti el.<br/><br/> A portálon egyszerre legfeljebb 10 gépet választhat a replikáláshoz. Több gép replikálásához vegyen fel 10-es kötegeket.
**Hyper-V virtuális gépek** | Egy Azure Migrate-projektben akár 35 000 virtuális gépet is felderítheti és felbecsülheti. | Akár 5 000 Hyper-V virtuális gép felderítése egyetlen Azure Migrate berendezéssel | Egy berendezés nem használatos a Hyper-V áttelepítéséhez. Ehelyett a Hyper-V replikációs szolgáltató minden Hyper-V-gazdagépen fut.<br/><br/> A replikációs kapacitást a teljesítménnyel kapcsolatos tényezők, például a virtuális gépek elváltozása és a replikációs adatok feltöltési sávszélessége befolyásolja.<br/><br/> A portálon egyszerre legfeljebb 10 gépet választhat a replikáláshoz. Több gép replikálásához vegyen fel 10-es kötegeket.
**Fizikai gépek** | Akár 35 000 gépet is felderítheti és felbecsülheti egyetlen Azure Migrate projektben. | Akár 250 fizikai kiszolgálót is felderítheti egyetlen Azure Migrate berendezéssel a fizikai kiszolgálók számára. | A [replikációs berendezés](migrate-replication-appliance.md) a nagy számú kiszolgáló replikálására is [méretezhető](./agent-based-migration-architecture.md#performance-and-scaling) .<br/><br/> A portálon egyszerre legfeljebb 10 gépet választhat a replikáláshoz. Több gép replikálásához vegyen fel 10-es kötegeket.

## <a name="select-a-vmware-migration-method"></a>VMware áttelepítési módszer kiválasztása

Ha VMware virtuális gépeket telepít át az Azure-ba, [hasonlítsa össze](server-migrate-overview.md#compare-migration-methods) az ügynök nélküli és az ügynök-alapú áttelepítési módszereket, hogy eldöntse, mi működik.

## <a name="verify-hypervisor-requirements"></a>A hypervisor-követelmények ellenőrzése

- Ellenőrizze a [VMware ügynök](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)nélküli vagy a [VMware Agent-alapú](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) követelményeket.
- Ellenőrizze a [Hyper-V gazdagépre](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) vonatkozó követelményeket.


## <a name="verify-operating-system-requirements"></a>Operációs rendszerre vonatkozó követelmények ellenőrzése

Az áttelepítéshez támogatott operációs rendszerek ellenőrzése:

- Ha VMware virtuális gépeket vagy Hyper-V virtuális gépeket telepít át, ellenőrizze a VMware virtuális gép követelményeit az [ügynök](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless)nélkül, valamint az [ügynök-alapú](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) áttelepítést és a [Hyper-v virtuális gépekre](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms)vonatkozó követelményeket.
- Ellenőrizze, hogy a [Windows operációs rendszerek](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) támogatottak-e az Azure-ban.
- Az Azure-ban támogatott [Linux-disztribúciók](../virtual-machines/linux/endorsed-distros.md) ellenőrzése.

## <a name="review-url-and-port-access"></a>URL-cím és a port elérésének áttekintése

Áttekintheti, hogy mely URL-címek és portok érhetők el az áttelepítés során.

**Forgatókönyv** | **Részletek** |  **URL-címek** | **Portok**
--- | --- | --- | ---
**VMware ügynök nélküli Migrálás** | Az áttelepítéshez a [Azure Migrate készüléket](migrate-appliance-architecture.md) használja. Nincs telepítve a VMware virtuális gépeken. | Tekintse át a berendezéssel kapcsolatos felderítéshez, értékeléshez és áttelepítéshez szükséges nyilvános Felhőbeli és kormányzati [URL-címeket](migrate-appliance.md#url-access) . | [Tekintse át](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) az ügynök nélküli áttelepítés portjának követelményeit.
**VMware-ügynök alapú áttelepítés** | A [replikációs berendezést](migrate-replication-appliance.md) használja az áttelepítéshez. A mobilitási szolgáltatás ügynöke telepítve van a virtuális gépeken. | Tekintse át a [nyilvános felhőt](migrate-replication-appliance.md#url-access) és [Azure Government](migrate-replication-appliance.md#azure-government-url-access) URL-címeket, amelyekhez a replikációs berendezésnek hozzá kell férnie. | [Tekintse át](migrate-replication-appliance.md#port-access) az ügynök-alapú áttelepítés során használt portokat.
**Hyper-V áttelepítése** | Hyper-V gazdagépekre telepített szolgáltatót használ az áttelepítéshez. A Hyper-V virtuális gépeken nincs telepítve semmi. | Tekintse át a [nyilvános felhőt](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud) és [Azure Government](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government) URL-címeket, amelyeket a gazdagépeken futó replikációs szolgáltatónak el kell érnie. | A Hyper-V-gazdagép replikációs szolgáltatója kimenő kapcsolatokat használ a 443-es HTTPS-porton a virtuális gép replikációs adatküldéséhez.
**Fizikai gépek** | A [replikációs berendezést](migrate-replication-appliance.md) használja az áttelepítéshez. A mobilitási szolgáltatás ügynöke telepítve van a fizikai gépeken. | Tekintse át a [nyilvános felhőt](migrate-replication-appliance.md#url-access) és [Azure Government](migrate-replication-appliance.md#azure-government-url-access) URL-címeket, amelyekhez a replikációs berendezésnek hozzá kell férnie. | [Tekintse át](migrate-replication-appliance.md#port-access) a fizikai áttelepítés során használt portokat.

## <a name="verify-required-changes-before-migrating"></a>A szükséges módosítások ellenőrzése az áttelepítés előtt

Az Azure-ba történő Migrálás előtt néhány módosításra van szükség a virtuális gépeken.

- Egyes operációs rendszerek esetében a Azure Migrate a replikálási/áttelepítési folyamat során automatikusan végrehajtja a módosításokat.
- Más operációs rendszerek esetében manuálisan kell konfigurálnia a beállításokat.
- Fontos, hogy az áttelepítés megkezdése előtt manuálisan konfigurálja a beállításokat. Ha a módosítás előtt áttelepíti a virtuális gépet, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban.

Tekintse át a táblákat a szükséges módosítások azonosításához.

### <a name="windows-machines"></a>Windows rendszerű gépek

A szükséges módosításokat a táblázat összegzi.

**Művelet** | **VMware (ügynök nélküli áttelepítés)** | **VMware (Agent-alapú)/Physical gépek** | **Windows a Hyper-V-n** 
--- | --- | --- | ---
**A TÁROLÓHÁLÓZATI házirend konfigurálása online állapotként**<br/><br/> Ez biztosítja, hogy az Azure virtuális gépen futó Windows-kötetek ugyanazt a meghajtóbetűjel-hozzárendelést használják, mint a helyszíni virtuális gép. | Automatikus beállítás a Windows Server 2008 R2 vagy újabb rendszert futtató gépeken.<br/><br/> Konfigurálja manuálisan a korábbi operációs rendszerekhez. | A legtöbb esetben a rendszer automatikusan beállítja. | Konfigurálja manuálisan.
**A Hyper-V vendég integrációjának telepítése** | [Telepítse manuálisan](prepare-windows-server-2003-migration.md#install-on-vmware-vms) a Windows Server 2003 rendszert futtató gépeken. | [Telepítse manuálisan](prepare-windows-server-2003-migration.md#install-on-vmware-vms) a Windows Server 2003 rendszert futtató gépeken. | [Telepítse manuálisan](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms) a Windows Server 2003 rendszert futtató gépeken.
**Engedélyezze az Azure soros konzolt**.<br/><br/>[Engedélyezze a konzolt az](../virtual-machines/troubleshooting/serial-console-windows.md) Azure virtuális gépeken a hibaelhárításhoz. Nem kell újraindítani a virtuális gépet. Az Azure-beli virtuális gép a lemezkép használatával fog elindulni. A lemezkép rendszerindítási értéke megegyezik az új virtuális gép újraindításával. | Manuális engedélyezés | Manuális engedélyezés | Manuális engedélyezés
**Kapcsolat az áttelepítés után**<br/><br/> A Migrálás utáni kapcsolódáshoz számos lépést kell végrehajtani az áttelepítés előtt. | [Állítsa be](#prepare-to-connect-to-azure-windows-vms) manuálisan. | [Állítsa be](#prepare-to-connect-to-azure-windows-vms) manuálisan. | [Állítsa be](#prepare-to-connect-to-azure-windows-vms) manuálisan.


#### <a name="configure-san-policy"></a>TÁROLÓHÁLÓZATI házirend konfigurálása

Alapértelmezés szerint az Azure-beli virtuális gépek a D meghajtót használják ideiglenes tárolóként való használatra.

- Ez a meghajtó-hozzárendelés azt eredményezi, hogy az összes többi csatlakoztatott tárolási meghajtó-hozzárendelés egy betűvel növekszik.
- Ha például a helyszíni telepítés olyan adatlemezt használ, amely az alkalmazás telepítéséhez a D meghajtóhoz van rendelve, a meghajtó hozzárendelése a virtuális gép Azure-ba való áttelepítése után az E meghajtóra növekszik. 
- Ha meg szeretné akadályozni az automatikus hozzárendelést, és győződjön meg arról, hogy az Azure a következő szabad meghajtóbetűjelet rendeli hozzá az ideiglenes kötethez, állítsa a Tárolóhálózati (SAN) házirendet **OnlineAll**:

Konfigurálja a beállítást manuálisan a következőképpen:

1. Nyisson meg egy rendszergazda jogú parancssort a helyszíni gépen (nem a gazdagépen).
2. Adja meg a **diskpartt**.
3. Adja meg a **San** értéket. Ha a vendég operációs rendszer meghajtóbetűjele nincs karbantartva, a rendszer **offline állapotba** helyezi az összes vagy **Offline megosztást** .
4. A **DiskPart** parancssorába írja be a következőt: **San Policy = OnlineAll**. Ez a beállítás biztosítja, hogy a lemezek online állapotba kerüljenek, és lehetővé teszi a lemezek olvasását és írását is.
5. A teszt áttelepítése során ellenőrizheti, hogy a meghajtóbetűjelek megmaradnak-e.


### <a name="linux-machines"></a>Linux rendszerű gépek

A Azure Migrate automatikusan végrehajtja ezeket a műveleteket ezen verziók esetében

- Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x (az Azure Linux rendszerű virtuális gép ügynöke is automatikusan települ az áttelepítés során)
- Cent OS 7,7, 7,6, 7,5, 7,4, 6. x (az Azure Linux VM-ügynök is automatikusan települ az áttelepítés során)
- SUSE Linux Enterprise Server 12 SP1 +
- SUSE Linux Enterprise Server 15 SP1
- Ubuntu 19,04, 19,10, 18.04 LTS, 16.04 LTS, 14.04 LTS (az Azure Linux VM Agent az áttelepítés során is automatikusan települ)
- Ubuntu 18.04 LTS, 16.04 LTS
- Debian 9, 8, 7
- Oracle Linux 7,7, 7,7-CI

Más verziók esetében készítse elő a gépeket a táblázatban foglaltak szerint.  


**Művelet** | **Részletek** | **Linux-verzió**
--- | --- | ---
**A Hyper-V Linux Integration Services telepítése** | Építse újra a Linux init-rendszerképet, hogy tartalmazza a szükséges Hyper-V-illesztőprogramokat. Az init-rendszerkép újraépítése biztosítja, hogy a virtuális gép az Azure-ban induljon el. | Alapértelmezés szerint a Linux-disztribúciók legtöbb új verziója szerepel.<br/><br/> Ha nem tartalmazza, telepítse manuálisan az összes verzióra, kivéve a fentiekben leírtakat.
**Az Azure soros konzol naplózásának engedélyezése** | A konzol naplózásának engedélyezése segít a hibakeresésben. Nem kell újraindítani a virtuális gépet. Az Azure-beli virtuális gép a lemezkép használatával fog elindulni. A lemezkép rendszerindítási értéke megegyezik az új virtuális gép újraindításával.<br/><br/> Az engedélyezéshez kövesse az [alábbi utasításokat](../virtual-machines/troubleshooting/serial-console-linux.md) .
**Eszköz-hozzárendelési fájl frissítése** | Frissítse az eszköz-hozzárendelési fájlt az eszköz név – kötet társításával, így állandó eszköz-azonosítókat használhat. | Telepítse manuálisan az összes verzióra a fentiekben leírtak kivételével. (Csak ügynök-alapú VMware-forgatókönyv esetén alkalmazható)
**Fstab-bejegyzések frissítése** |  Frissítse a bejegyzéseket az állandó kötet-azonosítók használatára.    | A fentiekben leírtak kivételével az összes verzióhoz manuálisan frissítse a frissítést.
**Udev-szabály eltávolítása** | Távolítson el minden olyan udev-szabályt, amely Mac-címeken alapuló kapcsolati neveket foglal le, stb. | Távolítsa el manuálisan az összes verziót, a fentiekben leírtak kivételével.
**Hálózati adapterek frissítése** | Hálózati adapterek frissítése a DHCP-alapú IP-cím fogadására. NST | A fentiekben leírtak kivételével az összes verzióhoz manuálisan frissítse a frissítést.
**SSH engedélyezése** | Győződjön meg arról, hogy az SSH engedélyezve van, és az sshd szolgáltatás automatikusan elindul az újraindításkor.<br/><br/> Győződjön meg arról, hogy az operációs rendszer tűzfala nem blokkolja a bejövő SSH-kapcsolatok kéréseit.| A fentiekben leírtak kivételével engedélyezze manuálisan az összes verziót.

A következő táblázat összefoglalja a fent felsorolt operációs rendszerek automatikusan végrehajtott lépéseit.


| Művelet                                      | Ügynökön \- alapuló VMware-áttelepítés | Ügynök nélküli VMware-áttelepítés | Hyper\-V   |
|---------------------------------------------|-------------------------------|----------------------------|------------|
| A Hyper \- V Linux Integration Services telepítése | Igen                           | Yes                        | Nem szükséges |
| Az Azure soros konzol naplózásának engedélyezése         | Igen                           | Igen                        | Nem         |
| Eszköz-hozzárendelési fájl frissítése                      | Igen                           | Nem                         | Nem         |
| Fstab-bejegyzések frissítése                        | Igen                           | Igen                        | Nem         |
| Udev-szabály eltávolítása                            | Igen                           | Igen                        | Nem         |
| Hálózati adapterek frissítése                   | Igen                           | Igen                        | Nem         |
| SSH engedélyezése                                  | Nem                            | Nem                         | Nem         |

További információ a [Linux rendszerű virtuális gépek Azure-beli futtatásáról](../virtual-machines/linux/create-upload-generic.md), valamint a népszerű Linux-disztribúciók használati lépéseiről.

Tekintse át a Linux rendszerű virtuális gép ügynökének telepítéséhez [szükséges csomagok](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux#requirements) listáját. Azure Migrate a Linux rendszerű virtuális gép ügynökét automatikusan telepíti a 64 bites RHEL6, a RHEL7, a CentOS7 (6), az Ubuntu 14,04, az Ubuntu 16,04, az Ubuntu 18.04 pedig a VMware-áttelepítés ügynök nélküli módszerének használata esetén.

## <a name="check-azure-vm-requirements"></a>Az Azure-beli virtuális gépekre vonatkozó követelmények keresése

Az Azure-ba replikált helyszíni számítógépeknek meg kell felelniük az operációs rendszerre és az architektúrára, a lemezekre, a hálózati beállításokra és a virtuális gépek elnevezésére vonatkozó Azure-beli virtuális gépek követelményeinek.

Az áttelepítés előtt tekintse át az Azure-beli virtuális gépekre vonatkozó követelményeket a [VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements), a [Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)és a [fizikai kiszolgálók](migrate-support-matrix-physical-migration.md#azure-vm-requirements) áttelepítéséhez.



## <a name="prepare-to-connect-after-migration"></a>Felkészülés a csatlakozásra az áttelepítés után

Az Azure-beli virtuális gépek az Azure-ba való áttelepítés során jönnek létre. Az áttelepítés után képesnek kell lennie csatlakozni az új Azure-beli virtuális gépekhez. A sikeres kapcsolódáshoz több lépés szükséges.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Felkészülés az Azure Windows rendszerű virtuális gépekhez való csatlakozásra

Helyszíni Windows rendszerű gépeken:

1. Konfigurálja a Windows beállításait. A beállítások közé tartozik a statikus állandó útvonalak vagy a WinHTTP-proxy eltávolítása.
2. Győződjön meg arról, hogy a [szükséges szolgáltatások](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) futnak.
3. Engedélyezze a távoli asztal (RDP) szolgáltatást, hogy engedélyezze a távoli kapcsolatokat a helyszíni géppel. Megtudhatja, hogyan [engedélyezheti az RDP használatát a PowerShell használatával](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Ha egy Azure-beli virtuális gépet az áttelepítés után az interneten keresztül szeretne elérni, a helyszíni gépen lévő Windows tűzfalon engedélyezze a TCP és az UDP protokollt a nyilvános profilban, és állítsa be az RDP-t engedélyezett alkalmazásként az összes profilhoz.
5. Ha az áttelepítés után egy helyek közötti VPN-en keresztül szeretné elérni az Azure virtuális gépet, a helyszíni gépen lévő Windows tűzfalon engedélyezze az RDP-t a tartomány és a privát profilok számára. Megtudhatja, hogyan [engedélyezheti az RDP-forgalmat](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. A Migrálás során győződjön meg arról, hogy a helyszíni virtuális gépen nincsenek függőben lévő Windows-frissítések. Ha vannak, előfordulhat, hogy a frissítések az áttelepítés után megkezdik az Azure virtuális gépen való telepítését, és a frissítések befejezéséig nem tud majd bejelentkezni a virtuális gépre.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Felkészülés a Linux Azure-beli virtuális gépekkel való csatlakozásra

Helyszíni Linux rendszerű gépeken:

1. Győződjön meg arról, hogy a Secure Shell szolgáltatás úgy van beállítva, hogy automatikusan induljon el a rendszerindításkor.
2. Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.

### <a name="configure-azure-vms-after-migration"></a>Azure-beli virtuális gépek konfigurálása Migrálás után

Az áttelepítés után hajtsa végre a következő lépéseket a létrehozott Azure-beli virtuális gépeken:

1. Ha az interneten keresztül szeretne csatlakozni a virtuális géphez, rendeljen egy nyilvános IP-címet a virtuális géphez. Az Azure-beli virtuális gép számára eltérő nyilvános IP-címet kell használnia, mint amelyet a helyszíni géphez használt. [További információ](../virtual-network/virtual-network-public-ip-address.md).
2. Győződjön meg arról, hogy a virtuális gép hálózati biztonsági csoport (NSG) szabályai engedélyezik a bejövő kapcsolatokat az RDP-vagy SSH-porton.
3. A virtuális gép megtekintéséhez tekintse meg a [rendszerindítási diagnosztikát](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) .


## <a name="next-steps"></a>Következő lépések

Döntse el, hogy melyik módszert szeretné használni a [VMWare virtuális gépek Azure-ba való áttelepítéséhez](server-migrate-overview.md) , vagy a [Hyper-V virtuális gépek](tutorial-migrate-hyper-v.md) vagy [fizikai kiszolgálók, illetve virtualizált vagy felhőalapú virtuális gépek](tutorial-migrate-physical-virtual-machines.md)áttelepítésének megkezdéséhez.

## <a name="see-whats-supported"></a>Lásd: mi támogatott

A VMware virtuális gépek esetében a kiszolgáló áttelepítése az [ügynök nélküli vagy az ügynök-alapú áttelepítést](server-migrate-overview.md)is támogatja.

- **VMWare virtuális gépek**: Ellenőrizze az [áttelepítési követelményeket és](migrate-support-matrix-vmware-migration.md) a VMWare virtuális gépek támogatását.
- **Hyper-v virtuális gépek**: Ellenőrizze az [áttelepítési követelményeket és](migrate-support-matrix-hyper-v-migration.md) a Hyper-v virtuális gépek támogatását.
- **Fizikai gépek**: Ellenőrizze az [áttelepítési követelményeket és](migrate-support-matrix-physical-migration.md) a helyszíni fizikai gépek és más virtualizált kiszolgálók támogatását. 
