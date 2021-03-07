---
title: Metrikák megtekintése Azure Monitor
titleSuffix: Azure Digital Twins
description: Tekintse meg, hogyan tekintheti meg az Azure Digital Twins mérőszámait Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 63817c8a65678579ce535a3c2e667e4eb0971a63
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434066"
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

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="A diagnosztikai beállítások lapot és a hozzáadni kívánt gombot ábrázoló képernyőkép":::

    A folyamattal kapcsolatos további információkért lásd [*: Hibaelhárítás: a diagnosztika beállítása*](troubleshoot-diagnostics.md).

4. A metrikák adataira vonatkozó riasztásokat úgy is beállíthatja, hogy a menüben kiválasztja a **riasztások** lehetőséget, majd az **+ új riasztási szabályt**.
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="A riasztások oldal és a hozzáadni kívánt gomb képernyőképe":::

    Erről a folyamatról a [*Hibaelhárítás: riasztások beállítása*](troubleshoot-alerts.md)című témakörben olvashat bővebben.

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure digitális Twins-metrikák és azok használatának módjai

Az Azure Digital Twins számos mérőszámot biztosít, amelyek áttekintést nyújtanak a példány állapotáról és a hozzájuk kapcsolódó erőforrásokról. Több mérőszámból is egyesítheti az adatokat, hogy a példány állapotának nagyobb képét fesse. 

Az alábbi táblázatok ismertetik az egyes Azure Digital Twins-példányok által követett mérőszámokat, valamint azt, hogy az egyes mérőszámok hogyan kapcsolódnak a példányok általános állapotához.

#### <a name="metrics-for-tracking-service-limits"></a>A nyomkövetési szolgáltatási korlátok mérőszámai

Ezeket a metrikákat úgy konfigurálhatja, hogy nyomon kövessék a [közzétett szolgáltatási korlátot](reference-service-limits.md#functional-limits) a megoldás valamilyen aspektusa esetében. 

Ennek beállításához használja a Azure Monitor [riasztások](troubleshoot-alerts.md) szolgáltatását. Megadhatja a metrikák küszöbértékeit, hogy riasztást kapjon, ha egy metrika eléri a közzétett korlát egy adott százalékát.

| Metric | Metrika megjelenítendő neve | Unit (Egység) | Összesítés típusa| Description | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| TwinCount | Twin count (előzetes verzió) | Darabszám | Összesen | Az ikrek teljes száma az Azure Digital Twins-példányban. Ezzel a metrikával meghatározhatja, hogy a [szolgáltatás](reference-service-limits.md#functional-limits) legfeljebb hány ikrek számára engedélyezett. |  Nincs |
| ModelCount | Modellek száma (előzetes verzió) | Darabszám | Összesen | Az Azure Digital Twins-példány modelljeinek száma összesen. Ezzel a metrikával meghatározhatja, hogy az adott példányon engedélyezett modellek maximális száma eléri-e a [szolgáltatási korlátot](reference-service-limits.md#functional-limits) . | Nincs |

#### <a name="api-request-metrics"></a>API-kérelmek metrikái

Az API-kérelmekkel rendelkező metrikák:

| Metric | Metrika megjelenítendő neve | Unit (Egység) | Összesítés típusa| Description | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API-kérelmek | Darabszám | Összesen | A digitális ikrek olvasási, írási, törlési és lekérdezési műveleteire vonatkozó API-kérések száma. |  Hitelesítés <br>Művelet <br>Protokoll <br>Állapotkód, <br>Állapotkód osztály, <br>Állapot szövege |
| ApiRequestsFailureRate | API-kérelmek meghibásodási aránya | Százalék | Átlag | A szolgáltatás által a példányhoz kapott API-kérelmek százalékos aránya (500) a digitális ikrek olvasási, írási, törlési és lekérdezési műveleteinek belső hibája (). | Hitelesítés <br>Művelet <br>Protokoll <br>Állapotkód, <br>Állapotkód osztály, <br>Állapot szövege
| ApiRequestsLatency | API-kérelmek késése | Ezredmásodpercben | Átlag | Az API-kérelmek válaszideje. Ez arra az időre vonatkozik, amikor a kérést az Azure Digital Twins fogadja, amíg a szolgáltatás nem küld sikeres/sikertelen eredményt a digitális ikrek olvasási, írási, törlési és lekérdezési műveleteihez. | Hitelesítés <br>Művelet <br>Protokoll |

#### <a name="billing-metrics"></a>Számlázási mérőszámok

A számlázással elvégezhető mérőszámok:

| Metric | Metrika megjelenítendő neve | Unit (Egység) | Összesítés típusa| Description | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Számlázási API-műveletek | Darabszám | Összesen | Az Azure Digital Twins szolgáltatásban végrehajtott API-kérelmek számának számlázási mérőszáma. | Meter ID |
| BillingMessagesProcessed | Feldolgozott számlázási üzenetek | Darabszám | Összesen | Számlázási metrika az Azure digitális Twins-ból külső végpontokra küldött üzenetek számának megadásához.<br><br>Ahhoz, hogy csak egyetlen üzenet legyen a számlázási célokra, a hasznos adatok nem lehetnek nagyobbak 1 KB-nál. Az ennél nagyobb hasznos adatok az 1 KB-os növekményekben további üzenetnek számítanak (ezért az 1. és 2. közötti üzenet 2 üzenetnek számít, 2 és 3 KB között 3 üzenet lesz, és így tovább).<br>Ez a korlátozás a válaszokra is vonatkozik – így a válasz törzsében az 1,5 KB értéket visszaadó hívás (például: 2 művelet lesz). | Meter ID |
| BillingQueryUnits | Számlázási lekérdezési egységek | Darabszám | Összesen | A lekérdezési egységek száma, a szolgáltatások erőforrás-felhasználásának belsőleg számított mértéke, a lekérdezések végrehajtásához felhasználva. A lekérdezési egységek méréséhez rendelkezésre áll egy segítő API is: [QueryChargeHelper Class](/dotnet/api/azure.digitaltwins.core.querychargehelper) | Meter ID |

Az Azure digitális ikrek számlázásával kapcsolatos további információkért lásd: [*Azure digitális ikrek díjszabása*](https://azure.microsoft.com/pricing/details/digital-twins/).

#### <a name="ingress-metrics"></a>Bejövő metrikák

Adatbevitelsel ellátott mérőszámok:

| Metric | Metrika megjelenítendő neve | Unit (Egység) | Összesítés típusa| Description | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | Bejövő események | Darabszám | Összesen | A bejövő telemetria események száma az Azure digitális Twins-ban. | Eredmény |
| IngressEventsFailureRate | Bejövő események meghibásodási aránya | Százalék | Átlag | Azon bejövő telemetria-események százalékos aránya, amelyek esetében a szolgáltatás belső hibát (500) ad vissza. | Eredmény |
| IngressEventsLatency | Bejövő események késése | Ezredmásodpercben | Átlag | Az az idő, amikor egy esemény megérkezik, amikor készen áll az Azure digitális ikrek általi egressed, és ekkor a szolgáltatás sikeres/sikertelen eredményt küld. | Eredmény |

#### <a name="routing-metrics"></a>Útválasztási metrikák

Az útválasztással elvégezhető mérőszámok:

| Metric | Metrika megjelenítendő neve | Unit (Egység) | Összesítés típusa| Description | Dimenziók |
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
