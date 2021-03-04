---
title: include file Description: include file Services: Event-hubok Author: spelluru MS. Service: Event-hubok MS. topic: include MS. Date: 02/01/2021 MS. Author: spelluru MS. Custom: "include file", "FastTrack-Edit", "IOT", "Event-hubok"

---

Az alábbi táblázatok az [Azure Event Hubsra](https://azure.microsoft.com/services/event-hubs/)vonatkozó kvótákat és korlátokat biztosítanak. További információ a Event Hubs díjszabásáról: [Event Hubs díjszabása](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Általános korlátok az összes szinten
Az alábbi korlátok az összes szinten gyakoriak. 

| Korlát |  Jegyzetek | Érték |
| --- |  --- | --- |
| Event Hubs névterek száma/előfizetés |- |100 |
| Az Event hubok száma névtérben | Az új Event hub létrehozására vonatkozó további kérelmeket a rendszer elutasítja. |10 |
| Event hub-név mérete |- | 256 karakter |
| Fogyasztói csoport nevének mérete |- | 256 karakter |
| Nem EPOCH-fogadók száma fogyasztói csoportonként |- |5 |
| Engedélyezési szabályok száma névtérben | Az engedélyezési szabályok létrehozására vonatkozó további kérelmeket a rendszer elutasítja.|12 |
| A GetRuntimeInformation metódushoz tartozó hívások száma |  - | 50 másodpercenként | 
| Virtuális hálózatok (VNet) száma | - | 128 | 
| IP-konfigurációs szabályok száma | - | 128 | 


### <a name="basic-vs-standard-tiers"></a>Alapszintű és standard csomag
Az alábbi táblázat az alapszintű és a standard szintekhez eltérő korlátozásokat mutat be. 

| Korlát | Jegyzetek | Alapszintű | Standard |
|---|---|--|---|
| Event Hubs kiadvány maximális mérete| &nbsp; | 256 KB | 1 MB |
| Fogyasztói csoportok száma az Event hub-ban | &nbsp; |1 |20 |
| AMQP-kapcsolatok száma névtérben | A további kapcsolatokra vonatkozó további kérelmeket a rendszer elutasítja, és a hívási kód kivételt kap. |100 |5000|
| Esemény-adatok maximális megőrzési ideje | &nbsp; |1 nap |1-7 nap |
| Maximális átviteli egységek |Ha túllépi ezt a korlátot, a rendszer leszabályozza az adatait, és létrehoz egy [kiszolgáló által foglalt kivételt](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Ha nagyobb számú átviteli egységet szeretne igényelni egy standard szintű csomaghoz, a [támogatási kérést](../articles/azure-portal/supportability/how-to-create-azure-support-request.md). A [további átviteli egységek](../articles/event-hubs/event-hubs-auto-inflate.md) 20 blokkban érhetők el egy véglegesített vásárlás alapján. |20 | 20 | 
| Partíciók száma az Event hub-ban | |32 | 32 | 

> [!NOTE]
>
> Az eseményeket egyenként vagy kötegelten teheti közzé. 
> A közzétételi korlát (SKU alapján) attól függetlenül érvényes, hogy egyetlen eseményről vagy kötegről van szó. A maximális küszöbértéknél nagyobb közzétételi eseményeket a rendszer elutasítja.

### <a name="dedicated-tier-vs-standard-tier"></a>Dedikált szint és standard szint
Az dedikált Event Hubs ajánlat számlázása rögzített havi díjszabással történik, amely legalább 4 órányi használatot biztosít. A dedikált szint a standard csomag összes funkcióját felkínálja, de nagyvállalati kapacitást és korlátokat biztosít az ügyfelek számára igényes számítási feladatokkal. 

Tekintse át ezt a [dokumentumot](../articles/event-hubs/event-hubs-dedicated-cluster-create-portal.md) arról, hogyan hozhat létre dedikált Event Hubs-fürtöt a Azure Portal használatával.

| Szolgáltatás | Standard | Dedikált |
| --- |:---|:---|
| Sávszélesség | 20 TUs (legfeljebb 40 TUs) | 20 ke |
| Névterek |  1 | 50/CU |
| Event Hubs |  10/névtér | 1000/névtér |
| Bejövő események | Díj/millió esemény | Tartalmazza |
| Üzenet mérete | 1 000 000 bájt | 1 000 000 bájt |
| Partíciók | 32/Event hub | 1024/Event hub<br/>2000/CU |
| Fogyasztói csoportok | 20/Event hub | Az Event hub-ban nincs korlát/CU, 1000 |
| Felügyelt kapcsolatok | 1 000 tartalmazott, 5 000 Max | 100 kb és max. |
| Üzenetek megőrzése | 7 nap, 84 GB/TU | 90 nap, 10 TB tartalmaz/CU |
| Rögzítés | Óránkénti fizetés | Tartalmazza |


### <a name="schema-registry-limitations"></a>A séma beállításjegyzékének korlátai

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>A standard és a dedikált szint esetében azonos korlátok 
| Jellemző | Korlát | 
|---|---|
| Séma-csoport nevének maximális hossza | 50 |  
| Séma nevének maximális hossza | 100 |    
| Méret bájt/séma szerint | 1 MB |   
| Tulajdonságok száma Schema csoportonként | 1024 |
| Méret bájtban csoportonként a Group tulajdonság kulcsa | 256 | 
| Méret (bájt/csoport) tulajdonság értéke | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>A standard és a dedikált szint esetében eltérő korlátok 

| Korlát | Standard | Dedikált | 
|---|---|--|
| A séma beállításjegyzékének mérete (névtér) Mega bájtban | 25 |  1024 |
| Séma-beállításjegyzékben vagy névtérben lévő séma-csoportok száma | 1 – az alapértelmezett csoport kizárása | 1000 |
| Séma-verziók száma az összes sémakezelő csoportban | 25 | 10000 |