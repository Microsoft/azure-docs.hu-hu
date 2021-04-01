---
title: Feladatátvétel és feladat-visszavétel a Azure Site Recovery
description: Tudnivalók a feladatátvételről és a Azure Site Recovery sikertelen működéséről.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: b900655d6fdf1143d430ac842bfd84eb1dfdf34c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98070744"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>A helyszíni vész-helyreállítási feladatátvétel/feladat-visszavétel

Ez a cikk áttekintést nyújt a feladatátvételről és a feladat-visszavételról a helyszíni gépeknek az Azure-ba való [Azure site Recoveryával](site-recovery-overview.md)történő vész-helyreállítás során.

## <a name="recovery-stages"></a>Helyreállítási szakaszok

A feladatátvétel és a feladat-visszavétel Site Recovery négy szakaszból áll:

- **1. lépés: feladatátvétel a helyszínen**: az Azure-ba történő replikáció beállítása után a helyszíni gépek esetében, ha a helyszíni hely leáll, a gépeket a rendszer nem teszi elérhetővé az Azure-ba. A feladatátvételt követően az Azure-beli virtuális gépek a replikált adatokból jönnek létre.
- **2. fázis: Azure-beli virtuális gépek újravédése**: az Azure-ban az Azure-beli virtuális gépeket újra kell védelemmel ellátni, hogy a rendszer a helyszíni helyre replikálja a replikációt. Az adatkonzisztencia biztosítása érdekében a helyszíni virtuális gép (ha elérhető) ki van kapcsolva az ismételt védelem során.
- **3. fázis: feladatátvétel az Azure-ból**: Ha a helyszíni hely ismét a szokásos módon fut, egy másik feladatátvételt futtat, ezúttal az Azure-beli virtuális gépek feladatátvételét a helyszíni helyre. Visszatérhet az eredeti helyre, ahonnan a feladatátvételt, vagy egy másik helyre.
- **4. lépés: a helyszíni gépek ismételt védetté** tétele: a visszalépést követően ismét engedélyezze a helyszíni gépek replikálását az Azure-ba.

## <a name="failover"></a>Feladatátvétel

Az üzletmenet-folytonosság és a vész-helyreállítási (BCDR) stratégia részeként feladatátvételt hajthat végre.

- A BCDR stratégiájának első lépéseként folyamatosan replikálja a helyszíni gépeket az Azure-ba. A felhasználók a helyszíni forrásoldali gépeken futó munkaterhelésekhez és alkalmazásokhoz férnek hozzá.
- Ha szükség van rá, például ha a helyszínen áramkimaradás történik, akkor a replikáló gépek nem működnek az Azure-ba. Az Azure-beli virtuális gépek a replikált adatszolgáltatások használatával jönnek létre.
- Az üzletmenet folytonossága érdekében a felhasználók továbbra is hozzáférhetnek az Azure-beli virtuális gépeken futó alkalmazásokhoz.

A feladatátvétel kétfázisú tevékenység:

- **Feladatátvétel**: az a feladatátvétel, amely létrehoz egy Azure-beli virtuális gépet a kiválasztott helyreállítási pont használatával.
- **Véglegesítés**: a feladatátvételt követően ellenőrizze a virtuális gépet az Azure-ban:
    - Ezután véglegesítheti a feladatátvételt a kijelölt helyreállítási pontra, vagy kijelölhet egy másik pontot a végrehajtáshoz.
    - A feladatátvétel véglegesítése után a helyreállítási pont nem módosítható.


## <a name="connect-to-azure-after-failover"></a>Kapcsolódás az Azure-hoz a feladatátvétel után

Ha az RDP/SSH használatával a feladatátvételt követően létrehozott Azure-beli virtuális gépekhez szeretne csatlakozni, számos követelményt kell megadnia.

**Feladatátvétel** | **Hely** | **Műveletek**
--- | --- | ---
**Windows rendszerű Azure-beli virtuális gép** | A helyszíni gépen a feladatátvétel előtt | **Hozzáférés az interneten keresztül**: engedélyezze az RDP-t. Győződjön meg arról, hogy a TCP-és UDP-szabályok **nyilvánosak**, és az RDP engedélyezve van a **Windows tűzfal**  >  **engedélyezett alkalmazásaiban** található összes profilhoz.<br/><br/> **Hozzáférés helyek közötti VPN-hez**: engedélyezze az RDP-t a gépen. Győződjön meg arról, hogy az RDP engedélyezve van a **Windows tűzfal**  ->  **engedélyezett alkalmazásaiban és szolgáltatásaiban** **tartomány-és magánhálózatok** esetén.<br/><br/>  Győződjön meg arról, hogy az operációs rendszer SAN-szabályzata **OnlineAll** értékre van állítva. [További információk](https://support.microsoft.com/kb/3031135).<br/><br/> Győződjön meg arról, hogy a virtuális gépen nincsenek függőben lévő Windows-frissítések a feladatátvétel elindításakor. Előfordulhat, hogy a Windows Update a feladatátvétel során indul el, és a frissítések elvégzése előtt nem tud majd bejelentkezni a virtuális gépre.
**Windows rendszerű Azure-beli virtuális gép** | Az Azure virtuális gépen feladatátvétel után |  [Nyilvános IP-cím hozzáadása](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) a virtuális gép számára.<br/><br/> A sikertelenül átadott virtuális gépen (és az Azure-alhálózaton, amelyhez csatlakozik) a hálózati biztonsági csoport szabályainak engedélyezniük kell a bejövő kapcsolatokat az RDP-porton.<br/><br/> A **rendszerindítási diagnosztika** ellenőrzésével ellenőrizheti a virtuális gép képernyőképét. Ha nem tud kapcsolatot létesíteni, ellenőrizze, hogy fut-e a virtuális gép, és tekintse át a [hibaelhárítási tippeket](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Linux rendszerű Azure-beli virtuális gép** | A helyszíni gépen a feladatátvétel előtt | Győződjön meg arról, hogy a virtuális gépen a Secure Shell szolgáltatás automatikusan elindul a rendszerindításkor.<br/><br/> Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.
**Linux rendszerű Azure-beli virtuális gép** | Az Azure virtuális gépen feladatátvétel után | Az átadott virtuális gép (és az ahhoz csatlakozó Azure-alhálózat) hálózati biztonsági csoportra vonatkozó szabályainak engedélyeznie kell a bejövő kapcsolatokat az SSH-porton.<br/><br/> [Nyilvános IP-cím hozzáadása](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) a virtuális gép számára.<br/><br/> A virtuális gép képernyőképének megtekintése a **rendszerindítási diagnosztika** szolgáltatásban.<br/><br/>

## <a name="types-of-failover"></a>Feladatátvételi típusok

Site Recovery különböző feladatátvételi lehetőségeket biztosít.

**Feladatátvétel** | **Részletek** | **Helyreállítási** | **Munkafolyamat**
--- | --- | --- | ---
**Feladatátvételi teszt** | Egy olyan részletezés futtatására szolgál, amely a BCDR stratégiát ellenőrzi, adatvesztés vagy állásidő nélkül.| Másolatot készít a virtuális gépről az Azure-ban, és nincs hatással a folyamatban lévő replikációra vagy az éles környezetre. | 1. futtasson feladatátvételi tesztet egyetlen virtuális gépen vagy egy helyreállítási terv több virtuális gépén.<br/><br/> 2. Válassza ki a feladatátvételi teszthez használni kívánt helyreállítási pontot.<br/><br/> 3. Válasszon ki egy Azure-hálózatot, amelyben az Azure-beli virtuális gép a feladatátvételt követően jön létre. A hálózat csak a feladatátvételi teszthez használatos.<br/><br/> 4. Ellenőrizze, hogy a részletezés a várt módon működik-e. Site Recovery automatikusan törli az Azure-ban létrehozott virtuális gépeket a részletezés során.
**Tervezett feladatátvétel – Hyper-V**  | Általában a tervezett állásidőhez használatos.<br/><br/> A forrás virtuális gépek le vannak állítva. A rendszer a legutóbbi adatokat szinkronizálja a feladatátvétel kezdeményezése előtt. | Nulla adatvesztés a tervezett munkafolyamatban. | 1. tervezze meg a leállás-karbantartási időszakot, és értesítse a felhasználókat.<br/><br/> 2. a felhasználók felé irányuló alkalmazások offline állapotba helyezése.<br/><br/> 3. indítson el egy tervezett feladatátvételt a legújabb helyreállítási ponttal. A feladatátvétel nem fut le, ha a gép nem áll le, vagy ha hiba történt.<br/><br/> 4. a feladatátvételt követően győződjön meg arról, hogy az Azure-beli virtuális gép aktív az Azure-ban.<br/><br/> 5. véglegesítse a feladatátvételt a befejezésig. A commit művelet törli az összes helyreállítási pontot.
**Feladatátvétel – Hyper-V** | Általában akkor fut, ha nem tervezett leállás van, vagy az elsődleges hely nem érhető el.<br/><br/> Szükség esetén leállíthatja a virtuális gépet, és szinkronizálhatja a végső módosításokat a feladatátvétel megkezdése előtt.  | Minimális adatvesztés az alkalmazások számára. | 1. kezdeményezzen BCDR tervet. <br/><br/> 2. feladatátvétel kezdeményezése. Azt határozza meg, hogy Site Recovery le kell-e állítani a virtuális gépet, és szinkronizálni/replikálni kell a legutóbbi módosításokat a feladatátvétel elindítása előtt.<br/><br/> 3. az alábbi táblázatban foglaltak szerint több helyreállítási pontra vonatkozó lehetőség is átadható.<br/><br/> Ha nem engedélyezi a virtuális gép leállítását, vagy ha Site Recovery nem tudja leállítani, a rendszer a legújabb helyreállítási pontot használja.<br/>A feladatátvétel akkor is fut, ha a gépet nem lehet leállítani.<br/><br/> 4. a feladatátvételt követően ellenőriznie kell, hogy az Azure virtuális gép aktív-e az Azure-ban.<br/> Ha szükséges, egy másik helyreállítási pontot választhat ki a 24 órás megőrzési időszakból.<br/><br/> 5. véglegesítse a feladatátvételt a befejezésig. A commit művelet törli az összes rendelkezésre álló helyreállítási pontot.
**Feladatátvétel – VMware** | Általában akkor fut, ha nem tervezett leállás van, vagy az elsődleges hely nem érhető el.<br/><br/> Azt is megadhatja, hogy Site Recovery próbálkozzon a virtuális gép leállításának elindításával, valamint a feladatátvétel megkezdése előtt a végső módosítások szinkronizálásával és replikálásával.  | Minimális adatvesztés az alkalmazások számára. | 1. kezdeményezzen BCDR tervet. <br/><br/> 2. feladatátvétel kezdeményezése Site Recoveryról. Itt adhatja meg, hogy a feladatátvétel futtatása előtt a Site Recovery megpróbálja-e elindítani a virtuális gépek leállítását és szinkronizálását.<br/> A feladatátvétel akkor is fut, ha a gépeket nem lehet leállítani.<br/><br/> 3. a feladatátvételt követően győződjön meg arról, hogy a replika Azure-beli virtuális gép aktív az Azure-ban. <br/>Ha szükséges, egy másik helyreállítási pontot választhat ki az 72 óra megőrzési időszakból.<br/><br/> 5. véglegesítse a feladatátvételt a befejezésig. A commit művelet törli az összes helyreállítási pontot.<br/> Windows rendszerű virtuális gépek esetén Site Recovery letiltja a VMware-eszközöket a feladatátvétel során.

## <a name="failover-processing"></a>Feladatátvétel feldolgozása

Bizonyos esetekben a feladatátvételhez további feldolgozásra van szükség, amely körülbelül 8 – 10 percet vesz igénybe. Előfordulhat, hogy a feladatátvételi teszt időpontját a következőre észleli:

* A 9,8-nál régebbi mobilitási szolgáltatást futtató VMware virtuális gépek.
* Fizikai kiszolgálók.
* VMware Linux rendszerű virtuális gépek.
* Fizikai kiszolgálóként védett Hyper-V virtuális gépek.
* Azok a VMware virtuális gépek, amelyeken nincs engedélyezve a DHCP szolgáltatás.
* A következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépek: storvsc, VMBus, storflt, Intelide, ATAPI.

## <a name="recovery-point-options"></a>Helyreállítási pontok beállításai

A feladatátvétel során számos helyreállítási pont lehetőség közül választhat.

**Beállítás** | **Részletek**
--- | ---
**Legutóbbi (legalacsonyabb RPO)** | Ez a beállítás a legalacsonyabb helyreállítási időkorlátot (RPO) adja meg. Először dolgozza fel az Site Recovery szolgáltatásnak elküldett összes adatát, hogy minden virtuális gép számára hozzon létre egy helyreállítási pontot, a feladatátvétel előtt. Ez a helyreállítási pont a feladatátvétel elindítása után Site Recovery replikált összes adattal rendelkezik.
**Legutóbb feldolgozott**  | Ez a beállítás feladatátvételt hajt végre a virtuális gépeken a Site Recovery által feldolgozott legújabb helyreállítási pontra. Egy adott virtuális gép legutóbbi helyreállítási pontjának megtekintéséhez tekintse meg a virtuális gép beállításainak **legutóbbi helyreállítási pontjait** . Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
**Legújabb alkalmazás – konzisztens** |  Ez a beállítás feladatátvételt hajt végre a virtuális gépeken a Site Recovery által feldolgozott legújabb, alkalmazás-konzisztens helyreállítási pontra, ha az alkalmazás-konzisztens helyreállítási pontok engedélyezve vannak. A virtuálisgép-beállításokban keresse meg a legutóbbi helyreállítási pontot.
**A legújabb több virtuális gépre feldolgozva** | Ez a lehetőség olyan helyreállítási tervekhez érhető el, amelyeken engedélyezve van egy vagy több virtuális gépre kiterjedő konzisztencia. Azok a virtuális gépek, amelyeken engedélyezve van a feladatátvétel, a legújabb közös, több virtuális gépre kiterjedő konzisztens helyreállítási pontot adják át. A tervben szereplő többi virtuális gép feladatátvételt hajt végre a legújabb feldolgozott helyreállítási pontra.
**Legújabb multi-VM-alkalmazás – konzisztens** |  Ez a lehetőség olyan helyreállítási tervekhez érhető el, amelyeken engedélyezve van egy vagy több virtuális gépre kiterjedő konzisztencia. A replikációs csoport részét képező virtuális gépek a legújabb közös, több virtuális gépre kiterjedő, alkalmazás-konzisztens helyreállítási pontot adják át. Más virtuális gépek feladatátvétele a legújabb, alkalmazás-konzisztens helyreállítási ponttal történik.
**Egyéni** | Ezzel a beállítással egy adott virtuális gépet átadhat egy adott helyreállítási időpontra. Ez a lehetőség helyreállítási tervekhez nem érhető el.

> [!NOTE]
> A helyreállítási pontok nem telepíthetők át másik Recovery Services-tárba.

## <a name="reprotectionfailback"></a>Ismételt védelem/feladat-visszavétel

Az Azure-ba történő feladatátvétel után a replikált Azure-beli virtuális gépek nem védett állapotban vannak.

- A helyszíni helyre történő visszaállítás első lépéseként el kell indítania az Azure-beli virtuális gépeket a helyszíni környezetbe. Az ismételt védelmi folyamat a feladatátvétel alatt álló gépek típusától függ.
- Miután a gépek az Azure-ból a helyszíni rendszerbe replikálódnak, feladatátvételt futtathat az Azure-ból a helyszíni helyre.
- Miután a gépek újra futnak a helyszínen, engedélyezheti a replikálást, hogy az Azure-ba replikálódjon vész-helyreállításra.

A feladat-visszavétel a következőképpen működik:

- A feladat-visszavételhez a virtuális gépnek legalább egy helyreállítási pontra van szüksége ahhoz, hogy visszaadja a feladatátvételt. Helyreállítási tervben a tervben szereplő összes virtuális gépnek legalább egy helyreállítási pontra van szüksége.
- Javasoljuk, hogy a **legutóbbi** helyreállítási pontot használja a feladat-visszavétel érdekében (ez egy összeomlás-konzisztens pont).
    - Egy alkalmazás-konzisztens helyreállítási pont lehetőség van. Ebben az esetben egyetlen virtuális gép a legújabb elérhető alkalmazás-konzisztens helyreállítási pontra kerül. Egy replikációs csoporttal rendelkező helyreállítási terv esetében minden replikációs csoport a közös rendelkezésre álló helyreállítási pontra lesz helyreállítva.
    - Az alkalmazással konzisztens helyreállítási pontok az idő alatt lehetnek, és az adatvesztés is lehet.
- Az Azure-ból a helyszíni helyre történő feladatátvétel során Site Recovery leállítja az Azure-beli virtuális gépeket. Ha véglegesíti a feladatátvételt, Site Recovery eltávolítja a sikertelen Azure-beli virtuális gépeket az Azure-ban.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/fizikai védelem/feladat-visszavétel

Ha a VMware-gépeket és fizikai kiszolgálókat az Azure-ból a helyszíni környezetbe szeretné újra védelemmel ellátni és visszaadni, a feladat-visszavételi infrastruktúrára van szükség, és számos követelmény van.

- **Ideiglenes Process Server az Azure-ban**: az Azure-ból való feladat-visszavételhez állítson be egy Azure-beli virtuális gépet, amely az Azure-ból történő replikáció kezelésére szolgáló folyamat-kiszolgálóként működik. Ez a virtuális gép a feladatok visszaadását követően törölhető.
- **VPN-kapcsolat**: a feladat-visszavétel érdekében VPN-kapcsolatra (vagy ExpressRoute) van szükség az Azure-hálózatról a helyszíni helyre.
- **Különálló fő célkiszolgáló**: alapértelmezés szerint a helyszíni VMWare virtuális gépen a konfigurációs kiszolgálóval telepített fő célkiszolgáló kezeli a feladat-visszavételt. Ha nagy mennyiségű forgalmat kell visszaadnia, hozzon létre egy külön helyszíni fő célkiszolgáló erre a célra.
- **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. A rendszer automatikusan létrehozza ezt a házirendet, amikor létrehoz egy replikációs házirendet a helyszínről az Azure-ba.
    - Ezt a házirendet a rendszer automatikusan társítja a konfigurációs kiszolgálóhoz.
    - Ez a szabályzat nem módosítható.
    - Szabályzat értékei: RPO küszöbértéke – 15 perc; Helyreállítási pont megőrzése – 24 óra; Alkalmazás-konzisztens pillanatkép gyakorisága – 60 perc.

További információ a VMware/fizikai ismételt védelemről és a feladat-visszavételről:
- [Tekintse át](vmware-azure-reprotect.md#before-you-begin) az ismételt védelem és a feladat-visszavétel további követelményeit.
- Folyamat-kiszolgáló [üzembe helyezése](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) az Azure-ban.
- [Helyezzen üzembe](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) egy különálló fő célkiszolgáló-kiszolgálót.

Amikor az Azure-beli virtuális gépeket a helyszíni környezetbe helyezi újra, megadhatja, hogy az eredeti helyre vagy egy másik helyre kívánja-e visszavenni a feladatokat.

- **Eredeti helyre történő helyreállítás**: az Azure-ból nem lehet visszatérni ugyanarra a forrásra a helyszíni gépre, ha van ilyen. Ebben az esetben csak a módosításokat replikálja a rendszer a helyszíni környezetbe.
- **Másodlagos hely helyreállítása**: Ha a helyszíni gép nem létezik, az Azure-ból egy másik helyre is visszatérhet. Ha az Azure-beli virtuális gépet a helyszíni rendszerre kívánja újra védetté tenni, a helyszíni gép létrejön. A teljes adatreplikálás az Azure-ból a helyszíni rendszerbe történik. – [Tekintse át](concepts-types-of-failback.md) a hely feladat-visszavételének követelményeit és korlátozásait.



## <a name="hyper-v-reprotectionfailback"></a>Hyper-V-védelem/feladat-visszavétel

A Hyper-V virtuális gépek ismételt védetté és feladatátvétele az Azure-ból a helyszíni rendszerbe:

- Csak a Storage-fiókkal replikált Hyper-V virtuális gépek végezhetnek biztonsági mentést. A felügyelt lemezek használatával replikált Hyper-V virtuális gépek feladat-visszavétele nem támogatott.
- A helyszíni Hyper-V-gazdagépeket (vagy a System Center VMM, ha használják) csatlakozni kell az Azure-hoz.
- A tervezett feladat-visszavételt az Azure-ból a helyszíni környezetbe futtathatja.
- A Hyper-V virtuális gépek feladat-visszavételéhez nincs szükség speciális összetevők beállítására.
- A tervezett feladatátvétel során kiválaszthatja az adatokat a feladat-visszavétel előtt szinkronizálni kívánt beállításokat:
    - **Adatszinkronizálás a feladatátvétel előtt**: Ez a beállítás a virtuális gépek leállását is lekicsinyíti, mivel a gépeket nem leállítás nélkül szinkronizálja.
        - 1. fázis: pillanatképet készít az Azure-beli virtuális gépről, és átmásolja a helyszíni Hyper-V-gazdagépre. A gép továbbra is fut az Azure-ban.
        - 2. fázis: leállítja az Azure-beli virtuális gépet, hogy ne történjen új változás. A különbözeti változások végső készlete a helyszíni kiszolgálóra kerül, és a helyszíni virtuális gép elindult.
    - **Adatok szinkronizálása csak feladatátvétel során**: Ez a beállítás gyorsabb, mert várható, hogy a lemez nagy része megváltozott, így nem végeznek ellenőrzőösszeg-számításokat. Elvégzi a lemez letöltését. Azt javasoljuk, hogy ezt a beállítást akkor használja, ha a virtuális gép egy ideig (egy hónap vagy több) fut az Azure-ban, vagy ha a helyszíni virtuális gép törölve lett.

[További](hyper-v-azure-failback.md) információ a Hyper-V ismételt védelméről és a feladat-visszavételről.

Amikor az Azure-beli virtuális gépeket a helyszíni környezetbe helyezi újra, megadhatja, hogy az eredeti helyre vagy egy másik helyre kívánja-e visszavenni a feladatokat.

- **Eredeti helyre történő helyreállítás**: az Azure-ból nem lehet visszatérni ugyanarra a forrásra a helyszíni gépre, ha van ilyen. Ebben az esetben az előző eljárásban ismertetett szinkronizálási lehetőségek egyikét kell választania.
- **Másodlagos hely helyreállítása**: Ha a helyszíni gép nem létezik, az Azure-ból egy másik helyre is visszatérhet. Ha az Azure-beli virtuális gépet a helyszíni rendszerre kívánja újra védetté tenni, a helyszíni gép létrejön. Ha ezt a beállítást választja, javasoljuk, hogy a feladatátvétel előtt szinkronizálja az adatokat.
- [Tekintse át](hyper-v-azure-failback.md) a hely feladat-visszavételére vonatkozó követelményeket és korlátozásokat.


A helyszíni helyre való visszalépést követően engedélyezze a **visszirányú replikálást** a virtuális gép Azure-ba való replikálásának megkezdéséhez, a ciklus befejezéséhez.




## <a name="next-steps"></a>Következő lépések
- [Adott VMWare virtuális gépek](vmware-azure-tutorial-failover-failback.md) feladatátvétele
- [Adott Hyper-V virtuális gépek](hyper-v-azure-failover-failback-tutorial.md)feladatátvétele.
- [Hozzon létre](site-recovery-create-recovery-plans.md) egy helyreállítási tervet.
- [Helyreállítási tervben szereplő virtuális gépek](site-recovery-failover.md)feladatátvétele.
- [Felkészülés a következőre:](vmware-azure-failback.md) VMware-ismételt védelem és feladat-visszavétel.
- A [Hyper-V virtuális gépek](hyper-v-azure-failback.md)feladatátvétele.
