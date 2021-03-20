---
title: A csatlakoztatott gyári topológia konfigurálása – Azure | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a csatlakoztatott gyári megoldás-gyorsító a topológiával együtt.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: e015c6761b920ef37af2bbfd67ced5fc3218d532
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002238"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>A csatlakoztatott gyári megoldás-gyorsító konfigurálása

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

A csatlakoztatott Factory megoldás-gyorsító egy szimulált irányítópultot mutat egy kitalált vállalati contoso számára. Ez a vállalat számos globális helyen rendelkezik gyárakkal világszerte.

Ez a cikk a contoso példáját használja példaként, amely leírja, hogyan konfigurálható egy csatlakoztatott gyári megoldás topológiája.

## <a name="simulated-factories-configuration"></a>Szimulált gyárak konfigurálása

Minden contoso-gyár rendelkezik olyan üzemi vonalakkal, amelyek mindegyike három állomásból áll. Minden állomás egy adott szerepkörrel rendelkező valódi OPC UA-kiszolgáló.

* Szerelvény-állomás
* Tesztelési állomás
* Csomagoló állomás

Ezek az OPC UA-kiszolgálók OPC UA-csomópontokkal rendelkeznek, és az [OPC-közzétevő](overview-opc-publisher.md) elküldi a csomópontok értékeit a csatlakoztatott gyárnak. Ide tartoznak az alábbiak:

* Aktuális működési állapot, például aktuális energiafogyasztás.
* Termelési információk, például az előállított termékek száma.

Az irányítópult használatával a globális nézetből megtekintheti a contoso gyári topológiáját egy állomás szintű nézetre. A csatlakoztatott gyári irányítópult a következőket teszi lehetővé:

* Az OEE és KPI-számok vizualizációja a topológia minden rétegéhez.
* Az OPC UA-csomópontok aktuális értékeinek vizualizációja az állomásokon.
* Az OEE-és KPI-számok összesítése az állomás szintjéről a globális szintre.
* A riasztások és a végrehajtandó műveletek megjelenítése, ha az értékek meghatározott küszöbértékeket érnek el.

## <a name="connected-factory-topology"></a>Csatlakoztatott gyári topológia

A gyárak, a termelési vonalak és az állomások topológiája hierarchikus:

* A globális szinten a gyári csomópontok gyermekekként vannak elmentve.
* A gyárak a termelési vonal csomópontjait gyermekeik.
* Az éles vonalakban az állomás-csomópontok gyermekeik.
* Az állomások (OPC UA-kiszolgálók) gyermekekként OPC UA-csomópontokkal rendelkeznek.

A topológia minden csomópontja a tulajdonságok közös készletét határozza meg, amelyek a következőket határozzák meg:

* A topológia csomópontjának egyedi azonosítója.
* Egy név.
* Egy leírás.
* Egy rendszerkép.
* A topológiai csomópont gyermekei.
* Az OEE-és KPI-számok minimális, célként megadott és maximális értékei, valamint a végrehajtandó riasztási műveletek.

## <a name="topology-configuration-file"></a>Topológia konfigurációs fájlja

Az előző szakaszban felsorolt tulajdonságok konfigurálásához a csatlakoztatott gyári megoldás egy [ContosoTopologyDescription.js](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json)nevű konfigurációs fájlt használ.

Ezt a fájlt a mappában található megoldás forráskódjában találja `WebApp/Contoso/Topology` .

A következő kódrészlet a konfigurációs fájl vázlatát mutatja be `ContosoTopologyDescription.json` :

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

A, a, a és a gyakori tulajdonságai `<global_configuration>` `<factory_configuration>` `<production_line_configuration>` `<station_configuration>` :

* **Név** (Type string)

  Definiál egy leíró nevet, amelynek csak egyetlen szava lehet, hogy a topológiai csomópont megjelenjen az irányítópulton.

* **Leírás** (Type string)

  Részletesebben ismerteti a topológia csomópontját.

* **Rendszerkép** (Type string)

  A WebApp-megoldás rendszerképének elérési útja, amely megjeleníti, hogy mikor jelenjen meg a topológia csomópontjának információja az irányítópulton.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (típus `<performance_definition>` )

  Ezek a tulajdonságok határozzák meg a riasztások létrehozásához használt működési számadat minimális, célként megadott és maximális értékét. Ezek a tulajdonságok meghatározzák a riasztás észlelésekor végrehajtandó műveleteket is.

A `<factory_configuration>` és az `<production_line_configuration>` elemek tulajdonsága:

* **GUID** (Type string)

  Egyedileg azonosítja a topológia csomópontját.

`<factory_configuration>` tulajdonsága:

* **Hely** (típus `<location_definition>` )

  Megadja, hogy a gyár hol található.

`<station_configuration>` tulajdonságok:

* **OpcUri** (Type string)

  Ezt a tulajdonságot az OPC UA-kiszolgáló OPC UA-alkalmazás URI-ja értékre kell beállítani.
  Mivel az OPC UA-specifikációnak globálisan egyedinek kell lennie, ez a tulajdonság az állomás-topológiai csomópont azonosítására szolgál.

* **OpcNodes**, amely OPC ua-csomópontok tömbje (típus `<opc_node_description>` )

`<location_definition>` tulajdonságok:

* **Város** (Type string)

  A helyhez legközelebb eső város neve

* **Ország** (Type string)

  A hely országa

* **Szélesség** (dupla típus)

  A hely földrajzi szélessége

* **Hosszúság** (dupla típus)

  A hely földrajzi hosszúsága

`<performance_definition>` tulajdonságok:

* **Minimum** (dupla típus)

  Az érték alacsonyabb küszöbértéket érhet el. Ha az aktuális érték a küszöbérték alá esik, a rendszer riasztást generál.

* **Cél** (dupla típus)

  Ideális célként megadott érték.

* **Maximum** (dupla típus)

  Az érték felső küszöbértéke elérhető. Ha az aktuális érték meghaladja ezt a küszöbértéket, a rendszer riasztást generál.

* **MinimumAlertActions** (típus `<alert_action>` )

  Meghatározza a műveletek készletét, amely a minimális riasztásra adott válaszként lehet.

* **MaximumAlertActions** (típus `<alert_action>` )

  Meghatározza a műveletek készletét, amely a maximális riasztásra adott válaszként lehet.

`<alert_action`> tulajdonságai:

* **Típus** (Type string)

  A riasztási művelet típusa. A következő típusok ismertek:

  * **AcknowledgeAlert**: a riasztás állapotának nyugtázott értékre kell váltania.
  * **CloseAlert**: az azonos típusú régebbi riasztások már nem jelennek meg az irányítópulton.
  * **CallOpcMethod**: egy OPC ua-metódust kell meghívni.
  * **OpenWebPage**: meg kell nyitni egy böngészőablakot, amely további környezetfüggő információkat mutat be.

* **Leírás** (Type string)

  Az irányítópulton megjelenített művelet leírása.

* **Paraméter** (Type string)

  A művelet végrehajtásához szükséges paraméterek. Az érték a Művelettípus típusától függ.

  * **AcknowledgeAlert**: nincs szükség paraméterre.
  * **CloseAlert**: nincs szükség paraméterre.
  * **CallOpcMethod**: az OPC ua metódus csomópont-információi és paraméterei "a NodeId, a NodeId, az OPC ua-kiszolgáló URI-ja" formátumban hívható meg.
  * **OpenWebPage**: a böngészőablakban megjelenítendő URL-cím.

`<opc_node_description>` az állomáson található OPC UA-csomópontokról tartalmaz információkat (OPC UA-kiszolgáló). Azok a csomópontok, amelyek nem rendelkeznek meglévő OPC UA-csomópontokkal, de a csatlakoztatott gyár számítási logikájában tárolóként használatosak, szintén érvényesek. A következő tulajdonságokkal rendelkezik:

* **NodeId** (Type string)

  Az OPC UA-csomópont címe az állomás (OPC UA-kiszolgáló) címterület számára. A szintaxisnak a NodeId OPC UA-specifikációjában megadott módon kell megadnia.

* **SymbolicName** (Type string)

  Az irányítópulton megjelenítendő név, ha megjelenik az OPC UA-csomópont értéke.

* **Relevancia** (karakterlánc típusú tömb)

  Azt jelzi, hogy mely OEE-vagy KPI-számításokhoz szükséges az OPC UA-csomópont értéke. Minden tömb elem a következő értékek egyike lehet:

  * **OeeAvailability_Running**: az érték az OEE rendelkezésre állásának kiszámításához szükséges.
  * **OeeAvailability_Fault**: az érték az OEE rendelkezésre állásának kiszámításához szükséges.
  * **OeePerformance_Ideal**: az érték az OEE-teljesítmény kiszámításához szükséges, és általában állandó érték.
  * **OeePerformance_Actual**: az érték az OEE-teljesítmény kiszámításához szükséges.
  * **OeeQuality_Good**: az érték az OEE-minőség kiszámításához szükséges.
  * **OeeQuality_Bad**: az érték az OEE-minőség kiszámításához szükséges.
  * **Kpi1**: az érték a Kpi1 számításához szükséges.
  * **Kpi2**: az érték a Kpi2 számításához szükséges.

* **Műveleti kód** (Type string)

  Azt jelzi, hogy az OPC UA-csomópont értékét hogyan kezelje a Time Series Insight-lekérdezések és az OEE/KPI-számítások. Minden idősorozat-betekintési lekérdezés egy adott TimeSpan céloz meg, amely a lekérdezés paramétere, és eredményként szolgál. A műveleti kód vezérli az eredmény kiszámításának módját, és az alábbi értékek egyike lehet:

  * **Diff**: a TimeSpan utolsó és első értéke közötti különbség.
  * **Átlag: a** TimeSpan összes értékének átlaga.
  * **Sum**: a TimeSpan összes értékének összege.
  * **Utolsó**: jelenleg nincs használatban.
  * **Darabszám**: a TimeSpan értékeinek száma.
  * **Max**: a TimeSpan maximális értéke.
  * **Min**: a TimeSpan minimális értéke.
  * **CONST**: az eredmény a ConstValue tulajdonság által megadott érték.
  * **SubMaxMin**: a maximális és a minimális érték közötti különbség.
  * **TimeSpan**: a TimeSpan.

* **Egységek** (Type string)

  Meghatározza az irányítópulton megjelenítendő érték egységét.

* **Látható** (logikai típus)

  Meghatározza, hogy az érték megjelenjen-e az irányítópulton.

* **ConstValue** (dupla típus)

  Ha a **műveleti kód** **CONST**, akkor ez a tulajdonság a csomópont értéke.

* **Minimum** (dupla típus)

  Ha az aktuális érték alá esik az érték, akkor a rendszer minimális riasztást generál.

* **Maximum** (dupla típus)

  Ha az aktuális érték meghaladja ezt az értéket, a rendszer maximális riasztást generál.

* **MinimumAlertActions** (típus `<alert_action>` )

  Meghatározza a műveletek készletét, amely a minimális riasztásra adott válaszként lehet.

* **MaximumAlertActions** (típus `<alert_action>` )

  Meghatározza a műveletek készletét, amely a maximális riasztásra adott válaszként lehet.

Az állomás szintjén a **szimulációs** objektumok is láthatók. Ezek az objektumok csak a csatlakoztatott gyári szimuláció konfigurálására szolgálnak, és nem használhatók valódi Topológia konfigurálására.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>A konfigurációs információ használata futásidőben

A konfigurációs fájlban használt összes tulajdonság különböző kategóriákba csoportosítható attól függően, hogy milyen módon használják őket. Ezek a kategóriák a következők:

### <a name="visual-appearance"></a>Vizuális megjelenés

A kategória tulajdonságai a csatlakoztatott gyári irányítópult vizualizációs megjelenését határozzák meg. Példák:

* Név
* Leírás
* Kép
* Hely
* Egység
* Látható

### <a name="internal-topology-tree-addressing"></a>Belső topológia faszerkezetének kezelése

A WebApp egy belső adatszótárt tart fenn, amely az összes topológiai csomópontról tartalmaz információkat. A tulajdonságok **GUID azonosítóját** és **OpcUri** a rendszer kulcsként használja a szótár eléréséhez, és egyedinek kell lennie.

### <a name="oeekpi-computation"></a>OEE/KPI-számítás

A csatlakoztatott gyári szimulációhoz tartozó OEE/KPI-számadatok paraméterei a következők:

* A számításban szerepeltetni kívánt OPC UA-csomópontok értékei.
* Az ábra kiszámítása a telemetria értékei alapján.

A csatlakoztatott gyár a által közzétett OEE-képleteket használja [http://www.oeefoundation.org](http://www.oeefoundation.org) .

A állomások OPC UA Node objektumai lehetővé teszik a címkézést az OEE/KPI-számításokban való használathoz. A **relevancia** tulajdonság azt jelzi, hogy melyik OEE/KPI-nek az OPC ua-csomópont értékét kell használnia. A **műveleti kód** tulajdonság határozza meg, hogy az érték hogyan szerepeljen a számításban.

### <a name="alert-handling"></a>Riasztások kezelése

A csatlakoztatott gyár egy egyszerű minimális/maximális küszöbérték-alapú riasztás-létrehozási mechanizmust támogat. A riasztásokra adott válaszban számos előre definiált művelet is konfigurálható. A következő tulajdonságok vezérlik ezt a mechanizmust:

* Maximum
* Minimális
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Korreláció a telemetria-adatbázisokhoz

Bizonyos műveletekhez, például az utolsó érték megjelenítéséhez vagy a Time Series Insight-lekérdezések létrehozásához a WebAppnak címzési sémára van szüksége a betöltött telemetria-adatokhoz. A csatlakoztatott gyárnak elküldett telemetria is belső adatstruktúrákban kell tárolni. A műveletek engedélyezésének két tulajdonsága az állomáson (OPC UA-kiszolgáló) és az OPC UA-csomópont szintjén történik:

* **OpcUri**

  A (globálisan egyedi) azonosítja azt az OPC UA-kiszolgálót, amelyből a telemetria származik. A betöltött üzenetekben ez a tulajdonság **ApplicationUri** lesz elküldve.

* **NodeId**

  A csomópont értékét azonosítja az OPC UA-kiszolgálón. A tulajdonság formátumának az OPC UA-specifikációban megadott formátumban kell lennie. A betöltött üzenetekben ez a tulajdonság **NodeId** lesz elküldve.

A telemetria-adatok csatlakoztatott gyárba való betöltésével kapcsolatos további információkért lásd: [Mi az OPC-közzétevő](overview-opc-publisher.md) .

## <a name="example-how-kpi1-is-calculated"></a>Példa: a KPI1 kiszámítása

A fájlban megadott konfiguráció `ContosoTopologyDescription.json` vezérli, hogyan számítja ki az OEE/KPI-számokat. Az alábbi példa azt szemlélteti, hogy a fájlban lévő tulajdonságok hogyan szabályozzák a KPI1 számítását.

A csatlakoztatott gyár KPI1 az elmúlt órában sikeresen előállított termékek számának mérésére szolgál. A csatlakoztatott gyári szimulációban minden állomás (OPC UA-kiszolgáló) egy OPC UA-csomópontot ( `NodeId: "ns=2;i=385"` ) biztosít, amely a KPI kiszámításához biztosít telemetria.

Az OPC UA-csomópont konfigurációja a következő kódrészlethez hasonlít:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Ez a konfiguráció lehetővé teszi a csomópont telemetria értékeinek lekérdezését Time Series Insights használatával. A Time Series Insights lekérdezés a következőket kérdezi le:

* Az értékek száma.
* A minimális érték.
* A maximális érték.
* Az összes érték átlaga.
* Az összes egyedi **OpcUri** (**ApplicationUri**), az adott TimeSpan lévő **NodeId** -párok összes értékének összege.

A **NumberOfManufactureredProducts** csomópont értékének egyik jellemzője, hogy az csak a növekedésre nő. A TimeSpan előállított termékek számának kiszámításához a csatlakoztatott gyár a **műveleti kód** **SubMaxMin** használja. A számítás a TimeSpan elején lévő minimális értéket és a TimeSpan végén található maximális értéket kérdezi le.

A konfigurációban lévő **műveleti kód** úgy konfigurálja a számítási logikát, hogy kiszámítsa a maximális és a minimális érték különbözetének eredményét. Ezeket az eredményeket a rendszer a legfelső szintű (globális) szintre összesíti, és az irányítópulton jelenik meg.

## <a name="next-steps"></a>Következő lépések

Egy javasolt következő lépés a [csatlakoztatott gyári megoldás testreszabásának](iot-accelerators-connected-factory-customize.md)megismerése.
