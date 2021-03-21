---
title: Feladatátvétel futtatása a vész-helyreállítás során Azure Site Recovery
description: Virtuális gépek/fizikai kiszolgálók feladatátvétele az Azure-ba a Azure Site Recovery használatával.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 6737f64773f91ede1631d42cd7f28c7d961c0454
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368621"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Feladatátvétel futtatása a helyszíni rendszerről az Azure-ba

Ez a cikk bemutatja, hogyan hajthat végre feladatátvételt a helyszíni gépeken az Azure-ban [Azure site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>Előkészületek

- [Ismerje meg](failover-failback-overview.md) a feladatátvétel folyamatát a vész-helyreállításban.
- Ha több gép feladatátvételét szeretné elkészíteni, [Ismerje meg](recovery-plan-overview.md) , hogyan gyűjtheti össze a gépeket egy helyreállítási tervben.
- A teljes feladatátvétel elvégzése előtt futtasson egy vész- [helyreállítási gyakorlatot](site-recovery-test-failover-to-azure.md) , hogy minden a várt módon működjön.

## <a name="prepare-to-connect-after-failover"></a>Felkészülés a csatlakozásra a feladatátvétel után

Annak érdekében, hogy a feladatátvételt követően létrehozott Azure-beli virtuális gépekhez csatlakozhasson, itt számos dolog szükséges a helyszíni feladatok elvégzéséhez a feladatátvétel előtt.


### <a name="prepare-on-premises-to-connect-after-failover"></a>A helyszíni felkészülés a feladatátvétel utáni csatlakozásra

Ha az Azure-beli virtuális gépeket a feladatátvételt követően RDP/SSH használatával kívánja használni, számos dolog szükséges a helyszíni feladatok elvégzéséhez a feladatátvétel előtt.

**Feladatátvétel után** | **Hely** | **Műveletek**
--- | --- | ---
**Windows rendszerű Azure-beli virtuális gép** | Helyszíni gép feladatátvétel előtt | Ha az Azure-beli virtuális gépet az interneten keresztül szeretné elérni, engedélyezze az RDP-t, és győződjön meg arról, hogy a TCP-és UDP-szabályok **nyilvánosak**, és az RDP engedélyezve van a **Windows tűzfal**  >  **engedélyezett alkalmazásaiban** található összes profilhoz.<br/><br/> Ha az Azure-beli virtuális gépet helyek közötti kapcsolaton keresztül szeretné elérni, engedélyezze az RDP-t a gépen, és győződjön meg arról, hogy a **Windows tűzfal**  ->  **engedélyezett alkalmazásaiban és szolgáltatásaiban** engedélyezve van-e az RDP a **tartomány-és magánhálózati** hálózatokhoz.<br/><br/> <br/><br/> Távolítsa el a statikus állandó útvonalakat és a WinHTTP proxyt. Győződjön meg arról, hogy az operációs rendszer SAN-szabályzata **OnlineAll** értékre van állítva. [További információ](https://support.microsoft.com/kb/3031135).<br/><br/> Győződjön meg arról, hogy a virtuális gépen nincsenek függőben lévő Windows-frissítések a feladatátvétel elindításakor. Előfordulhat, hogy a Windows Update akkor indul el, ha átadja a feladatátvételt, és a frissítés befejezéséig nem tud majd bejelentkezni a virtuális gépre.
**Linux rendszerű Azure-beli virtuális gép** | Helyszíni gép feladatátvétel előtt | Győződjön meg arról, hogy a virtuális gépen a Secure Shell szolgáltatás automatikusan elindul a rendszerindításkor.<br/><br/> Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.


## <a name="run-a-failover"></a>Feladatátvétel futtatása

Ez az eljárás azt ismerteti, hogyan futtatható feladatátvétel egy [helyreállítási tervhez](site-recovery-create-recovery-plans.md). Ha feladatátvételt szeretne futtatni egyetlen virtuális gépen, kövesse a [VMWare virtuális gép](vmware-azure-tutorial-failover-failback.md), a [fizikai kiszolgáló](physical-to-azure-failover-failback.md)vagy a [Hyper-V virtuális gép](hyper-v-azure-failover-failback-tutorial.md)utasításait.


Futtassa a helyreállítási terv feladatátvételét a következőképpen:

1. A site Recovery-tárolóban válassza a **helyreállítási tervek**  >  *recoveryplan_name* lehetőséget.
2. Kattintson a **feladatátvétel** elemre.

    ![Képernyőkép a Azure Site Recoveryről, amely a ADRP panelt jeleníti meg a további menüből kiválasztott Feladatátvételsel.](./media/site-recovery-failover/Failover.png)

3. A **feladatátvétel**  >  **feladatátvételi irányában** hagyja meg az alapértelmezett értéket, ha az Azure-ba végzi a replikálást.
4. A feladatátvétel területen válassza ki azt a **helyreállítási pontot** , amelyre a **feladatátvételt** végre szeretné adni.

    - **Legújabb**: használja a legújabb pontot. Ez feldolgozza a Site Recovery szolgáltatásnak elküldett összes olyan adatfeldolgozást, amely minden egyes géphez létrehoz egy helyreállítási pontot. Ez a beállítás biztosítja a legalacsonyabb RPO (helyreállítási pont célkitűzés), mert a feladatátvételt követően létrehozott virtuális gép minden olyan adattal rendelkezik, amelyet a rendszer replikált Site Recovery a feladatátvétel elindítása után.
    Vegye figyelembe, hogy ha a forrás-régió leáll, nem lehet több naplózási feldolgozás. Ezért a feladatátvételt a legújabb feldolgozott helyreállítási pontra kell átadnia. További tudnivalókat a következő pontban talál.
   - **Legutóbb feldolgozott**: Ha ezt a beállítást választja, a virtuális gépeket a site Recovery által már feldolgozott legújabb helyreállítási pontra hajthatja végre. A legújabb feldolgozott helyreállítási pontot a virtuális gép **legújabb helyreállítási pontjaiban** tekintheti meg. Ez a beállítás alacsony RTO biztosít, mivel a feldolgozatlan adatmennyiség feldolgozásához nem kell időt fordítani
   - **Legújabb alkalmazás-konzisztens**: ezzel a beállítással feladatátvételt hajthat végre a virtuális gépeken a site Recovery által feldolgozott legújabb alkalmazás-konzisztens helyreállítási pontra.
   - **Legújabb több virtuális gépre feldolgozva**: ezzel a beállítással a replikálási csoport részét képező virtuális gépek a legújabb közös, több virtuális gépre kiterjedő konzisztens helyreállítási pontra kerülnek. Más virtuális gépek feladatátvétele a legújabb feldolgozott helyreállítási pontra történik. Ez a beállítás csak olyan helyreállítási tervekhez használható, amelyek legalább egy virtuális géppel rendelkeznek, és engedélyezve van a több virtuális gépre kiterjedő konzisztencia.
   - A **legújabb, több virtuális gépre kiterjedő alkalmazás – konzisztens**: ezzel a beállítással a replikálási csoport részét képező virtuális gépek feladatátvételt hajtanak végre a legutóbb használt, több virtuális gépre kiterjedő, alkalmazás-konzisztens helyreállítási ponttal. Más virtuális gépek feladatátvétele a legújabb alkalmazás-konzisztens helyreállítási pontra. Csak olyan helyreállítási tervekhez, amelyeknél engedélyezve van a több virtuális gépre kiterjedő konzisztencia, legalább egy virtuális géppel.
   - **Egyéni**: a helyreállítási tervekhez nem érhető el. Ez a beállítás csak az egyes virtuális gépek feladatátvételére szolgál.

5. A feladatátvétel megkezdése előtt válassza a **leállított gép** lehetőséget, ha azt szeretné, hogy a feladatátvétel elindítása előtt site Recovery állítsa le a forrás virtuális gépeket. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul.  

    > [!NOTE]
    > Hyper-V virtuális gépek feladatátvétele esetén a Leállítás megkísérli a szolgáltatásnak még nem küldött helyszíni adatokat szinkronizálni és replikálni a feladatátvétel elindítása előtt. 

6. Kövesse a **feladatok** lapon a feladatátvétel folyamatát. Ha hiba történik, a helyreállítási terv addig fut, amíg be nem fejeződik.
7. A feladatátvételt követően jelentkezzen be a virtuális gépre a hitelesítéshez. 
8. Ha másik helyreállítási pontra szeretne váltani a feladatátvételhez, használja a **helyreállítási pont módosítása** lehetőséget.
9. Ha elkészült, véglegesítheti a feladatátvételt. A **commit** művelet törli a szolgáltatásban elérhető összes helyreállítási pontot. A **helyreállítási pont módosítása** lehetőség többé nem lesz elérhető.

## <a name="run-a-planned-failover-hyper-v"></a>Tervezett feladatátvétel futtatása (Hyper-V)

Futtathat egy tervezett feladatátvételt a Hyper-V virtuális gépekhez.

- A tervezett feladatátvétel egy nulla adatvesztési feladatátvételi lehetőség.
- Tervezett feladatátvétel indításakor először a forrásként szolgáló virtuális gépek leállnak, a legfrissebb adatokat szinkronizálja a rendszer, majd elindítja a feladatátvételt.
- A tervezett feladatátvételt a **tervezett feladatátvételi** lehetőség használatával futtathatja. A rendszeres feladatátvételhez hasonló módon fut.
 
## <a name="track-failovers"></a>Feladatátvételek nyomon követése

A feladatátvételhez számos feladat van társítva.

![Képernyőkép a feladatok lapról, amely az 1. csoportba tartozó feladatok listáját tartalmazza: Start (1) a Name (név) oszlopban kibontva. A SQLServer feladatokhoz tartozó sor ki van emelve.](./media/site-recovery-failover/FailoverJob.png)

- **Előfeltételek ellenőrzése**: ellenőrzi, hogy a feladatátvételhez szükséges összes feltétel teljesül-e.
- **Feladatátvétel**: az adatok feldolgozásával az Azure-beli virtuális gépek létrehozhatók. Ha a **legutóbbi** helyreállítási pontot választotta, a rendszer egy helyreállítási pontot hoz létre a szolgáltatásnak elküldett adatokból.
- **Indítás**: létrehoz egy Azure-beli virtuális gépet az előző lépésben feldolgozott adatfeldolgozási művelettel.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: a feladatátvétel elindítása előtt a rendszer leállította a virtuális gép replikálását. Ha megszakít egy folyamatban lévő feladatot, a feladatátvétel leáll, de a virtuális gép nem fog elindulni a replikáláshoz. A replikáció nem indítható újra.


### <a name="extra-failover-time"></a>További feladatátvételi idő

Bizonyos esetekben a virtuális gépek feladatátvétele olyan közbenső lépést igényel, amely általában 8 – 10 percet vesz igénybe. Ez a további lépés/idő által érintett gépek:

* A mobilitási szolgáltatás 9,8-nál régebbi verzióját futtató VMware virtuális gépek.
* Fizikai kiszolgálók és fizikai kiszolgálóként védett Hyper-V virtuális gépek.
* VMware Linux rendszerű virtuális gépek.
* Azok a VMware virtuális gépek, amelyeken ezek az illesztőprogramok nem találhatók rendszerindítási illesztőprogramként:
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* Azok a VMware virtuális gépek, amelyeken nincs engedélyezve a DHCP, függetlenül attól, hogy DHCP-vagy statikus IP-címeket használnak.


## <a name="automate-actions-during-failover"></a>Műveletek automatizálása a feladatátvétel során

Előfordulhat, hogy automatizálni szeretné a műveleteket a feladatátvétel során. Ehhez a helyreállítási tervekben parancsfájlokat vagy Azure Automation-runbookok használhat.

- [Útmutató](site-recovery-create-recovery-plans.md) helyreállítási tervek létrehozásához és testreszabásához, beleértve a parancsfájlok hozzáadását.
- [Tudnivalók Azure Automation](site-recovery-runbook-automation.md) runbookok helyreállítási tervekhez való hozzáadásáról.


## <a name="configure-settings-after-failover"></a>Beállítások konfigurálása a feladatátvétel után

### <a name="retain-drive-letters-after-failover"></a>Meghajtóbetűjelek megőrzése a feladatátvétel után

A Site Recovery kezeli a meghajtóbetűjelek megőrzését. Ha a virtuális gépek replikálásakor kizárja a lemezeket, [tekintse át](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) a működésének példáját.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Felkészülés az Azure-ba a feladatátvételt követően való csatlakozáshoz

Ha az RDP vagy SSH használatával a feladatátvételt követően létrehozott Azure virtuális gépekhez szeretne csatlakozni, kövesse a táblázatban foglalt követelményeket.

**Feladatátvétel** | **Hely** | **Műveletek**
--- | --- | ---
**Windows rendszerű Azure-beli virtuális gép** | Azure virtuális gép feladatátvétel után |  [Nyilvános IP-cím hozzáadása](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) a virtuális gép számára.<br/><br/> A feladatátvételi virtuális gépen (és az Azure-alhálózaton, amelyhez csatlakozik) a hálózati biztonsági csoport szabályainak engedélyeznie kell a bejövő kapcsolatokat az RDP-porton.<br/><br/> A **rendszerindítási diagnosztika** ellenőrzésével ellenőrizheti a virtuális gép képernyőképét.<br/><br/> Ha nem tud kapcsolatot létesíteni, ellenőrizze, hogy fut-e a virtuális gép, és tekintse át ezeket a [hibaelhárítási tippeket](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Linux rendszerű Azure-beli virtuális gép** | Azure virtuális gép feladatátvétel után | Az átadott virtuális gép (és az ahhoz csatlakozó Azure-alhálózat) hálózati biztonsági csoportra vonatkozó szabályainak engedélyeznie kell a bejövő kapcsolatokat az SSH-porton.<br/><br/> [Nyilvános IP-cím hozzáadása](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) a virtuális gép számára.<br/><br/> A virtuális gép képernyőképének megtekintése a **rendszerindítási diagnosztika** szolgáltatásban.<br/><br/>

Kövesse az [itt](site-recovery-failover-to-azure-troubleshoot.md) leírt lépéseket a feladatátvitelt követő csatlakozási problémák megoldása érdekében.

## <a name="set-up-ip-addressing"></a>IP-címzés beállítása

- **Belső IP-címek**: a feladatátvételt követően egy Azure virtuális gép belső IP-címének beállításához több lehetőség közül választhat:
    - Azonos IP-cím megőrzése: az Azure-beli virtuális gépen ugyanazt az IP-címet használhatja, mint a helyszíni gép számára.
    - Eltérő IP-cím használata: használhat másik IP-címet az Azure-beli virtuális géphez.
    - [További](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) információ a belső IP-címek beállításáról.
- **Külső IP-címek**: a feladatátvételhez nyilvános IP-címeket is megtarthat. A feladatátvételi folyamat részeként létrehozott Azure-beli virtuális gépekhez az Azure-régióban elérhető Azure nyilvános IP-címet kell rendelni. A nyilvános IP-címeket manuálisan is hozzárendelheti, vagy automatizálhatja a folyamatot egy helyreállítási terv használatával. [További információ](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Következő lépések

A feladatátvételt követően újra kell védetté tenni az Azure-beli virtuális gépeknek a helyszíni helyre történő replikálásának megkezdéséhez. A replikálást követően a rendszer visszaállíthatja a helyszíni feladatokat, ha elkészült.

- [További](failover-failback-overview.md#reprotectionfailback) információ az ismételt védelemről és a feladat-visszavételről.
- [Felkészülés](vmware-azure-reprotect.md) a VMware ismételt védelmére és a feladat-visszavételre.
- Feladat [-visszavétel](hyper-v-azure-failback.md) Hyper-V virtuális gépek.
- [Ismerje meg](physical-to-azure-failover-failback.md) a fizikai kiszolgálók feladatátvételi és feladat-visszavételi folyamatát.