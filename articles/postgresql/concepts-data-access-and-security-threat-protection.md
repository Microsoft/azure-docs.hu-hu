---
title: Komplex veszélyforrások elleni védelem – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerje meg a komplex veszélyforrások elleni védelem használatát a rendellenes adatbázis-tevékenységek észleléséhez, amely az adatbázis potenciális biztonsági fenyegetéseket jelez.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: cfe565c45ea6aa0a4bcfecc95b1e1149b17542a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708050"
---
# <a name="advanced-threat-protection-in-azure-database-for-postgresql---single-server"></a>Komplex veszélyforrások elleni védelem Azure Database for PostgreSQL – egyetlen kiszolgáló

Az Advanced Threat Protection for Azure Database for PostgreSQL észleli a rendellenes tevékenységeket, amelyek az adatbázisok hozzáférésére vagy kihasználására irányuló szokatlan és potenciálisan rosszindulatú kísérletekre utalnak.

> [!NOTE]
> A komplex veszélyforrások elleni védelem nyilvános előzetes verzióban érhető el.

A veszélyforrások elleni védelem a komplex veszélyforrások elleni védelem (ATP) ajánlat része, amely egy egységes csomag a speciális biztonsági funkciókhoz. A komplex veszélyforrások elleni védelem a [Azure Portalon](https://portal.azure.com) keresztül vagy [REST API](/rest/api/postgresql)használatával érhető el és kezelhető. A szolgáltatás általános célú és memóriára optimalizált kiszolgálókon érhető el.

> [!NOTE]
> A komplex veszélyforrások elleni védelem funkció a következő Azure Government-és szuverén felhő-régiókban **nem** érhető el: US Gov Texas, US Gov Arizona, US gov Iowa, USA, gov Virginia, US DoD – keleti régió, US DoD – középső régió, Közép-németország, Észak-Németország, Kelet-Kína, Kelet-Kína 2. Tekintse meg a [régiók által elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/) az általános termékek rendelkezésre állása érdekében.

## <a name="what-is-advanced-threat-protection"></a>Mi a komplex veszélyforrások elleni védelem?

A Azure Database for PostgreSQL komplex veszélyforrások elleni védelme új biztonsági réteget biztosít, amely lehetővé teszi az ügyfelek számára, hogy a rendellenes tevékenységekre vonatkozó biztonsági riasztások révén észlelni és reagálni tudjanak a potenciális fenyegetésekre. A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről, valamint a lehetséges sebezhetőségekről, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. A Azure Database for PostgreSQL komplex veszélyforrások elleni védelme a riasztásokat [Azure Security Centerokkal](https://azure.microsoft.com/services/security-center/)integrálja, amely a gyanús tevékenységek részleteit tartalmazza, és a fenyegetés kivizsgálásával és enyhítésével kapcsolatos lépéseket javasolja. A Azure Database for PostgreSQL komplex veszélyforrások elleni védelme egyszerűvé teszi a potenciális fenyegetések kezelését az adatbázison anélkül, hogy biztonsági szakértőnek kellene lennie, vagy speciális biztonsági monitorozási rendszereket kellene kezelnie. 

:::image type="content" source="media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png" alt-text="Komplex veszélyforrások elleni védelem fogalma":::

## <a name="advanced-threat-protection-alerts"></a>Komplex veszélyforrások elleni védelem – riasztások 
A Azure Database for PostgreSQL komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához, és a következő riasztásokat indíthatják el:
- **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, ha megváltozik a Azure Database for PostgreSQL-kiszolgáló hozzáférési mintája, ahol valaki szokatlan földrajzi helyről jelentkezett be a Azure Database for PostgreSQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan Azure-adatközpontból**: Ez a riasztás akkor aktiválódik, ha módosul a hozzáférési minta a Azure Database for PostgreSQL-kiszolgálón, ahol valaki egy szokatlan Azure-adatközpontból jelentkezett be a kiszolgálóra, amely a legutóbbi időszakban volt látható. Bizonyos esetekben a riasztás legitim műveletet észlel (az új alkalmazás az Azure-ban, Power BI, Azure Database for PostgreSQL a lekérdezés-szerkesztőben). Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés a nem ismert rendszerbiztonsági**csoporttól: Ez a riasztás akkor aktiválódik, ha módosul a hozzáférési minta a Azure Database for PostgreSQL-kiszolgálón, ahol valaki egy szokatlan rendszerbiztonsági tag (Azure Database for PostgreSQL felhasználó) használatával jelentkezett be a kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés potenciálisan kártékony alkalmazással**: Ez a riasztás akkor aktiválódik, ha valaki egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
- **Találgatásos kényszerített Azure Database for PostgreSQL hitelesítő adatok**: Ez a riasztás akkor aktiválódik, ha a különböző hitelesítő adatokkal rendelkező sikertelen bejelentkezések rendellenesen magas száma. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* A díjszabással kapcsolatos további információkért tekintse meg a [Azure Database for PostgreSQL díjszabási oldalát](https://azure.microsoft.com/pricing/details/postgresql/) . 
* [Azure Database for PostgreSQL komplex veszélyforrások elleni védelem](howto-database-threat-protection-portal.md) konfigurálása a Azure Portal használatával  
