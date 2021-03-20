---
title: Teljesítménnyel kapcsolatos útmutatás az Azure SignalR Service-hez
description: Az Azure Signaler szolgáltatás teljesítményének és teljesítménytesztének áttekintése. A kapacitás megtervezése során figyelembe venni kívánt fő mérőszámok.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74157664"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Teljesítménnyel kapcsolatos útmutatás az Azure SignalR Service-hez

Az Azure Signaler szolgáltatás használatának egyik legfőbb előnye, hogy a könnyebben méretezhető szignáló alkalmazások. Nagy léptékű forgatókönyv esetén a teljesítmény fontos tényező. 

Ebben az útmutatóban bemutatjuk azokat a tényezőket, amelyek befolyásolják a Signaler-alkalmazások teljesítményét. A tipikus teljesítményt a különböző használati esetekben mutatjuk be. A végén bemutatjuk a környezetet és az eszközöket, amelyeket a teljesítmény-jelentés létrehozásához használhat.

## <a name="term-definitions"></a>Kifejezés-definíciók

*Bejövő*: a bejövő üzenet az Azure Signaler szolgáltatásnak.

*Kimenő*: az Azure signaler szolgáltatás kimenő üzenete.

*Sávszélesség*: az összes üzenet teljes mérete 1 másodperc.

*Alapértelmezett mód*: az alapértelmezett működési mód, amikor létrejön egy Azure Signal Service-példány. Az Azure Signaler szolgáltatás arra vár, hogy az alkalmazáskiszolgáló kapcsolatot hozzon létre, mielőtt a rendszer bármilyen ügyfélkapcsolatot fogad.

*Kiszolgáló nélküli mód*: az az üzemmód, amelyben az Azure signaler szolgáltatás csak ügyfélkapcsolatokat fogad el. Nincs engedélyezve a kiszolgálói kapcsolatok.

## <a name="overview"></a>Áttekintés

Az Azure Signaler szolgáltatás hét standard szintű csomagot definiál a különböző teljesítményű kapacitásokhoz. Ez az útmutató a következő kérdésekre ad választ:

-   Mi a tipikus Azure Signaler szolgáltatás teljesítménye az egyes szinteknél?

-   Az Azure Signaler szolgáltatás megfelel az üzenetek átviteli követelményeinek (például 100 000 üzenet küldése másodpercenként)?

-   Az adott forgatókönyv esetében melyik szint megfelelő számomra? Vagy Hogyan választhatom ki a megfelelő szintet?

-   Milyen típusú app Server-(VM-méret) alkalmas a számomra? Hányat kell üzembe helyezni?

A kérdések megválaszolásához ez az útmutató először a teljesítményt befolyásoló tényezők magas szintű magyarázatát ismerteti. Ezt követően az összes réteg bejövő és kimenő üzeneteit mutatja be a tipikus használati esetekhez: **echo**, **Broadcast**, **Send to Group**, és **Küldés a kapcsolódáshoz** (egyenrangú csevegés).

Ez az útmutató nem fedi le az összes forgatókönyvet (és a különböző használati eseteket, az üzenetek méretét, az üzenetek küldésének mintáit stb.). Ez azonban néhány módszert biztosít a következőkhöz:

- Értékelje ki a beérkező vagy kimenő üzenetek hozzávetőleges követelményét.
- Keresse meg a megfelelő szinteket a teljesítmény tábla ellenőrzésével.

## <a name="performance-insight"></a>Teljesítmény-betekintés

Ez a szakasz a teljesítmény-értékelési módszereket ismerteti, majd felsorolja az összes olyan tényezőt, amely hatással van a teljesítményre. A végén a teljesítményre vonatkozó követelmények kiértékelését segítő módszereket biztosít.

### <a name="methodology"></a>Módszertan

Az *átviteli sebesség* és a *késés* a teljesítmény ellenőrzésének két jellemző aspektusa. Az Azure Signaler szolgáltatás esetében minden SKU-szinten saját átviteli sávszélesség-szabályozási házirend tartozik. A házirend határozza meg *a maximálisan engedélyezett átviteli sebességet (bejövő és kimenő sávszélesség)* , amely a maximálisan elért átviteli sebesség, ha az üzenetek 99%-ában a késés kevesebb, mint 1 másodperc.

A késés azt az időkorlátot jelzi, amikor a rendszer elküldi az üzenetet, hogy fogadja a válaszüzenetet az Azure Signaler szolgáltatástól. Vegyük példaként az **echo** -t. Minden ügyfélkapcsolat egy időbélyeget hoz létre az üzenetben. Az App Server hub az eredeti üzenetet az ügyfélnek küldi vissza. Így a terjesztési késleltetés könnyen kiszámítható minden ügyfél-kapcsolatban. Az időbélyegző a **szórás**, a **csoportba való** küldés és a **Küldés a kapcsolódáshoz** üzenethez van csatolva.

Több ezer egyidejű ügyfélkapcsolat szimulálása érdekében több virtuális gép jön létre az Azure-beli virtuális magánhálózaton. Az összes virtuális gép ugyanahhoz az Azure Signaler Service-példányhoz csatlakozik.

Az Azure Signaler szolgáltatás alapértelmezett üzemmódjában az App Server-alapú virtuális gépek ugyanazon a virtuális magánhálózaton lesznek telepítve, mint az ügyfél virtuális gépei. Az összes ügyfél virtuális gép és az App Server rendszerű virtuális gép ugyanabban a régióban van üzembe helyezve, hogy elkerülje a régiók közötti késést.

### <a name="performance-factors"></a>Teljesítménnyel kapcsolatos tényezők

Az Azure Signaler szolgáltatás kapacitását elméletileg számítási erőforrások korlátozzák: CPU, memória és hálózat. Például az Azure Signaler szolgáltatáshoz való nagyobb kapcsolatok miatt a szolgáltatás több memóriát is használhat. Nagyobb üzenetkezelési forgalom esetén (például minden üzenet 2 048 bájtnál nagyobb), az Azure Signaler szolgáltatásnak több CPU-ciklust kell költenie a forgalom feldolgozásához. Eközben az Azure hálózati sávszélesség korlátot is biztosít a maximális forgalomhoz.

A szállítási típus egy másik tényező, amely hatással van a teljesítményre. A három típus a [WebSocket](https://en.wikipedia.org/wiki/WebSocket), a [kiszolgáló által eljuttatott esemény](https://en.wikipedia.org/wiki/Server-sent_events)és a [hosszú lekérdezés](https://en.wikipedia.org/wiki/Push_technology). 

A WebSocket egy kétirányú és teljes kétirányú kommunikációs protokoll egyetlen TCP-kapcsolaton keresztül. A kiszolgáló által küldött esemény egy egyirányú protokoll, amely üzeneteket küld a kiszolgálóról az ügyfélnek. Long-Polling megköveteli, hogy az ügyfelek egy HTTP-kérelem alapján rendszeresen lekérdezzenek adatokat a kiszolgálóról. Ugyanarra az API-ra ugyanazok a feltételek érvényesek, a WebSocket a legjobb teljesítményt nyújtja, a kiszolgáló által eljuttatott esemény lassabb, és Long-Polling a leglassabb. Az Azure Signaler szolgáltatás alapértelmezés szerint a WebSocket szolgáltatást javasolja.

Az üzenet-útválasztási díj is korlátozza a teljesítményt. Az Azure Signaler szolgáltatás üzenet-útválasztóként játszik szerepet, amely az üzeneteket az ügyfelek vagy kiszolgálók készletéről más ügyfelek vagy kiszolgálók számára irányítja. Egy másik forgatókönyvnek vagy API-nak eltérő Útválasztási házirendre van szüksége. 

Az **echo** esetében az ügyfél önmagára küld egy üzenetet, az útválasztási cél pedig maga is. Ez a minta a legalacsonyabb útválasztási költségeket veszi igénybe. Az Azure Signaler szolgáltatásnak azonban a **szórás**, a **csoportba** való küldés és a csatlakozás céljából való **küldéssel** kell megkeresnie a cél kapcsolatokat a belső elosztott adatstruktúrán keresztül. Ez az extra feldolgozás több PROCESSZORt, memóriát és hálózati sávszélességet használ. Ennek eredményeképpen a teljesítmény lassabb.

Az alapértelmezett módban az alkalmazáskiszolgáló bizonyos helyzetekben szűk keresztmetszetet jelenthet. Az Azure Signaler SDK-nak meg kell hívnia a hubot, miközben élő kapcsolatot tart fenn minden ügyféllel a Szívveréses jeleken keresztül.

Kiszolgáló nélküli módban az ügyfél HTTP Post üzenetet küld, amely nem annyira hatékony, mint a WebSocket.

Egy másik tényező a protokoll: JSON és [MessagePack](https://msgpack.org/index.html). A MessagePack kisebb méretű, és gyorsabb, mint a JSON. Előfordulhat, hogy a MessagePack nem javítja a teljesítményt. Az Azure Signaler szolgáltatás teljesítménye nem érzékeny a protokollokra, mert nem dekódolja az üzenet tartalmát az ügyfelek és a kiszolgálók között továbbított üzenetek továbbítása közben, vagy fordítva.

Az összefoglalás területen az alábbi tényezők befolyásolják a bejövő és a kimenő kapacitást:

-   SKU-rétegek (CPU/memória)

-   Kapcsolatok száma

-   Üzenet mérete

-   Üzenet küldési sebessége

-   Átviteli típus (WebSocket, kiszolgáló által eljuttatott esemény vagy hosszú lekérdezés)

-   Használati eset (útválasztási díj)

-   Az App Server és a szolgáltatás kapcsolatai (kiszolgáló módban)


### <a name="finding-a-proper-sku"></a>Megfelelő SKU megkeresése

Hogyan értékelheti ki a bejövő/kimenő kapacitást, vagy megkeresheti, hogy melyik csomag alkalmas egy adott használati esetre?

Tegyük fel, hogy az alkalmazáskiszolgáló elég hatékony, és nem a teljesítmény szűk keresztmetszete. Ezután győződjön meg arról, hogy minden szinten a bejövő és a kimenő sávszélesség engedélyezett.

#### <a name="quick-evaluation"></a>Gyors Értékelés

Először is egyszerűsítse a kiértékelést, ha feltételez néhány alapértelmezett beállítást: 

- Az átvitel típusa WebSocket.
- Az üzenet mérete 2 048 bájt.
- 1 másodpercenként küldünk üzenetet.
- Az Azure Signaler szolgáltatás alapértelmezett üzemmódban van.

Minden szinten a saját maximális bejövő sávszélessége és a kimenő sávszélesség van. Ha a bejövő vagy kimenő kapcsolatok túllépik a korlátot, a zökkenőmentes felhasználói élmény nem garantált.

Az **echo** a maximális bejövő sávszélességet adja meg, mert a legalacsonyabb útválasztási díja van. A **szórás** meghatározza a kimenő üzenetek maximális sávszélességét.

Ne *lépje* túl a következő két táblázat Kiemelt értékeit.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok                       | 1,000 | 2000 | 5000 | 10,000 | 20 000 | 50,000 | 100.000 |
| **Bejövő sávszélesség** | **2 MBps**    | **4 MBps**    | **10 MBps**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Kimenő sávszélesség | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Adás             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2000 | 5000  | 10,000 | 20 000 | 50,000  | 100.000 |
| Bejövő sávszélesség  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Kimenő sávszélesség** | **4 MBps**    | **8 MBps**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

A *bejövő sávszélesség* és a *kimenő sávszélesség* az üzenetek teljes mérete másodpercenként.  Ezek a képletek:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: az üzenetet küldő kapcsolatok száma.

- *outboundConnections*: az üzenetet fogadó kapcsolatok száma.

- *messageSize*: egyetlen üzenet mérete (átlagos érték). Egy kisebb, 1 024 bájtnál kisebb üzenet teljesítményének hatása a 1 024 bájtos üzenethez hasonló.

- *sendInterval*: egy üzenet küldésének időpontja. Az üzenet általában 1 másodperc, ami azt jelenti, hogy másodpercenként egy üzenetet küldenek. A kisebb időköz azt jelenti, hogy több üzenetet küld egy adott időszakban. Például az 0,5 másodperces üzenet azt jelenti, hogy másodpercenként két üzenetet küld.

- *Kapcsolatok*: az Azure Signaler szolgáltatáshoz tartozó maximális küszöbérték minden szinten. Ha a kapcsolatok száma továbbra is növekszik, a rendszer a kapcsolatok szabályozását fogja sújtani.

#### <a name="evaluation-for-complex-use-cases"></a>Összetett használati esetek kiértékelése

##### <a name="bigger-message-size-or-different-sending-rate"></a>Nagyobb méretű üzenet vagy eltérő küldési arány

A valós használati eset bonyolultabb. Előfordulhat, hogy 2 048 bájtnál nagyobb üzenetet küld, vagy a küldési üzenet aránya másodpercenként nem egy üzenet. Vegyük példaként a Unit100's, hogy megtudja, hogyan értékelheti ki a teljesítményét.

Az alábbi táblázat a **szórás** valós használati eseteit mutatja be. Az üzenetek mérete, a kapcsolatok száma és az üzenetek küldési sebessége azonban eltér az előző szakaszban feltételezetttől. A kérdés az, hogy miként lehet következtetni bármelyik elemre (az üzenetek mérete, a kapcsolatok száma vagy az üzenetek küldési sebessége), ha tudjuk, hogy csak kettő közülük van.

| Adás  | Üzenet mérete | Bejövő üzenetek másodpercenként | Kapcsolatok | Küldési időközök |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100.000     | 5 MP                      |
| 2 | 256 KB               | 1                        | 8,000       | 5 MP                      |

A következő képlet az előző képlet alapján könnyen következtethető ki:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

A Unit100 esetében a maximális kimenő sávszélesség 400 MB az előző táblából. A 20 KB-os üzenet mérete esetén a kimenő kapcsolatok maximális száma 400 MB \* 5/20 kb = 100 000, amely megfelel a valós értéknek.

##### <a name="mixed-use-cases"></a>Vegyes használati esetek

A valós használati eset általában a négy alapvető használati esetet ötvözi: **echo**, **Broadcast**, **Küldés csoportba**, és **Küldés a kapcsolódáshoz**. A kapacitás kiértékeléséhez használt módszer a következő:

1. A vegyes használati esetek négy alapvető használati esetre oszthatók.
1. Kiszámítja a maximális bejövő és kimenő üzenetek sávszélességét az előző képletek külön történő használatával.
1. Adja meg a sávszélesség-számításokat a teljes bejövő/kimenő sávszélesség beszerzéséhez. 

Ezután vegye fel a megfelelő szintet a maximális bejövő/kimenő sávszélesség-táblákból.

> [!NOTE]
> Ha több száz vagy több ezer kis csoport számára szeretne üzenetet küldeni, vagy több ezer ügyfél számára üzenetet küld egymásnak, az útválasztási díj erőfölényben lesz. Vegye figyelembe ezt a hatást.

Ha az ügyfelek számára szeretne üzenetet küldeni, győződjön meg arról, hogy az alkalmazáskiszolgáló *nem* a szűk keresztmetszet. A következő "esettanulmány" szakasz útmutatást nyújt a szükséges alkalmazások számának és a konfigurálni kívánt kiszolgálói kapcsolatok számának a megadásához.

## <a name="case-study"></a>Esettanulmány

A következő fejezetek négy tipikus használati esetet mutatnak be a WebSocket-átvitelhez: **echo**, **szórás**, **Küldés csoportba**, és **Küldés a kapcsolatba**. Az egyes forgatókönyvek esetében a szakasz felsorolja az Azure Signaler szolgáltatás aktuális bejövő és kimenő kapacitását. Emellett ismerteti a teljesítményt érintő főbb tényezőket is.

Az alapértelmezett módban az App Server öt kiszolgálói kapcsolatot hoz létre az Azure Signaler szolgáltatással. Az App Server alapértelmezés szerint az Azure Signaler Service SDK-t használja. A következő teljesítményteszt eredményeiben a kiszolgáló kapcsolatainak száma 15 (vagy még több a szórásos küldéshez és egy nagy csoportnak küldött üzenet küldése).

A különböző használati esetek eltérő követelményeket támasztanak az App serverek esetében. A **szórásnak** kis számú alkalmazás-kiszolgálóra van szüksége. Az **echo** vagy **a Küldés a kapcsolódáshoz** sok App-kiszolgáló szükséges.

Az összes felhasználási esetben az üzenet alapértelmezett mérete 2 048 bájt, az üzenet küldési időköze pedig 1 másodperc.

### <a name="default-mode"></a>Alapértelmezett mód

Az ügyfelek, a webalkalmazás-kiszolgálók és az Azure Signaler szolgáltatás az alapértelmezett módban van. Minden ügyfél egyetlen kapcsolatban áll.

#### <a name="echo"></a>Echo

Először egy webalkalmazás csatlakozik az Azure Signaler szolgáltatáshoz. Másodszor, sok ügyfél csatlakozik a webalkalmazáshoz, amely átirányítja az ügyfeleket az Azure Signaler szolgáltatásba a hozzáférési jogkivonattal és végponttal. Ezután az ügyfelek létrehozhatnak WebSocket-kapcsolatokat az Azure Signaler szolgáltatással.

Miután az összes ügyfél kapcsolatot létesít, elindít egy üzenetet, amely egy időbélyeget tartalmaz az adott hubhoz, másodpercenként. A hub visszaküldi az üzenetet az eredeti ügyfelének. Minden ügyfél kiszámítja a késést, amikor visszakapja az ECHO-üzenetet.

A következő ábrán az 5 – 8 (piros Kiemelt forgalom) ciklusban van. A hurok alapértelmezett időtartamra fut (5 perc), és lekéri az összes üzenet késésének statisztikáit.

![Az ECHO használati esetének forgalma](./media/signalr-concept-performance/echo.png)

Az **echo** működése meghatározza, hogy a bejövő sávszélesség maximális értéke megegyezik a kimenő sávszélesség maximális értékével. A részletekért lásd az alábbi táblázatot.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok                       | 1,000 | 2000 | 5000 | 10,000 | 20 000 | 50,000 | 100.000 |
| Bejövő/kimenő üzenetek másodpercenként | 1,000 | 2000 | 5000 | 10,000 | 20 000 | 50,000 | 100.000 |
| Bejövő/kimenő sávszélesség | 2 MBps   | 4 MBps   | 10 MBps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

Ebben a használati esetben minden ügyfél meghívja az App Serverben definiált hubot. A hub csak meghívja az eredeti ügyféloldali oldalon definiált metódust. Ez a hub az **echo** legkönnyűebb központja.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Még ennél az egyszerű Központnál is, az alkalmazás-kiszolgáló forgalmi nyomása kiemelten jelenik meg, mivel az **echo** bejövő üzenetek betöltése megnövekszik. Ennél a forgalmi nyomásnál számos alkalmazáskiszolgáló szükséges a nagyméretű SKU-rétegek számára. Az alábbi táblázat az összes réteghez tartozó app Server-darabszámot sorolja fel.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20 000 | 50,000 | 100.000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Az ügyfél-kapcsolódási szám, az üzenetek mérete, a küldési sebesség, az SKU-rétegek és az alkalmazás-kiszolgáló PROCESSZORa/memóriája befolyásolja az **echo** általános teljesítményét.

#### <a name="broadcast"></a>Adás

**Szórás** esetén, amikor a webalkalmazás fogadja az üzenetet, a rendszer az összes ügyfelet közvetíti. Minél több ügyfelet szeretne közvetíteni, annál több üzenet van az összes ügyfél számára. Lásd az alábbi diagramot.

![Forgalom a szórásos használati esethez](./media/signalr-concept-performance/broadcast.png)

Kis számú ügyfél sugároz. A bejövő üzenet sávszélessége kicsi, de a kimenő sávszélesség óriási. A kimenő üzenetek sávszélessége növekszik, ahogy az Ügyfélkapcsolat vagy a szórási arány növekszik.

A következő táblázat összefoglalja a maximális ügyfélkapcsolatokat, a bejövő/kimenő üzenetek darabszámát és a sávszélességet.

|     Adás             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2000 | 5000  | 10,000 | 20 000 | 50,000  | 100.000 |
| Bejövő üzenetek másodpercenként  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Kimenő üzenetek másodpercenként | 2000 | 4,000 | 10,000 | 20 000 | 40,000 | 100.000 | 200,000 |
| Bejövő sávszélesség  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Kimenő sávszélesség | 4 MBps   | 8 MBps   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

Az üzeneteket követő műsorszolgáltatási ügyfelek nem több mint négynél. Az **echo** -hoz képest kevesebb alkalmazáskiszolgáló szükséges, mivel a bejövő üzenetek mennyisége kicsi. Két alkalmazáskiszolgáló elegendő az SLA-hoz és a teljesítménnyel kapcsolatos megfontolásokhoz is. Érdemes azonban a kiszolgáló alapértelmezett kapcsolatainak növelésével elkerülni az egyensúlyhiányt, különösen a Unit50 és a Unit100 esetében.

|   Adás      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20 000 | 50,000 | 100.000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Növelje az alapértelmezett kiszolgáló kapcsolatait 5 és 40 között minden egyes alkalmazás-kiszolgálón, hogy elkerülje a lehetséges kiegyensúlyozatlan kiszolgálói kapcsolatokat az Azure Signaler szolgáltatással.
>
> Az ügyfél-kapcsolódási szám, az üzenet mérete, a küldési sebesség és az SKU-csomag hatással van a **szórás** általános teljesítményére.

#### <a name="send-to-group"></a>Küldés csoportba

A **Küldés csoportnak** használati esethez hasonló forgalmi minta van a **szóráshoz**. A különbség az, hogy miután az ügyfelek WebSocket-kapcsolatokat létesítettek az Azure Signaler szolgáltatással, hozzá kell csatlakozniuk a csoportokhoz, mielőtt üzenetet tudnak küldeni egy adott csoportnak. A következő ábra a forgalmat mutatja be.

![A küldési csoport használati esetének forgalma](./media/signalr-concept-performance/sendtogroup.png)

A csoport tagjai és a csoportok száma két tényező befolyásolja a teljesítményt. Az elemzés egyszerűsítése érdekében két típusú csoportot definiálunk:

- **Kis csoport**: minden csoport 10 kapcsolattal rendelkezik. A csoport száma egyenlő (a kapcsolatok maximális száma)/10. Ha például a Unit1 esetében 1 000 a kapcsolatok száma, akkor a 1000/10 = 100 csoportokkal rendelkezik.

- **Big Group**: a csoport száma mindig 10. A csoport tagjainak száma egyenlő (a kapcsolatok maximális száma)/10. Ha például a Unit1 esetében 1 000 a kapcsolatok száma, akkor minden csoport 1000/10 = 100 taggal rendelkezik.

A **Küldés a csoportba** útválasztási díj kerül az Azure Signaler szolgáltatásba, mert a cél kapcsolatokat egy elosztott adatstruktúrán keresztül kell megkeresni. A küldési kapcsolatok növekedésével a díjak növekednek.

##### <a name="small-group"></a>Kis csoport

Az útválasztási díj jelentős számú, mert az üzenetek sok kis csoportba küldhetők. Az Azure Signaler szolgáltatás implementációja jelenleg a Unit50-ben eléri az útválasztási költségeket. További CPU és memória hozzáadása nem segít, így a Unit100 nem tudja tovább javítani a kialakítást. Ha több bejövő sávszélességre van szüksége, forduljon az ügyfélszolgálathoz.

|   Küldés kis csoportba     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Kapcsolatok               | 1,000 | 2000 | 5000  | 10,000 | 20 000 | 50,000 | 100.000
| Csoport tagjainak száma        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Csoportok száma               | 100   | 200   | 500    | 1,000  | 2000  | 5000  | 10,000 
| Bejövő üzenetek másodpercenként  | 200   | 400   | 1,000  | 2500  | 4,000  | 7 000  | 7 000   |
| Bejövő sávszélesség  | 400 KBps  | 800 KBps  | 2 MBps     | 5 MBps     | 8 MBps     | 14 MBps    | 14 MBps     |
| Kimenő üzenetek másodpercenként | 2000 | 4,000 | 10,000 | 25,000 | 40,000 | 70 000 | 70 000  |
| Kimenő sávszélesség | 4 MBps    | 8 MBps    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Számos ügyfélkapcsolat meghívja a központot, így az alkalmazáskiszolgáló száma is kritikus fontosságú a teljesítmény szempontjából. A következő táblázat felsorolja a javasolt app Server-számlálókat.

|  Küldés kis csoportba   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20 000 | 50,000 | 100.000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Az ügyfél-kapcsolódási szám, az üzenet mérete, a küldési sebesség, az útválasztási díj, az SKU-mennyiség, valamint az alkalmazás-kiszolgáló PROCESSZORa/memóriája hatással van a **kis csoportba küldés** teljes teljesítményére.

##### <a name="big-group"></a>Nagy csoport

A **Big csoportba való küldéshez** a kimenő sávszélesség a szűk keresztmetszetet megelőzően, az útválasztási többletköltségek megkezdése előtt válik. A következő táblázat felsorolja a maximális kimenő sávszélességet, amely majdnem megegyeznek a **szórásos küldéssel**.

|    Küldés a Big Group-ba      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2000 | 5000  | 10,000 | 20 000 | 50,000  | 100.000
| Csoport tagjainak száma        | 100   | 200   | 500    | 1,000  | 2000  | 5000   | 10,000 
| Csoportok száma               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Bejövő üzenetek másodpercenként  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Bejövő sávszélesség  | 80 KBps   | 40 KBps   | 40 KBps    | 20 KBps    | 40 KBps    | 40 KBps     | 40 KBps     |
| Kimenő üzenetek másodpercenként | 2000 | 4,000 | 10,000 | 20 000 | 40,000 | 100.000 | 200,000 |
| Kimenő sávszélesség | 8 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

A küldési kapcsolatok száma nem haladja meg a 40-ot. Az alkalmazás-kiszolgáló terhelése kicsi, ezért a javasolt számú webalkalmazás kicsi.

|  Küldés a Big Group-ba  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20 000 | 50,000 | 100.000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Növelje az alapértelmezett kiszolgáló kapcsolatait 5 és 40 között minden egyes alkalmazás-kiszolgálón, hogy elkerülje a lehetséges kiegyensúlyozatlan kiszolgálói kapcsolatokat az Azure Signaler szolgáltatással.
> 
> Az ügyfél-kapcsolódási szám, az üzenet mérete, a küldési sebesség, az útválasztási díj és az SKU-rétegek hatással vannak a **Big Group küldésének** teljes teljesítményére.

#### <a name="send-to-connection"></a>Küldés a kapcsolódásba

Ha az ügyfelek kapcsolatot létesít az Azure Signaler szolgáltatással, a **Küldés kapcsolaton keresztüli** használati esethez minden ügyfél egy speciális hubot hív meg a saját KAPCSOLATi azonosítójának lekéréséhez. A teljesítmény-teljesítményteszt az összes kapcsolódási azonosítót gyűjti, megkeveri őket, és hozzárendeli azokat az összes ügyfélhez küldési célként. Az ügyfelek továbbra is az üzenetet küldik a célként megadott kapcsolódásnak, amíg a teljesítményteszt be nem fejeződik.

![A Küldés és az ügyfél közötti használati eset forgalma](./media/signalr-concept-performance/sendtoclient.png)

A **küldéshez a kapcsolódáshoz** használt útválasztási díj hasonló a **kis csoportoknak küldött küldési** díjakhoz.

Ahogy nő a kapcsolatok száma, az útválasztási díj a teljes teljesítményt korlátozza. A Unit50 elérte a korlátot. Ennek eredményeképpen a Unit100 nem tud tovább javítani.

A következő táblázat egy statisztikai összefoglaló a **Küldés a kapcsolódási** teljesítményteszthez való futtatása után.

|   Küldés a kapcsolódásba   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Kapcsolatok                        | 1,000 | 2000 | 5000 | 10,000 | 20 000 | 50,000          | 100.000         |
| Bejövő/kimenő üzenetek másodpercenként | 1,000 | 2000 | 5000 | 8,000  | 9000  | 20 000 | 20 000 |
| Bejövő/kimenő sávszélesség | 2 MBps    | 4 MBps    | 10 MBps   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Ez a használati eset nagy terhelést igényel az App Server oldalán. Tekintse meg az alábbi táblázatban a javasolt app Server-darabszámot.

|  Küldés a kapcsolódásba  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20 000 | 50,000 | 100.000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Az ügyfél-kapcsolódási szám, az üzenet mérete, a küldési sebesség, az útválasztási díj, az SKU-mennyiség, valamint a processzor/memória az alkalmazás-kiszolgáló számára hatással van a **Küldés** teljes teljesítményére.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET-jelző echo, broadcast és küldés kis csoportba

Az Azure Signaler szolgáltatás ugyanazt a teljesítményt biztosítja a ASP.NET-jelzőhöz. 

A teljesítményteszt az Azure Web Appst használja az ASP.NET-jelzőhöz tartozó [standard Service-csomag S3](https://azure.microsoft.com/pricing/details/app-service/windows/) -s verziójában.

A következő táblázat a ASP.NET Signaler **echo** által javasolt webalkalmazások számának megadását ismerteti.

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20 000 | 50,000 | 100.000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

A következő táblázat a ASP.NET Signaler- **szóráshoz** javasolt webalkalmazások számának áttekintését tartalmazza.

|  Adás       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20 000 | 50,000 | 100.000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

A következő táblázat a ASP.NET-szignáló számára javasolt webalkalmazások számának a **kis csoportba való küldését** ismerteti.

|  Küldés kis csoportba     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Kapcsolatok      | 1,000 | 2000 | 5000 | 10,000 | 20 000 | 50,000 | 100.000 |
| Alkalmazás-kiszolgálók száma | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Kiszolgáló nélküli mód

Az ügyfelek és az Azure Signaler szolgáltatás kiszolgáló nélküli módban vesz részt. Minden ügyfél egyetlen kapcsolatban áll. Az ügyfél az REST APIon keresztül küld üzeneteket egy másik ügyfélnek, vagy az összes üzenetet közvetíti.

Nagy sűrűségű üzenetek küldése a REST APIon keresztül nem annyira hatékony, mint a WebSocket használata. Minden alkalommal új HTTP-kapcsolat kiépítését igényli, és ez a kiszolgáló nélküli üzemmódban további költségeket eredményez.

#### <a name="broadcast-through-rest-api"></a>Közvetítés REST API
Az összes ügyfél létrehozza a WebSocket-kapcsolatokat az Azure Signaler szolgáltatással. Ezután egyes ügyfelek megkezdik az REST API közvetítését. Az üzenetküldés (bejövő) a HTTP Poston keresztül történik, ami nem hatékony a websockethez képest.

|   Közvetítés REST API     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2000 | 5000  | 10,000 | 20 000 | 50,000  | 100.000 |
| Bejövő üzenetek másodpercenként  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Kimenő üzenetek másodpercenként | 2000 | 4,000 | 10,000 | 20 000 | 40,000 | 100.000 | 200,000 |
| Bejövő sávszélesség  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Kimenő sávszélesség | 4 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Küldés a felhasználónak a REST API
A teljesítményteszt az összes ügyfélhez hozzárendeli a felhasználóneveket, mielőtt megkezdené az Azure Signaler szolgáltatáshoz való csatlakozást. Miután az ügyfelek WebSocket-kapcsolatokat létesítettek az Azure Signaler szolgáltatással, elkezdik másoknak üzeneteket küldeni a HTTP Post használatával.

|   Küldés a felhasználónak a REST API | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Kapcsolatok               | 1,000 | 2000 | 5000  | 10,000 | 20 000 | 50,000  | 100.000 |
| Bejövő üzenetek másodpercenként  | 300   | 600   | 900    | 1 300  | 2000  | 10,000  | 18000  |
| Kimenő üzenetek másodpercenként | 300   | 600   | 900    | 1 300  | 2000  | 10,000  | 18000 |
| Bejövő sávszélesség  | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |
| Kimenő sávszélesség | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 MBps     | 10 MBps     | 36 MBps    |

## <a name="performance-test-environments"></a>Teljesítmény-tesztelési környezetek

A korábban felsorolt használati esetek esetében a teljesítményteszteket egy Azure-környezetben hajtottuk végre. A legtöbb esetben 50 ügyfél virtuális gépeket és 20 app Server-alapú virtuális gépet használtunk. Íme néhány részlet:

- Ügyfél virtuális gép mérete: StandardDS2V2 (2 vCPU, 7G memória)

- App Server VM-méret: StandardF4sV2 (4 vCPU, 8G memória)

- Azure Signaler SDK-kiszolgáló kapcsolatai: 15

## <a name="performance-tools"></a>Teljesítmény-eszközök

Az Azure Signaler szolgáltatáshoz tartozó teljesítményfigyelő eszközöket a [githubon](https://github.com/Azure/azure-signalr-bench/)találja.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben áttekinti az Azure Signaler szolgáltatás teljesítményét a tipikus használati esetekben.

A szolgáltatás belső szolgáltatásairól és a skálázásról a következő útmutatókban talál további információt:

* [Az Azure SignalR szolgáltatás belső elemei](signalr-concept-internals.md)
* [Azure Signaler szolgáltatás skálázása](signalr-howto-scale-multi-instances.md)
