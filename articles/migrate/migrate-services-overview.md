---
title: Az Azure Migrate bemutatása
description: Ismerkedjen meg a Azure Migrate szolgáltatással.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 858f006de7425a9fa8bea25e356a148b877aa30d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040600"
---
# <a name="about-azure-migrate"></a>Az Azure Migrate bemutatása

Ez a cikk a Azure Migrate szolgáltatás gyors áttekintését tartalmazza.

A Azure Migrate központosított központot biztosít az Azure helyszíni kiszolgálóira, infrastruktúrára, alkalmazásokra és adatszolgáltatásokra való kiértékeléséhez és átköltöztetéséhez. A következőket biztosítja:

- **Egységes áttelepítési platform**: egyetlen portál az Azure-ba való Migrálás elindításához, futtatásához és nyomon követéséhez.
- **Eszközök köre**: számos eszköz az értékeléshez és az áttelepítéshez. Azure Migrate eszközök közé tartozik a Azure Migrate: a kiszolgáló értékelése és a Azure Migrate: kiszolgáló áttelepítése. A Azure Migrate emellett integrálható más Azure-szolgáltatásokkal és-eszközökkel, valamint független szoftvergyártók (ISV) ajánlatokkal.
- **Értékelés és áttelepítés**: az Azure Migrate központban a következőket értékelheti és telepítheti át:
    - **Kiszolgálók**: mérje fel a helyszíni kiszolgálókat, és telepítse át őket az Azure Virtual Machines vagy az Azure VMware Solution (AVS) rendszerbe (előzetes verzió).
    - **Adatbázisok**: helyszíni adatbázisok értékelése és migrálása Azure SQL Database vagy SQL felügyelt példányra.
    - **Webalkalmazások**: kiértékelheti a helyszíni webalkalmazásokat, és áttelepítheti azokat Azure app Servicere a Azure app Service Migration Assistant használatával.
    - **Virtuális asztalok**: mérje fel a helyszíni virtuális asztali infrastruktúrát (VDI), és telepítse át a Windows rendszerű virtuális asztalra az Azure-ban.
    - **Adatforgalom**: nagy mennyiségű adatforgalom migrálása az Azure-ba gyorsan és költséghatékonyan Azure Data Box termékek használatával.

## <a name="integrated-tools"></a>Integrált eszközök

Az Azure Migrate hub az alábbi eszközöket tartalmazza:

**Eszköz** | **Értékelés és migrálás** | **Részletek**
--- | --- | ---
**Azure Migrate: kiszolgáló értékelése** | Kiszolgálók értékelése. | A helyszíni VMware virtuális gépek, Hyper-V virtuális gépek és fizikai kiszolgálók felderítése és kiértékelése az Azure-ba való Migrálás előkészítése során.
**Azure Migrate: kiszolgáló áttelepítése** | Kiszolgálók migrálása. | Telepítse át a VMware virtuális gépeket, a Hyper-V virtuális gépeket, a fizikai kiszolgálókat, a többi virtualizált gépet és az Azure-ba irányuló nyilvános Felhőbeli
**Data Migration Assistant** | Mérje fel SQL Server adatbázisait Azure SQL Databaseba, Azure SQL felügyelt példányba vagy SQL Server-t futtató Azure-beli virtuális gépekre való áttelepítésre. | A Data Migration Assistant segít az áttelepítést blokkoló lehetséges problémák azonosításában. Azonosítja a nem támogatott funkciókat, új szolgáltatásokat, amelyek hasznosak lehetnek az áttelepítés után, valamint az adatbázis-áttelepítés helyes elérési útját. [További információ](/sql/dma/dma-overview).
**Azure Database Migration Service** | Helyszíni adatbázisok migrálása SQL Server, Azure SQL Database vagy SQL felügyelt példányokat futtató Azure-beli virtuális gépekre. | [További](../dms/dms-overview.md) információ a Database Migration Serviceról.
**Movere** | Kiszolgálók értékelése. | [További](#movere) információ a mozgató szolgáltatásról.
**Webalkalmazás-áttelepítési segéd** | Mérje fel a helyszíni webalkalmazásokat, és telepítse át őket az Azure-ba. |  A Azure App Service Migration Assistant segítségével kihasználhatja a helyszíni webhelyeket Azure App Servicere való áttelepítéshez.<br/><br/> A .NET-és PHP-alapú webalkalmazásokat az Azure-ba áttelepítheti a Migration Assistant használatával. [További](https://appmigration.microsoft.com/) információ a Azure app Service Migration Assistantról.
**Azure Data Box** | Offline adatáttelepítés. | Azure Data Box termékek használatával nagy mennyiségű offline állapotú adatátvitelt helyezhet át az Azure-ba. [További információ](../databox/index.yml).

> [!NOTE]
> Ha Azure Government, a külső integrált eszközök és az ISV-ajánlatok nem tudnak adatküldeni Azure Migrate projekteknek. Az eszközöket egymástól függetlenül is használhatja.

## <a name="isv-integration"></a>ISV-integráció

A Azure Migrate számos ISV-ajánlattal integrálható. 

**ISV**    | **Szolgáltatás**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Kiszolgálók migrálása.
[Cloudamize](https://www.cloudamize.com/platform) | Kiszolgálók értékelése.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Kiszolgálók felmérése és migrálása.
[Device42](https://docs.device42.com/) | Kiszolgálók értékelése.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI kiértékelése.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Kiszolgálók migrálása.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Kiszolgálók értékelése.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Kiszolgálók és adatbázisok értékelése.

## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: kiszolgáló-Assessment eszköz

A Azure Migrate: a kiszolgáló-értékelő eszköz felkeresi és kiértékeli a helyszíni VMware virtuális gépeket, a Hyper-V virtuális gépeket és a fizikai kiszolgálókat az Azure-ba való áttelepítéshez. 

Az eszköz a következőket teszi:

- **Azure-készültség**: azt vizsgálja, hogy a helyszíni gépek készen állnak-e az Azure-ba való áttelepítésre.
- **Azure-méretezés**: a Migrálás után megbecsüli az Azure-beli virtuális gépek méretét vagy az Azure VMware-csomópontok számát.
- **Azure Cost-becslés**: a helyszíni kiszolgálók Azure-ban történő futtatásával kapcsolatos költségek becslése.
- **Függőség elemzése**: a kiszolgálók közötti függőségeket és optimalizálási stratégiákat azonosítja a egymástól függő kiszolgálók Azure-ba történő áthelyezéséhez. További információ a [függőségi elemzéssel](concepts-dependency-visualization.md)rendelkező kiszolgálók értékeléséről.

A kiszolgáló értékelése a helyszínen üzembe helyezett, könnyű [Azure Migrate berendezést](migrate-appliance.md) használ.

- A készülék egy virtuális gépen vagy fizikai kiszolgálón fut. Egyszerűen telepítheti egy letöltött sablon használatával.
- A készülék felfedi a helyszíni gépeket. Emellett folyamatosan küldi a számítógép-metaadatokat és teljesítményadatokat a Azure Migrate.
- A készülék felderítése ügynök nélkül történik. Nincs telepítve a felderített gépekre.
- A készülék felderítése után a felderített gépeket csoportokba gyűjtheti, és az egyes csoportok értékeléseit is futtathatja.

> [!Note]
> A VMware-környezetben futó SQL Server példányok és adatbázisok felderítése és értékelése már előzetes verzióban érhető el. A funkció kipróbálásához hozzon létre egy projektet a **Kelet-Ausztrália** régióban [**ezzel a hivatkozással**](https://go.microsoft.com/fwlink/?linkid=2155668). Ha már van egy projektje Kelet-Ausztráliában, és szeretné kipróbálni ezt a funkciót, ellenőrizze, hogy eleget tett-e ezeknek az [**előfeltételeknek**](how-to-discover-sql-existing-project.md) a portálon.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: kiszolgáló áttelepítési eszköze

A Azure Migrate: a kiszolgáló áttelepítési eszköze segíti az Azure-ba való áttelepítést:

**Migrate** | **Részletek**
--- | ---
Helyszíni VMware virtuális gépek | Virtuális gépek migrálása az Azure-ba ügynök nélküli vagy ügynök-alapú áttelepítés használatával.<br/><br/> Az ügynök nélküli áttelepítés esetében a kiszolgáló áttelepítése ugyanazt a Azure Migrate készüléket használja, amelyet a kiszolgáló értékelésével is használhat a VMware virtuális gépek felderítéséhez és értékeléséhez.<br/><br/> Az ügynök-alapú áttelepítés esetében a kiszolgáló áttelepítése replikációs berendezést használ.
Helyszíni Hyper-V rendszerű virtuális gépek | Virtuális gépek migrálása az Azure-ba.<br/><br/> A kiszolgáló áttelepítése a Hyper-V gazdagépre telepített szolgáltatói ügynököket használja az áttelepítéshez.
Helyszíni fizikai kiszolgálók | A fizikai gépeket áttelepítheti az Azure-ba. Más virtualizált gépeket és más nyilvános felhőkből származó virtuális gépeket is áttelepíthet, ha fizikai kiszolgálóként kezeli őket az áttelepítés céljából. | A kiszolgáló áttelepítése replikációs berendezést használ az áttelepítéshez.


## <a name="selecting-assessment-and-migration-tools"></a>Értékelési és áttelepítési eszközök kiválasztása

Az Azure Migrate központban kiválaszthatja az értékeléshez vagy az áttelepítéshez használni kívánt eszközt, és hozzáadhatja egy Azure Migrate projekthez. Ha ISV-eszközt vagy Mozgatót ad hozzá:

- Első lépésként szerezze be a licencet, vagy regisztráljon az ingyenes próbaverzióra az eszköz utasításait követve. Minden ISV vagy eszköz megadja az eszköz licencelését.
- Minden eszközhöz lehetőség van a Azure Migratehoz való kapcsolódásra. A kapcsolódáshoz kövesse az eszközre vonatkozó utasításokat.
- Az áttelepítést nyomon követheti az összes eszközön a Azure Migrate projekten belül.

## <a name="movere"></a>Movere

A mozgató egy szolgáltatott szoftver (SaaS) platform. Az üzleti intelligenciát úgy növeli, hogy a teljes informatikai környezeteket egyetlen napon belül kiterjesztheti. A szervezetek és a vállalatok növelhetik, módosíthatják és digitálisan optimalizálják az optimalizálást. Ahogy igen, a mozgatói a szükséges megbízhatóságot biztosítanak a környezetük, a platform, az alkalmazás vagy a földrajz tekintetében is.

A Microsoft [beszerzett](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) győztese, és már nem önálló ajánlatként vásárolható meg. A mozgató szolgáltatás a Microsoft Solution Assessment és Microsoft Cloud Economics programon keresztül érhető el. [További](https://www.movere.io) információ a mozgató szolgáltatásról.

Javasoljuk, hogy tekintse meg a Azure Migrate, a beépített áttelepítési szolgáltatást is. A Azure Migrate egy központi központot biztosít a felhő áttelepítésének egyszerűbbé tételéhez. A hub átfogóan támogatja a számítási feladatokat, például a fizikai és virtuális kiszolgálókat, az adatbázisokat és az alkalmazásokat. A végpontok közötti láthatósággal könnyedén követheti nyomon az előrehaladást a felderítés, az értékelés és az áttelepítés során.

A beépített Azure-és partner ISV-eszközökkel Azure Migrate számos funkcióval rendelkezik, beleértve a következőket:

- Virtuális és fizikai kiszolgálók felderítése.
- Teljesítmény-alapú megadásában.
- A Cost megtervezése.
- Importálás-alapú értékelések.
- Ügynök nélküli alkalmazások függőségi elemzése.

Ha szakértői segítségre van szüksége az első lépésekhez, a Microsoft az [Azure szakértői által felügyelt szolgáltatók](https://azure.microsoft.com/partners) segítségével segíti Önt. Tekintse meg a [Azure Migrate webhelyét](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Azure Migrate verziók

A Azure Migrate szolgáltatásnak két verziója van.

- **Aktuális verzió**: ezzel a verzióval Azure Migrate projekteket hozhat létre, felderítheti a helyszíni gépeket, és összehangolhatja az értékeléseket és áttelepítéseket. [További](whats-new.md) információ az ebben a verzióban található újdonságokról.
- **Előző verzió**: a Azure Migrate korábbi, klasszikus Azure Migrate néven is ismert verziója csak a helyszíni VMWare virtuális gépek értékelését támogatja. A klasszikus Azure Migrate kivonása február 2024. Február 2024 után a Azure Migrate klasszikus verziója már nem támogatott, és a rendszer a klasszikus projektekben lévő leltári metaadatokat törli. Az előző verzióban lévő projektek vagy összetevők nem frissíthetők az új verzióra. [Létre kell hoznia egy új Azure Migrate projektet](create-manage-projects.md), és [hozzá kell adnia az értékelési és áttelepítési eszközöket](./create-manage-projects.md) . Az oktatóanyagok segítségével megismerheti, hogyan használhatja az elérhető értékelési és áttelepítési eszközöket. Ha egy klasszikus projekthez csatlakoztatott Log Analytics munkaterülettel rendelkezik, a klasszikus projekt törlése után csatolhatja azt egy aktuális verzió projekthez.

    A Azure Portal meglévő projektjeinek eléréséhez keresse meg és válassza a **Azure Migrate** lehetőséget. A **Azure Migrate** irányítópulton egy értesítés és egy hivatkozás látható a régi Azure Migrate projektek eléréséhez.

## <a name="next-steps"></a>Következő lépések

- A [VMWare virtuális gépek](./tutorial-discover-vmware.md), a [Hyper-V virtuális gépek](./tutorial-discover-hyper-v.md)és a [fizikai kiszolgálók](./tutorial-discover-physical.md)értékeléséhez próbálja ki az oktatóanyagokat.
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).