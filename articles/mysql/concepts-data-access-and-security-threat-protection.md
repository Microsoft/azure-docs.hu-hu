---
title: Komplex veszélyforrások elleni védelem – Azure Database for MySQL
description: Ismerje meg a komplex veszélyforrások elleni védelem fogalmait, amely észleli a rendellenes adatbázis-tevékenységeket, amelyek az adatbázis potenciális biztonsági fenyegetéseit jelzik.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c9e884d153e85e7b68dee38494ac5d6f4271978a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542575"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Azure Database for MySQL komplex veszélyforrások elleni védelem

Az Advanced Threat Protection for Azure Database for MySQL észleli a rendellenes tevékenységeket, amelyek az adatbázisok hozzáférésére vagy kihasználására irányuló szokatlan és potenciálisan rosszindulatú kísérletekre utalnak.

> [!NOTE]
> A komplex veszélyforrások elleni védelem nyilvános előzetes verzióban érhető el.

A komplex veszélyforrások elleni védelem a fejlett adatbiztonsági ajánlat része, amely egy egységes csomag a speciális biztonsági funkciókhoz. A komplex veszélyforrások elleni védelem a [Azure Portalon](https://portal.azure.com) keresztül vagy [REST API](/rest/api/mysql)használatával érhető el és kezelhető. A szolgáltatás általános célú és memóriára optimalizált kiszolgálókon érhető el.

> [!NOTE]
> A komplex veszélyforrások elleni védelem funkció a következő Azure Government-és szuverén felhő-régiókban **nem** érhető el: US Gov Texas, US Gov Arizona, US gov Iowa, USA, gov Virginia, US DoD – keleti régió, US DoD – középső régió, Közép-németország, Észak-Németország, Kelet-Kína, Kelet-Kína 2. Tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/) az általános termékek rendelkezésre állása érdekében.


## <a name="what-is-advanced-threat-protection"></a>Mi a komplex veszélyforrások elleni védelem?

A Azure Database for MySQL komplex veszélyforrások elleni védelme új biztonsági réteget biztosít, amely lehetővé teszi az ügyfelek számára, hogy a rendellenes tevékenységekre vonatkozó biztonsági riasztások révén észlelni és reagálni tudjanak a potenciális fenyegetésekre. A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről, valamint a lehetséges sebezhetőségekről, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. A Azure Database for MySQL komplex veszélyforrások elleni védelme a riasztásokat [Azure Security Centerokkal](https://azure.microsoft.com/services/security-center/)integrálja, amely a gyanús tevékenységek részleteit tartalmazza, és a fenyegetés kivizsgálásával és enyhítésével kapcsolatos lépéseket javasolja. A Azure Database for MySQL komplex veszélyforrások elleni védelme egyszerűvé teszi a potenciális fenyegetések kezelését az adatbázison anélkül, hogy biztonsági szakértőnek kellene lennie, vagy speciális biztonsági monitorozási rendszereket kellene kezelnie. 

:::image type="content" source="media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png" alt-text="Komplex veszélyforrások elleni védelem fogalma":::

## <a name="advanced-threat-protection-alerts"></a>Komplex veszélyforrások elleni védelem – riasztások 
A Azure Database for MySQL komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához, és a következő riasztásokat indíthatják el:
- **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, ha megváltozik a Azure Database for MySQL-kiszolgáló hozzáférési mintája, ahol valaki szokatlan földrajzi helyről jelentkezett be a Azure Database for MySQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan Azure-adatközpontból**: Ez a riasztás akkor aktiválódik, ha módosul a hozzáférési minta a Azure Database for MySQL-kiszolgálón, ahol valaki egy szokatlan Azure-adatközpontból jelentkezett be a kiszolgálóra, amely a legutóbbi időszakban volt látható. Bizonyos esetekben a riasztás legitim műveletet észlel (az új alkalmazás az Azure-ban, Power BI, Azure Database for MySQL a lekérdezés-szerkesztőben). Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés a nem ismert rendszerbiztonsági** csoporttól: Ez a riasztás akkor aktiválódik, ha módosul a hozzáférési minta a Azure Database for MySQL-kiszolgálón, ahol valaki egy szokatlan rendszerbiztonsági tag (Azure Database for MySQL felhasználó) használatával jelentkezett be a kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés potenciálisan kártékony alkalmazással**: Ez a riasztás akkor aktiválódik, ha valaki egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
- **Találgatásos kényszerített Azure Database for MySQL hitelesítő adatok**: Ez a riasztás akkor aktiválódik, ha a különböző hitelesítő adatokkal rendelkező sikertelen bejelentkezések rendellenesen magas száma. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Security Center](../security-center/security-center-introduction.md)
* A díjszabással kapcsolatos további információkért tekintse meg a [Azure Database for MySQL díjszabási oldalát](https://azure.microsoft.com/pricing/details/mysql/) . 
* [Azure Database for MySQL komplex veszélyforrások elleni védelem](howto-database-threat-protection-portal.md) konfigurálása a Azure Portal használatával