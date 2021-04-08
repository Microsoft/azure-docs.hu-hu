---
title: Az Azure éles hálózat kezelése – Microsoft Azure
description: Ez a cikk azt ismerteti, hogy a Microsoft hogyan kezeli és működteti az Azure-beli üzemi hálózatot az Azure-adatközpontok biztonságossá tételéhez.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: d41fe409b4a44a4c2af3670d76dd3a83a300feae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727118"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Az Azure üzemi hálózatának felügyelete és működése    
Ez a cikk azt ismerteti, hogy a Microsoft hogyan kezeli és működteti az Azure-beli üzemi hálózatot az Azure-adatközpontok biztonságossá tételéhez.

## <a name="monitor-log-and-report"></a>Figyelés, napló és jelentés

Az Azure-beli üzemi hálózat felügyelete és üzemeltetése az Azure-beli operatív csapatok és a Azure SQL Database között összehangolt erőfeszítés. A csapatok számos rendszer-és alkalmazás-teljesítményfigyelő eszközt használnak a környezetben. És a megfelelő eszközöket használják a hálózati eszközök, kiszolgálók, szolgáltatások és alkalmazás-folyamatok figyelésére.

Az Azure-környezetben futó szolgáltatások biztonságos végrehajtásának biztosítása érdekében az operatív csapatok több megfigyelési, naplózási és jelentéskészítési szintet is megvalósítanak, beleértve a következő műveleteket:

- A Microsoft monitoring Agent (MMA) elsősorban számos helyről gyűjti a megfigyelési és diagnosztikai napló adatait, beleértve a háló vezérlőt (FC) és a gyökér operációs rendszert (OS), majd a naplófájlba írja azt. Az ügynök végül egy előre konfigurált Azure Storage-fiókba küldi le az információk egy megemésztett részhalmazát. Emellett a különálló figyelési és diagnosztikai szolgáltatás a különböző monitorozási és diagnosztikai adatokat is beolvassa, és összefoglalja az adatokat. A monitorozási és diagnosztikai szolgáltatás az adatokat egy integrált naplóba írja. Az Azure az egyéni fejlesztésű Azure biztonsági monitorozást használja, amely az Azure monitoring rendszer kiterjesztése. Olyan összetevőket tartalmaz, amelyek megfigyelik, elemzik és jelentést készítenek a platform különböző pontjairól származó biztonsági eseményekről.

- A Azure SQL Database Windows Fabric platform a Azure SQL Database felügyeletét, üzembe helyezését, fejlesztését és operatív felügyeleti szolgáltatásait biztosítja. A platform elosztott, többlépéses üzembe helyezési szolgáltatásokat, az állapot figyelését, az automatikus javításokat és a szolgáltatási verziók megfelelőségét kínálja. A következő szolgáltatásokat biztosítja:

   - A szolgáltatás-modellezési képességek magas megbízhatóságú fejlesztési környezettel rendelkeznek (a Datacenter-fürtök költségesek és szűkösek).
   - Egy kattintással üzembe helyezési és frissítési munkafolyamatok a szolgáltatás rendszerindításához és karbantartásához.
   - Állapot-jelentéskészítés automatizált javítási munkafolyamatokkal az önjavító funkció engedélyezéséhez.
   - Valós idejű figyelés, riasztás és hibakeresési lehetőségek az elosztott rendszerek csomópontjain.
   - Operatív adatok és mérőszámok központosított gyűjtése az elosztott kiváltó okok elemzéséhez és a szolgáltatások betekintéséhez.
   - Operatív eszközök az üzembe helyezéshez, a változások kezeléséhez és a figyeléshez.
   - A Azure SQL Database Windows Fabric platform és a watchdog parancsfájlok folyamatosan futnak, és valós időben figyelik a figyelést.

Ha bármilyen rendellenesség történik, az incidens-válasz folyamatát, majd az Azure incidens osztályozási csapata aktiválva van. A megfelelő Azure-támogatási munkatársak értesítést kapnak az incidensre való reagálásról. A probléma nyomon követése és feloldása központi jegyrendszer-kezelő rendszeren történik. A rendszer rendelkezésre állási metrikái a nem közzétételi szerződés (NDA) alatt, illetve kérelem esetén érhetők el.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Vállalati hálózat és többtényezős hozzáférés az éles környezethez
A vállalati hálózat felhasználói bázisa az Azure-támogatási munkatársakat is tartalmazza. A vállalati hálózat támogatja a belső vállalati funkciókat, és hozzáférést biztosít az Azure ügyfélszolgálatához használt belső alkalmazásokhoz. A vállalati hálózat mind logikailag, mind fizikailag el van különítve az Azure éles hálózatával. Az Azure-munkatársak Azure-munkaállomások és laptopok használatával férhetnek hozzá a vállalati hálózathoz. A vállalati hálózati erőforrások eléréséhez minden felhasználónak rendelkeznie kell egy Azure Active Directory (Azure AD) fiókkal, beleértve a felhasználónevet és a jelszót is. A vállalati hálózati hozzáférés az Azure AD-fiókokat használja, amelyek az összes Microsoft-munkatárs,-alvállalkozó és-gyártó számára elérhetők, és a Microsoft Information Technology kezeli őket. Az egyedi felhasználói azonosítók a Microsoftnál alkalmazott foglalkoztatási állapotuk alapján különböztetik meg a személyzetet.

A belső Azure-alkalmazásokhoz való hozzáférés vezérlése Active Directory összevonási szolgáltatások (AD FS) (AD FS) hitelesítéssel történik. AD FS a Microsoft Information Technology által üzemeltetett szolgáltatás, amely biztonságos jogkivonat-és felhasználói jogcímek alkalmazásával biztosítja a vállalati hálózati felhasználók hitelesítését. AD FS lehetővé teszi a belső Azure-alkalmazások számára a felhasználók hitelesítését a Microsoft vállalati Active Directory-tartományon. Az éles hálózat vállalati hálózati környezetből való eléréséhez a felhasználóknak a multi-Factor Authentication használatával kell hitelesíteniük magukat.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni arról, hogy a Microsoft Hogyan védi az Azure-infrastruktúrát, olvassa el a következő témakört:

- [Azure-létesítmények,-telephelyek és fizikai biztonság](physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Azure Information System-összetevők és-határok](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure-beli üzemi hálózat](production-network.md)
- [Azure SQL Database biztonsági funkciók](infrastructure-sql.md)
- [Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](infrastructure-integrity.md)
- [Azure Customer-adatvédelem](protection-customer-data.md)
