---
title: Azure Service Bus metrikák a Azure Monitorban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Azure Monitor az Service Bus entitások (várólisták, témakörök és előfizetések) figyelésére.
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: 1e57def2ee7409ae62cdbe065c36e8a53a140e18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570576"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Monitor Azure Service Bus metrikák

Service Bus mérőszámok az Azure-előfizetésében lévő erőforrások állapotát adják meg. A metrikai adatok gazdag készletével a Service Bus erőforrásainak általános állapotát, és nem csak a névtér szintjén, hanem az entitás szintjén is mérhető. Ezek a statisztikák fontosak lehetnek, mivel segítenek a Service Bus állapotának figyelésében. A metrikák az Azure-támogatáshoz való kapcsolódás nélkül is segíthetnek a hibák elhárításában.

A Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [Microsoft Azure figyelése](../azure-monitor/overview.md) és a [Azure monitor metrikáinak beolvasása](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a githubon .net-minta használatával.

> [!IMPORTANT]
> Ha egy entitáshoz 2 órán keresztül nem történt interakció, a metrikák a "0" értéket fogják megmutatni, amíg az entitás már nem tétlen.

## <a name="access-metrics"></a>Hozzáférési metrikák

Azure Monitor több módszert biztosít a metrikák eléréséhez. A mérőszámokat a [Azure Portalon](https://portal.azure.com)keresztül érheti el, vagy használhatja a Azure monitor API-kat (REST és .net) és az elemzési megoldásokat, például a Azure monitor naplókat és Event Hubseket. További információ: [Azure monitor mérőszámai](../azure-monitor/essentials/data-platform-metrics.md).

A metrikák alapértelmezés szerint engedélyezve vannak, és a legutóbbi 30 nap adatait is elérheti. Ha hosszabb ideig kell megőriznie az adatokat, archiválhatja a metrikák adatait egy Azure Storage-fiókba. Ez az érték a Azure Monitor [diagnosztikai beállításaiban](../azure-monitor/essentials/diagnostic-settings.md) van konfigurálva.

## <a name="access-metrics-in-the-portal"></a>Hozzáférési metrikák a portálon

A metrikák a [Azure Portalban](https://portal.azure.com)is megfigyelhetők. Az alábbi példa bemutatja, hogyan tekintheti meg a sikeres kérelmeket és a bejövő kérelmeket a fiók szintjén:

![Képernyőkép a Azure Portal figyelő-metrikák (előzetes verzió) lapján.][1]

A metrikákat közvetlenül a névtér használatával is elérheti. Ehhez válassza ki a névteret, majd válassza a **metrikák** lehetőséget. Az entitás hatókörére szűrt metrikák megjelenítéséhez válassza ki az entitást, majd válassza a **metrikák** lehetőséget.

![A figyelő-metrikák (előzetes verzió) oldalának képernyőképe az entitás hatókörére szűrve.][2]

A dimenziókat támogató metrikák esetében a kívánt dimenzió értékkel kell szűrnie.

## <a name="billing"></a>Számlázás

A Azure Monitor metrikáit és riasztásait riasztási alapon számítjuk fel. Ezeket a díjakat a portálon elérhetővé kell tenniük a riasztás beállítása és mentése előtt. 

A metrikák adatait tartalmazó további megoldásokat közvetlenül ezek a megoldások számlázzák. Ha például az Azure Storage-ba számít, akkor a metrikák adatait egy Azure Storage-fiókba archiválja. a Log Analytics számlázása akkor is történik, ha a metrikák adatait a speciális elemzéshez Log Analyticsre továbbítják.

A következő mérőszámok áttekintést nyújtanak a szolgáltatás állapotáról. 

> [!NOTE]
> Számos mérőszámot elavultunk, mert más néven vannak áthelyezve. Előfordulhat, hogy frissítenie kell a hivatkozásokat. Az "elavult" kulcsszóval jelölt mérőszámok nem lesznek támogatottak.

Minden metrikai érték Azure Monitor percenként lesz elküldve. Az idő részletessége határozza meg azt az időintervallumot, ameddig a metrikák értékei bemutatva lesznek. Az összes Service Bus-metrika támogatott időintervalluma 1 perc.

## <a name="request-metrics"></a>Kérelmek metrikái

Megszámolja az adatok és a felügyeleti műveleti kérelmek számát.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| Bejövő kérelmek| A Service Bus szolgáltatásnak megadott időszakon keresztül küldött kérések száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: entitás neve|
|Sikeres kérelmek|A Service Bus szolgáltatásnak adott időszakon keresztül végrehajtott sikeres kérelmek száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: entitás neve|
|Kiszolgálói hibák|A nem feldolgozott kérelmek száma a Service Bus szolgáltatásban egy adott időszakra vonatkozó hiba miatt.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: entitás neve|
|Felhasználói hibák (lásd a következő szakaszt)|A megadott időszakra vonatkozó felhasználói hibák miatt nem feldolgozott kérelmek száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: entitás neve|
|Szabályozott kérelmek|A lekért kérelmek száma, mert túllépte a használatot.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: entitás neve|

### <a name="user-errors"></a>Felhasználói hibák

A következő két típusú hiba van besorolva felhasználói hibaként:

1. Ügyféloldali hibák (HTTP-ben 400-hibák esetén).
2. Az üzenetek feldolgozásakor előforduló hibák, például a [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Üzenet metrikái

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Bejövő üzenetek|A megadott időszakban Service Bus küldött események vagy üzenetek száma. Ez a metrika nem tartalmaz automatikusan továbbított üzeneteket.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: entitás neve|
|Kimenő üzenetek|A Service Bus a megadott időszakban fogadott események vagy üzenetek száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: entitás neve|
| Üzenetek| Üzenetsor vagy témakör üzeneteinek száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Dimenzió: entitás neve |
| Aktív üzenetek| Üzenetsor vagy témakör aktív üzeneteinek száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Dimenzió: entitás neve |
| Kézbesítetlen üzenetek| Egy várólistában vagy témakörben lévő kézbesítetlen üzenetek száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/>Dimenzió: entitás neve |
| Ütemezett üzenetek| Az üzenetsor/témakör ütemezett üzeneteinek száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag  <br/> Dimenzió: entitás neve |
| Befejezett üzenetek| Az üzenetsor/témakörben befejezett üzenetek száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Dimenzió: entitás neve |
| Elhagyott üzenetek| Várólistán vagy témakörben elhagyott üzenetek száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Dimenzió: entitás neve |
| Méret | Egy entitás (Üzenetsor vagy témakör) mérete bájtban kifejezve. <br/><br/>Egység: darabszám <br/>Összesítés típusa: átlag <br/>Dimenzió: entitás neve | 

> [!NOTE]
> Az üzenetek, az aktív, a kézbesítetlen, az ütemezett, a befejezett és a megszakított üzenetek értékei az időponthoz tartozó értékek. Előfordulhat, hogy az adott időpontot követően azonnal felhasznált bejövő üzenetek nem jelennek meg ezekben a metrikákban. 

## <a name="connection-metrics"></a>Kapcsolatok metrikái

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Aktív kapcsolatok|Az aktív kapcsolatok száma a névtérben és a névtérben lévő entitásokban. A metrika értéke egy adott időponthoz tartozó érték. Azok a kapcsolatok, amelyek közvetlenül az adott időpontot követően aktívak voltak, nem tükröződnek a metrikában.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: entitás neve|
|Megnyitott kapcsolatok |A nyitott kapcsolatok száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: entitás neve|
|Lezárt kapcsolatok |A lezárt kapcsolatok száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: entitás neve|

## <a name="resource-usage-metrics"></a>Erőforrás-használati metrikák

> [!NOTE] 
> A következő metrikák csak a **prémium** szinttel érhetők el. 
> 
> A prémium szintű névtérhez tartozó kimaradások figyeléséhez fontos mérőszámok a következők: a **CPU-használat a névtér** és a **memória mérete régiónként**. [Riasztásokat állíthat be](../azure-monitor/alerts/alerts-metric.md) a metrikák számára a Azure monitor használatával.
> 
> A figyelt másik mérőszám: **szabályozott kérelmek**. Nem lehet probléma, ha a névtér a memóriájában, a PROCESSZORon és a felügyelt kapcsolaton belül marad. További információ: [szabályozás Azure Service Bus Premium](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier) szinten

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|CPU-használat/névtér|A névtér százalékos CPU-használata.<br/><br/> Egység: százalék <br/> Összesítés típusa: maximum <br/> Dimenzió: entitás neve|
|Memória méretének használata névtérben|A névtér százalékos memória-használata.<br/><br/> Egység: százalék <br/> Összesítés típusa: maximum <br/> Dimenzió: entitás neve|

## <a name="metrics-dimensions"></a>Metrikák méretei

A Azure Service Bus a Azure Monitor metrikáinak következő dimenzióit támogatja. Nem kötelező dimenziókat hozzáadni a metrikához. Ha nem ad hozzá dimenziókat, a metrikák a névtér szintjén vannak megadva. 

|Dimenzió neve|Leírás|
| ------------------- | ----------------- |
|Entitás neve| Service Bus támogatja az üzenetküldési entitásokat a névtérben.|

## <a name="set-up-alerts-on-metrics"></a>Riasztások beállítása mérőszámokon

1. A **Service Bus névtér** **mérőszámok** lapján válassza a **riasztások beállítása** lehetőséget. 

    ![Metrikák lap – riasztások beállítása menü](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Jelölje ki a **cél kiválasztása** lehetőséget, majd hajtsa végre a következő műveleteket az **erőforrás kiválasztása** oldalon: 
    1. Válassza ki **Service Bus névtereket** a **szűrés erőforrástípus** mezőben. 
    2. Válassza ki az előfizetését a **szűrés előfizetés alapján** mezőben.
    3. Válassza ki a **Service Bus-névteret** a listából. 
    4. Válassza a **Kész** lehetőséget. 
    
        ![Névtér kiválasztása](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Válassza a **feltétel hozzáadása** lehetőséget, és hajtsa végre a következő műveleteket a **jel logikai beállítása** lapon:
    1. Válasszon **mérőszámokat** a **jel típusához**. 
    2. Válasszon ki egy jelet. Például: **szolgáltatási hibák**. 

        ![Kiszolgálói hibák kiválasztása](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Válasszon **nagyobbat** a **feltételnél**.
    2. Válassza az **összeg** lehetőséget az **idő összesítéséhez**. 
    3. Adja meg az **5** értéket a **küszöbértékhez**. 
    4. Válassza a **Kész** lehetőséget.    

        ![Feltétel meghatározása](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. A **szabály létrehozása** lapon bontsa ki a **riasztás részleteinek meghatározása** elemet, majd hajtsa végre a következő műveleteket:
    1. Adja meg a riasztás **nevét** . 
    2. Adja meg a riasztás **leírását** .
    3. Válassza ki a riasztás **súlyosságát** . 

        ![Képernyőkép a szabály létrehozása lapról. A riasztás részleteinek meghatározása kibontva, a riasztási szabály nevének, leírásának és súlyosságának mezői pedig ki vannak emelve.](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. A **szabály létrehozása** lapon bontsa ki **a műveleti csoport definiálása** elemet, válassza az **új műveleti csoport** lehetőséget, majd hajtsa végre a következő műveleteket a **műveleti csoport hozzáadása lapon**. 
    1. Adja meg a műveleti csoport nevét.
    2. Adja meg a műveleti csoport rövid nevét. 
    3. Válassza ki előfizetését. 
    4. Válasszon ki egy erőforráscsoportot. 
    5. Ehhez a bemutatóhoz írja be a következőt: **E-mail küldése** a **művelet neveként**.
    6. Válassza az **e-mail/SMS/leküldés/hang** lehetőséget a **Művelettípus mezőben**. 
    7. Válassza a **Részletek szerkesztése** lehetőséget. 
    8. Az **e-mail/SMS/leküldés/hang** oldalon hajtsa végre a következő műveleteket:
        1. Válassza az **e-mail** lehetőséget. 
        2. Adja meg az **e-mail-címet**. 
        3. Válassza az **OK** lehetőséget.

            ![A műveleti csoport hozzáadása lap képernyőképe. Az "e-mail küldése" nevű művelet a csoportba kerülő e-mail-/SMS/push/Voice típusú művelettel.](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. A **műveleti csoport hozzáadása** lapon kattintson **az OK gombra**. 
1. A **szabály létrehozása** lapon válassza a **riasztási szabály létrehozása** lehetőséget. 

    ![Riasztási szabály létrehozása gomb](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Következő lépések

Lásd: [Azure monitor áttekintése](../azure-monitor/overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png