---
title: Mi a megoldás az Oracle WebLogic Server Azure-beli futtatására Virtual Machines
description: Ismerje meg, hogyan futtathatja az Oracle WebLogic Servert Microsoft Azure Virtual Machineson.
author: rezar
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/23/2020
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: d8639214773a4444876b2a89dda5dd831b8c36ce
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927290"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-azure-virtual-machines"></a>Melyik megoldásokkal futtatható az Oracle WebLogic Server az Azure Virtual Machinesben?

Ez az oldal az Oracle WebLogic Server (WLS) Azure-beli virtuális gépeken való futtatásának megoldásait ismerteti. Ezeket a megoldásokat az Oracle és a Microsoft közösen fejleszti és támogatja.

A WLS az Azure Kubernetes szolgáltatásban is futtatható. Ennek a megoldásnak a leírását [ebben a Microsoft-cikkben](/azure/virtual-machines/workloads/oracle/weblogic-aks)találja.

A WLS egy vezető Java-alkalmazáskiszolgáló, amely a legtöbb kritikus vállalati Java-alkalmazást futtatja szerte a világon. A WLS az Oracle Software Suite-hoz készült middleware Foundation-t képezi. Az Oracle és a Microsoft elkötelezte magát arra, hogy az Azure-ban vezető felhőalapú platformként futtatott munkaterheléseket és rugalmasságot biztosító WLS ügyfelei számára.

Az Azure WLS-megoldások célja, hogy a lehető legegyszerűbb legyen a Java EE-alkalmazások Azure-beli virtuális gépekre történő átállítása és átirányítása a legtöbb kihelyezett művelet automatizálásával. A megoldások automatikusan kiépítik a virtuális hálózati, tárolási, Java-és Linux-erőforrásokat. Minimális erőfeszítéssel a WebLogic-kiszolgáló telepítve van. A megoldások hálózati biztonsági csoporttal állíthatnak be biztonságot, terheléselosztást végeznek az Azure app Gateway és a Azure Active Directory használatával történő hitelesítéssel. Emellett automatikusan csatlakozhat a meglévő adatbázishoz, beleértve az Azure PostgreSQL-t, az Azure SQL-t és a Oracle DBt az Oracle-felhőben vagy az Azure-ban. A megoldások útiterve lehetővé teszi az elosztott naplózás és az elosztott gyorsítótárazás engedélyezését az Oracle-koherencia használatával.  

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="A WebLogic-kiszolgáló Azure-beli üzembe helyezéséhez használhatja a Azure Portal":::

A különböző forgatókönyvek teljesítéséhez négy ajánlat áll rendelkezésre: [egyetlen csomópont rendszergazdai kiszolgáló nélkül](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls), [egyetlen csomópont rendszergazdai kiszolgálóval](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin), [fürttel](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)és [dinamikus fürttel](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster). Az ajánlatok díjmentesen elérhetők. Ezek az ajánlatok alább olvashatók és kapcsolhatók.

_Ezek az ajánlatok saját licencet kapnak_ . Feltételezik, hogy már megkapta a megfelelő licenceket az Oracle-vel, és megfelelő licenccel rendelkezik az ajánlatok Azure-beli futtatásához.

Az ajánlatok számos operációsrendszer-, Java-és WLS-verziót támogatnak alaplemezképeken keresztül (például WebLogic Server 14 és JDK 11 Oracle Linux 7,6). Ezek az alaplemezképek az Azure-ban is elérhetők az Azure-ban. Az alaplemezképek olyan ügyfelek számára alkalmasak, akik összetett, testreszabott Azure-alapú üzembe helyezést igényelnek. Az alapképek aktuális készlete [itt](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=WebLogic%20Server%20Base%20Image&page=1)érhető el.

_Ha szeretné, hogy az áttelepítési forgatókönyvek szorosan együttműködve legyenek az ezen ajánlatokat fejlesztő mérnöki csapattal, válassza a [CONTACT ME](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)_ [Piactéri ajánlat – áttekintés oldalon](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)található Kapcsolatfelvétel gombot. A program-menedzserek, az építészek és a mérnökök hamarosan megközelítik Önt, és megkezdik a szoros együttműködést. Az áttelepítési forgatókönyvben való együttműködés lehetősége ingyenes, amíg az ajánlatok aktív kezdeti fejlesztés alatt állnak.

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server – egyetlen csomópont

[Ez az ajánlat](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls) egyetlen virtuális gépet foglal le, és telepíti a WLS. Nem hoz létre tartományt, vagy nem indítja el a felügyeleti kiszolgálót. Az egycsomópontos ajánlat olyan forgatókönyvekhez hasznos, amelyek nagyvállalati konfigurációval rendelkeznek.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic-kiszolgáló felügyeleti kiszolgálóval

[Ez az ajánlat](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin) egyetlen virtuális gépet foglal le, és telepíti a WLS. Létrehoz egy tartományt, és elindítja a felügyeleti kiszolgálót. A tartományt kezelheti, és azonnal megkezdheti az alkalmazások központi telepítésének megkezdését.

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server-fürt

[Ez az ajánlat](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster) egy magasan elérhető WLS virtuális gépekből álló fürtöt hoz létre. Alapértelmezés szerint a felügyeleti kiszolgáló és az összes felügyelt kiszolgáló elindult. Kezelheti a fürtöt, és azonnal megkezdheti a magasan elérhető alkalmazások első lépéseit.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server dinamikus fürt

[Ez az ajánlat](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster) egy magasan elérhető és méretezhető dinamikus fürtöt hoz létre a WLS virtuális gépekhez. Alapértelmezés szerint a felügyeleti kiszolgáló és az összes felügyelt kiszolgáló elindult.

A megoldások az éles környezetben felépíthető üzembe helyezési architektúrák széles választékát teszik lehetővé. Az üzleti alkalmazások fejlesztésére összpontosítva az áttelepítési esetek többségét a lehető legtermelékenyebb módon lehet teljesíteni.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-vms.png" alt-text="A WebLogic-kiszolgáló Azure-beli üzembe helyezéséhez használhatja a Azure Portal":::

A megoldások által automatikusan kiépített igényeken túl az ügyfelek rugalmasan testre szabhatják az üzembe helyezést. Valószínű, hogy az alkalmazások üzembe helyezése során az ügyfelek a további Azure-erőforrásokat is integrálják az üzemelő példányokkal. Javasoljuk, hogy visszajelzéseket nyújtson a megoldások további tökéletesítéséről.

## <a name="next-steps"></a>Következő lépések

Fedezze fel az ajánlatokat az Azure-ban.

> [!div class="nextstepaction"]
> [Oracle WebLogic Server – egyetlen csomópont](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic-kiszolgáló felügyeleti kiszolgálóval](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server-fürt](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server dinamikus fürt](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
