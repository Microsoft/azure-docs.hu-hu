---
title: Intelligens szerződések integrációs mintái – Azure Blockchain Workbench
description: Az intelligens szerződések integrációs mintáinak áttekintése az Azure Blockchain Workbench előzetes verziójában.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: dae63e16356e825d3be31380df1648749e59d8bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015502"
---
# <a name="smart-contract-integration-patterns"></a>Intelligens szerződések integrációs mintái

Az intelligens szerződések gyakran olyan üzleti munkafolyamatot jelentenek, amelynek integrálnia kell a külső rendszerekkel és eszközökkel.

Ezeknek a munkafolyamatoknak a követelményei közé tartozik egy olyan elosztott Főkönyv tranzakcióinak kezdeményezése, amely külső rendszerből, szolgáltatásból vagy eszközből származó adatokkal is rendelkezik. Emellett a külső rendszereknek is szükségük van arra, hogy az elosztott főkönyvben lévő intelligens szerződésekből származó eseményekre reagáljanak.

Az REST API és üzenetküldési integráció külső rendszerekből származó tranzakciókat küld az Azure Blockchain Workbench alkalmazásban található intelligens szerződéseknek. Emellett az eseményekre vonatkozó értesítéseket is küld a külső rendszereknek az alkalmazáson belüli módosítások alapján.

Az adatintegrációs forgatókönyvek esetében az Azure Blockchain Workbench olyan adatbázis-nézeteket tartalmaz, amelyek tranzakciós adatok kombinációját egyesítik a Blockchain, valamint az alkalmazásokkal és az intelligens szerződésekkel kapcsolatos metaadatokat.

Emellett bizonyos forgatókönyvek, például az ellátási lánchoz vagy az adathordozóhoz kapcsolódó esetekben a dokumentumok integrálására is szükség lehet. Habár az Azure Blockchain Workbench nem biztosít API-hívásokat a dokumentumok közvetlen kezelésére, a dokumentumokat beépítheti egy Blockchain-alkalmazásba. Ez a szakasz szintén tartalmazza ezt a mintát.

Ez a szakasz tartalmazza az egyes típusú integrációk megvalósításához szükséges mintákat a végpontok közötti megoldásokban.

## <a name="rest-api-based-integration"></a>REST API-alapú integráció

Az Azure Blockchain Workbench által generált webalkalmazáson belüli képességek a REST APIon keresztül érhetők el. A képességek közé tartozik az Azure Blockchain Workbench az alkalmazások feltöltése, konfigurálása és felügyelete, tranzakciók küldése egy elosztott főkönyvbe, valamint az alkalmazás metaadatainak és a főkönyvi adatok lekérdezése.

A REST API elsődlegesen olyan interaktív ügyfelekhez használják, mint a webes, mobil és bot-alkalmazások.

Ez a szakasz a REST API azon szempontjaira összpontosít, amelyek tranzakciókat küldenek egy elosztott főkönyvbe, valamint olyan mintákat, amelyek az Azure Blockchain Workbench *off Chain* adatbázisából származó tranzakciók adatait kérdezik le.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Tranzakciók küldése egy elosztott főkönyvnek egy külső rendszerből

Az Azure Blockchain Workbench REST API hitelesített kérelmeket küld a tranzakciók végrehajtásához egy elosztott főkönyvben.

![Tranzakciók küldése elosztott főkönyvbe](./media/integration-patterns/send-transactions-ledger.png)

A tranzakciók végrehajtása a korábban bemutatott folyamat használatával történik, ahol:

-   A külső alkalmazás hitelesíti magát az Azure Blockchain Workbench-telepítés részeként kiépített Azure Active Directoryon.
-   A jogosult felhasználók olyan tulajdonosi jogkivonatot kapnak, amely az API-hoz intézett kérelmekkel küldhető el.
-   A külső alkalmazások hívásokat kezdeményeznek a REST API a tulajdonosi jogkivonat használatával.
-   A REST API a kérést üzenetként csomagolja, és elküldi a Service Busnak. Innen lekéri, aláírja és elküldi a megfelelő elosztott főkönyvbe.
-   A REST API egy kérést küld az Azure Blockchain Workbench SQL DB-nek a kérelem rögzítéséhez és az aktuális kiépítési állapot létrehozásához.
-   Az SQL-adatbázis visszaadja a kiépítési állapotot, és az API-hívás visszaadja az azonosítót az őt meghívó külső alkalmazásnak.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Blockchain Workbench-metaadatok és elosztott főkönyvi tranzakciók lekérdezése

Az Azure Blockchain Workbench REST API hitelesített kéréseket küld az intelligens Szerződés végrehajtásával kapcsolatos adatok lekérdezéséhez egy elosztott főkönyvben.

![Metaadatok lekérdezése](./media/integration-patterns/querying-metadata.png)

A lekérdezés a korábban bemutatott folyamat használatával történik, ahol:

1. A külső alkalmazás hitelesíti magát az Azure Blockchain Workbench-telepítés részeként kiépített Azure Active Directoryon.
2. A jogosult felhasználók olyan tulajdonosi jogkivonatot kapnak, amely az API-hoz intézett kérelmekkel küldhető el.
3. A külső alkalmazások hívásokat kezdeményeznek a REST API a tulajdonosi jogkivonat használatával.
4. A REST API lekérdezi a kérelem adatait az SQL-ADATBÁZISból, és visszaküldi az ügyfélnek.

## <a name="messaging-integration"></a>Üzenetkezelési integráció

Az üzenetkezelési integráció elősegíti a rendszerekkel, szolgáltatásokkal és eszközökkel való interakciót, ha az interaktív bejelentkezés nem lehetséges vagy nem kívánatos. Az üzenetkezelési integráció két típusú üzenetre összpontosít: a tranzakciókat kérő üzenetek egy elosztott főkönyvön futnak, és az adott Főkönyv által a tranzakciók végrehajtásakor kitett események.

Az üzenetkezelési integráció a felhasználói létrehozással, a szerződések létrehozásával és a szerződések tranzakcióinak végrehajtásával kapcsolatos tranzakciók végrehajtásán és figyelésén alapul, és elsősorban a *fej* nélküli háttérrendszer-rendszerek használják.

Ez a szakasz az üzenetsor-alapú API azon szempontjaira koncentrál, amelyek tranzakciókat küldenek egy elosztott főkönyvbe, valamint olyan mintákat, amelyek az alapul szolgáló elosztott Főkönyv által közzétett üzeneteket jelölik.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Egyirányú esemény kézbesítése intelligens szerződésből egy esemény-felhasználó felé 

Ebben az esetben egy esemény egy intelligens szerződésen belül történik, például egy állapot változása vagy egy adott típusú tranzakció végrehajtása. Ez az esemény egy Event Gridon keresztül történik a továbbfelhasználó számára, és ezek a fogyasztók a megfelelő műveleteket végzik.

Ilyen eset például, ha egy tranzakció bekövetkezik, a fogyasztó riasztást kap, és műveleteket végezhet, például az adatok rögzítését egy SQL-ADATBÁZISba vagy a Common Data Service. Ez a forgatókönyv ugyanaz a minta, amelyet a Workbench az SQL-adatbázis *ki* -és betöltéséhez is követ.

Egy másik lenne, ha egy intelligens szerződés egy adott állapotra vált, például ha egy szerződés egy *OutOfCompliance* kerül. Ha ez az állapot változik, riasztást indíthat a rendszergazda mobiltelefonjára.

![Egyirányú esemény kézbesítése](./media/integration-patterns/one-way-event-delivery.png)

Ez a forgatókönyv a korábban bemutatott folyamat használatával fordul elő, ahol:

-   Az intelligens szerződés új állapotba vált, és egy eseményt küld a főkönyvnek.
-   A Főkönyv fogadja és kézbesíti az eseményt az Azure Blockchain Workbench számára.
-   Az Azure Blockchain Workbench feliratkozott a főkönyvből származó eseményekre, és fogadja az eseményt.
-   Az Azure Blockchain Workbench közzéteszi az eseményt az Event Grid előfizetőknek.
-   A rendszer Előfizeti a külső rendszereket a Event Gridra, felhasználja az üzenetet, és végrehajtja a megfelelő műveleteket.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Külső rendszerről intelligens szerződésre küldött üzenet egyirányú kézbesítése

Van olyan forgatókönyv is, amely az ellenkező irányba áramlik. Ebben az esetben egy érzékelő vagy egy külső rendszer generál egy eseményt, és az adott eseményből származó adatoknak egy intelligens szerződésbe kell elküldeni.

Gyakori példa a pénzügyi piacokról származó adatok továbbítása, például az árucikkek, a készletek vagy a kötvények árai egy intelligens szerződéshez.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Azure Blockchain Workbench közvetlen kézbesítése a várt formátumban

Egyes alkalmazások az Azure Blockchain Workbench-sel való integrációhoz készültek, és közvetlenül a várt formátumokban hozzanak létre és küldenek üzeneteket.

![Közvetlen kézbesítés](./media/integration-patterns/direct-delivery.png)

A kézbesítés a korábban bemutatott folyamat használatával történik, ahol:

-   Egy esemény egy külső rendszeren történik, amely elindítja az Azure Blockchain Workbench üzenet létrehozását.
-   A külső rendszernek van olyan kódja, amely az üzenet ismert formátumban való létrehozására van írva, és közvetlenül a Service Bus küldi el.
-   Az Azure Blockchain Workbench feliratkozott a Service Bus eseményeire, és lekéri az üzenetet.
-   Az Azure Blockchain Workbench hívást kezdeményez a főkönyvnek, és adatokat küld a külső rendszertől egy adott szerződésnek.
-   Az üzenet kézhezvétele után a Szerződés új állapotba vált.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Ismeretlen formátumú üzenet kézbesítése az Azure Blockchain Workbenchben

Néhány rendszer nem módosítható úgy, hogy az Azure Blockchain Workbench által használt szabványos formátumban kézbesítse az üzeneteket. Ezekben az esetekben gyakran használhatók a rendszerek meglévő mechanizmusai és az üzenetek formátuma is. A rendszerek natív üzeneteinek típusait Logic Apps, Azure Functions vagy más egyéni kód használatával lehet átalakítani, hogy a várt standard üzenetküldési formátumok egyikét képezze.

![Ismeretlen üzenet formátuma](./media/integration-patterns/unknown-message-format.png)

Ez a korábban bemutatott folyamat használatával történik, ahol:

-   Egy esemény egy külső rendszeren történik, amely egy üzenet létrehozását indítja el.
-   A logikai alkalmazások vagy egyéni kódok használatával fogadhatják ezt az üzenetet, és átalakíthatja azt egy szabványos Azure Blockchain Workbench formátumú üzenetbe.
-   A logikai alkalmazás közvetlenül a Service Bus küldi az átalakított üzenetet.
-   Az Azure Blockchain Workbench feliratkozott a Service Bus eseményeire, és lekéri az üzenetet.
-   Az Azure Blockchain Workbench hívást kezdeményez a főkönyvnek, és adatokat küld a külső rendszertől a szerződés egy adott függvényének.
-   A függvény végrehajtja és általában módosítja az állapotot. Az állapot módosítása továbbítja az intelligens szerződésben tükröződő üzleti munkafolyamatot, amely lehetővé teszi más függvények megfelelő végrehajtását.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Vezérlés átirányítása külső folyamatra, és várakozás a befejezésre

Vannak olyan forgatókönyvek, amelyekben egy intelligens szerződésnek le kell állítania a belső végrehajtást, és ki kell kapcsolnia egy külső folyamatot. Ezt követően a külső folyamat elkészül, küldjön egy üzenetet az intelligens szerződésnek, a végrehajtás pedig azután folytatja az intelligens szerződést.

#### <a name="transition-to-the-external-process"></a>Áttérés a külső folyamatra

Ez a minta jellemzően a következő módszer használatával valósítható meg:

-   Az intelligens szerződés adott állapotra vált. Ebben az állapotban a nem vagy a korlátozott számú függvény hajtható végre, amíg egy külső rendszer el nem végzi a kívánt műveletet.
-   Az állapot változása eseményként felszínre kerül egy alsóbb rétegbeli fogyasztó számára.
-   Az alárendelt fogyasztó megkapja az eseményt, és elindítja a külső kód végrehajtását.

![A diagramon az állapot változása látható a szerződésen belül, ami egy eseményt mutat be az elosztott főkönyvbe. A Blockchain Workbench ezután felveszi az eseményt, és közzéteszi azt.](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Az intelligens szerződés vezérlésének visszaadása

A külső rendszer testreszabásának lehetősége attól függően előfordulhat, hogy nem tudja kézbesíteni az üzeneteket az Azure Blockchain Workbench által várt szabványos formátumok egyikében. Az ilyen üzenetek egyikének létrehozásához szükséges külső rendszerek alapján megállapítható, hogy a következő két visszatérési útvonal közül melyiket kell megtenni.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Azure Blockchain Workbench közvetlen kézbesítése a várt formátumban

![Az ábrán látható egy P I üzenet a külső rendszerről, amelyet a Blockchain Workbench a Service Buson keresztül felvesz. Az Blockchain Workbench ezután az ügynök nevében küldi el az üzenetet tranzakcióként az elosztott főkönyvnek. A rendszer átadja a szerződést a szerződésnek, ahol az állapot változását okozza.](./media/integration-patterns/direct-delivery.png)

Ebben a modellben a szerződéssel és az azt követő állapottal való kommunikáció az előző folyamat után történik, ahol:

-   A külső programkód végrehajtásának befejezését vagy egy adott mérföldkövet követően a rendszer egy eseményt továbbít az Azure Blockchain Workbenchhez kapcsolódó Service Bus.

-   Azokhoz a rendszerekhez, amelyek nem módosíthatók közvetlenül az API elvárásainak megfelelő üzenet írásához, átalakítja őket.

-   Az üzenet tartalma fel van csomagolva, és egy adott függvénynek küldi el az intelligens szerződést. Ez a kézbesítés a külső rendszerhez társított felhasználó nevében történik.

-   A függvény végrehajtja és általában módosítja az állapotot. Az állapot módosítása továbbítja az intelligens szerződésben tükröződő üzleti munkafolyamatot, amely lehetővé teszi más függvények megfelelő végrehajtását.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Ismeretlen formátumú üzenet kézbesítése az Azure Blockchain Workbenchben

![Ismeretlen üzenet formátuma](./media/integration-patterns/unknown-message-format.png)

Ebben a modellben, amikor egy szabványos formátumban lévő üzenet nem küldhető el közvetlenül, a szerződéssel folytatott kommunikáció és az azt követő állapotváltozás az előző folyamat után következik be, ahol:

1.  A külső programkód végrehajtásának befejezését vagy egy adott mérföldkövet követően a rendszer egy eseményt továbbít az Azure Blockchain Workbenchhez kapcsolódó Service Bus.
2.  A logikai alkalmazások vagy egyéni kódok használatával fogadhatják ezt az üzenetet, és átalakíthatja azt egy szabványos Azure Blockchain Workbench formátumú üzenetbe.
3.  A logikai alkalmazás közvetlenül a Service Bus küldi az átalakított üzenetet.
4.  Az Azure Blockchain Workbench feliratkozott a Service Bus eseményeire, és lekéri az üzenetet.
5.  Az Azure Blockchain Workbench hívást kezdeményez a főkönyvnek, és adatokat küld a külső rendszertől egy adott szerződésnek.
6. Az üzenet tartalma fel van csomagolva, és egy adott függvénynek küldi el az intelligens szerződést. Ez a kézbesítés a külső rendszerhez társított felhasználó nevében történik.
7.  A függvény végrehajtja és általában módosítja az állapotot. Az állapot módosítása továbbítja az intelligens szerződésben tükröződő üzleti munkafolyamatot, amely lehetővé teszi más függvények megfelelő végrehajtását.

## <a name="iot-integration"></a>IoT-integráció

A közös integrációs forgatókönyvek az érzékelőkből beolvasott telemetria-adatok bevonása egy intelligens szerződésbe. Az érzékelők által szállított adatmennyiség alapján az intelligens szerződések tájékozott műveleteket végezhetnek, és megváltoztathatják a szerződés állapotát.

Ha például egy olyan teherautót szállítanak, amely a gyógyszert a 110 fok fölé helyezi, akkor ez hatással lehet a gyógyszer hatékonyságára, és a nyilvános biztonsági problémát okozhat, ha a szolgáltatás nem észleli és nem távolítja el az ellátási láncot. Ha az illesztőprogram óránként 100 mérföldre felgyorsítja autóját, az eredményül kapott érzékelő információi kiválthatják a biztosításuk szolgáltató általi törlését. Ha az autó bérelt autó volt, a GPS-adatjelzés azt jelezheti, hogy az illesztőprogram a bérleti szerződés által érintett földrajzi helyen kívülre került, és díjat számít fel.

A kihívás az, hogy ezek az érzékelők állandó módon tudják szolgáltatni az adatmennyiséget, és nem alkalmasak arra, hogy az összes adat egy intelligens szerződésbe legyen küldve. Egy tipikus módszer a blockchain küldött üzenetek számának korlátozása, miközben az összes üzenetet egy másodlagos tárolóba kézbesíti. Például csak a rögzített időközönként fogadott üzeneteket, például óránként egyszer, és ha egy foglalt érték az intelligens szerződés tartományán kívül esik. A tűréshatárokon kívül eső értékek ellenőrzése biztosítja, hogy a szerződések üzleti logikájának megfelelő adatok fogadása és végrehajtása megtörténjen. Az intervallumban lévő érték ellenőrzése megerősíti, hogy az érzékelő továbbra is jelentéskészítésre kerül. A rendszer a szélesebb körű jelentéskészítés, elemzés és gépi tanulás érdekében minden adattal elküld egy másodlagos jelentési tárolóba. Előfordulhat például, hogy egy intelligens szerződés esetében nem szükséges percenként beolvasni az érzékelőt a GPS-hez, ezért fontos, hogy a jelentésekben vagy a leképezési útvonalakban használt információkkal szolgálnak.

Az Azure platformon az eszközökkel való integráció általában IoT Hubtel történik. IoT Hub az üzenetek továbbítását biztosítja a tartalom alapján, és lehetővé teszi a korábban leírt funkciók típusát.

![IoT üzenetek](./media/integration-patterns/iot.png)

A folyamat egy mintázatot ábrázol:

-   Az eszközök közvetlenül vagy egy helyszíni átjárón keresztül kommunikálnak a IoT Hub.
-   IoT Hub fogadja az üzeneteket, és kiértékeli az üzeneteket a létrehozott útvonalakon, például az üzenet tartalmának ellenőrzéséhez. *Az érzékelő a 50 fokosnál nagyobb hőmérsékletet jelent?*
-   A IoT Hub a feltételeknek megfelelő üzeneteket küld egy meghatározott Service Bus az útvonalhoz.
-   Egy logikai alkalmazás vagy más kód figyeli a Service Bus, amelyet az útvonalhoz IoT Hub létesített.
-   A logikai alkalmazás vagy más kód egy ismert formátumba kérdezi le és alakítja át az üzenetet.
-   Az átalakított üzenetet – a standard formátumban – az Azure Blockchain Workbench Service Bus küldi el a rendszer.
-   Az Azure Blockchain Workbench feliratkozott a Service Bus eseményeire, és lekéri az üzenetet.
-   Az Azure Blockchain Workbench hívást kezdeményez a főkönyvnek, és adatokat küld a külső rendszertől egy adott szerződésnek.
-   Az üzenet kézhezvétele után a szerződés kiértékeli az adatmennyiséget, és a kiértékelés eredménye alapján megváltoztathatja az állapotot, például ha magas hőmérsékletre módosítja az állapotot, a rendszer nem felel meg a *megfelelőségnek*.

## <a name="data-integration"></a>Adatintegráció

A REST és az üzenetsor-alapú API-k mellett az Azure Blockchain Workbench hozzáférést biztosít az alkalmazás-és a szerződéses meta-adatokkal feltöltött SQL-ADATBÁZISokhoz, valamint az elosztott főkönyvből származó tranzakciós adatokhoz is.

![Adatintegráció](./media/integration-patterns/data-integration.png)

Az Adatintegráció jól ismert:

-   Az Azure Blockchain Workbench az alkalmazásokkal, munkafolyamatokkal, szerződésekkel és tranzakciókkal kapcsolatos metaadatokat tárolja a szokásos működési működésük részeként.
-   A külső rendszerek vagy eszközök egy vagy több párbeszédpanelt biztosítanak, amelyek megkönnyítik az adatbázissal kapcsolatos információk gyűjtését, például az adatbázis-kiszolgáló nevét, az adatbázis nevét, a hitelesítés típusát, a bejelentkezési hitelesítő adatokat, valamint a használni kívánt adatbázis-nézeteket.
-   A lekérdezések az adatbázis nézeteivel vannak írva, hogy a külső rendszerek, a szolgáltatások, a jelentéskészítés, a fejlesztői eszközök és a nagyvállalati hatékonyságnövelő eszközök segítségével elősegítsék az alárendelt felhasználást

## <a name="storage-integration"></a>Tárterület-integráció

Számos esetben szükség lehet a tanúsítható fájlok beépítésére. Több okból kifolyólag nem helyénvaló, hogy fájlokat helyezzen el egy blockchain. Ehelyett egy közös módszer egy titkosítási kivonat (például SHA-256) végrehajtása egy fájlon, és a kivonat megosztása egy elosztott főkönyvben. A kivonat ismételt elküldése minden jövőbeli időpontban ugyanezt az eredményt kell visszaadnia. Ha a fájl módosítva lett, még akkor is, ha csak egy képpontot módosítanak egy képen, a kivonat egy másik értéket ad vissza.

![Tárterület-integráció](./media/integration-patterns/storage-integration.png)

A minta a következő esetekben valósítható meg:

-   Egy külső rendszer megőrzi a fájlt egy tárolási mechanizmusban, például az Azure Storage-ban.
-   A rendszer kivonatot hoz létre a fájllal vagy a fájllal, valamint a hozzá tartozó metaadatokkal, például a tulajdonos azonosítóját, az URL-címet, ahol a fájl található stb.
-   A kivonatot és a metaadatokat egy intelligens szerződés függvénye, például *FileAdded*
-   A jövőben a fájl és a meta-adatok kivonatolása újra elvégezhető, és összehasonlítható a főkönyvben tárolt értékekkel.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Az integrációs minták REST és Message API-k használatával történő megvalósításának előfeltételei

Annak érdekében, hogy egy külső rendszer vagy eszköz a REST vagy az Message API használatával kommunikáljon az intelligens szerződéssel, az alábbiaknak kell történnie:

1. A konzorcium Azure Active Directory egy olyan fiókot hoz létre, amely a külső rendszerre vagy eszközre vonatkozik.
2. Az Azure Blockchain Workbench-alkalmazáshoz egy vagy több megfelelő intelligens szerződés van definiálva, hogy elfogadja az eseményeket a külső rendszertől vagy eszköztől.
3. Az intelligens szerződéshez tartozó alkalmazás-konfigurációs fájl tartalmazza azt a szerepkört, amelyet a rendszer vagy az eszköz hozzá van rendelve.
4. Az intelligens szerződéshez tartozó alkalmazás-konfigurációs fájl azonosítja, hogy mely állapotokban hívja meg ezt a függvényt a megadott szerepkör.
5. Az alkalmazás konfigurációs fájlja és az intelligens szerződések feltöltése az Azure Blockchain Workbenchbe történik.

Az alkalmazás feltöltése után a külső rendszer Azure Active Directory fiókja hozzá van rendelve a szerződéshez és a társított szerepkörhöz.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>A külső rendszerintegrációs folyamatok tesztelése az integrációs kód írása előtt 

A külső rendszerekkel való integráció számos forgatókönyv kulcsfontosságú követelménye. A külső rendszerekkel való integrációhoz szükséges kód fejlesztéséhez érdemes lehet ellenőrizni az intelligens szerződés kialakítását.

A Azure Active Directory (Azure AD) használata nagy mértékben felgyorsíthatja a fejlesztői hatékonyságot, és időt is igénybe vehet. A kód egy külső rendszerrel való integrációja nem triviális időt vehet igénybe. Az Azure AD és az Azure Blockchain Workbench automatikus generálásával lehetővé teheti a fejlesztők számára, hogy külső rendszerként bejelentkezzenek a Blockchain Workbenchbe, és a külső rendszerből származó értékeket a UX használatával feltöltsék. Gyorsan fejlesztheti és érvényesítheti az ötleteket a koncepciós környezet igazolása előtt, mielőtt az integrációs kód bekerül a külső rendszerekre.
