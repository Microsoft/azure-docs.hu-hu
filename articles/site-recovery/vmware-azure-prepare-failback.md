---
title: VMware virtuális gépek előkészítése a Azure Site Recovery ismételt védelmére és visszavételére
description: A VMware virtuális gépek feladatátvétel utáni visszavételének előkészítése Azure Site Recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: f3e352577d65bda021f886d6612c8c0101922003
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727506"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>VMware rendszerű virtuális gépek védelmének újbóli beállítása és feladat-visszavétele

A helyszíni VMware virtuális gépek vagy fizikai kiszolgálók Azure-ba történő [feladatátvétele](site-recovery-failover.md) után a feladatátvételt követően létrehozott Azure-beli virtuális gépeket újra kell védetté tenni, hogy azok a helyszíni helyre replikálódnak. Az Azure-ból a helyszíni környezetbe való replikálással visszatérhet, ha készen áll az Azure-ból a helyszíni feladatátvételre.

A folytatás előtt gyors áttekintést kaphat arról, hogyan végezheti el az Azure-ból a helyszíni helyre való visszatérést.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Ismételt védelem/feladat-visszavételi összetevők

Az Azure-ból történő ismételt védelem és a feladat-visszavétel előtt számos összetevőre és beállításra van szükség.

| **Összetevő**| **Részletek** |
| --- | --- |
| **Helyszíni konfigurációs kiszolgáló** | A helyszíni konfigurációs kiszolgálónak futnia kell, és kapcsolódnia kell az Azure-hoz.<br/><br/> Az a virtuális gép, amelyről nem tud visszatérni, léteznie kell a konfigurációs kiszolgáló adatbázisában. Ha a katasztrófa hatással van a konfigurációs kiszolgálóra, állítsa vissza ugyanazzal az IP-címmel, hogy a feladat-visszavétel is működjön.<br/><br/>  Ha a replikált gépek IP-címeit megtartotta a feladatátvétel, a helyek közötti kapcsolat (vagy a ExpressRoute-kapcsolat) az Azure-beli virtuális gépek és a konfigurációs kiszolgáló feladat-visszavételi hálózati adaptere között kell létrehozni. A megőrzött IP-címek esetében a konfigurációs kiszolgálónak két hálózati adapterre van szüksége – egyet a forrásoldali számítógép-kapcsolathoz, egyet pedig az Azure feladat-visszavételi kapcsolathoz. Ezzel elkerülhető, hogy a forrás és a feladatátvétel alatt álló virtuális gépek alhálózati címtartományok átfedésben legyenek. |
| **A Process Server az Azure-ban** | A helyszíni helyhez való visszatéréshez szüksége van egy Azure-beli Process Serverre.<br/><br/> A Process Server adatokat fogad a védett Azure-beli virtuális gépről, és elküldi azt a helyszíni helyre.<br/><br/> Kis késleltetésű hálózatra van szükség a Process Server és a védett virtuális gép között, ezért javasoljuk, hogy a folyamat-kiszolgálót az Azure-ban a nagyobb replikációs teljesítmény érdekében telepítse.<br/><br/> A megvalósíthatóság érdekében a helyszíni folyamat-kiszolgálót és a ExpressRoute is használhatja.<br/><br/> A folyamat-kiszolgálónak abban az Azure-hálózaton kell lennie, amelyben a feladatátvételt átvevő virtuális gép található. A folyamat-kiszolgálónak képesnek kell lennie kommunikálni a helyszíni konfigurációs kiszolgálóval és a fő célkiszolgálón is. |
| **Fő célkiszolgáló elkülönítése** | A fő célkiszolgáló fogadja a feladat-visszavételi adatok fogadását, és alapértelmezés szerint a Windows fő célkiszolgáló fut a helyszíni konfigurációs kiszolgálón.<br/><br/> A fő célkiszolgáló legfeljebb 60 lemezzel csatlakoztatható. A sikertelenül működő virtuális gépek több mint 60 lemezt használnak, vagy ha nagy mennyiségű forgalmat hajtanak végre, a feladat-visszavételhez hozzon létre egy külön fő célkiszolgáló-kiszolgálót.<br/><br/> Ha a gépeket egy replikációs csoportba gyűjtik a több virtuális gépre kiterjedő konzisztencia esetében, akkor a virtuális gépnek mind a Windows, mind a Linux rendszernek kell lennie. Miért? Mivel a replikációs csoportban lévő összes virtuális gépnek ugyanazt a fő célkiszolgáló-kiszolgálót kell használnia, és a fő célkiszolgáló azonos operációs rendszerrel kell rendelkeznie (azonos vagy magasabb verziójú), mint a replikált gépeken.<br/><br/> A fő célkiszolgáló nem rendelkezhet pillanatképekkel a lemezekről, ellenkező esetben az ismételt védelem és a feladat-visszavétel nem fog működni.<br/><br/> A fő cél nem rendelkezhet Paravirtual SCSI-vezérlővel. A vezérlő csak LSI logikai vezérlő lehet. LSI logikai vezérlő nélkül az ismételt védelem sikertelen lesz. |
| **Feladat-visszavételi replikációs házirend** | A helyszíni helyre történő replikáláshoz feladat-visszavételi szabályzatra van szükség. Ez a szabályzat automatikusan létrejön, amikor létrehoz egy replikációs házirendet az Azure-ba.<br/><br/> A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. A RPO küszöbértéke 15 perc, a helyreállítási pontok megőrzési ideje 24 óra, az alkalmazás-konzisztens pillanatkép gyakorisága pedig 60 perc. A szabályzat nem szerkeszthető. |
| **Helyek közötti VPN/ExpressRoute privát társítása** | Az ismételt védelem és a feladat-visszavétel helyek közötti VPN-kapcsolatra van szüksége, vagy ExpressRoute az adatreplikáláshoz. |


## <a name="ports-for-reprotectionfailback"></a>Ismételt védelemre vagy feladat-visszavételre vonatkozó portok

Egy számú portnak nyitva kell lennie az ismételt védelemhez/feladat-visszavételhez. A következő ábra a portokat és az ismételt védelem/feladat-visszavétel folyamatát szemlélteti.

![Feladatátvételi és feladat-visszavételi portok](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Folyamat-kiszolgáló üzembe helyezése az Azure-ban

1. [Folyamat-kiszolgáló beállítása](vmware-azure-set-up-process-server-azure.md) az Azure-ban feladat-visszavételhez.
2. Győződjön meg arról, hogy az Azure-beli virtuális gépek elérik a folyamat-kiszolgálót. 
3. Győződjön meg arról, hogy a két hálózat közötti pont-pont típusú VPN-kapcsolat vagy a ExpressRoute-kiszolgáló elegendő sávszélességgel rendelkezik ahhoz, hogy az adatok a Process Serverről a helyszíni helyre legyenek küldve.

## <a name="deploy-a-separate-master-target-server"></a>Különálló fő célkiszolgáló üzembe helyezése

1. Jegyezze fel a fő célkiszolgáló [követelményeit és korlátozásait](#reprotectionfailback-components).
2. Hozzon létre egy Windows vagy [Linux](vmware-azure-install-linux-master-target.md) [rendszerű](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) fő célkiszolgáló-kiszolgálót, hogy az megfeleljen az újravédelemmel ellátni kívánt virtuális gépek operációs rendszerének és a feladatátvételi műveletnek.
3. Győződjön meg arról, hogy nem használja a fő célkiszolgáló tárolási vMotion, vagy a feladat-visszavétel sikertelen lehet. A virtuális gép nem indítható el, mert a lemezek nem érhetők el.
    - Ennek megelőzése érdekében zárja ki a fő célkiszolgáló a vMotion listájáról.
    - Ha egy fő cél az ismételt védelem után egy tárolási vMotion tevékenységet végez, a fő célkiszolgáló számára csatolt védett virtuálisgép-lemezek átkerülnek a vMotion feladat céljára. Ha ezt követően próbálkozik vissza, a lemez leválasztása meghiúsul, mert a lemezek nem találhatók. Ezt követően nehéz megkeresni a Storage-fiókok lemezeit. Ha ez történik, keresse meg őket manuálisan, és csatolja őket a virtuális géphez. Ezt követően a helyszíni virtuális gép indítható el.

4. Adjon hozzá egy adatmegőrzési meghajtót a meglévő Windows fő célkiszolgáló számára. Adjon hozzá egy új lemezt, és formázza a meghajtót. Az adatmegőrzési meghajtó segítségével leállíthatja azokat az időpontokat, amikor a virtuális gép a helyszíni helyre replikálódik. Jegyezze fel ezeket a feltételeket. Ha nem teljesülnek, a meghajtó nem jelenik meg a fő célkiszolgáló számára:
    - A kötet nem használatos más célra, például replikációs célra, és nincs zárolási módban.
    - A kötet nem gyorsítótár-kötet. A Process Server és a fő cél egyéni telepítési kötete nem jogosult az adatmegőrzési kötetre. Ha a Process Server és a fő cél egy kötetre van telepítve, a kötet a fő cél gyorsítótár-kötete.
    - A kötet fájlrendszerének típusa nem FAT vagy FAT32.
    - A kötet kapacitása nem nulla.
    - A Windows alapértelmezett megőrzési kötete az R-kötet.
    - A Linux alapértelmezett megőrzési kötete a/mnt/Retention.

5. Ha meglévő folyamat-kiszolgálót használ, vegyen fel egy meghajtót. Az új meghajtónak meg kell felelnie az utolsó lépés követelményeinek. Ha az adatmegőrzési meghajtó nincs jelen, a portálon nem jelenik meg a kiválasztás legördülő listában. Miután hozzáadta a meghajtót a helyszíni fő célhoz, akár 15 percet is igénybe vehet, hogy a meghajtó megjelenjen a portálon a kijelölésben. A konfigurációs kiszolgálót frissítheti, ha a meghajtó 15 perc elteltével nem jelenik meg.
6. Telepítse a VMware-eszközöket, vagy nyissa meg a VM-Tools eszközt a fő célkiszolgálón. Az eszközök nélkül a fő cél ESXi-gazdagépén lévő adattárolók nem észlelhetők.
7. Állítsa be a lemezt. EnableUUID = True beállítás a fő cél virtuális gép konfigurációs paraméterében a VMware-ben. Ha ez a sor nem létezik, adja hozzá. Erre a beállításra akkor van szükség, ha konzisztens UUID-t biztosít a VMDK, hogy az megfelelően legyen csatolva.
8. VCenter Server hozzáférési követelmények keresése:
    - Ha a virtuális gép, amelyről vissza szeretne térni, VMware vCenter Server által felügyelt ESXi-gazdagépen található, a fő célkiszolgálón hozzá kell férnie a helyszíni virtuális gép VMDK-fájljához, hogy a replikált adatok a virtuális gép lemezei legyenek. Győződjön meg arról, hogy a helyszíni virtuálisgép-adattároló csatlakoztatva van a fő célkiszolgáló olvasási/írási hozzáféréssel.
    - Ha a virtuális gép nem egy VMware vCenter Server által kezelt ESXi-gazdagépen található, Site Recovery új virtuális gépet hoz létre az ismételt védelem során. Ez a virtuális gép az ESXi-gazdagépen jön létre, amelyen létrehozza a fő célkiszolgáló virtuális gépet. Gondosan válassza ki az ESXi-gazdagépet, hogy létrehozza a virtuális gépet a kívánt gazdagépen. A virtuális gép merevlemezének olyan adattárolón kell lennie, amelyhez hozzáfér a fő célkiszolgálót futtató gazdagép.
    - Egy másik lehetőség, ha a helyszíni virtuális gép már létezik a feladat-visszavételhez, törölje a feladat-visszavétel előtt. A feladat-visszavétel Ezután létrehoz egy új virtuális gépet ugyanazon a gazdagépen, mint a fő célként szolgáló ESXi-gazdagép. Ha egy másik helyre végzi a feladatátvételt, az adatok ugyanarra az adattárolóra és a helyszíni fő célkiszolgáló által használt ESXi-gazdagépre lesznek helyreállítva.
9. A VMware virtuális gépeknek visszakerülő fizikai gépek esetében a gép ismételt védetté megkezdése előtt el kell végeznie annak a gazdagépnek a felderítését, amelyen a fő célkiszolgáló fut.
10. Győződjön meg arról, hogy az ESXi-gazdagép, amelyen a fő célként megadott virtuális gép rendelkezik legalább egy, a hozzá csatolt virtuálisgép-fájlrendszerbeli (VMFS) adattárral. Ha nincsenek csatolva VMFS adattárolók, akkor az adattároló bemenete üres, és a művelet nem folytatható.


## <a name="next-steps"></a>Következő lépések

Egy virtuális gép [újravédése](vmware-azure-reprotect.md) .
