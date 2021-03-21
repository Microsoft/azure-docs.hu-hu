---
title: Gyakran használt Azure Sentinel-munkafüzetek | Microsoft Docs
description: Ismerje meg a leggyakrabban használt munkafüzeteket a népszerű, beépített Azure Sentinel-erőforrások használatához.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/07/2021
ms.author: bagol
ms.openlocfilehash: a37501498a9222025860702a7f29dccc9abfc8f7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102450062"
---
# <a name="commonly-used-azure-sentinel-workbooks"></a>Gyakran használt Azure Sentinel-munkafüzetek

A következő táblázat felsorolja a leggyakrabban használt, beépített Azure Sentinel-munkafüzeteket.

A bal oldalon található **veszélyforrások** felügyelete alatt férhet hozzá a munkafüzetekhez az Azure sentinelben  >   , majd megkeresheti a használni kívánt munkafüzetet. További információ: [oktatóanyag: adatok megjelenítése és figyelése](tutorial-monitor-your-data.md).

|Munkafüzet neve  |Description  |
|---------|---------|
|**Elemzési hatékonyság**     |  Betekintést nyújt az elemzési szabályok hatékonyságára, hogy könnyebben elérhető legyen a SOC-teljesítmény. <br><br>További információ: [Data-Driven SOCs eszközkészlete](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152).|
|**Azure-tevékenység**     |     A teljes körű betekintést nyújt a szervezet Azure-tevékenységeibe az összes felhasználói művelet és esemény elemzésével és korrelációjának kiszámításával. <br><br>További információ: [Az Azure-beli tevékenység naplóinak naplózása](audit-sentinel-data.md#auditing-with-azure-activity-logs).    |
|**Azure AD-naplók**     |  A Azure Active Directory naplózási naplókat használ az Azure AD-forgatókönyvek betekintésének biztosításához. <br><br>További információ: gyors útmutató  [: az Azure Sentinel használatának első lépései](quickstart-get-visibility.md).     |
|**Azure AD-naplózási, tevékenység-és bejelentkezési naplók**     |   Betekintést nyújt Azure Active Directory naplózási, tevékenység-és bejelentkezési információkkal egy munkafüzettel. Ezt a munkafüzetet a biztonsági és az Azure-rendszergazdák is használhatják.      |
|**Azure AD bejelentkezési naplók**     | Az Azure AD bejelentkezési naplóit használja az Azure AD-forgatókönyvek betekintésének biztosításához.        |
|**Kiberbiztonsági érettségi modell minősítése (CMMC)**     |   A lehetővé teszi a Microsoft portfóliójában CMMC vezérlőkhöz igazított naplók megtekintésének mechanizmusát, beleértve a Microsoft biztonsági ajánlatait, az Office 365, a Teams, az Intune, a Windows Virtual Desktop és más szolgáltatásokat. <br><br>További információ: [kiberbiztonsági érettségi modell minősítése (CMMC) munkafüzet nyilvános előzetes](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cybersecurity-maturity-model-certification-cmmc/ba-p/2111184)verzióban.|
|**Adatgyűjtés állapotának figyelése**     |   Betekintést nyújt a munkaterület adatfeldolgozási állapotára. Megtekintheti a figyelőket és észlelheti a rendellenességeket a munkaterületek adatgyűjtési állapotának meghatározásához.  <br><br>További információ: [az adatösszekötők állapotának figyelése az Azure Sentinel-munkafüzettel](monitor-data-connector-health.md).    |
|**Event Analyzer**     |  Lehetővé teszi a Windows Eseménynapló-elemzések megismerését, naplózását és felgyorsítását, beleértve az összes esemény részleteit és attribútumait, például a biztonságot, az alkalmazást, a rendszert, a telepítést, a címtárszolgáltatást, a DNS-t stb.       |
|**Exchange Online**     |Betekintést nyújt a Microsoft Exchange Online-ba az összes Exchange-művelet és felhasználói tevékenység nyomon követésével és elemzésével.         |
|**Identitás és hozzáférés**     |   Betekintést nyújt az identitás-és hozzáférési műveletekre a Microsoft-termékek használata során, biztonsági naplók segítségével, amelyek naplózási és bejelentkezési naplókat tartalmaznak.     |
|**Incidens áttekintése**     |   Az osztályozás és a vizsgálat megkönnyítésére szolgál, amely részletes információkat biztosít az incidensről, beleértve az általános információkat, az entitások adatait, az osztályozási időt, a kockázatcsökkentő időt és a megjegyzéseket. <br><br>További információ: [Data-Driven SOCs eszközkészlete](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152).      |
|**Vizsgálati elemzések**     | Az elemzők számára az incidensek, a könyvjelzők és az entitások adataiba való betekintést nyújt. A gyakori lekérdezések és a részletes vizualizációk segítségével az elemzők gyanús tevékenységeket vizsgálnak meg.       |
|**Microsoft Cloud App Security – felderítési naplók**     |   Részletes információkat nyújt a szervezetében használt felhőalapú alkalmazásokról, valamint az egyes felhasználók és alkalmazások használati trendjeiről és részletezési adatairól.  <br><br>További információ: [adatok Összekötése Microsoft Cloud app Securityról](connect-cloud-app-security.md).|
|**MITRE ATT&CK-munkafüzet**     |   Részletesen ismerteti a MITRE ATT&a CK lefedettségét az Azure Sentinel számára.      |
|**Office 365**     |  Betekintést nyújt az Office 365-be az összes művelet és tevékenység nyomon követésével és elemzésével. A SharePoint, a OneDrive és az Exchange adatainak részletezése.       |
|**Biztonsági riasztások**     |  Biztonsági riasztásokat tartalmazó irányítópultot biztosít az Azure Sentinel-környezetben lévő riasztásokhoz. <br><br>További információ: [incidensek automatikus létrehozása a Microsoft biztonsági értesítéseiből](create-incidents-from-alerts.md).      |
|**Biztonsági műveletek hatékonysága**     |  A Security Operations Center (SOC) kezelői számára készült általános hatékonysági mérőszámok és a csapatuk teljesítményével kapcsolatos mértékek megtekintése. <br><br>További információ: [a SoC jobb kezelése az incidens metrikákkal](manage-soc-with-incident-metrics.md).  |
|**Fenyegetések felderítése**     | Elemzéseket nyújt a fenyegetésekről, beleértve a veszélyforrások típusát és súlyosságát, a veszélyforrások aktivitását az idő múlásával, valamint a más adatforrásokkal való korrelációt, beleértve az Office 365-et és a tűzfalakat.  <br><br>További információ: [fenyegetési intelligencia importálása az Azure sentinelbe](import-threat-intelligence.md).      |
|**Munkaterület naplózása**     |  Olyan munkaterület-naplózási jelentést biztosít, amely lehetővé teszi a lekérdezési futtatások megértését a munkaterületen.   <br><br>További információ: [Azure Sentinel-lekérdezések és-tevékenységek naplózása](audit-sentinel-data.md).  |
|     |         |

