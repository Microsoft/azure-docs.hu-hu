---
title: Azure Active Directory tevékenység naplói a Azure Monitorban | Microsoft Docs
description: Bevezetés a Azure Active Directoryi tevékenység naplóiba Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/09/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73130c906d4d9f0da51db1b666e8562570cce40f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571273"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Azure AD-tevékenységnaplók az Azure Monitorban

A hosszú távú adatmegőrzéshez és az adatelemzéshez Azure Active Directory (Azure AD) tevékenység naplóit több végpontra is átirányíthatja. Ez a funkció lehetővé teszi a következőket:

* Archiválja az Azure AD-tevékenységek naplóit egy Azure Storage-fiókba, hogy hosszú ideig őrizze meg az adatok megőrzését.
* Stream Azure AD-tevékenységek naplóit az Azure Event hub for Analytics szolgáltatásban, a népszerű biztonsági információk és az eseménykezelő (SIEM) eszközök, például a splunk és a QRadar használatával.
* Integrálhatja az Azure AD-tevékenységek naplóit a saját egyéni bejelentkezési megoldásaival az Event hub-ba való továbbítással.
* Az Azure AD-tevékenység naplófájljainak elküldése Azure Monitor naplókba, így lehetővé teszi a kapcsolódó adatmegjelenítést, monitorozást és riasztást.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Támogatott jelentések

Ezzel a szolgáltatással átirányíthatja az Azure AD-naplókat és bejelentkezési naplókat az Azure Storage-fiókjába, az Event hub-ba, Azure Monitor-naplókba vagy egyéni megoldásba. 

* **Auditnaplók**: Az [auditnaplók tevékenységjelentés](concept-audit-logs.md) hozzáférést nyújt a bérlőn elvégzett összes feladat előzményeihez.
* **Bejelentkezési naplók**: A [bejelentkezések tevékenységjelentéssel](concept-sign-ins.md) meghatározhatja, hogy ki hajtotta végre az auditnaplók által jelentett feladatokat.

> [!NOTE]
> A B2C-hez kapcsolódó audit- és bejelentkezési tevékenységnaplók jelenleg nem támogatottak.
>

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:

* Azure-előfizetés. Ha nincs Azure-előfizetése, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/free/).
* Azure AD Ingyenes, Alapszintű, Prémium 1 vagy Prémium 2 [licenc](https://azure.microsoft.com/pricing/details/active-directory/) az Azure AD-auditnaplók eléréséhez az Azure Portalon. 
* Egy Azure AD-bérlő.
* Egy felhasználó, aki az adott Azure AD-bérlő **globális** vagy **biztonsági rendszergazdája**.
* Azure AD Prémium 1 vagy Prémium 2 [licenc](https://azure.microsoft.com/pricing/details/active-directory/) az Azure AD bejelentkezési naplók eléréséhez az Azure Portalon. 

Attól függően, hogy hová szeretné irányítani a naplózási adatokat, a következők valamelyikére is szüksége lesz:

* Egy Azure Storage-fiók, amelyen *ListKeys* jogosultsággal rendelkezik. Azt javasoljuk, hogy általános tárfiókot használjon, ne Blob Storage-fiókot. A tárolás díjszabásával kapcsolatban lásd az [Azure Storage-díjkalkulátort](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Azure Event Hubs-névtér a külső megoldásokkal való integrációhoz.
* Egy Azure Log Analytics-munkaterület, amely naplókat küld Azure Monitor naplókba.

## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok

Ha már rendelkezik Azure AD-licenccel, egy Azure-előfizetésre lesz szüksége a tárfiók és az eseményközpont beállításához. Az Azure-előfizetés ingyenesen érhető el, az Azure-erőforrások használatáért azonban fizetnie kell, beleértve az archiváláshoz használt tárfiókot és a streameléshez használt eseményközpontot. Az adatok mennyisége és ezáltal a vonatkozó költségek is a bérlő méretétől függően jelentős mértékben változhatnak. 

### <a name="storage-size-for-activity-logs"></a>Tevékenységnaplók tárterületmérete

Minden auditnapló-esemény körülbelül 2 KB adattárhelyet foglal el. A bejelentkezési eseménynaplók körülbelül 4 KB tárhellyel rendelkeznek. Egy 100 000 felhasználót számláló bérlőhöz, amely naponta körülbelül 1,5 millió eseményt hoz létre, hozzávetőleg napi 3 GB adattárhelyre lesz szüksége. Mivel az írási műveletek hozzávetőleg ötperces kötegekben történnek, havonta várhatóan körülbelül 9000 írási művelettel számolhat. 


Az alábbi táblázat tartalmaz egy költségbecslést a bérlő méretének függvényében egy általános célú v2-es tárfiókra az USA nyugati régiójában, legalább egyéves megőrzéssel. Az [Azure Storage-díjkalkulátor](https://azure.microsoft.com/pricing/details/storage/blobs/) használatával ennél pontosabb becslést is készíthet az alkalmazása várható adatmennyiségéről.


| Naplókategória | Felhasználók száma | Napi események | Havi adatmennyiség (becsült) | Havi költség (becsült) | Éves költség (becsült) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Naplózás | 100.000 | 1,5&nbsp;millió | 90 GB | 1,93 dollár | 23,12 dollár |
| Naplózás | 1,000 | 15 000 | 900 MB | 0,02 dollár | 0,24 dollár |
| Bejelentkezések | 1,000 | 34 800 | 4 GB | 0,13 dollár | 1,56 dollár |
| Bejelentkezések | 100.000 | 15&nbsp;millió | 1,7 TB | 35,41 dollár | 424,92 dollár |
 









### <a name="event-hub-messages-for-activity-logs"></a>Tevékenységnaplók eseményközpont-üzenetei

A rendszer az eseményeket körülbelül ötperces intervallumokba kötegeli, majd egyetlen üzenetben küldi el, amely az adott időablakba tartozó összes eseményt tartalmazza. Az eseményközpontban az egyes üzenetek mérete legfeljebb 256 KB lehet, és ha az adott időablakba tartozó üzenetek összesített mérete meghaladja ezt a mennyiséget, a rendszer több üzenetet küld. 

Például egy több mint 100 000 felhasználót számláló bérlőn általában körülbelül 18 esemény történik másodpercenként, ez ötpercenként 5400 eseményt jelent. Mivel az auditnaplók eseményenként nagyjából 2 KB méretűek, ez összesen 10,8 MB adatot jelent. Így az adott ötperces időablakban a rendszer 43 üzenetet küld az eseményközpontra. 

Az alábbi táblázat az USA nyugati régiójában található alapszintű Event hub becsült költségét tartalmazza, attól függően, hogy az események mennyisége milyen mennyiségtől függ a bérlőtől a bérlőig, mint a felhasználók bejelentkezési viselkedése stb. Az alkalmazáshoz várható adatmennyiség pontos becslésének kiszámításához használja a [Event Hubs árképzési számológépet](https://azure.microsoft.com/pricing/details/event-hubs/).

| Naplókategória | Felhasználók száma | Események száma másodpercenként | Események száma ötperces időközönként | Adatmennyiség az egyes időablakokban | Üzenetek száma időközönként | Üzenetek száma havonta | Havi költség (becsült) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Naplózás | 100.000 | 18 | 5400 | 10,8 MB | 43 | 371 520 | 10,83 dollár |
| Naplózás | 1,000 | 0.1 | 52 | 104 KB | 1 | 8640 | 10,80 dollár |
| Bejelentkezések | 100.000 | 18000 | 5 400 000 | 10,8 GB | 42188 | 364 504 320 | $23,9 |  
| Bejelentkezések | 1,000 | 178 | 53 400 | 106,8&nbsp;MB | 418 | 3 611 520 | 11,06 dollár |  

### <a name="azure-monitor-logs-cost-considerations"></a>A Azure Monitor naplózza a költségeket



| Naplókategória | Felhasználók száma | Napi események | Esemény havonta (30 nap) | Havi díj USD-ben (EST) |
|:-|--|--|--|-:|
| Naplózás és bejelentkezések | 100.000 | 16 500 000 | 495 000 000 | $1093,00 |
| Naplózás | 100.000 | 1 500 000 | 45 000 000 | $246,66 |
| Bejelentkezések | 100.000 | 15 000 000 | 450 000 000 | $847,28 |










A Azure Monitor naplók kezelésével kapcsolatos költségek áttekintését lásd: a [költségek kezelése az adatmennyiség szabályozásával és a megőrzéssel Azure monitor naplókban](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a szakasz az Azure AD-naplók az Azure Monitorban való kezelésével kapcsolatos gyakori kérdéseket válaszolja meg, és ismerteti az ismert problémákat.

**K: Melyik naplókat kezeli a rendszer?**

**V**: A bejelentkezési tevékenységnaplókat és az auditnaplókat egyaránt át lehet irányítani a szolgáltatás használatával, azonban ez a B2C-vel kapcsolatos auditeseményekre jelenleg még nem érvényes. Ha szeretné megtudni, hogy jelenleg milyen naplótípusok és mely szolgáltatásalapú naplók támogatottak, olvassa el [az auditnaplók sémáját](reference-azure-monitor-audit-log-schema.md) és [a bejelentkezési naplók sémáját](reference-azure-monitor-sign-ins-log-schema.md) ismertető cikkeket. 

---

**K: Milyen hamar megjelenik a művelet a kapcsolódó naplókban az Event hub-ban?**

**V**: A naplóknak körülbelül két-öt percen belül kell megjelenniük az eseményközpontban a műveletek végrehajtása után. Az Event Hubsról a [Mi az Azure Event Hubs?](../../event-hubs/event-hubs-about.md) című cikkben talál további információt.

---

**K: Milyen hamar megjelenik a megfelelő naplók a Storage-fiókomban?**

**V**: Az Azure Storage-fiókok esetében a késés 5–15 perc az egyes műveletek végrehajtása után.

---

**K: mi történik, ha egy rendszergazda megváltoztatja a diagnosztikai beállítások megőrzési időtartamát?**

**A**: az új adatmegőrzési szabály a változás után gyűjtött naplókra lesz alkalmazva. A szabályzat módosítása előtt gyűjtött naplók nem lesznek hatással.

---

**K: Mennyibe kerül az adataim tárolása?**

**V**: A tárolás díja a naplók méretétől és a választott megőrzési időtől függ. A bérlőkhöz tartozó, a létrehozott naplók mennyiségétől függő hozzávetőleges becsült költségekért lásd a [Tevékenységnaplók tárterületméretéről](#storage-size-for-activity-logs) szóló szakaszt.

---

**K: Mennyibe kerül az adataim eseményközpontba való streamelése?**

**V**: A streamelés költsége a percenként kapott üzenetmennyiségtől függ. Ez a cikk ismerteti a költségek kiszámításának módját, és felsorolja az üzenetek száma alapján kiszámított költségbecsléseket. 

---

**K: Hogyan integrálhatom az Azure AD-tevékenységnaplókat az SIEM-rendszeremmel?**

**V**: Ezt kétféleképpen teheti meg:

- Az Azure Monitor és az Event Hubs együttes használatával streamelje a naplókat az SIEM-rendszerbe. Először [streamelje a naplókat egy eseményközpontba](tutorial-azure-monitor-stream-logs-to-event-hub.md), majd [állítsa be az SIEM-eszközt](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub) a konfigurált eseményközponttal. 

- A [Reporting Graph API](concept-reporting-api.md) használatával elérheti az adatokat, amelyeket aztán saját szkriptjeivel leküldhet az SIEM-rendszerbe.

---

**K: Mely SIEM-eszközök támogatottak jelenleg?** 

**A**: **a** jelenleg Azure monitor a [splunk](./howto-integrate-activity-logs-with-splunk.md), az IBM QRadar, a [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory), a [ArcSight](./howto-integrate-activity-logs-with-arcsight.md), a LogRhythm és a Logz.IO támogatja. Az összekötők működéséről [az Azure monitorozási adatok egy eseményközpontba külső eszközökben való használat céljából való streamelését](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md) ismertető cikkben talál további információt.

---

**K: Hogyan integrálhatom az Azure AD-tevékenységnaplókat a Splunk-példányommal?**

**V**: Először [irányítsa át az Azure AD-tevékenységnaplókat egy eseményközpontba](./tutorial-azure-monitor-stream-logs-to-event-hub.md), majd a vonatkozó lépéseket követve [integrálja a tevékenységnaplókat a Splunkkal](./howto-integrate-activity-logs-with-splunk.md).

---

**K: Hogyan integrálhatom az Azure AD-tevékenységnaplókat és a Sumo Logicot?** 

**V**: Először [irányítsa át az Azure AD-tevékenységnaplókat egy eseményközpontba](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory), majd a vonatkozó lépéseket követve [telepítse az Azure AD-alkalmazást, és tekintse át az irányítópultokat a SumoLogicban](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

---

**K: Elérhetem az adatokat egy eseményközpontban külső SIEM-eszköz használata nélkül is?** 

**A**: igen. Az [Event Hubs API](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) használatával is elérheti a naplókat az egyéni alkalmazásokban. 

---


## <a name="next-steps"></a>Következő lépések

* [Tevékenységnaplók archiválása egy Storage-fiókba](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Tevékenységnaplók irányítása egy eseményközpontba](./tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Tevékenységek naplóinak integrálása Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)