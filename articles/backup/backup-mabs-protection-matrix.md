---
title: MABS (Azure Backup Server) v3 UR1 védelmi mátrix
description: Ez a cikk egy támogatási mátrixot tartalmaz, amely felsorolja az összes olyan munkaterhelést, adattípust és telepítést, amelyet Azure Backup Server véd.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: 2c7caad1d377070d4d4ac8436c1d8c08e786dc44
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986836"
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
| Kiszolgálók (64 bites)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Azure virtuális gép (ha a számítási feladat Azure-beli virtuális gépként fut)  <br><br>    Fizikai kiszolgáló  <br><br>    Hyper-V virtuális gép <br><br>     VMware virtuális gép  <br><br>    Azure Stack | V3 UR1                            | Kötet, megosztás, mappa, fájl <br><br>    Deduplikált kötetek (csak NTFS)  <br><br>   Rendszerállapot és operációs rendszer nélküli (nem támogatott, ha a számítási feladat Azure-beli virtuális gépként fut) |
| Kiszolgálók (64 bites)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (telepítenie kell a [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)-t) | Fizikai kiszolgáló  <br><br>    Hyper-V virtuális gép  <br><br>      VMware virtuális gép  <br><br>   Azure Stack | V3 UR1                            | Kötet, megosztás, mappa, fájl, rendszerállapot/operációs rendszer nélküli        |
| SQL Server                | SQL Server 2019, 2017, 2016 és [támogatott SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 és támogatott [SPS](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | Fizikai kiszolgáló  <br><br>     Hyper-V virtuális gép   <br><br>     VMware virtuális gép  <br><br>   Azure virtuális gép (ha a munkaterhelés Azure virtuális gépként fut)  <br><br>     Azure Stack | V3 UR1                            | Az összes központi telepítési forgatókönyv: adatbázis       <br><br>  A MABS v3 UR1 támogatja az SQL-adatbázisok ReFS-köteteken keresztüli biztonsági mentését                  |
| Exchange                   | Exchange 2019, 2016                                         | Fizikai kiszolgáló   <br><br>   Hyper-V virtuális gép  <br><br>      VMware virtuális gép  <br><br>   Azure Stack  <br><br>    Azure virtuális gép (ha a munkaterhelés Azure virtuális gépként fut) | V3 UR1                            | Védelem (az összes központi telepítési forgatókönyv): önálló Exchange-kiszolgáló, adatbázis egy adatbázis-elérhetőségi csoportban (DAG)  <br><br>    Helyreállítás (az összes központi telepítési forgatókönyv): Egy adatbázis-elérhetőségi csoportban található postaláda és postaláda-adatbázisok    <br><br>  Az Exchange ReFS-en keresztüli biztonsági mentése támogatott a MABS v3 UR1 |
| SharePoint                 | SharePoint 2019, 2016 legújabb SPs-vel                       | Fizikai kiszolgáló  <br><br>    Hyper-V virtuális gép <br><br>    VMware virtuális gép  <br><br>   Azure virtuális gép (ha a munkaterhelés Azure virtuális gépként fut)   <br><br>   Azure Stack | V3 UR1                            | Védelem (az összes központi telepítési forgatókönyv): Farm, előtér-Webkiszolgálói tartalom  <br><br>    Helyreállítás (az összes központi telepítési forgatókönyv): Farm, adatbázis, webalkalmazás, fájl vagy listaelem, SharePoint-keresés, előtér-webkiszolgáló  <br><br>    A tartalom-adatbázisok SQL Server 2012 AlwaysOn szolgáltatást használó SharePoint-farmok védelme nem támogatott. |

## <a name="vm-backup"></a>Virtuális gép biztonsági mentése

| **Számítási feladat**                                                 | **Verzió**                                             | **Azure Backup Server telepítés**                      | **Támogatott Azure Backup Server** | **Védelem és helyreállítás**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-V gazdagép – MABS védelmi ügynök a Hyper-V-gazdagépen, a fürtön vagy a virtuális gépen | Windows Server 2019, 2016, 2012 R2, 2012               | Fizikai kiszolgáló  <br><br>    Hyper-V virtuális gép <br><br>    VMware virtuális gép | V3 UR1                             | Védelem: Hyper-V számítógépek, fürt megosztott kötetei (CSV)  <br><br>    Helyreállítás: virtuális gép, a fájlok és mappák elemszintű helyreállítása csak Windows, kötetek és virtuális merevlemezek esetén érhető el |
| VMware virtuális gépek                                                  | VMware Server 5,5, 6,0 vagy 6,5, 6,7 (licencelt verzió) | Hyper-V virtuális gép  <br><br>   VMware virtuális gép         | V3 UR1                             | Védelem: VMware virtuális gépek a fürt megosztott kötetein (CSV), az NFS-en és a TÁROLÓHÁLÓZATI tárolóban   <br><br>     Helyreállítás: virtuális gép, a fájlok és mappák elemszintű helyreállítása csak Windows, kötetek és virtuális merevlemezek esetén érhető el <br><br>    A VMware Vapp nem támogatottak. |

## <a name="linux"></a>Linux

| **Számítási feladat** | **Verzió**                               | **Azure Backup Server telepítés**                      | **Támogatott Azure Backup Server** | **Védelem és helyreállítás**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | [Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) vagy [VMware](backup-azure-backup-server-vmware.md) vendégként futó Linux | Fizikai kiszolgáló, helyszíni Hyper-V virtuális gép, Windows rendszerű virtuális gép VMWare-ben | V3 UR1                             | A Hyper-V-nek Windows Server 2012 R2, Windows Server 2016 vagy Windows Server 2019 rendszeren kell futnia. Védelem: teljes virtuális gép   <br><br>   Helyreállítás: az egész virtuális gép   <br><br>    Csak a fájlkonzisztens pillanatképek támogatottak.    <br><br>   A támogatott Linux-disztribúciók és-verziók teljes listájáért tekintse meg a [Linux az Azure által támogatott disztribúciókkal foglalkozó](../virtual-machines/linux/endorsed-distros.md)cikket. |

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

A Azure Backup Server a MABS-kiszolgálóval azonos tartományban található, illetve egy gyermek vagy megbízható tartományba tartozó munkaterhelések elleni védelemmel rendelkezhet. Ha nem megbízható tartományokban vagy munkacsoportokban lévő adatforrásokat kíván védelemmel ellátni, használja az NTLM-t vagy a tanúsítványalapú hitelesítést egyetlen kiszolgáló esetén, vagy csak a fürt tanúsítvány-hitelesítését.
