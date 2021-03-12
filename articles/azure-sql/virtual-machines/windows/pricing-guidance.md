---
title: Díjszabási útmutató & a költségek kezelése
description: Ajánlott eljárásokat biztosít a virtuális gépek díjszabásának megfelelő SQL Server kiválasztásához.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: d980b92bc4effc58ef84ef6ec70c3908f575e484
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615601"
---
# <a name="pricing-guidance-for-sql-server-on-azure-vms"></a>Az Azure-beli virtuális gépek SQL Server díjszabási útmutatója
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk az [Azure Virtual Machines SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)díjszabását ismerteti. Több lehetőség is van, amelyek befolyásolják a költségeket, és fontos, hogy kiválassza a megfelelő képet, amely egyensúlyt teremt az üzleti követelményekkel.

> [!TIP]
> Ha csak a SQL Server Edition és a virtuális gép (VM) méretének adott kombinációjára vonatkozó költségbecslés szükséges, tekintse meg a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) vagy [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux)díjszabását ismertető oldalt. Válassza ki a platformot és SQL Server kiadást az **operációs rendszer/szoftver** listából.
>
> ![Felhasználói felület a virtuális gépek díjszabási oldalán](./media/pricing-guidance/virtual-machines-pricing-ui.png)
>
> A virtuális gép hozzáadásához és konfigurálásához használja a [díjszabási számológépet](https://azure.microsoft.com/pricing/#explore-cost) . 

## <a name="free-licensed-sql-server-editions"></a>Ingyenes licenccel rendelkező SQL Server kiadások

Ha szeretné fejleszteni, tesztelni vagy felépíteni a koncepciót, használja a szabadon licencelt **SQL Server Developer kiadást**. Ez a kiadás a SQL Server Enterprise Edition összes funkcióját tartalmazza, így bármilyen típusú alkalmazást felépítheti és tesztelheti. A fejlesztői kiadás azonban nem futtatható éles környezetben. A SQL Server Developer Edition rendszerű virtuális gépek csak a virtuális gép költségeiért számítanak fel díjat, mivel nincsenek hozzárendelve SQL Server licencelési költségek.

Ha éles számítási feladatot szeretne futtatni (<4 mag, <1 GB memória, <10 GB/adatbázis), használja a szabadon licencelt **SQL Server Express kiadást**. A SQL Server Express Edition rendszerű virtuális gépek esetében a virtuális gép költségei is csak a költségekkel járnak.

A fejlesztéssel/teszteléssel és a könnyű üzemi számítási feladatokkal pénzt takaríthat meg, ha egy kisebb méretű virtuálisgép-méretet választ, amely megfelel ezeknek a számítási feladatoknak. A DS1v2 bizonyos helyzetekben jó választás lehet.

Ha SQL Server 2017-at futtató Azure-beli virtuális gépet szeretne létrehozni ezen rendszerképek egyikével, tekintse meg az alábbi hivatkozásokat:

| Platform | Szabadon licencelt lemezképek |
|---|---|
| Windows Server 2016 | [SQL Server 2017 fejlesztői Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 fejlesztői Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 fejlesztői Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 fejlesztői Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Fizetős SQL Server kiadások

Ha nem könnyű üzemi számítási feladattal rendelkezik, használja a következő SQL Server kiadások egyikét:

| SQL Server-kiadás | Számítási feladat |
|-----|-----|
| Webes | Kisméretű webhelyek |
| Standard | Kis-és közepes számítási feladatok |
| Enterprise | Nagy vagy kritikus fontosságú számítási feladatok|

A következő kiadásokra vonatkozóan két lehetőség SQL Server közül választhat: *fizetés/használat* vagy *saját licenc (BYOL)*.

## <a name="pay-per-usage"></a>Fizetés/használat

Ha az Azure-beli virtuális gép futtatásának másodpercenkénti díja (más néven **fizetési** díj) miatt a **SQL Server licencet** kell fizetnie, az az SQL Server licenc költségeit is tartalmazza. A különböző SQL Server kiadásai (web, standard, Enterprise) díjszabását a Windows vagy [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux) [rendszerhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows) készült Azure Virtual Machines díjszabási oldalán tekintheti meg.

A Cost a SQL Server összes verziójának (2012 SP3 – 2019). A másodpercenkénti licencelési díjak a virtuális gépek vCPU számától függnek.

A SQL Server licencelési licencek használata a következő esetén ajánlott:

- **Ideiglenes vagy időszakos számítási feladatok**. Például egy olyan alkalmazásnak, amely évente több hónapra, vagy hétfőnként üzleti elemzésre van szüksége, egy eseményt kell támogatnia.

- **Ismeretlen élettartammal vagy méretezéssel rendelkező munkaterhelések**. Előfordulhat például, hogy egy olyan alkalmazást, amely néhány hónapon belül nem igényel, vagy amely több vagy kevesebb számítási teljesítményt igényelhet igénytől függően.

Ha SQL Server 2017 rendszerű Azure-beli virtuális gépet szeretne létrehozni ezen utólagos elszámolású lemezképek egyikével, tekintse meg az alábbi hivatkozásokat:

| Platform | Licencelt lemezképek |
|---|---|
| Windows Server 2016 | [SQL Server 2017 web Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 standard Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 web Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 standard Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 web Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 standard Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 web Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 standard Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Amikor SQL Server virtuális gépet hoz létre a Azure Portal, a **méret kiválasztása** ablak a becsült költségeket mutatja. Fontos megjegyezni, hogy ez a becslés csak a virtuális gép futtatásának számítási költségei, valamint az operációs rendszer licencelési költségei (Windows vagy harmadik féltől származó Linux operációs rendszerek).
>
> ![VM-méret panel kiválasztása](./media/pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Nem tartalmaz további SQL Server licencelési költségeket a web, standard és Enterprise kiadásokhoz. A legpontosabb díjszabási becsléshez válassza ki az operációs rendszert, és SQL Server a kiadást a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) vagy [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)díjszabási oldalán.

> [!NOTE]
> Mostantól megváltoztathatja a licencelési modellt a díjköteles használatból, hogy saját licencet (BYOL) és vissza lehessen állítani. További információ: [a SQL Server VM licencelési modelljének módosítása](licensing-model-azure-hybrid-benefit-ahb-change.md). 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a> Saját licenc használata (BYOL)

Ha **a saját SQL Server licencét licenchordozhatóság**(más néven **BYOL**) használja, akkor egy Azure-beli virtuális gépen meglévő, frissítési garanciával rendelkező SQL Server mennyiségi licencet használ. A BYOL-t használó SQL Server VM kizárólag a virtuális gép futtatásának költségeiért kell fizetnie, nem SQL Server licenceléshez, mivel a mennyiségi licencelési programon keresztül vagy egy felhőalapú megoldási partneren (CSP) keresztül már beszerezte a licenceket és a frissítési garanciát.

> [!NOTE]
> A BYOL-lemezképek jelenleg csak a Windows rendszerű virtuális gépek esetében érhetők el. Azonban manuálisan is telepítheti a SQL Servert egy Linux-alapú virtuális gépre. Tekintse [meg a Linux rendszerű virtuális](../linux/frequently-asked-questions-faq.md)gépekkel kapcsolatos gyakori kérdések a SQL Server című témakör útmutatását.

A saját SQL Server licencelésének engedélyezése licenchordozhatóság használata esetén ajánlott:

- **Folyamatos munkaterhelések**. Például egy olyan alkalmazás, amelynek támogatnia kell az üzleti műveletek nonstop.

- **Ismert élettartammal és méretezéssel rendelkező munkaterhelések**. Például egy olyan alkalmazás, amely az egész évre szükséges, és a keresletet előre jelezték.

Ahhoz, hogy a BYOL-t a SQL Server VM használatával használhassa, rendelkeznie kell egy, a mennyiségi licencelési programok keretében szükséges licenccel, valamint a többi szolgáltatással való opcionális vásárlással SQL Server Standard-vagy nagyvállalati és [frissítési garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1). A mennyiségi licencelési programok által biztosított díjszabás a szerződés típusától és a SQL Serverre vonatkozó mennyiségi és elkötelezettségi szinttől függően változhat. A folyamatos üzemi munkaterhelések esetében azonban a saját licence a következő előnyökkel jár:

| BYOL juttatás | Leírás |
|-----|-----|
| **Költségmegtakarítás** | A [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) akár 55%-os megtakarítást is biztosít. További információ: [switch licencelési modell](licensing-model-azure-hybrid-benefit-ahb-change.md) |
| **Szabad passzív másodlagos replika** | A saját licencének egy másik előnye, hogy a magas rendelkezésre állás érdekében SQL Server [egy passzív másodlagos replika ingyenes licencelését](https://azure.microsoft.com/pricing/licensing-faq/) . Ez feldarabolja a nagyszámú SQL Server üzemelő példány licencelési költségeit (például az Always On rendelkezésre állási csoportok használata esetén). A passzív másodlagos futtatásához szükséges jogosultságokat a Fail-Over-kiszolgálók frissítési garanciája biztosítja. |

Ha SQL Server 2017 operációs rendszert futtató Azure-beli virtuális gépet szeretne létrehozni ezekkel a saját licenccel rendelkező rendszerképekkel, tekintse meg a "{BYOL}" előtaggal rendelkező virtuális gépeket:

- [SQL Server 2017 Enterprise Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 standard Azure-beli virtuális gép](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Tudassa velünk 10 napon belül, hogy hány SQL Server licencet használ az Azure-ban. Az előző rendszerképekre mutató hivatkozásokra vonatkozó útmutatást talál.

> [!NOTE]
> Mostantól megváltoztathatja a licencelési modellt a díjköteles használatból, hogy saját licencet (BYOL) és vissza lehessen állítani. További információ: [a SQL Server VM licencelési modelljének módosítása](licensing-model-azure-hybrid-benefit-ahb-change.md). 



## <a name="reduce-costs"></a>Költségek csökkentése

A szükségtelen költségek elkerülése érdekében válasszon ki egy optimális virtuálisgép-méretet, és vegye figyelembe a nem folyamatos számítási feladatok időszakos leállítását.

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a> A virtuális gép megfelelő méretezése

A SQL Server licencelési díja közvetlenül kapcsolódik a vCPU számához. Válasszon olyan virtuálisgép-méretet, amely megfelel a CPU-, memória-, tárterület-és I/O-sávszélesség várható igényeinek. A gép méretére vonatkozó beállítások teljes listáját a [Windows rendszerű virtuális gépek méretei](../../../virtual-machines/sizes.md) és a linuxos virtuálisgép- [méretek](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című részben tekintheti meg.

Az új gépi méretek bizonyos típusú SQL Server számítási feladatokhoz is jól működnek. Ezek a gépek méretei nagy mennyiségű memóriát, tárterületet és I/O-sávszélességet foglalnak magukban, de alacsonyabb virtualizált alapszámmal rendelkeznek. Vegyük például a következő példát:

| Virtuális gép mérete | vCPU-k | Memória | Lemezek maximális száma | Maximális bemeneti/kimeneti átvitel | Licencelési költségek SQL Server | Teljes költség (számítás + licencelés) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51,200 IOPS vagy 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51,200 IOPS vagy 768 MB/s | 75%-kal alacsonyabb | 57%-kal alacsonyabb |

> [!IMPORTANT]
> Ez egy időpontra vonatkozó példa. A legfrissebb specifikációkat a Windows és a [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) [rendszerhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) készült Azure-díjszabást ismertető cikkben találja.

Az előző példában láthatja, hogy a **Standard_DS14v2** és a **Standard_DS14-4v2** specifikációi azonosak, kivéve a vCPU. Az **Standard_DS14-4v2** gép végén lévő **4V2** az aktív vCPU számát jelzi. Mivel SQL Server licencelési költségek a vCPU számával vannak kötve, ez jelentősen csökkenti a virtuális gép költségét olyan helyzetekben, amikor a további vCPU nem szükségesek. Ez az egyik példa, és számos, korlátozott vCPU rendelkező, az utótag mintázatával azonosított számítógép mérete van. További információ: új Azure-beli virtuálisgép- [méretek bejelentése a költséghatékony adatbázis-munkához](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>A virtuális gép leállítása, ha lehetséges

Ha olyan munkaterhelést használ, amely nem folyamatosan fut, érdemes lehet leállítani a virtuális gépet az inaktív időszakokban. A fizetés használat alapján történik.

Ha például egyszerűen kipróbál SQL Server egy Azure-beli virtuális gépen, akkor nem érdemes felszámítani a költségeket, ha véletlenül a héten fut. Az egyik megoldás az [automatikus leállítási funkció](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)használata.

![Automatikus leállítás SQL Server VM](./media/pricing-guidance/sql-vm-auto-shutdown.png)

Az automatikus leállítás a [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab)által biztosított hasonló szolgáltatások nagyobb készletének részét képezi.

Más munkafolyamatok esetében érdemes lehet az Azure-beli virtuális gépek automatikus leállítása és újraindítása egy parancsfájl-megoldással (például [Azure Automation](https://azure.microsoft.com/services/automation/)).

> [!IMPORTANT]
> A költségek elkerülése érdekében a virtuális gép leállítása és felszabadítása az egyetlen módszer. A virtuális gép leállításához szükséges energiaellátási beállítások egyszerűen leállíthatók, és a használati díjak is megmaradnak.

## <a name="next-steps"></a>Következő lépések

Az Azure díjszabásával kapcsolatos általános útmutatóért lásd: a [váratlan költségek megelőzése az Azure-számlázással és a költségek kezelésével](../../../cost-management-billing/cost-management-billing-overview.md). A legújabb Azure Virtual Machines díjszabását, beleértve a SQL Servert is, tekintse meg a [Windows rendszerű virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) és a [Linux virtuális gépek](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)Azure Virtual Machines díjszabási oldalát.

Az Azure Virtual Machines SQL Serverának áttekintését a következő cikkekben találja:

- [A Windows rendszerű virtuális gépeken futó SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [SQL Server on Linux virtuális gépek áttekintése](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
