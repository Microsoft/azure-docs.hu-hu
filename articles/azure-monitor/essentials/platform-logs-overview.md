---
title: Az Azure platform naplói – áttekintés | Microsoft Docs
description: A Azure Monitor naplófájljainak áttekintése, amelyek gazdag és gyakori információkat biztosítanak az Azure-erőforrások működéséről.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.openlocfilehash: 3300b0587cbb6c2193b56c9152af0cb11ea51936
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033299"
---
# <a name="overview-of-azure-platform-logs"></a>Az Azure-platform naplóinak áttekintése
A platform naplói részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokhoz és az Azure-platformtól függenek. Ezek automatikusan létrejönnek, bár bizonyos platform-naplókat egy vagy több megőrzött célhelyre kell továbbítani. Ez a cikk áttekintést nyújt a platform naplóiról, beleértve az általuk biztosított információkat, valamint azt, hogy hogyan konfigurálhatja őket gyűjteményekhez és elemzésekhez.

## <a name="types-of-platform-logs"></a>A platform naplófájljainak típusai
A következő táblázat felsorolja az Azure különböző rétegeiben elérhető platform-naplókat.

| Napló | Réteg | Leírás |
|:---|:---|:---|
| [Erőforrásnaplók](./resource-logs.md) | Azure-erőforrások | Betekintést nyerhet az Azure-erőforrásokon (az *adatsíkon*) végrehajtott műveletekre, például a titkos kulcs beszerzése egy Key Vault vagy egy adatbázisra vonatkozó kérelem elkészítése. Az erőforrás-naplók tartalma az Azure-szolgáltatás és az erőforrás típusa szerint változik.<br><br>*Az erőforrás-naplókat korábban diagnosztikai naplóknak nevezzük.*  |
| [Tevékenységnapló](../essentials/activity-log.md) | Azure-előfizetés | Betekintést nyújt a műveletekre az előfizetésben lévő egyes Azure-erőforrásokon kívülről (*a felügyeleti síkon*), valamint a Service Health események frissítései mellett. A tevékenység naplójának használatával meghatározhatja a _mi_, _ki_ és _Mikor_ az előfizetésben lévő erőforrásokra vonatkozó írási műveletek (Put, post, DELETE) esetében. Minden egyes Azure-előfizetéshez egyetlen tevékenységi napló van. |
| [Az Azure Active Directory naplói](../../active-directory/reports-monitoring/overview-reports.md) | Azure-bérlő |  A bejelentkezési tevékenység előzményeit és a Azure Active Directory egy adott bérlő esetében végrehajtott módosítások naplózási nyomvonalát tartalmazza.   |

> [!NOTE]
> Az Azure-tevékenység naplója elsősorban a Azure Resource Managerban bekövetkező tevékenységek esetében fordul elő. A klasszikus/RDFE modellt használó erőforrásokat nem követi nyomon. Néhány klasszikus erőforrástípus rendelkezik egy proxy erőforrás-szolgáltatóval Azure Resource Managerban (például: Microsoft. ClassicCompute). Ha egy klasszikus erőforrástípust használ a Azure Resource Manager ezen proxy erőforrás-szolgáltatók használatával, a műveletek megjelennek a tevékenység naplójában. Ha a Azure Resource Manager proxyn kívüli klasszikus erőforrástípus is működik, a műveletek csak a műveleti naplóban lesznek rögzítve. A műveleti naplót a portál egy külön szakasza is megkeresheti.

![Platformnaplók áttekintése](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Platform naplófájljainak megtekintése
A különböző Azure platform-naplók megtekintésére és elemzésére különböző lehetőségek állnak rendelkezésre.

- Tekintse meg a Azure Portal és a PowerShell és a parancssori felület eseményeinek elérését ismertető naplót. A részletekért tekintse [meg a tevékenység naplójának megtekintése](../essentials/activity-log.md#view-the-activity-log) című témakört. 
- Azure Active Directory biztonsági és tevékenységi jelentések megtekintése a Azure Portalban. Lásd: [Mik azok a Azure Active Directory-jelentések?](../../active-directory/reports-monitoring/overview-reports.md)  a részletekért.
- Az erőforrás-naplókat a támogatott Azure-erőforrások automatikusan létrehozzák, de csak akkor érhetők el, ha nem küldi őket a [célhelyre](#destinations). 

## <a name="destinations"></a>Célhelyek
A figyelési követelményektől függően a következő táblázatban szereplő célok közül egy vagy több célhelyre is küldhet platform-naplókat. [Diagnosztikai beállítások létrehozásával](../essentials/diagnostic-settings.md)konfigurálja a platform-naplók célhelyeit.

| Cél | Description |
|:---|:---|
| Log Analytics-munkaterület | Elemezze az összes Azure-erőforrás naplóit, és használja ki az összes elérhető funkciót [Azure monitor naplókhoz](../logs/data-platform-logs.md) , beleértve a [naplózási lekérdezéseket](../logs/log-query-overview.md) és a [naplózási riasztásokat](../alerts/alerts-log.md). Egy interaktív jelentés részeként rögzítheti egy naplóbeli lekérdezés eredményeit egy Azure-irányítópulton, vagy belefoglalhatja azt egy munkafüzetbe. |  |
| Eseményközpont | Platform-naplóbejegyzések küldése az Azure-on kívül, például egy harmadik féltől származó SIEM vagy egyéni telemetria platformra.
| Azure Storage tárterület | Archiválja a naplókat a naplózáshoz vagy a biztonsági mentéshez. |

- A műveletnapló vagy az erőforrás-naplók diagnosztikai beállításainak létrehozásával kapcsolatos részletekért lásd: [diagnosztikai beállítások létrehozása a platform-naplók és-metrikák különböző célhelyekre küldéséhez](../essentials/diagnostic-settings.md). 
- A Azure Active Directory naplók diagnosztikai beállításainak létrehozásával kapcsolatos részletekért tekintse meg a következő cikkeket.
  - [Azure AD-naplók integrálása Azure Monitor naplókkal](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
  - [Oktatóanyag: stream Azure Active Directory naplók az Azure Event hub-ba](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
  - [Oktatóanyag: Azure AD-naplók archiválása Azure Storage-fiókba](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)



## <a name="next-steps"></a>Következő lépések

* [További információ a tevékenység naplóról](../essentials/activity-log.md)
* [További információ az erőforrás-naplókról](./resource-logs.md)