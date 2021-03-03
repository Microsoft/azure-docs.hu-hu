---
title: Karbantartási időszak
description: Megtudhatja, hogyan konfigurálható a Azure SQL Database és a felügyelt példányok karbantartási ablaka.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/02/2021
ms.openlocfilehash: 9dc4d17ea95362dd915bd1dfdfd82f4cdec611b8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692810"
---
# <a name="maintenance-window-preview"></a>Karbantartási időszak (előzetes verzió)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A karbantartási időszak funkció lehetővé teszi, hogy a [Azure SQL Database](sql-database-paas-overview.md) és az [SQL felügyelt példányának](../managed-instance/sql-managed-instance-paas-overview.md)előre jelzett karbantartási időszakait ütemezze. 

További információ a karbantartási eseményekről: az [Azure karbantartási eseményeinek tervezése Azure SQL Database és az Azure SQL felügyelt példányain](planned-maintenance.md).

## <a name="overview"></a>Áttekintés

Az Azure rendszeres karbantartási frissítéseket végez a Azure SQL Database és az SQL felügyelt példányának erőforrásairól, amelyek gyakran tartalmazzák a mögöttes hardverek, szoftverek, a mögöttes operációs rendszer (OS) és az SQL-motor frissítését. A karbantartási frissítés során az erőforrások teljes mértékben elérhetők és elérhetők, de a karbantartási frissítések némelyikének feladatátvételre van szükségük, mivel az Azure rövid idő alatt elvégzi a példányok offline állapotba helyezését a karbantartási frissítések (átlagosan nyolc másodperc) alkalmazásával.  A tervezett karbantartási frissítések átlagosan 35 naponta történnek, ami azt jelenti, hogy az ügyfél körülbelül egy tervezett karbantartási eseményt várhat havonta Azure SQL Database vagy SQL felügyelt példányon, és csak az ügyfél által kiválasztott karbantartási időszakokban.   

A karbantartási időszak olyan üzleti számítási feladatokhoz készült, amelyek nem rugalmasak a tervezett karbantartási eseményekből eredő átmeneti kapcsolódási problémák miatt.

A karbantartási időszakot a Azure Portal, a PowerShell, a CLI vagy az Azure API használatával lehet konfigurálni. Konfigurálható a létrehozáskor, illetve a meglévő SQL-adatbázisok és az SQL-felügyelt példányok esetében is.

### <a name="gain-more-predictability-with-maintenance-window"></a>A karbantartási időszak további kiszámíthatósága

Alapértelmezés szerint az összes Azure SQL Database-adatbázis és felügyelt példány-adatbázis csak napi 5 – 8-án frissül, hogy elkerülje a maximális munkaidő-megszakítást. A helyi időt az erőforrást üzemeltető [Azure-régió](https://azure.microsoft.com/global-infrastructure/geographies/) határozza meg. A karbantartási frissítéseket a következő két További karbantartási időszakra kiválasztva tovább módosíthatja az adatbázisának megfelelő időre:

* **Alapértelmezett** ablak, 5 – 08:00 helyi idő szerint hétfő – vasárnap 
* Hét napja, 22:00 – 06:00 helyi idő szerint hétfő – csütörtök
* Hétvégi időszak, 10 – 6 helyi idő szerint péntek – vasárnap

A karbantartási időszak kiválasztása után a rendszer az összes tervezett karbantartási frissítést csak a választott ablakban fogja elvégezni.   

> [!Note]
> A tervezett karbantartási frissítések mellett ritka esetekben nem tervezett karbantartási események is okozhatják a rendelkezésre állást. 

### <a name="cost-and-eligibility"></a>Cost és támogathatóság

A karbantartási időszak kiválasztása a következő előfizetési [ajánlati típusok](https://azure.microsoft.com/support/legal/offer-details/)esetében díjmentes: utólagos elszámolású, felhőalapú megoldás-szolgáltató (CSP), Microsoft Enterprise vagy Microsoft Customer Agreement.

> [!Note]
> Az Azure-ajánlat az Ön Azure-előfizetésének típusa. Például az utólagos elszámolású előfizetések [, a](https://azure.microsoft.com/offers/ms-azr-0003p/) [Azure in Open Licencprogram](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/)és a [Visual Studio Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) minden Azure-ajánlat. Minden ajánlat vagy csomag eltérő feltételekkel és előnyökkel rendelkezik. Ajánlatát vagy tervét az előfizetés áttekintése mutatja be. Az előfizetés egy másik ajánlatra váltásával kapcsolatos további információkért lásd: [Azure-előfizetés módosítása egy másik ajánlatra](/azure/cost-management-billing/manage/switch-azure-offer).

## <a name="advance-notifications"></a>Előzetes értesítések

A karbantartási értesítések beállítható úgy, hogy az ügyfeleket 24 órával előre, a karbantartás időpontjában és a karbantartási időszak végén riasztást kapjanak a közelgő tervezett karbantartási eseményekről. További információ: [Advance Notifications (előzetes értesítések](advance-notifications.md)).

## <a name="availability"></a>Rendelkezésre állás

### <a name="supported-service-level-objectives"></a>Támogatott szolgáltatási szint célkitűzései

Az alapértelmezetttől eltérő karbantartási időszak az összes slo elérhető, kivéve a **következőt**:
* Rugalmas skálázás 
* Örökölt Gen4 virtuális mag
* Alapszintű, S0 és S1 
* DC, Fsv2, M-sorozat

### <a name="azure-region-support"></a>Azure-régió támogatása

Az alapértelmezetttől eltérő karbantartási időszak jelenleg a következő régiókban érhető el:

- Kelet-Ausztrália
- Délkelet-Ausztrália
- Dél-Brazília
- Közép-Kanada
- USA középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- Kelet-Japán
- NorthCentral minket
- Észak-Európa
- SouthCentral minket
- Délkelet-Ázsia
- Az Egyesült Királyság déli régiója
- Nyugat-Európa
- USA nyugati régiója
- USA 2. nyugati régiója

## <a name="gateway-maintenance-for-azure-sql-database"></a>Az átjáró karbantartása Azure SQL Database

A karbantartási időszakok maximális előnyének lekéréséhez győződjön meg arról, hogy az ügyfélalkalmazások használják az átirányítás kapcsolódási házirendjét. Az átirányítás a javasolt kapcsolati házirend, amelyben az ügyfelek közvetlenül az adatbázist üzemeltető csomóponthoz csatlakoznak, ami csökkenti a késést és a jobb teljesítményt.  

* Azure SQL Database a proxy kapcsolati házirendjét használó kapcsolatokat a kiválasztott karbantartási időszak és az átjáró-csomópont karbantartási időszaka is befolyásolhatja. Az átjáró-csomópontok karbantartási feladatátvétele azonban nem érinti a javasolt átirányítási kapcsolati házirendet használó ügyfélkapcsolatokat. 

* Az Azure SQL felügyelt példányaiban az átjáró-csomópontok [a virtuális fürtön belül](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) találhatók, és a felügyelt példányokkal megegyező karbantartási időszaktal rendelkeznek, így a proxy kapcsolati házirend használata nem teszi lehetővé a További karbantartási időszakokhoz való kapcsolódást.

További információ a Azure SQL Database az ügyfélkapcsolati házirendről: [Azure SQL Database-kapcsolatok házirendje](../database/connectivity-architecture.md#connection-policy). 

Az Azure SQL felügyelt példányának ügyfélkapcsolati házirendjével kapcsolatos további információkért lásd: az [Azure SQL felügyelt példányok kapcsolatainak típusai](../../azure-sql/managed-instance/connection-types-overview.md).


## <a name="next-steps"></a>Következő lépések

* [Előzetes értesítések](advance-notifications.md)
* [Karbantartási időszak konfigurálása](maintenance-window-configure.md)

## <a name="learn-more"></a>Tudjon meg többet

* [Karbantartási időszak – gyakori kérdések](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)
* [Az Azure karbantartási eseményeinek megtervezése Azure SQL Database és az Azure SQL felügyelt példányain](planned-maintenance.md)




