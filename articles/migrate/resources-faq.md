---
title: Azure Migrate GYIK
description: Választ kaphat a Azure Migrate szolgáltatással kapcsolatos gyakori kérdésekre.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: a5db000ad76f23be60d279923a590204c2fcf5e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377380"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: gyakori kérdések

Ez a cikk a Azure Migrateával kapcsolatos gyakori kérdésekre ad választ. Ha kérdései vannak a cikk elolvasása után, közzéteheti azokat a [Azure Migrate fórumban](https://aka.ms/AzureMigrateForum). A következő cikkeket is ellenőrizheti:

- Az [Azure Migrate berendezéssel](common-questions-appliance.md) kapcsolatos kérdések
- A [felderítéssel, az értékeléssel és a függőségi vizualizációval](common-questions-discovery-assessment.md) kapcsolatos kérdések

## <a name="what-is-azure-migrate"></a>Mi az az Azure Migrate?

Azure Migrate egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint a magán-és nyilvános Felhőbeli virtuális gépek felderítésének, értékelésének és áttelepítésének nyomon követésére az Azure-ban. A hub Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a külső gyártóktól származó ISV-ajánlatokhoz. [További információk](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Mire használhatom a Azure Migrate?

A Azure Migrate használatával felderítheti, elemezheti és áttelepítheti a helyszíni infrastruktúráját, alkalmazásait és az Azure-ba irányuló alkalmazásokat. Azure Migrate támogatja a helyszíni VMware virtuális gépek, a Hyper-V virtuális gépek, a fizikai kiszolgálók, a más virtualizált virtuális gépek, az adatbázisok, a webalkalmazások és a virtuális asztalok értékelését és áttelepítését. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Mi a különbség a Azure Migrate és az Azure Site Recovery között?

A [Azure Migrate](migrate-services-overview.md) központosított hubot biztosít az Azure-ba történő értékeléshez és áttelepítéshez. 

- A Azure Migrate használata lehetővé teszi a Azure Migrate eszközökkel, más Azure-szolgáltatásokkal és harmadik féltől származó eszközökkel való együttműködés és jövőbeni bővíthetőség biztosítását.
- A Azure Migrate: a kiszolgáló áttelepítési eszköze az Azure-ba történő kiszolgáló-áttelepítéshez készült. Áttelepítésre van optimalizálva. Nem kell megismernie az áttelepítéshez közvetlenül nem kapcsolódó fogalmakat és forgatókönyveket. 
- A 180 napos áttelepítéshez nem tartoznak eszköz-használati díjak a virtuális gépek replikálásának időpontjában. Időt biztosít az áttelepítés befejezésére. Csak a replikációhoz használt tárolási és hálózati erőforrásokért, valamint a tesztelési áttelepítések során felhasznált számítási díjakért kell fizetnie.
- A Azure Migrate a Site Recovery által támogatott összes áttelepítési forgatókönyvet támogatja. A VMware virtuális gépek esetében a Azure Migrate ügynök nélküli áttelepítési lehetőséget is biztosít.
- Új áttelepítési funkciókat rangsorolunk a Azure Migrate: csak a kiszolgáló áttelepítését szolgáló eszközhöz. Ezek a szolgáltatások nem célozzák meg Site Recovery.

[Azure site Recovery](../site-recovery/site-recovery-overview.md) csak vész-helyreállításra használható.

A Azure Migrate: a kiszolgáló áttelepítési eszköze néhány háttér-Site Recovery funkciót használ néhány helyszíni gép áttelepítésének áttelepítéséhez.

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>Van egy olyan projektem, amely a Azure Migrate korábbi klasszikus élményét. Hogyan az új verzió használatának megkezdése?

A klasszikus Azure Migrate kivonása február 2024. Február 2024 után a Azure Migrate klasszikus verziója már nem támogatott, és a klasszikus projektben lévő leltári metaadatokat törli a rendszer. Az előző verzióban lévő projektek vagy összetevők nem frissíthetők az új verzióra. [Létre kell hoznia egy új Azure Migrate projektet](create-manage-projects.md), és [hozzá kell adnia az értékelési és áttelepítési eszközöket](./create-manage-projects.md) . Az oktatóanyagok segítségével megismerheti, hogyan használhatja az elérhető értékelési és áttelepítési eszközöket. Ha egy klasszikus projekthez csatlakoztatott Log Analytics munkaterülettel rendelkezik, a klasszikus projekt törlése után csatolhatja azt egy aktuális verzió projekthez.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Mi a különbség a Azure Migrate: Server Assessment és a MAP Toolkit között?

A kiszolgáló értékelése értékelést nyújt az áttelepítésre való felkészüléshez és az Azure-ba való áttelepítéshez szükséges munkaterhelések kiértékeléséhez. A [Microsoft Assessment and Planning (MAP) eszközkészlet](https://www.microsoft.com/download/details.aspx?id=7826) más feladatokkal is segít, beleértve a Windows ügyfél és kiszolgáló operációs rendszereinek újabb verzióinak áttelepítésének megtervezését, valamint a szoftverek használatának nyomon követését. Ezekben a forgatókönyvekben folytassa a MAP Toolkit használatát.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Mi a különbség a kiszolgálók értékelése és a Site Recovery Deployment Planner között?

A kiszolgáló értékelése egy áttelepítési tervező eszköz. A Site Recovery Deployment Planner vész-helyreállítási tervezési eszköz.

Válassza ki az eszközt a kívánt műveletek alapján:

- Helyszíni **áttelepítés megtervezése az Azure-** ba: Ha a helyszíni kiszolgálókat az Azure-ba szeretné áttelepíteni, használja az áttelepítés megtervezésére szolgáló kiszolgáló-értékelést. A kiszolgáló értékelése a helyszíni számítási feladatokat értékeli, és útmutatást és eszközöket biztosít a Migrálás megkönnyítéséhez. Az áttelepítési terv érvénybe lépését követően a Azure Migrate: kiszolgáló áttelepítése a gépek Azure-ba való áttelepítéséhez.
- Vész- **helyreállítási terv az Azure**-ba: Ha a helyszíni rendszerről az Azure-ba történő vész-helyreállítást tervezi a site Recovery használatával, használja a site Recovery Deployment Planner. A Deployment Planner a helyi környezet alapos, Site Recovery-specifikus értékelését biztosítja a vész-helyreállítási célra. A vész-helyreállítással kapcsolatos ajánlásokat, például a replikálást és a feladatátvételt ismerteti.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Hogyan működik a kiszolgáló áttelepítése a Site Recovery?

- Ha Azure Migrate-t használ: a kiszolgáló áttelepítése a helyszíni VMware virtuális gépek *ügynök* nélküli áttelepítésének elvégzésére, a Migrálás natív, Azure Migrate és site Recovery nincs használatban.
- Ha Azure Migrate-t használ: a kiszolgáló áttelepítése a VMware virtuális gépek *ügynök-alapú* áttelepítésének elvégzésére, illetve Hyper-V virtuális gépek vagy fizikai kiszolgálók áttelepítésére Azure Migrate: a kiszolgáló áttelepítése a Azure site Recovery replikációs motort használja.

## <a name="which-geographies-are-supported"></a>Mely földrajzi területek támogatottak?

Tekintse át a [nyilvános](migrate-support-matrix.md#supported-geographies-public-cloud) és a [Government-felhők](migrate-support-matrix.md#supported-geographies-azure-government) támogatott régióit.

## <a name="how-do-i-get-started"></a>Hogyan kezdhetek hozzá?

Azonosítsa a szükséges eszközt, majd adja hozzá az eszközt egy Azure Migrate projekthez. 

ISV-eszköz vagy-mozgató hozzáadása:

1. Első lépésként szerezze be a licencet, vagy regisztráljon az ingyenes próbaverzióra az eszköz házirendjének megfelelően. Az eszközök licencelése az ISV vagy az eszköz licencelési modelljének megfelelően történik.
2. Minden eszközön lehetőség van a Azure Migratehoz való kapcsolódásra. Az eszköz Azure Migrate való összekapcsolásához kövesse az eszköz útmutatásait és dokumentációját.

Az áttelepítési utat nyomon követheti a Azure Migrate projekt, az Azure és más eszközök között.

## <a name="how-do-i-delete-a-project"></a>Hogyan törölni a projektet?

További információ a [projektek törléséről](how-to-delete-project.md). 

## <a name="next-steps"></a>Következő lépések

Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).