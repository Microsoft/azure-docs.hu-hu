---
title: Azure Storage Analytics metrikák (klasszikus)
description: Ismerje meg, hogyan használhatja Storage Analytics metrikákat az Azure Storage-ban. Ismerje meg a tranzakciók és a kapacitás mérőszámait, a metrikák tárolási módját, a metrikák engedélyezését és egyebeket.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 8f698aadc24d0dc0691743f1d8dd54c5d5fd287e
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220956"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Storage Analytics metrikák (klasszikus)

Az **2023-as augusztus 31-** én Storage Analytics metrikákat, más néven a *klasszikus metrikákat* is kivezetjük. További információért tekintse meg a [hivatalos bejelentést](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Ha klasszikus metrikákat használ, a megadott dátum előtt gondoskodjon az Azure Monitor-metrikákra való áttérésről. Ez a cikk segítséget nyújt a váltásban. 

Az Azure Storage a Storage Analytics megoldást használja az összesített tranzakciós statisztikát és a tárolási szolgáltatásra irányuló kérelmekre vonatkozó kapacitási adatokat tartalmazó mérőszámok tárolására. A tranzakciókat az API műveleti szintjén és a tárolási szolgáltatás szintjén kell jelenteni. A kapacitás a tárolási szolgáltatás szintjén szerepel. A metrikák adatai a következőket használhatják:
- A tárolási szolgáltatás használatának elemzése.
- A tárolási szolgáltatáshoz intézett kérelmekkel kapcsolatos problémák diagnosztizálása.
- Javítsa a szolgáltatást használó alkalmazások teljesítményét.

 Storage Analytics metrikák alapértelmezés szerint engedélyezve vannak az új Storage-fiókok esetében. A metrikák a [Azure Portalban](https://portal.azure.com/), a PowerShell vagy az Azure CLI használatával konfigurálhatók. Részletes útmutatásért lásd: [Azure Storage analitikai metrikák (klasszikus) engedélyezése és kezelése](./storage-monitor-storage-account.md). Storage Analytics programozott módon is engedélyezheti a REST API vagy az ügyféloldali kódtár használatával. A szolgáltatás tulajdonságainak beállítása művelettel engedélyezheti a Storage Analytics az egyes szolgáltatásokhoz.  

> [!NOTE]
> Storage Analytics metrikák érhetők el az Azure Blob Storage-hoz, az Azure üzenetsor-tároláshoz, az Azure Table Storage-hoz és a Azure Fileshoz.
> Storage Analytics metrikák mostantól klasszikus metrikák. Azt javasoljuk, hogy Storage Analytics mérőszámok helyett [Azure monitor használjon tárolási metrikákat](../blobs/monitor-blob-storage.md) .

## <a name="transaction-metrics"></a>Tranzakciómetrikák  
 Az egyes tárolási szolgáltatások és a kért API-művelet óránkénti vagy percenkénti időközönként rögzíti az adatmennyiséget, amely magában foglalja a bejövő és a kimenő, a rendelkezésre állási, a hibák és a kategorizált kérelmek százalékos arányát. A tranzakció részleteinek teljes listáját itt tekintheti meg: [Storage Analytics mérőszámok tábla sémája](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 A tranzakciós adatai a szolgáltatási szinten és az API-műveleti szinten vannak rögzítve. A szolgáltatási szinten az összes igényelt API-műveletet összefoglaló statisztikát óránként egy táblázatos entitásba írja a rendszer, még akkor is, ha nem történtek kérelmek a szolgáltatáshoz. Az API működési szintjén a statisztikák csak akkor íródnak az entitásba, ha a műveletet az adott órán belül kérték.  

 Ha például **GetBlob** műveletet hajt végre a blob szolgáltatásban, Storage Analytics metrikák naplózzák a kérést, és belefoglalják a blob szolgáltatás összesített adataiba és a **GetBlob** műveletbe. Ha az óra során nem kér **GetBlob** műveletet, akkor az entitás nem íródik a művelet *$MetricsTransactionsBlob* .  

 A tranzakciós metrikák a Storage Analytics saját maga által küldött felhasználói kérésekhez és kérésekhez vannak rögzítve. A naplók és a tábla entitások írásához Storage Analytics kérelmeket például rögzíti a rendszer.

## <a name="capacity-metrics"></a>Kapacitásmetrikák  

> [!NOTE]
>  A kapacitási metrikák jelenleg csak a blob szolgáltatáshoz érhetők el.

 A rendszer naponta rögzíti a kapacitási adatkészletet a Storage-fiók blob-szolgáltatásához, és két tábla entitást ír. Az egyik entitás statisztikai adatokat biztosít a felhasználói adatokhoz, a másik pedig a `$logs` Storage Analytics által használt BLOB-tároló statisztikáit tartalmazza. A *$MetricsCapacityBlob* tábla a következő statisztikákat tartalmazza:  

- **Kapacitás**: a Storage-fiók blob szolgáltatása által felhasznált tárterület mérete bájtban megadva.  
- **ContainerCount**: a Storage-fiók blob szolgáltatásában lévő blob-tárolók száma.  
- **ObjectCount**: a Storage-fiók blob szolgáltatásában lévő véglegesített és nem véglegesített blokk-vagy oldal-Blobok száma.  

  További információ a kapacitási metrikákkal kapcsolatban: [Storage Analytics mérőszámok tábla sémája](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>A metrikák tárolása  

 Az egyes tárolási szolgáltatásokhoz tartozó összes metrikai adat a szolgáltatás számára fenntartott három táblában tárolódik. Az egyik tábla a tranzakciós adatokra szolgál, az egyik tábla a tranzakciós adatokhoz, a másik pedig a kapacitásra vonatkozó információkat tartalmazza. A tranzakciós és a perces tranzakciós adatok kérelmekből és válaszokból állnak. A kapacitás adatai tárolási használati adatokból állnak. A Storage-fiók blob szolgáltatásának óra mérőszámai, perc mérőszámai és kapacitása a következő táblázatban leírtaknak megfelelően elnevezett táblákban érhető el.  

|Metrikák szintje|Táblák nevei|Verziók esetében támogatott|  
|-------------------|-----------------|----------------------------|  
|Óránkénti mérőszámok, elsődleges hely|– $MetricsTransactionsBlob<br />– $MetricsTransactionsTable<br />– $MetricsTransactionsQueue|Csak 2013. augusztus 15. előtti verziók. Habár ezek a nevek továbbra is támogatottak, javasoljuk, hogy váltson az alábbi táblázatok használatára.|  
|Óránkénti mérőszámok, elsődleges hely|– $MetricsHourPrimaryTransactionsBlob<br />– $MetricsHourPrimaryTransactionsTable<br />– $MetricsHourPrimaryTransactionsQueue<br />– $MetricsHourPrimaryTransactionsFile|Minden verzió. A file Service-metrikák támogatása csak a 2015-es és újabb verziókban érhető el.|  
|Perc mérőszámok, elsődleges hely|– $MetricsMinutePrimaryTransactionsBlob<br />– $MetricsMinutePrimaryTransactionsTable<br />– $MetricsMinutePrimaryTransactionsQueue<br />– $MetricsMinutePrimaryTransactionsFile|Minden verzió. A file Service-metrikák támogatása csak a 2015-es és újabb verziókban érhető el.|  
|Óránkénti metrika, másodlagos hely|– $MetricsHourSecondaryTransactionsBlob<br />– $MetricsHourSecondaryTransactionsTable<br />– $MetricsHourSecondaryTransactionsQueue|Minden verzió. Az olvasási hozzáférés geo-redundáns replikációját engedélyezni kell.|  
|Perc mérőszámok, másodlagos hely|– $MetricsMinuteSecondaryTransactionsBlob<br />– $MetricsMinuteSecondaryTransactionsTable<br />– $MetricsMinuteSecondaryTransactionsQueue|Minden verzió. Az olvasási hozzáférés geo-redundáns replikációját engedélyezni kell.|  
|Kapacitás (csak blob-szolgáltatás)|$MetricsCapacityBlob|Minden verzió.|  

 Ezek a táblák automatikusan létrejönnek, ha Storage Analytics van engedélyezve a tárolási szolgáltatás végpontja számára. Ezek a Storage-fiók névterén keresztül érhetők el, például: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")` . A metrikák táblázat nem jelenik meg a listázási műveletben, és közvetlenül a tábla nevén keresztül kell elérni.

## <a name="metrics-alerts"></a>Metrikák riasztásai
Érdemes lehet riasztásokat beállítani a [Azure Portalban](https://portal.azure.com) , hogy automatikusan értesüljön a tárolási szolgáltatások működésével kapcsolatos fontos változásokról. Részletes útmutatásért lásd a [metrikák riasztások létrehozása](storage-monitor-storage-account.md#create-metric-alerts)című témakört.

Ha Storage Explorer eszközzel tölti le ezeket a metrikákat, a Microsoft Excel segítségével elemezheti az adatokat. Az elérhető Storage Explorer eszközök listáját az [Azure Storage-ügyféleszközök](./storage-explorers.md)című témakörben tekintheti meg.

> [!IMPORTANT]
> Előfordulhat, hogy a tárolási esemény és a megfelelő óránkénti vagy perces metrikai adatok rögzítése között késés történik. Percenkénti metrika esetén több percnyi adat is írható egyszerre. Ez a probléma a korábbi percekben lebonyolított tranzakciókat eredményezheti az aktuális percek tranzakciójában. Ha ez a probléma történik, előfordulhat, hogy a riasztási szolgáltatás nem rendelkezik az összes rendelkezésre álló metrikai adattal a beállított riasztási intervallumhoz, ami a riasztások váratlan elégetését eredményezheti.
>

## <a name="billing-on-storage-metrics"></a>Számlázás a tárolási mérőszámokon
A táblázatos entitások metrikák létrehozására vonatkozó írási kéréseket az összes Azure Storage-műveletre érvényes standard díjszabás szerint számítjuk fel.  

A metrikai adatoknak az ügyfelek által küldött olvasási és törlési kérelmei a standard díjszabás szerint is számlázva vannak. Ha adatmegőrzési szabályzatot állított be, akkor nem számítunk fel díjat, ha az Azure Storage törli a régi metrikák adatait. Ha törli az elemzési adatok mennyiségét, a fiók a törlési műveletekért kell fizetnie.  

A metrikák táblái által használt kapacitás is számlázható. A következő információk segítségével becsülheti meg a metrikák adatainak tárolására használt kapacitás mennyiségét:  

-   Ha minden órában a szolgáltatás minden API-t használ minden szolgáltatásban, körülbelül 148 KB adat tárolódik óránként a metrikák tranzakciós tábláiban, ha engedélyezte a szolgáltatási szintet és az API-szintű összegzést.  
-   Ha minden órában a szolgáltatás minden API-t használ, a metrikák tranzakciós tábláiban körülbelül 12 KB adat tárolódik, ha csak egy szolgáltatási szintű összegzést engedélyez.  
-   A Blobok kapacitási táblája két sorral bővült, amelyeket a naplókhoz választott ki. Ez a forgatókönyv azt feltételezi, hogy a táblázat mérete minden nap körülbelül 300 bájttal nő.

## <a name="next-steps"></a>Következő lépések
* [Tárfiók figyelése](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Storage Analytics mérőszámok táblázatának sémája](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Naplózott műveletek és állapotüzenetek Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Storage Analytics naplózás](storage-analytics-logging.md)