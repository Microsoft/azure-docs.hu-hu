---
title: include file description: include file services: event-hubs author: spelltrac ms.service: event-hubs ms.topic: include ms.date: 03/31/2021 ms.author: spellemet ms.custom: "include file","fasttrack-edit","iot","event-hubs"

---

Az alábbi táblázatok a következő táblázatban megadott kvótákat és korlátokat [Azure Event Hubs.](https://azure.microsoft.com/services/event-hubs/) További információ a Event Hubs díjszabásról: Event Hubs [díjszabása.](https://azure.microsoft.com/pricing/details/event-hubs/)

### <a name="common-limits-for-all-tiers"></a>Az összes szint közös korlátai
Az alábbi korlátok minden szinten közösek. 

| Korlát |  Jegyzetek | Érték |
| --- |  --- | --- |
| Előfizetésenként Event Hubs névterek száma |- |100 |
| Eseményközpontok száma névtérenként | Az új eseményközpont létrehozására vonatkozó későbbi kérelmeket a rendszer elutasítja. |10 |
| Eseményközpont nevének mérete |- | 256 karakter |
| Fogyasztói csoport nevének mérete | A Kafka protokollhoz nincs szükség felhasználói csoport létrehozására. | <p>Kafka: 256 karakter</p><p>AMQP: 50 karakter |
| Nem epoch típusú fogadók száma fogyasztói csoportonként |- |5 |
| Engedélyezési szabályok száma névtérenként | Az engedélyezési szabályok létrehozásának további kérései el vannak utasítva.|12 |
| A GetRuntimeInformation metódus hívásának száma |  - | Másodpercenként 50 | 
| Virtuális hálózatok száma (VNet) | - | 128 | 
| IP-konfigurációs szabályok száma | - | 128 | 

### <a name="basic-vs-standard-tiers"></a>Alapszintű és standard szintek
Az alábbi táblázat az alapszintű és a standard szintekre vonatkozó korlátozásokat mutatja be. 

| Korlát | Jegyzetek | Alapszintű | Standard |
|---|---|--|---|
| A közzététel Event Hubs mérete| &nbsp; | 256 KB | 1 MB |
| Fogyasztói csoportok száma eseményközpontonként | &nbsp; |1 |20 |
| AMQP-kapcsolatok száma névtérenként | A további kapcsolatokra vonatkozó további kérelmeket a rendszer elutasítja, és a hívó kód kivételt kap. |100 |5000|
| Eseményadatok maximális megőrzési ideje | &nbsp; |1 nap |1–7 nap |
| Maximális átviteli egységek |A korlát túllépése az adatok szabályozását és foglalt [kiszolgálói kivételt hoz létre.](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) Ha nagyobb mennyiségű átviteli egységet igényel egy Standard szinthez, nyújtsa be a [támogatási kérést.](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) [A további átviteli egységek](../articles/event-hubs/event-hubs-auto-inflate.md) 20-as blokkokban érhetők el a vásárláskor. |20 | 20 | 
| Partíciók száma eseményközpontonként | |32 | 32 | 

> [!NOTE]
>
> Az eseményeket közzéteheti egyenként vagy kötegekkel. 
> A közzététel korlátja (a termékváltozattól függően) attól függetlenül érvényes, hogy egyetlen eseményről vagy kötegről van-e szó. A maximális küszöbértéknél nagyobb események közzététele el lesz utasítva.

### <a name="dedicated-tier-vs-standard-tier"></a>Dedikált szint és standard szint
A dedikált Event Hubs ajánlat számlázása rögzített havi áron történik, legalább 4 órányi használattal. A Dedikált csomag a Standard csomag összes funkcióját kínálja, de nagyvállalati szintű kapacitással és korlátokkal a nagy erőforrásigényű számítási feladatokat igénylő ügyfelek számára. 

Tekintse meg ezt a [dokumentumot,](../articles/event-hubs/event-hubs-dedicated-cluster-create-portal.md) amely bemutatja, hogyan hozhat létre dedikált Event Hubs fürtöt a Azure Portal.

| Szolgáltatás | Standard | Dedikált |
| --- |:---|:---|
| Sávszélesség | 20 TUS (legfeljebb 40 TUS) | 20 CUS |
| Névterek |  Előfizetésenként 100 | 50 CU-nként (előfizetésenként 100) |
| Event Hubs |  Névtérenként 10 | Névtérenként 1000 |
| Bejövő események | Milliós fizetéses események | Tartalmazza |
| Üzenetméret | 1 millió bájt | 1 millió bájt |
| Partíciók | Eseményközpontonként 32 | Eseményközpontonként 1024<br/>2000 CU-nként |
| Fogyasztói csoportok | Eseményközpontonként 20 | Nincs korlát CU-nként, eseményközpontonként 1000 |
| Közvetített kapcsolatok | 1000 a csomagban, legfeljebb 5000 | 100 ezer a csomagban és maximum |
| Üzenetmegőrzés | 7 nap, rekordonként 84 GB | 90 nap, cu-nként 10 TB |
| Rögzítés | Óránkénti fizetés | Tartalmazza |


### <a name="schema-registry-limitations"></a>Séma-beállításjegyzék korlátozásai

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>A standard és dedikált szintekre vonatkozó azonos korlátok 
| Jellemző | Korlát | 
|---|---|
| Sémacsoport nevének maximális hossza | 50 |  
| Sémanév maximális hossza | 100 |    
| Méret bájtban sémánként | 1 MB |   
| Tulajdonságok száma sémacsoportonként | 1024 |
| Méret bájtban, csoporttulajdonság-kulcsonként | 256 | 
| Méret bájtban, csoporttulajdonságonként | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>A standard és a dedikált szintekre vonatkozó korlátozások 

| Korlát | Standard | Dedikált | 
|---|---|--|
| A sémajegyzék (névtér) mérete megabájtban | 25 |  1024 |
| Sémajegyzékben vagy névtérben a sémacsoportok száma | 1 – az alapértelmezett csoport kizárása | 1000 |
| Sémaverziók száma az összes sémacsoportban | 25 | 10000 |