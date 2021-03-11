---
title: MABS (Azure Backup Server) v3 UR1 védelmi mátrix
description: Ez a cikk egy támogatási mátrixot tartalmaz, amely felsorolja az összes olyan munkaterhelést, adattípust és telepítést, amelyet Azure Backup Server véd.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: d8f2a38db468ec57cdc0a4f0813fe7ae8e341d52
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609770"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>MABS (Azure Backup Server) v3 UR1 védelmi mátrix

Ez a cikk felsorolja az Azure Backup Server által védhető különböző kiszolgálókat és munkaterheléseket. A következő mátrix felsorolja, hogy mit lehet védeni a Azure Backup Serverokkal.

Használja a következő mátrixot a MABS v3 UR1:

* Számítási feladatok – a technológia számítási feladatának típusa.

* Version – támogatott MABS-verzió a munkaterhelésekhez.

* MABS-telepítés – a számítógép/hely, ahová a MABS telepíteni kívánja.

* Védelem és helyreállítás – felsorolja a munkaterhelésekkel kapcsolatos részletes információkat, például a támogatott tárolókat vagy a támogatott üzembe helyezést.

>[!NOTE]
>Az 32 bites védelmi ügynök támogatása elavult a MABS v3 UR1. Lásd: [32 bites védelmi ügynök elavulása](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation).

## <a name="protection-support-matrix"></a>A támogatási mátrix

A következő részben a MABS védelmi támogatási mátrixát részletezjük:

* [Alkalmazások biztonsági mentése](#applications-backup)
* [Virtuális gép biztonsági mentése](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Alkalmazások biztonsági mentése

| **Számítási feladat**               | **Verzió**                                                  | **Azure Backup Server telepítés**                       | **Támogatott Azure Backup Server** | **Védelem és helyreállítás**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Ügyfélszámítógépek (64 bites) | Windows 10                                                  | Fizikai kiszolgáló  <br><br>    Hyper-V virtuális gép   <br><br>   VMware virtuális gép | V3 UR1                            | Kötet, megosztás, mappa, fájlok, deduplikált kötetek   <br><br>   A védett köteteknek NTFS fájlrendszerűeknek kell lenniük. A FAT és FAT32 nem támogatottak.  <br><br>    A köteteknek legalább 1 GB méretűnek kell lenniük. A Azure Backup Server a Kötet árnyékmásolata szolgáltatás (VSS) segítségével készíti el az adatok pillanatképét, és a pillanatkép csak akkor működik, ha a kötet legalább 1 GB. |
| Kiszolgálók (64 bites)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Azure virtuális gép (ha a számítási feladat Azure-beli virtuális gépként fut)  <br><br>    Fizikai kiszolgáló  <br><br>    Hyper-V virtuális gép <br><br>     VMware virtuális gép  <br><br>    Azure Stack | V3 UR1                            | Kötet, megosztás, mappa, fájl <br><br>    Deduplikált kötetek (csak NTFS) <br><br>Ha egy WS 2016 NTFS rendszerű, a Windows Server 2019-on futó MABS v3-es verzióját használja, akkor a helyreállítást érintheti. Kijavítjuk a helyreállítást olyan nem deduplikált módon, amely a MABS későbbi verzióiban lesz része. Ha erre a javításra van szüksége a MABS v3 UR1, forduljon a MABS támogatási szolgálatához.<br><br> Ha egy WS 2019 NTFS rendszerű, deduplikált kötetet véd a Windows Server 2016-es MABS v3 verzióval, a biztonsági mentések és a visszaállítások nem lesznek kiválasztva. Ez azt jelenti, hogy a biztonsági másolatok több helyet foglalnak el a MABS-kiszolgálón, mint az eredeti NTFS deduped kötet.   <br><br>   Rendszerállapot és operációs rendszer nélküli (nem támogatott, ha a számítási feladat Azure-beli virtuális gépként fut) |
| Kiszolgálók (64 bites)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (telepítenie kell a [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)-t) | Fizikai kiszolgáló  <br><br>    Hyper-V virtuális gép  <br><br>      VMware virtuális gép  <br><br>   Azure Stack | V3 UR1                            | Kötet, megosztás, mappa, fájl, rendszerállapot/operációs rendszer nélküli        |
| SQL Server                | SQL Server 2019, 2017, 2016 és [támogatott SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 és támogatott [SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | Fizikai kiszolgáló  <br><br>     Hyper-V virtuális gép   <br><br>     VMware virtuális gép  <br><br>   Azure virtuális gép (ha a munkaterhelés Azure virtuális gépként fut)  <br><br>     Azure Stack | V3 UR1                            | Az összes központi telepítési forgatókönyv: adatbázis       <br><br>  A MABS v3 UR1 támogatja az SQL-adatbázisok ReFS-köteteken keresztüli biztonsági mentését     <br><br>     A MABS nem támogatja a Windows Server 2012 Scale-Out-fájlkiszolgálók (SOFS) által üzemeltetett SQL Server-adatbázisokat. <br><br>   A MABS nem tudja biztosítani az SQL Server elosztott rendelkezésre állási csoport (DAG) vagy a rendelkezésre állási csoport (AG) számára, hogy a feladatátvevő fürtön a szerepkör neve eltér az SQL megnevezett AG-től.       |
| Exchange                   | Exchange 2019, 2016                                         | Fizikai kiszolgáló   <br><br>   Hyper-V virtuális gép  <br><br>      VMware virtuális gép  <br><br>   Azure Stack  <br><br>    Azure virtuális gép (ha a munkaterhelés Azure virtuális gépként fut) | V3 UR1                            | Védelem (az összes központi telepítési forgatókönyv): önálló Exchange-kiszolgáló, adatbázis egy adatbázis-elérhetőségi csoportban (DAG)  <br><br>    Helyreállítás (az összes központi telepítési forgatókönyv): Egy adatbázis-elérhetőségi csoportban található postaláda és postaláda-adatbázisok    <br><br>  Az Exchange ReFS-en keresztüli biztonsági mentése támogatott a MABS v3 UR1 |
| SharePoint                 | SharePoint 2019, 2016 legújabb SPs-vel                       | Fizikai kiszolgáló  <br><br>    Hyper-V virtuális gép <br><br>    VMware virtuális gép  <br><br>   Azure virtuális gép (ha a munkaterhelés Azure virtuális gépként fut)   <br><br>   Azure Stack | V3 UR1                            | Védelem (az összes központi telepítési forgatókönyv): Farm, előtér-Webkiszolgálói tartalom  <br><br>    Helyreállítás (az összes központi telepítési forgatókönyv): Farm, adatbázis, webalkalmazás, fájl vagy listaelem, SharePoint-keresés, előtér-webkiszolgáló  <br><br>    A tartalom-adatbázisok SQL Server 2012 AlwaysOn szolgáltatást használó SharePoint-farmok védelme nem támogatott. |

## <a name="vm-backup"></a>Virtuális gép biztonsági mentése

| **Számítási feladat**                                                 | **Verzió**                                             | **Azure Backup Server telepítés**                      | **Támogatott Azure Backup Server** | **Védelem és helyreállítás**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-V gazdagép – MABS védelmi ügynök a Hyper-V-gazdagépen, a fürtön vagy a virtuális gépen | Windows Server 2019, 2016, 2012 R2, 2012               | Fizikai kiszolgáló  <br><br>    Hyper-V virtuális gép <br><br>    VMware virtuális gép | V3 UR1                             | Védelem: Hyper-V számítógépek, fürt megosztott kötetei (CSV)  <br><br>    Helyreállítás: virtuális gép, csak Windows, kötetek és virtuális merevlemezek számára elérhető fájlok és mappák elemszintű helyreállítása |
| VMware virtuális gépek                                                  | VMware Server 5,5, 6,0 vagy 6,5, 6,7 (licencelt verzió) | Hyper-V virtuális gép  <br><br>   VMware virtuális gép         | V3 UR1                             | Védelem: VMware virtuális gépek a fürt megosztott kötetein (CSV), az NFS-en és a TÁROLÓHÁLÓZATI tárolóban   <br><br>     Helyreállítás: virtuális gép, csak Windows, kötetek és virtuális merevlemezek számára elérhető fájlok és mappák elemszintű helyreállítása <br><br>    A VMware Vapp nem támogatottak. |

>[!NOTE]
> A MABS nem támogatja a csatlakoztatott lemezekkel vagy távoli VHD-ket használó virtuális gépek biztonsági mentését. Javasoljuk, hogy ezekben a forgatókönyvekben a MABS használatával használja a vendég szintű biztonsági mentést, és telepítsen egy ügynököt a virtuális gépre az adatok biztonsági mentéséhez.

## <a name="linux"></a>Linux

| **Számítási feladat** | **Verzió**                               | **Azure Backup Server telepítés**                      | **Támogatott Azure Backup Server** | **Védelem és helyreállítás**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | [Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) vagy [VMware](backup-azure-backup-server-vmware.md) vendégként futó Linux | Fizikai kiszolgáló, helyszíni Hyper-V virtuális gép, Windows rendszerű virtuális gép VMware-ben | V3 UR1                             | A Hyper-V-nek Windows Server 2012 R2, Windows Server 2016 vagy Windows Server 2019 rendszeren kell futnia. Védelem: teljes virtuális gép   <br><br>   Helyreállítás: az egész virtuális gép   <br><br>    Csak a fájlkonzisztens pillanatképek támogatottak.    <br><br>   A támogatott Linux-disztribúciók és-verziók teljes listájáért tekintse meg a [Linux az Azure által támogatott disztribúciókkal foglalkozó](../virtual-machines/linux/endorsed-distros.md)cikket. |

## <a name="azure-expressroute-support"></a>Azure ExpressRoute-támogatás

Az Azure ExpressRoute-on keresztül biztonsági mentést készíthet az adatairól a nyilvános (a régi áramkörökhöz elérhető) és a Microsoft-partnerek számára. A privát kapcsolaton keresztüli biztonsági mentés nem támogatott.

Nyilvános társítás esetén: a következő tartományokhoz/címekhez való hozzáférés biztosítása:

* URL-címek
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-címek
  * 20.190.128.0/18
  * 40.126.0.0/18

A Microsoft-partnerekkel válassza ki a következő szolgáltatásokat/régiókat és a vonatkozó közösségi értékeket:

* Azure Active Directory (12076:5060)
* Microsoft Azure régió (az Recovery Services-tároló helyének megfelelően)
* Azure Storage (az Recovery Services-tároló helyének megfelelően)

További információkért tekintse meg a [ExpressRoute útválasztási követelményeit](../expressroute/expressroute-routing.md).

>[!NOTE]
>Az új áramkörök esetében a nyilvános társítás elavult.

## <a name="cluster-support"></a>Fürttámogatás

A Azure Backup Server a következő fürtözött alkalmazásokban lévő adatvédelmet tudja biztosítani:

* Fájlkiszolgálók

* SQL Server

* Hyper-V – ha a Hyper-V fürtöt kibővített MABS védelmi ügynök használatával védi, a védett Hyper-V munkaterhelésekhez nem adhat hozzá másodlagos védelmet.

* Az Exchange Server-Azure Backup Server képes biztosítani a nem megosztott lemezes fürtöket a támogatott Exchange Server-verziókhoz (fürt folyamatos replikációja), és a helyi folyamatos replikálásra konfigurált Exchange Servert is képes védelemmel ellátni.

* A SQL Server-Azure Backup Server nem támogatja a fürt megosztott kötetein (CSV) tárolt SQL Server-adatbázisok biztonsági mentését.

>[!NOTE]
>A MABS csak a fürt megosztott kötetein (CSV) lévő Hyper-V virtuális gépek védelmét támogatja. A megosztott fürtköteteken futtatott egyéb munkaterhelések védelme nem támogatott.

A Azure Backup Server a MABS-kiszolgálóval azonos tartományban található, illetve egy gyermek vagy megbízható tartományba tartozó munkaterhelések elleni védelemmel rendelkezhet. Ha nem megbízható tartományokban vagy munkacsoportokban lévő adatforrásokat kíván védelemmel ellátni, használja az NTLM-t vagy a tanúsítványalapú hitelesítést egyetlen kiszolgáló esetén, vagy csak a fürt tanúsítvány-hitelesítését.

## <a name="data-protection-issues"></a>Adatvédelmi problémák

* A MABS nem tud biztonsági másolatot készíteni a virtuális gépekről megosztott meghajtókkal (amelyek esetleg más virtuális gépekhez vannak csatolva), mivel a Hyper-V VSS-író nem tud biztonsági másolatot készíteni a megosztott VHD-k által készített biztonsági másolatokból.

* Egy megosztott mappa védelemmel való ellátásakor a megosztott mappa elérési útja tartalmazza a kötet logikai elérési útját. Ha áthelyezik a megosztott mappát, a védelem sikertelen lesz. Ha egy védett megosztott mappát kell áthelyeznie, távolítsa el a védelmi csoportból, majd az áthelyezés után adja hozzá a védelemhez.  Továbbá, ha módosítja egy védett adatforrás elérési útját egy olyan köteten, amely a titkosított fájlrendszert (EFS) használja, és az új fájl elérési útja meghaladja az 5120 karaktert, az adatvédelem sikertelen lesz.

* A védett számítógépek tartománya nem módosítható, és a védelem megszakítás nélkül folytatható. Emellett a védett számítógépek tartománya nem módosítható, és a meglévő replikákat és helyreállítási pontokat a számítógéppel együtt társíthatja a számítógép újravédelmekor. Ha módosítania kell egy védett számítógép tartományát, először távolítsa el a számítógépen lévő adatforrásokat a védelemből. Ezután gondoskodjon arról, hogy a számítógép adatforrása új tartománnyal is védve legyen.

* A védett számítógépek neve nem módosítható, és a védelem megszakítás nélkül folytatható. Emellett a védett számítógépek neve nem módosítható, és a meglévő replikákat és helyreállítási pontokat a számítógéppel együtt társíthatja a számítógép újravédelmekor. Ha mégis módosítania kell egy védett számítógép nevét, először szüntesse meg a számítógépen lévő adatforrások védelmét. A név módosítását követően újra biztosítsa az adatforrás védelmét a számítógépen.

* A MABS automatikusan azonosítja a védett számítógép időzónáját a védelmi ügynök telepítése során. Ha egy védett számítógépet a védelem konfigurálását követően áthelyeznek egy másik időzónába, mindenképp ellenőrizze, hogy módosította-e a számítógép idejét a Vezérlőpulton. Ezután frissítse az időzónát a MABS-adatbázisban.

* A MABS a MABS-kiszolgálóval azonos tartományban, illetve a gyermek-és megbízható tartományokban lévő munkaterheléseket is képes védelemmel ellátni. A munkacsoportokban és a nem megbízható tartományokban a következő munkaterheléseket is biztosíthatja az NTLM vagy a tanúsítványalapú hitelesítés használatával:

  * SQL Server
  * Fájlkiszolgáló
  * Hyper-V

  A munkaterhelések futhatnak önálló kiszolgálón vagy egy fürtkonfiguráción. A nem megbízható tartományba tartozó munkaterhelések elleni védelemért lásd: a [munkacsoportokban és](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm) a nem megbízható tartományokban lévő számítógépek előkészítése a támogatott és a szükséges hitelesítés pontos részleteiért.

## <a name="unsupported-data-types"></a>Nem támogatott adattípusok

A MABS nem támogatja a következő adattípusok védelmét:

* Rögzített hivatkozások

* Újraelemzési pontok, beleértve az elosztott fájlrendszeri hivatkozásokat és a csatlakozási pontokat

* Csatlakoztatási pontok metaadatai – A védelmi csoportok tartalmazhatnak csatlakoztatási pontokkal rendelkező adatokat. Ebben az esetben a DPM védi a csatlakoztatási pont által célzott csatlakoztatott kötetet, azonban nem védi a csatlakoztatási pont metaadatait. Ha csatlakoztatási pontokat tartalmazó adatokat állít helyre, újra létre kell hoznia manuálisan a csatlakoztatási pontok hierarchiáját.

* A csatlakoztatott kötetekben lévő csatlakoztatott köteteken lévő adatok

* Lomtár

* Lapozófájlok

* A rendszerkötet információit tartalmazó mappa. A számítógép rendszerinformációinak védelemmel való ellátásához a számítógép rendszerállapotát kell kijelölnie a védelmi csoport tagjaként.

* Nem NTFS kötetek

* A Windows Vista rendszerből származó rögzített vagy szimbolikus hivatkozásokat tartalmazó fájlok.

* A UPD üzemeltető fájlmegosztás információi (felhasználói profil lemezei)

* A bármely alábbi attribútumkombinációval rendelkező fájlok:

  * Titkosítás és újraelemzés

  * Titkosítás és egypéldányos tárolás (SIS)

  * Titkosítás és kisbetű/nagybetű megkülönböztetése

  * Titkosítás és ritkán használt

  * Kisbetű/nagybetű megkülönböztetése és SIS

  * Tömörítés és SIS

## <a name="next-steps"></a>Következő lépések

* [Támogatási mátrix a Microsoft Azure Backup Server vagy System Center DPM való biztonsági mentéshez](backup-support-matrix-mabs-dpm.md)
