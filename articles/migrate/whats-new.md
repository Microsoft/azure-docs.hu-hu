---
title: A Azure Migrate
description: Ismerje meg az újdonságokat és a legújabb frissítéseket a Azure Migrate szolgáltatásban.
ms.topic: overview
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: 3f49c0acc99f82cc2986ca896d40b3998cf47c5c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835001"
---
# <a name="whats-new-in-azure-migrate"></a>A Azure Migrate

[Azure Migrate](migrate-services-overview.md) segít a helyszíni kiszolgálók, alkalmazások és adatok felderítésében, értékelésében és a felhőbe való Microsoft Azure áttelepítésében. Ez a cikk az új kiadásokat és funkciókat foglalja Azure Migrate.

## <a name="update-march-2021"></a>Frissítés (2021. március)
- Támogatás több kiszolgálói hitelesítő adat megadásához Azure Migrate berendezésen a telepített alkalmazások (szoftverleltár), az ügynök nélküli függőségelemzés és a VMware-környezetben található SQL Server-példányok és adatbázisok felderítéséhez. [További információ](tutorial-discover-vmware.md#provide-server-credentials)
- A VMware-SQL Server futó virtuális gépek és adatbázisok felderítése és értékelése jelenleg előzetes verzióban érhető el. [További információ](concepts-azure-sql-assessment-calculation.md) Az első [lépésekhez](tutorial-discover-vmware.md) tekintse meg [a felderítéssel](tutorial-assess-sql.md) és értékeléssel kapcsolatban oktatóanyagokat.
- Az ügynök nélküli VMware-migrálás mostantól vCenterenként 500 virtuális gép egyidejű replikációját támogatja.

## <a name="update-january-2021"></a>Frissítés (2021. január)
-  Azure Migrate: A Server Migration eszközzel mostantól VMware virtuális gépeket, fizikai kiszolgálókat és virtuális gépeket migrálhat más felhőkből Azure-beli virtuális gépekre, kiszolgálóoldali titkosítással, ügyfél által felügyelt kulcsokkal (CMK) titkosított lemezekkel.

## <a name="update-december-2020"></a>Frissítés (2020. december)
- Azure Migrate automatikusan telepíti az Azure-beli virtuálisgép-ügynököt a VMware virtuális gépekre, miközben migrálja őket az Azure-ba a VMware-migrálás ügynök nélküli módszerével. (Windows Server 2008 R2 és újabb verziók)
- A VMware virtuális gépek migrálását kiszolgálóoldali titkosítással (SSE) ügyfél által felügyelt kulcsokkal (CMK) titkosított lemezeket használó Azure-beli virtuális gépekre az Azure Migrate Server Migration (ügynök nélküli replikáció) használatával már Azure Portal.

## <a name="update-september-2020"></a>Frissítés (2020. szeptember)
- A kiszolgálók áttelepítése Availability Zones támogatott.
- Az UEFI-alapú virtuális gépek és fizikai kiszolgálók áttelepítése az Azure 2. generációs virtuális gépekre mostantól támogatott. Ebben a kiadásban a Azure Migrate: A Server Migration eszköz nem végzi el a 2. generációs virtuális gépről az 1. generációs virtuális gépre való átalakítást a migrálás során.
- Elérhető egy Azure Migrate Power BI értékelési irányítópult, amely segít összehasonlítani a különböző értékelési beállítások költségeit. Az irányítópulthoz egy PowerShell-segédprogram is jár, amely automatikusan létrehozza az irányítópultra Power BI értékeléseket. [Részletek](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/assessment-utility)
- A függőségelemzés (ügynök nélküli) mostantól egyidejűleg futtatható 1000 virtuális gépen.
- A függőségelemzés (ügynök nélküli) mostantól nagy méretekben engedélyezhető vagy letiltható PowerShell-szkriptekkel. [Részletek](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- Hálózati kapcsolatok vizualizációja a Power BI függőségelemzéssel gyűjtött adatok használatával (ügynök [nélkül) További információ.](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- A legfeljebb 32 TB adatlemezméretű VMware virtuális gépek áttelepítése mostantól támogatott a Azure Migrate: Kiszolgálómigrálás ügynök nélküli VMware migrálási módszerrel.

## <a name="update-august-2020"></a>Frissítés (2020. augusztus)

- Továbbfejlesztett regisztrációs élmény, amelyben Azure Migrate projektkulcs jön létre a portálról, és a rendszer a berendezés regisztrációjának befejezésére használja.
- A VMware- és Hyper-V-berendezések beállításának lehetőségével letöltheti az OVA-/VHD-fájlokat vagy a telepítő szkripteket a portálról.
- Frissített berendezéskonfiguráció-kezelő továbbfejlesztett felhasználói élmével.
- Több hitelesítő adat támogatása a Hyper-V virtuális gépek felderítésében.

## <a name="update-july-2020"></a>Frissítés (2020. július)

- Az ügynök nélküli VMware-migrálás mostantól támogatja a 300 virtuális gép egyidejű replikálást vCenterenként

## <a name="update-june-2020"></a>Frissítés (2020. június)

- A helyszíni VMware virtuális gépek Azure VMware Solution [(AVS)](./concepts-azure-vmware-solution-assessment-calculation.md) való áttelepítésének értékelései már támogatottak. [További információ](how-to-create-azure-vmware-solution-assessment.md)
- Több hitelesítő adat támogatása a berendezésen a fizikai kiszolgálók felderítéséhez.
- Azure-bejelentkezés engedélyezése a berendezésről olyan bérlő számára, ahol konfigurálva van a bérlői korlátozás.


## <a name="update-april-2020"></a>Frissítés (2020. április)

Azure Migrate támogatja az üzembe helyezést a Azure Government. 

- Felderítheti és kiértékelheti a VMware virtuális gépeket, a Hyper-V virtuális gépeket és a fizikai kiszolgálókat.
- A VMware virtuális gépeket, a Hyper-V virtuális gépeket és a fizikai kiszolgálókat az Azure-ba mimitálhatja.
- A VMware-migráláshoz használhat ügynök nélküli vagy ügynökalapú áttelepítést. [További információ](server-migrate-overview.md).
- [Tekintse át](migrate-support-matrix.md#supported-geographies-azure-government) a támogatott földrajzi helyeket és régiókat a Azure Government.
- [Az ügynökalapú függőségelemzés](concepts-dependency-visualization.md#agent-based-analysis) nem támogatott a Azure Government.
- Az előzetes verziójú funkciók a Azure Government, különösen az ügynök nélküli [függőségelemzésben](concepts-dependency-visualization.md#agentless-analysis)és az [alkalmazásfelderítésben támogatottak.](how-to-discover-applications.md)


## <a name="update-march-2020"></a>Frissítés (2020. március)

Már elérhető egy szkriptalapú telepítés a Azure Migrate [beállításhoz:](migrate-appliance.md)

- A szkriptalapú telepítés a alternatívája a parancsnak. A berendezés OVA(VMware)/VHD (Hyper-V) telepítése.
- Egy PowerShell-telepítő szkriptet biztosít, amely a VMware-hez/Hyper-V-hez a Windows Server 2016-ot futtató meglévő gépen való beállításhoz használható.

## <a name="update-november-2019"></a>Frissítés (2019. november)

Számos új funkcióval bővült a Azure Migrate:

- **Fizikai kiszolgáló értékelése.** A helyszíni fizikai kiszolgálók felmérése mostantól támogatott a már támogatott fizikai kiszolgálók áttelepítése mellett.
- **Importálásalapú értékelés.** A gépek CSV-fájlban megadott metaadatokkal és teljesítményadatokkal való értékelése mostantól támogatott.
- **Alkalmazásfelderítés:** Azure Migrate már támogatja az alkalmazások, szerepkörök és szolgáltatások alkalmazásszintű felderítését a Azure Migrate berendezés használatával. Ez jelenleg csak vMware virtuális gépek esetén támogatott, és csak felderítésre van korlátozva (az értékelés jelenleg nem támogatott). [További információ](how-to-discover-applications.md)
- **Ügynök nélküli függőségek vizualizációja:** Már nem kell ügynököket telepítenie a függőségek megjelenítéséhez. Az ügynök nélküli és az ügynökalapú is támogatott.
- **Virtual Desktop:** IsV-eszközök használata a helyszíni virtuális asztali infrastruktúra (VDI) értékeléséhez és az Azure-ban Windows Virtual Desktop áttelepítéséhez.
- **Webalkalmazás:** A Azure App Service Migration Assistant értékelésére és migrálásra használt alkalmazás mostantól integrálva van a Azure Migrate.

Új értékelési és migrálási eszközök bővültek a Azure Migrate:

- **RackWare:** Felhőbe való migrálást kínál.
- **Movere:** Ajánlat értékelése.

[További információ](migrate-services-overview.md) az eszközök és isV-ajánlatok felméréshez és migráláshoz való használatával kapcsolatban a Azure Migrate.

## <a name="azure-migrate-current-version"></a>Azure Migrate aktuális verzió

A Azure Migrate (2019 júliusában kiadott) aktuális verziója számos új funkciót kínál:

- **Egységes migrálási platform:** Azure Migrate portálon központosíthatja, kezelheti és nyomon követheti az Azure-ba való migrálási folyamatot, továbbfejlesztett üzembe helyezési folyamattal és portálélménysel.
- **Értékelési és migrálási eszközök:** Azure Migrate natív eszközöket biztosít, és integrálható más Azure-szolgáltatásokkal, valamint független szoftverszállítói (ISV) eszközökkel. [További információ az](migrate-services-overview.md#isv-integration) ISV-integrációról.
- **Azure Migrate értékelése:** A Azure Migrate Server Assessment eszközzel értékelheti a VMware virtuális gépeket és a Hyper-V virtuális gépeket az Azure-ba való migráláshoz. A migrálást más Azure-szolgáltatásokkal és ISV-eszközökkel is felmérheti.
- **Azure Migrate** áttelepítése: A Azure Migrate Server Migration eszközzel helyszíni VMware virtuális gépeket és Hyper-V virtuális gépeket migrálhat az Azure-ba, valamint fizikai kiszolgálókat, más virtualizált kiszolgálókat és magán- és nyilvános felhőbeli virtuális gépeket. Az Azure-ba is mi egyik eszközét is át lehet milaniálni.
- **Azure Migrate berendezés:** Azure Migrate egyszerű berendezést helyez üzembe a helyszíni VMware virtuális gépek és Hyper-V virtuális gépek felderítéséhez és értékeléséhez.
    - Ezt a berendezést a Azure Migrate Server Assessment és a Azure Migrate Server Migration használja az ügynök nélküli migráláshoz.
    - A berendezés felmérés és migrálás céljából folyamatosan felderíti a kiszolgáló metaadatait és teljesítményadatait.  
- **VMware virtuális** gépek migrálása: Azure Migrate Server Migration több módszert is kínál a helyszíni VMware virtuális gépek Azure-ba való migrálása során.  Ügynök nélküli migrálás az Azure Migrate-berendezés használatával, valamint egy ügynökalapú migrálás, amely egy replikációs berendezést használ, és minden migrálni kívánt virtuális gépen üzembe helyez egy ügynököt. [További információ](server-migrate-overview.md)
 - **Adatbázis-felmérés és**-migrálás: A Azure Migrate az Azure-ba való migráláshoz a helyszíni adatbázisokat az Azure Database Migration Assistant. Az adatbázisokat a következő Azure Database Migration Service.
- **Webalkalmazás migrálása:** A webalkalmazásokat nyilvános végponti URL-cím használatával értékelheti a Azure App Service. Belső .NET-alkalmazások áttelepítéséhez letöltheti és futtathatja a App Service Migration Assistant.
- **Data Box:** Nagy mennyiségű offline adat importálása az Azure-ba a Azure Data Box használatával Azure Migrate.

## <a name="azure-migrate-previous-version"></a>Azure Migrate előző verzió

Ha a Azure Migrate előző verzióját használja (csak a helyszíni VMware virtuális gépek értékelése támogatott), most az aktuális verziót kell használnia. Az előző verzióban már nem hozhat létre új Azure Migrate, és nem hajthat végre új felderítéseket. Továbbra is hozzáférhet a meglévő projektekhez. Ehhez a Minden szolgáltatás Azure Portal > **a** következőt: **Azure Migrate.** A Azure Migrate értesítések között található egy hivatkozás, amely a régi Azure Migrate hozzáférést.



## <a name="next-steps"></a>Következő lépések

- [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
- Próbálja ki az oktatóanyagokat a [VMware](./tutorial-assess-vmware-azure-vm.md) virtuális gépek és a [Hyper-V virtuális gépek értékeléséhez.](tutorial-assess-hyper-v.md)
