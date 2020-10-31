---
title: Metrikák megtekintése Azure Monitor
titleSuffix: Azure Digital Twins
description: Tekintse meg, hogyan tekintheti meg az Azure Digital Twins mérőszámait Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 7fde67ab9b9160bb89493748d09e83bd9cfcff34
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091701"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Azure Digital Twins hibaelhárítása: mérőszámok

A cikkben ismertetett mérőszámok az Azure-előfizetésében található Azure Digital Twins-erőforrások állapotáról nyújtanak információt. Az Azure digitális Twins mérőszámai segítenek felmérni az Azure Digital Twins szolgáltatás általános állapotát és a hozzájuk kapcsolódó erőforrásokat. Ezek a felhasználók felé irányuló statisztikák segítenek megtekinteni, hogy mi történik az Azure-beli digitális Ikrekben, és segítsen a problémák kiváltó okának elemzésében anélkül, hogy kapcsolatba kellene lépnie az Azure ügyfélszolgálatával.

A metrikák alapértelmezés szerint engedélyezve vannak. Az Azure digitális Twins mérőszámait a [Azure Portal](https://portal.azure.com)tekintheti meg.

## <a name="how-to-view-azure-digital-twins-metrics"></a>Azure digitális Twins-mérőszámok megtekintése

1. Hozzon létre egy Azure Digital Twins-példányt. Az Azure Digital Twins-példányok beállításával kapcsolatos utasításokat itt találja: a [*példányok és a hitelesítés beállítása*](how-to-set-up-instance-portal.md).

2. Keresse meg az Azure Digital Twins-példányát a [Azure Portalban](https://portal.azure.com) (Nyissa meg a lapot, és írja be a nevét a portál keresési sávjába). 

    A példány menüjében válassza a **metrikák** lehetőséget.
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Képernyőfelvétel az Azure Digital Twins metrikáinak oldaláról":::

    Ez az oldal az Azure Digital Twins-példány metrikáit jeleníti meg. A metrikák egyéni nézeteit is létrehozhatja a listából megjeleníteni kívánt elemek kiválasztásával.
    
3. Kiválaszthatja, hogy a metrikák adatait egy Event Hubs-végpontba vagy egy Azure Storage-fiókba küldje el a **diagnosztika beállításainak** kiválasztásával a menüből, majd **adja hozzá a diagnosztikai** beállításokat.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Képernyőfelvétel az Azure Digital Twins metrikáinak oldaláról":::

    A folyamattal kapcsolatos további információkért lásd [*: Hibaelhárítás: a diagnosztika beállítása*](troubleshoot-diagnostics.md).

4. A metrikák adataira vonatkozó riasztásokat úgy is beállíthatja, hogy a menüben kiválasztja a **riasztások** lehetőséget, majd az **+ új riasztási szabályt** .
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Képernyőfelvétel az Azure Digital Twins metrikáinak oldaláról":::

    Erről a folyamatról a [*Hibaelhárítás: riasztások beállítása*](troubleshoot-alerts.md)című témakörben olvashat bővebben.

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure digitális Twins-metrikák és azok használatának módjai

Az Azure Digital Twins számos mérőszámot biztosít, amelyek áttekintést nyújtanak a példány állapotáról és a hozzájuk kapcsolódó erőforrásokról. Több mérőszámból is egyesítheti az adatokat, hogy a példány állapotának nagyobb képét fesse. 

Az alábbi táblázatok ismertetik az egyes Azure Digital Twins-példányok által követett mérőszámokat, valamint azt, hogy az egyes mérőszámok hogyan kapcsolódnak a példányok általános állapotához.

#### <a name="api-request-metrics"></a>API-kérelmek metrikái

Az API-kérelmekkel rendelkező metrikák:

| Metrika | Metrika megjelenítendő neve | Egység | Összesítés típusa| Leírás | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API-kérelmek | Darabszám | Összesen | A digitális ikrek olvasási, írási, törlési és lekérdezési műveleteire vonatkozó API-kérések száma. |  Hitelesítés <br>Művelet <br>Protokoll <br>Állapotkód, <br>Állapotkód osztály, <br>Állapot szövege |
| ApiRequestsFailureRate | API-kérelmek meghibásodási aránya | Százalék | Átlag | A szolgáltatás által a példányhoz kapott API-kérelmek százalékos aránya (500) a digitális ikrek olvasási, írási, törlési és lekérdezési műveleteinek belső hibája (). | Hitelesítés <br>Művelet <br>Protokoll <br>Állapotkód, <br>Állapotkód osztály, <br>Állapot szövege
| ApiRequestsLatency | API-kérelmek késése | Ezredmásodpercben | Átlag | Az API-kérelmek válaszideje. Ez arra az időre vonatkozik, amikor a kérést az Azure Digital Twins fogadja, amíg a szolgáltatás nem küld sikeres/sikertelen eredményt a digitális ikrek olvasási, írási, törlési és lekérdezési műveleteihez. | Hitelesítés <br>Művelet <br>Protokoll |

#### <a name="billing-metrics"></a>Számlázási mérőszámok

A számlázással elvégezhető mérőszámok:

>[!NOTE]
>Habár ezek a metrikák továbbra is megjelennek a választható listában, addig maradnak a nullánál, amíg a szolgáltatás új díjszabása elérhetővé nem válik. További információ: [*Azure digitális ikrek díjszabása*](https://azure.microsoft.com/pricing/details/digital-twins/).

| Metrika | Metrika megjelenítendő neve | Egység | Összesítés típusa| Leírás | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Számlázási API-műveletek | Darabszám | Összesen | Az Azure Digital Twins szolgáltatásban végrehajtott API-kérelmek számának számlázási mérőszáma. | Fogyasztásmérő azonosítója |
| BillingMessagesProcessed | Feldolgozott számlázási üzenetek | Darabszám | Összesen | Számlázási metrika az Azure digitális Twins-ból külső végpontokra küldött üzenetek számának megadásához.<br><br>Ahhoz, hogy csak egyetlen üzenet legyen a számlázási célokra, a hasznos adatok nem lehetnek nagyobbak 1 KB-nál. Az ennél nagyobb hasznos adatok az 1 KB-os növekményekben további üzenetnek számítanak (ezért az 1. és 2. közötti üzenet 2 üzenetnek számít, 2 és 3 KB között 3 üzenet lesz, és így tovább).<br>Ez a korlátozás a válaszokra is vonatkozik – így a válasz törzsében az 1,5 KB értéket visszaadó hívás (például: 2 művelet lesz). | Fogyasztásmérő azonosítója |
| BillingQueryUnits | Számlázási lekérdezési egységek | Darabszám | Összesen | A lekérdezési egységek száma, a szolgáltatások erőforrás-felhasználásának belsőleg számított mértéke, a lekérdezések végrehajtásához felhasználva. A lekérdezési egységek méréséhez rendelkezésre áll egy segítő API is: [QueryChargeHelper Class](/dotnet/api/azure.digitaltwins.core.querychargehelper?preserve-view=true&view=azure-dotnet-preview) | Fogyasztásmérő azonosítója |

#### <a name="ingress-metrics"></a>Bejövő metrikák

Adatbevitelsel ellátott mérőszámok:

| Metrika | Metrika megjelenítendő neve | Egység | Összesítés típusa| Leírás | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | Bejövő események | Darabszám | Összesen | A bejövő telemetria események száma az Azure digitális Twins-ban. | Eredmény |
| IngressEventsFailureRate | Bejövő események meghibásodási aránya | Százalék | Átlag | Azon bejövő telemetria-események százalékos aránya, amelyek esetében a szolgáltatás belső hibát (500) ad vissza. | Eredmény |
| IngressEventsLatency | Bejövő események késése | Ezredmásodpercben | Átlag | Az az idő, amikor egy esemény megérkezik, amikor készen áll az Azure digitális ikrek általi egressed, és ekkor a szolgáltatás sikeres/sikertelen eredményt küld. | Eredmény |

#### <a name="routing-metrics"></a>Útválasztási metrikák

Az útválasztással elvégezhető mérőszámok:

| Metrika | Metrika megjelenítendő neve | Egység | Összesítés típusa| Leírás | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | Átirányított üzenetek | Darabszám | Összesen | Az Azure-szolgáltatásokhoz (például Event hub, Service Bus vagy Event Grid) továbbított üzenetek száma. | Végpont típusa, <br>Eredmény |
| RoutingFailureRate | Útválasztási hibák aránya | Százalék | Átlag | Az olyan események százalékos aránya, amelyek az Azure digitális Twins-ból egy Endpoint Azure-szolgáltatásba, például az Event hub-ba, a Service Busba vagy a Event Gridra irányítják a hibát. | Végpont típusa, <br>Eredmény |
| RoutingLatency | Útválasztási késés | Ezredmásodpercben | Átlag | Az Azure Digital Twins-ból átirányított esemény között eltelt idő a végponti Azure-szolgáltatásba (például Event hub, Service Bus vagy Event Grid) való közzétételkor. | Végpont típusa, <br>Eredmény |

## <a name="dimensions"></a>Dimenziók

A dimenziók segítenek azonosítani a metrikák további részleteit. Az útválasztási metrikák némelyike végponti információkat biztosít. Az alábbi táblázat felsorolja a méretek lehetséges értékeit.

| Dimenzió | Értékek |
| --- | --- |
| Hitelesítés | OAuth |
| Művelet (API-kérelmek esetén) | Microsoft. DigitalTwins/DigitalTwins/DELETE, <br>Microsoft. DigitalTwins/DigitalTwins/Write, <br>Microsoft. DigitalTwins/DigitalTwins/READ, <br>Microsoft. DigitalTwins/eventroutes/READ, <br>Microsoft. DigitalTwins/eventroutes/Write, <br>Microsoft. DigitalTwins/eventroutes/DELETE, <br>Microsoft. DigitalTwins/modellek/olvasás, <br>Microsoft. DigitalTwins/modellek/írás, <br>Microsoft. DigitalTwins/models/DELETE, <br>Microsoft. DigitalTwins/lekérdezés/művelet |
| Végpont típusa | Event Grid <br>Event hub, <br>Service Bus |
| Protokoll | HTTPS |
| Eredmény | Sikeres <br>Hiba |
| Állapotkód | 200, 404, 500 és így tovább. |
| Állapotkód osztálya | 2xx, 4xx, 5xx stb. |
| Állapot szövege | Belső kiszolgálóhiba, nem található, és így tovább. |

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure Digital Twins rögzített metrikáinak kezelésével kapcsolatban, tekintse meg a [*Hibaelhárítás: diagnosztika beállítása*](troubleshoot-diagnostics.md)című témakört.
