---
title: Azure AD Connect Health – az állapotfigyelő szolgáltatásra vonatkozó adatok nem naprakészek a riasztások számára | Microsoft Docs
description: Ez a dokumentum leírja, hogy az "állapotfigyelő szolgáltatásbeli adatok nem naprakészek" riasztást és a hibaelhárítás módját ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00518eb91e57efaacb7abc63b6ad4531619be2ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012869"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Az állapotfigyelő szolgáltatás nem naprakész állapotú riasztás

## <a name="overview"></a>Áttekintés

Az ügynökök a helyszíni gépeken, amelyek Azure AD Connect Health figyelik rendszeresen az adatok feltöltését a Azure AD Connect Health szolgáltatásba. Ha a szolgáltatás nem kap adatokat az ügynöktől, a portál által megjelenített információk elavultak lesznek. A probléma kiemeléséhez a szolgáltatás nem naprakész állapotba állítja az állapotfigyelő **szolgáltatás adatait** . Ez a riasztás akkor jön létre, ha a szolgáltatás nem kapott teljes adatkérést az elmúlt két órában.  

- A **Figyelmeztetési** állapot riasztást küld, ha a állapotfigyelő szolgáltatás csak a kiszolgáló által az elmúlt két órában küldött **részleges** adattípusokat fogadta. A figyelmeztetési állapot riasztása nem indítja el az e-mailes értesítéseket a konfigurált címzetteknek. 
- A **hiba** állapotának riasztása akkor következik be, ha a állapotfigyelő szolgáltatás nem kapott adattípust a kiszolgálóról az elmúlt két órában. A hiba állapota riasztás elindítja az e-mailes értesítéseket a konfigurált címzetteknek.

A szolgáltatás a szolgáltatás típusától függően lekéri a helyszíni gépeken futó ügynökök adatait. A következő táblázat felsorolja a gépen futó ügynököket, azok teendőit és a szolgáltatás által generált adattípusokat. Bizonyos esetekben a folyamat több szolgáltatást is magában foglal, így ezek közül bármelyik lehet a bűnös. 

## <a name="understanding-the-alert"></a>A riasztás ismertetése

A **riasztás részletei** panelen látható, hogy mikor történt a riasztás, és utoljára észlelte a rendszer. A két óránként futó háttérbeli folyamat létrehozza és újraértékeli a riasztást. A következő példában a kezdeti riasztás a 03/10-es időpontban történt a 9:59 ÓRAKOR. A riasztás továbbra is létezett a 03/12-on 10:00 ÓRAKOR, ha a riasztást újra kiértékelték. A panel azt az időpontot is részletezi, amikor a Állapotfigyelő szolgáltatás utoljára kapott egy adott adattípust. 
 
 ![Azure AD Connect Health riasztás részletei](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
A következő táblázat a megfelelő adattípusokhoz rendeli a szolgáltatási típusokat:

| Szolgáltatás típusa | Ügynök (Windows-szolgáltatás neve) | Cél | Adattípus létrehozva  |
| --- | --- | --- | --- |  
| Azure AD Connect (szinkronizálás) | Azure AD Connect Health Sync Insights szolgáltatás | HRE-kapcsolatra vonatkozó információk gyűjtése (összekötők, szinkronizálási szabályok stb.) | – AadSyncService-SynchronizationRules <br />  – AadSyncService-Connectors <br /> – AadSyncService-GlobalConfigurations  <br />  – AadSyncService-RunProfileResults <br /> – AadSyncService-ServiceConfigurations <br /> – AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring szolgáltatás | HRE-specifikus teljesítményszámláló-számlálók, ETW-Nyomkövetések, fájlok gyűjtése | Teljesítményszámláló |
| AD DS | Azure AD Connect Health AD DS Insights szolgáltatás | Szintetikus tesztek végrehajtása, topológiai információk gyűjtése, replikációs metaadatok |  -Add-TopologyInfo-JSON <br /> -Common-TestData-JSON (létrehozza a teszt eredményeit)   | 
|  | Azure AD Connect Health AD DS Monitoring szolgáltatás | A Hozzáadás-specifikus teljesítményszámláló-számlálók, ETW-Nyomkövetések és-fájlok gyűjtése | – Teljesítményszámláló  <br /> -Common-TestData-JSON (feltölti a teszt eredményeit)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics szolgáltatás | Szintetikus tesztek végrehajtása | TestResult (a tesztek eredményeinek létrehozása) | 
| | Azure AD Connect Health AD FS Insights szolgáltatás  | ADFS használati metrikáinak összegyűjtése | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring szolgáltatás | ADFS-specifikus teljesítményszámláló-számlálók, ETW-Nyomkövetések, fájlok gyűjtése | TestResult (a tesztek eredményeinek feltöltése) |

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések 

A probléma diagnosztizálásához szükséges lépések alább láthatók. Az első olyan alapszintű ellenőrzések összessége, amelyek az összes szolgáltatás típusával közösek. 

> [!IMPORTANT] 
> Ez a riasztás a kapcsolati állapot [adatmegőrzési szabályzatát](reference-connect-health-user-privacy.md#data-retention-policy) követi

* Ellenőrizze, hogy telepítve vannak-e az ügynökök legújabb verziói. Tekintse meg a [kiadások előzményeit](reference-connect-health-version-history.md). 
* Győződjön meg arról, hogy az Azure AD Connect Health Agents-szolgáltatások **futnak** a gépen. Az AD FS-hez készült Connect Healthnek például három szolgáltatással kell rendelkeznie.
  ![Az Azure AD Connect Health ellenőrzése](./media/how-to-connect-health-agent-install/install5.png)

* Győződjön meg arról, hogy átugorja és megfelel a [követelmények szakasznak](how-to-connect-health-agent-install.md#requirements).
* A kapcsolódási problémák felderítéséhez használja a [test connectivity Tool eszközt](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) .
* Ha HTTP-proxyval rendelkezik, kövesse az alábbi [konfigurációs lépéseket](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Következő lépések
Ha a fenti lépések bármelyike problémát észlelt, javítsa ki, és várjon, amíg a riasztás feloldódik. A riasztások hátterének folyamata 2 óránként fut, így a riasztás feloldása akár 2 óráig is eltarthat. 

* [Adatmegőrzési szabályzat Azure AD Connect Health](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health FAQ (Azure AD Connect Health – gyakori kérdések)](reference-connect-health-faq.md)
