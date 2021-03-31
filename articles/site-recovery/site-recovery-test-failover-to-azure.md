---
title: Feladatátvételi teszt (vész-helyreállítási részletezés) futtatása az Azure-ban Azure Site Recovery
description: Ismerje meg, hogyan futtathatja a helyszíni feladatátvételi teszteket az Azure-ba a Azure Site Recovery szolgáltatás használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 815a35c167bbcd3ac03dfdaaf6d699e58a791f33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369386"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Feladatátvételi teszt futtatása (vész-helyreállítási részletezés) az Azure-ba 


Ez a cikk azt ismerteti, hogyan futtathatók a vész-helyreállítási gyakorlatok az Azure-ba egy Site Recovery feladatátvételi teszt használatával.  

Futtasson feladatátvételi tesztet a replikálási és vész-helyreállítási stratégia ellenőrzéséhez adatvesztés vagy állásidő nélkül. A feladatátvételi teszt nem érinti a folyamatban lévő replikációt vagy az éles környezetet. Futtathat egy feladatátvételi tesztet egy adott virtuális gépen (VM) vagy több virtuális gépet tartalmazó [helyreállítási tervben](site-recovery-create-recovery-plans.md) .


## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása
Ez az eljárás azt ismerteti, hogyan futtathat feladatátvételi tesztet helyreállítási tervhez. Ha egyetlen virtuális géphez szeretne feladatátvételi tesztet futtatni, kövesse az [itt](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm) ismertetett lépéseket.

![Képernyőkép a Azure Portal feladatátvételi teszt oldaláról.](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. A Azure Portal site Recovery kattintson a **helyreállítási tervek**  >  *recoveryplan_name*  >  **feladatátvételi teszt** elemre.
2. Válassza ki azt a helyreállítási pontot, amelyre a feladatátvételt **hajtja** végre. Az alábbi lehetőségek egyikét használhatja:
    - **Legutóbb feldolgozott**: Ez a beállítás a tervben szereplő összes virtuális gép esetében a site Recovery által feldolgozott legutóbbi helyreállítási pontra meghiúsul. Egy adott virtuális gép legutóbbi helyreállítási pontjának megtekintéséhez tekintse meg a virtuális gép beállításainak **legutóbbi helyreállítási pontjait** . Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
    - **Legutóbb használt alkalmazás – konzisztens**: Ez a beállítás a tervben szereplő összes virtuális gép feladatátvételét végzi a site Recovery által feldolgozott legújabb, alkalmazás-konzisztens helyreállítási pontra. Egy adott virtuális gép legutóbbi helyreállítási pontjának megtekintéséhez tekintse meg a virtuális gép beállításainak **legutóbbi helyreállítási pontjait** .
    - **Legújabb**: Ez a lehetőség először feldolgozza a site Recovery szolgáltatásnak elküldett összes adatát, hogy minden virtuális gép számára létrehozzon egy helyreállítási pontot, mielőtt a művelet elvégezte a feladatátvételt. Ez a beállítás a legalacsonyabb RPO (helyreállítási pont célkitűzés) adja meg, mert a feladatátvételt követően létrehozott virtuális gép minden olyan adattal rendelkezik, amelyet a rendszer a feladatátvétel elindításakor Site Recovery replikál.
    - **Legújabb több virtuális gépre feldolgozva**: Ez a beállítás olyan helyreállítási tervekhez érhető el, amelyeken engedélyezve van egy vagy több virtuális gépre kiterjedő konzisztencia. Azok a virtuális gépek, amelyeken engedélyezve van a feladatátvétel, a legújabb közös, több virtuális gépre kiterjedő konzisztens helyreállítási pontot adják át. Más virtuális gépek feladatátvétele a legutóbb feldolgozott helyreállítási pontra történik.  
    - **Legújabb több virtuális gépre kiterjedő alkalmazás – konzisztens**: Ez a beállítás olyan helyreállítási tervekhez érhető el, amelyeken engedélyezve van egy vagy több virtuális gépre kiterjedő konzisztencia. A replikációs csoport részét képező virtuális gépek a legújabb közös, több virtuális gépre kiterjedő, alkalmazás-konzisztens helyreállítási pontot adják át. Más virtuális gépek feladatátvétele a legújabb, alkalmazás-konzisztens helyreállítási ponttal történik.
    - **Egyéni**: Ha ezt a lehetőséget választja, az adott virtuális gép egy adott helyreállítási pontra történő feladatátvételét hajthatja végre.
3. Válasszon ki egy Azure-beli virtuális hálózatot, amelyben a tesztelési virtuális gépek lesznek létrehozva.

    - Site Recovery a virtuális gép **számítási és hálózati** beállításaiban megadott névvel és UGYANAZZAL az IP-címmel kísérli meg a tesztelési virtuális gépek létrehozását.
    - Ha az azonos nevű alhálózat nem érhető el a feladatátvételi teszthez használt Azure-beli virtuális hálózatban, akkor a teszt virtuális gép betűrendben lesz létrehozva az első alhálózatban.
    - Ha ugyanaz az IP-cím nem érhető el az alhálózatban, akkor a virtuális gép egy másik elérhető IP-címet kap az alhálózatban. [További információk](#create-a-network-for-test-failover).
4. Ha feladatátvételt végez az Azure-ba, és engedélyezve van az adattitkosítás, a **titkosítási kulcsban** válassza ki azt a tanúsítványt, amelyet a rendszer a szolgáltató telepítése során a titkosítás engedélyezésekor adott ki. Ha nincs engedélyezve a titkosítás, figyelmen kívül hagyhatja ezt a lépést.
5. A **feladatok** lapon nyomon követheti a feladatátvétel folyamatát. A Azure Portalban láthatja a teszt replika gépet.
6. Ahhoz, hogy RDP-kapcsolatot kezdeményezzen az Azure-beli virtuális géppel, [hozzá kell adnia egy nyilvános IP-címet](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) a FELADATÁTVÉTELI virtuális gép hálózati adapterén.
7. Ha minden a várt módon működik, kattintson a **feladatátvételi teszt törlése** lehetőségre. Ezzel törli a feladatátvételi teszt során létrehozott virtuális gépeket.
8. A **jegyzetek** területen jegyezze fel és mentse a feladatátvételi teszttel kapcsolatos megfigyeléseket.


![Képernyőfelvétel a feladatátvételi teszttel kapcsolatos feladatok lapról.](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

A feladatátvételi teszt bekövetkeztekor a következő történik:

1. **Előfeltételek**: az előfeltételek ellenőrzésének futtatásával ellenőrizze, hogy a feladatátvételhez szükséges összes feltétel teljesül-e.
2. **Feladatátvétel**: a feladatátvétel feldolgozza és felkészítette az adatforrást, így egy Azure-beli virtuális gép létrehozható belőle.
3. **Legutóbbi**: Ha a legutóbbi helyreállítási pontot választotta, a rendszer egy helyreállítási pontot hoz létre a szolgáltatásnak elküldett adatokból.
4. **Indítás**: Ez a lépés egy Azure-beli virtuális gépet hoz létre az előző lépésben feldolgozott adatkezelési művelettel.

### <a name="failover-timing"></a>Feladatátvétel időzítése

A következő helyzetekben a feladatátvételhez egy további közbenső lépésre van szükség, amely általában körülbelül 8 – 10 percet vesz igénybe:

* A 9,8-nál régebbi mobilitási szolgáltatás verzióját futtató VMware virtuális gépek
* Fizikai kiszolgálók
* VMware Linux rendszerű virtuális gépek
* Fizikai kiszolgálóként védett Hyper-V virtuális gép
* VMware virtuális gép, ahol a következő illesztőprogramok nem rendszerindítási illesztőprogramok:
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* A DHCP-t nem engedélyező VMware virtuális gép, függetlenül attól, hogy DHCP-vagy statikus IP-címeket használ-e.

A többi esetben nem szükséges közbenső lépés, és a feladatátvétel jóval kevesebb időt vesz igénybe.


## <a name="create-a-network-for-test-failover"></a>Hálózat létrehozása feladatátvételi teszt számára

Azt javasoljuk, hogy a feladatátvétel teszteléséhez olyan hálózatot válasszon, amely el van különítve az éles helyreállítási oldal hálózatától, amely az egyes virtuális gépek **számítási és hálózati** beállításaiban van megadva. Alapértelmezés szerint egy Azure-beli virtuális hálózat létrehozásakor a hálózat el van különítve az egyéb hálózatoktól. A teszthálózatnak az éles hálózatot kell utánoznia:

- A teszthálózatnak ugyanannyi alhálózattal kell rendelkeznie, mint az éles hálózatnak. Az alhálózatoknak ugyanazzal a névvel kell rendelkezniük.
- A teszthálózatnak ugyanazt az IP-címtartományt kell használnia.
- Frissítse a teszthálózat DNS-ét a DNS VM-hez a **számítási és hálózati** beállítások között megadott IP-címmel. További információkért olvassa el az [Active Directoryra vonatkozó feladatátvételi szempontokat](site-recovery-active-directory.md#test-failover-considerations) ismertető cikket.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Feladatátvétel tesztelése üzemi hálózatra a helyreállítási helyen

Habár azt javasoljuk, hogy az üzemi hálózattól eltérő tesztelési hálózatot használjon, ha az üzemi hálózat vész-helyreállítási részletezését szeretné tesztelni, vegye figyelembe a következőket:

- Ellenőrizze, hogy az elsődleges virtuális gép le van-e állítva a feladatátvételi teszt futtatásakor. Ellenkező esetben két virtuális gép ugyanazzal az identitással fog futni, ugyanabban a hálózatban, ugyanabban az időben. Ez váratlan következményeket eredményezhet.
- A feladatátvételi teszt során létrehozott virtuális gépek módosításai elvesznek, amikor törli a feladatátvételt. Ezek a módosítások nem replikálódnak vissza az elsődleges virtuális gépre.
- Az éles környezetben történő tesztelés az éles környezetbeli alkalmazások leállását eredményezi. A felhasználók nem használhatják a virtuális gépeken futó alkalmazásokat, ha a feladatátvételi teszt folyamatban van.  



## <a name="prepare-active-directory-and-dns"></a>A Active Directory és a DNS előkészítése

Ha feladatátvételi tesztet szeretne futtatni az alkalmazás teszteléséhez, szüksége lesz az üzemi Active Directory környezet egy példányára a tesztkörnyezetben. További információért olvassa el [Active Directory feladatátvételi teszttel kapcsolatos tudnivalókat](site-recovery-active-directory.md#test-failover-considerations) .

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Ha az Azure-beli virtuális gépeket a feladatátvételt követően RDP/SSH használatával szeretné használni, kövesse a táblázatban összefoglalt követelményeket.

**Feladatátvétel** | **Hely** | **Műveletek**
--- | --- | ---
**Windows rendszerű Azure-beli virtuális gép** | Helyszíni gép feladatátvétel előtt | Ha az Azure-beli virtuális gépet az interneten keresztül szeretné elérni, engedélyezze az RDP-t, és győződjön meg arról, hogy a TCP-és UDP-szabályok **nyilvánosak**, és az RDP engedélyezve van a **Windows tűzfal**  >  **engedélyezett alkalmazásaiban** található összes profilhoz.<br/><br/> Ha az Azure-beli virtuális gépet helyek közötti kapcsolaton keresztül szeretné elérni, engedélyezze az RDP-t a gépen, és győződjön meg arról, hogy a **Windows tűzfal**  ->  **engedélyezett alkalmazásaiban és szolgáltatásaiban** engedélyezve van-e az RDP a **tartomány-és magánhálózati** hálózatokhoz.<br/><br/>  Győződjön meg arról, hogy az operációs rendszer SAN-szabályzata **OnlineAll** értékre van állítva. [További információk](https://support.microsoft.com/kb/3031135).<br/><br/> Győződjön meg arról, hogy a virtuális gépen nincsenek függőben lévő Windows-frissítések a feladatátvétel elindításakor. Előfordulhat, hogy a Windows Update akkor indul el, ha átadja a feladatátvételt, és a frissítés befejezéséig nem tud majd bejelentkezni a virtuális gépre.
**Windows rendszerű Azure-beli virtuális gép** | Azure virtuális gép feladatátvétel után |  [Nyilvános IP-cím hozzáadása](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) a virtuális gép számára.<br/><br/> A feladatátvételi virtuális gépen (és az Azure-alhálózaton, amelyhez csatlakozik) a hálózati biztonsági csoport szabályainak engedélyeznie kell a bejövő kapcsolatokat az RDP-porton.<br/><br/> A **rendszerindítási diagnosztika** ellenőrzésével ellenőrizheti a virtuális gép képernyőképét.<br/><br/> Ha nem tud kapcsolatot létesíteni, ellenőrizze, hogy fut-e a virtuális gép, és tekintse át ezeket a [hibaelhárítási tippeket](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Linux rendszerű Azure-beli virtuális gép** | Helyszíni gép feladatátvétel előtt | Győződjön meg arról, hogy a virtuális gépen a Secure Shell szolgáltatás automatikusan elindul a rendszerindításkor.<br/><br/> Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.
**Linux rendszerű Azure-beli virtuális gép** | Azure virtuális gép feladatátvétel után | Az átadott virtuális gép (és az ahhoz csatlakozó Azure-alhálózat) hálózati biztonsági csoportra vonatkozó szabályainak engedélyeznie kell a bejövő kapcsolatokat az SSH-porton.<br/><br/> [Nyilvános IP-cím hozzáadása](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) a virtuális gép számára.<br/><br/> A virtuális gép képernyőképének megtekintése a **rendszerindítási diagnosztika** szolgáltatásban.<br/><br/>

Kövesse az [itt](site-recovery-failover-to-azure-troubleshoot.md) leírt lépéseket a feladatátvitelt követő csatlakozási problémák megoldása érdekében.

## <a name="next-steps"></a>Következő lépések
A vész-helyreállítási részletezés befejezése után további információkat tudhat meg a [feladatátvétel](site-recovery-failover.md)egyéb típusairól.