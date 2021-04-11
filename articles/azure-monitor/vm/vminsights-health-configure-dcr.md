---
title: Az adatgyűjtési szabályok (előzetes verzió) használatával konfigurálhatja a virtuális gépek elemzését a vendég állapotában
description: Ismerteti, hogyan lehet módosítani az alapértelmezett monitorozást a virtuális gépeken a vendégek számára a Resource Manager-sablonok használatával.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 889a04d68de45a6270ae0c38615d841a526ad86a
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490692"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-using-data-collection-rules-preview"></a>Az adatgyűjtési szabályok (előzetes verzió) használatával konfigurálhatja a virtuális gépek elemzését a vendég állapotában
A [virtuális gép elemzése lehetővé teszi](vminsights-health-overview.md) a virtuális gépek állapotának megtekintését olyan teljesítmény-mérési készlet alapján, amely rendszeres időközönként mintavételt végez. Ez a cikk azt ismerteti, hogyan módosíthatja az alapértelmezett figyelést több virtuális gépen az adatgyűjtési szabályok használatával.


## <a name="monitors"></a>Monitorozások
A virtuális gép állapotát az egyes figyelők [állapotának összesítése](vminsights-health-overview.md#health-rollup-policy) határozza meg. A virtuális gépeken a következő táblázatban látható módon két típusú figyelőt láthat:

| Monitor | Description |
|:---|:---|
| Egységmonitor | Egy erőforrás vagy alkalmazás valamilyen jellemzőjét méri. Ez lehet egy teljesítményszámláló ellenőrzése az erőforrás teljesítményének vagy rendelkezésre állásának meghatározása céljából. |
| Összesített monitor | Több monitort csoportosít egyetlen összesített állapot biztosításához. Egy összesített monitor egy vagy több egységmonitort és más összesített monitorokat tartalmazhat. |

A virtuálisgép-bepillantások vendég állapota által használt figyelők készlete nem módosítható közvetlenül. [Felülbírálásokat](#overrides) is létrehozhat, ha módosítja az alapértelmezett konfiguráció viselkedését. A felülbírálások definiálva vannak az adatgyűjtési szabályokban. Több felülbírálást tartalmazó adatgyűjtési szabályt is létrehozhat a szükséges figyelési konfiguráció eléréséhez.

## <a name="monitor-properties"></a>Figyelő tulajdonságai
Az alábbi táblázat az egyes figyelőket konfiguráló tulajdonságokat ismerteti.

| Tulajdonság | Monitorozások | Description |
|:---|:---|:---|
| Engedélyezve | Összesítés<br>Unit (Egység) | Ha az értéke igaz, a rendszer kiszámítja az állapot figyelőt, és a virtuális gép állapotát is hozzájárul. Riasztási riasztást aktiválhat. |
| Riasztások kezelése | Összesítés<br>Unit (Egység) | Ha az értéke TRUE (igaz), a figyelő riasztást küld, ha nem Kifogástalan állapotra vált. Ha hamis, a figyelő állapota továbbra is hozzájárul a virtuális gép állapotához, amely riasztást vált ki. |
| Figyelmeztetés | Unit (Egység) | A figyelmeztetési állapotra vonatkozó feltételek Ha nincs, akkor a figyelő soha nem fog figyelmeztetési állapotot megadni. |
| Kritikus | Unit (Egység) | A kritikus állapotra vonatkozó feltételek. Ha nincs, akkor a figyelő soha nem ad meg kritikus állapotot. |
| Kiértékelés gyakorisága | Unit (Egység) | Az állapot kiértékelésének gyakorisága. |
| Lookback | Unit (Egység) | A lookback ablak mérete másodpercben. Részletes leírást a [monitorConfiguration elemnél](#monitorconfiguration-element) talál. |
| Kiértékelés típusa | Unit (Egység) | Meghatározza, hogy melyik értéket kell használni a minta készletből. Részletes leírást a [monitorConfiguration elemnél](#monitorconfiguration-element) talál. |
| Minimális minta | Unit (Egység) | Az érték kiszámításához használandó értékek minimális száma. |
| Maximális minta | Unit (Egység) | Az érték kiszámításához használandó értékek maximális száma. |


## <a name="default-configuration"></a>Alapértelmezett konfiguráció
A következő táblázat felsorolja az egyes figyelők alapértelmezett konfigurációját. Ez az alapértelmezett konfiguráció nem módosítható közvetlenül, de olyan [felülbírálásokat](#overrides) is megadhat, amelyek módosítják az egyes virtuális gépek figyelési konfigurációját.


| Monitor | Engedélyezve | Riasztások kezelése | Figyelmeztetés | Kritikus | Kiértékelés gyakorisága | Lookback | Kiértékelés típusa | Minimális minta | Minták maximális száma |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| Processzorhasználat  | Igaz | Hamis | Nincsenek | \> 90%    | 60 másodperc | 240 mp | Min | 2 | 3 |
| Igénybe vehető memória | Igaz | Hamis | Nincsenek | \< 100 MB | 60 másodperc | 240 mp | Max | 2 | 3 |
| Fájlrendszer      | Igaz | Hamis | Nincsenek | \< 100 MB | 60 másodperc | 120 mp | Max | 1 | 1 |


## <a name="overrides"></a>Felülbírálások
A *felülbírálás* egy adott figyelő egy vagy több tulajdonságát módosítja. Egy felülbírálás például letilthatja az alapértelmezés szerint engedélyezett figyelőt, megadhatja a figyelő figyelmeztetési feltételeit, vagy módosíthatja a figyelő kritikus küszöbértékét. 

A felülbírálások egy [adatgyűjtési szabályban (DCR)](../agents/data-collection-rule-overview.md)vannak definiálva. Több DCR is létrehozhat különböző felülbírálásokkal, és alkalmazhatja őket több virtuális gépre. A DCR-t egy virtuális gépre alkalmazza úgy, hogy létrehoz egy társítást az [adatgyűjtési szolgáltatás konfigurálása a Azure monitor ügynökhöz (előzetes verzió)](../agents/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations)című témakörben leírtak szerint.


## <a name="multiple-overrides"></a>Több felülbírálás

Egyetlen figyelő több felülbírálással is rendelkezhet. Ha a felülbírálások eltérő tulajdonságokat határoznak meg, akkor az eredményül kapott konfiguráció az összes felülbírálás kombinációja.

A `memory|available` figyelő például nem ad meg figyelmeztetési küszöbértéket, vagy alapértelmezés szerint engedélyezi a riasztást. Vegye figyelembe az alábbi, a figyelőre alkalmazott felülbírálásokat:

- Az 1. felülbírálás `alertConfiguration.isEnabled` a tulajdonságérték értékét adja meg `true`
- A 2. felülbírálás a `monitorConfiguration.warningCondition` és a küszöbértékét határozza meg `< 250` .

Az eredményül kapott konfiguráció olyan figyelő, amely figyelmeztetési állapotba kerül, ha a rendelkezésre álló memória kevesebb, mint 100%-os, és a súlyossági szint 2 riasztást hoz létre, és a kritikus állapotba kerül, ha a rendelkezésre álló memória mérete kisebb, mint 100 MB/s

Ha két felülbírálás ugyanazt a tulajdonságot határozza meg ugyanazon a figyelőn, akkor az egyik érték elsőbbséget élvez. A felülbírálások a [hatókörük](#scopes-element)alapján lesznek alkalmazva, a legáltalánosabbtól egészen a legpontosabbig. Ez azt jelenti, hogy a legpontosabb felülbírálások a legnagyobb valószínűséggel lesznek alkalmazva. A megadott sorrend a következő:

1. Globális 
2. Előfizetés
3. Erőforráscsoport
4. Virtuális gép. 

Ha ugyanazon a hatókör-szinten több felülbírálás is definiálja ugyanazt a tulajdonságot ugyanazon a figyelőn, akkor azokat a rendszer a DCR-ben megjelenő sorrendben alkalmazza. Ha a felülbírálások eltérő DCR találhatók, akkor a rendszer a DCR erőforrás-azonosítók betűrendben jeleníti meg őket.


## <a name="data-collection-rule-configuration"></a>Adatgyűjtési szabály konfigurációja
A felülbírálásokat definiáló adatgyűjtési szabály JSON-elemeit az alábbi szakasz ismerteti. A [minta adatgyűjtési szabályban](#sample-data-collection-rule)teljes példa szerepel.

## <a name="extensions-structure"></a>bővítmények szerkezete
A vendég állapota bővítményként van implementálva a Azure Monitor ügynök számára, ezért a felülbírálások az `extensions` adatgyűjtési szabály elemében vannak meghatározva. 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| Elem | Kötelező | Leírás |
|:---|:---|:---|
| `name` | Igen | A bővítmény felhasználó által definiált karakterlánca. |
| `streams` | Yes | Azon adatfolyamok listája, amelyekhez vendég állapotú adatmennyiséget küld a rendszer. Ennek tartalmaznia kell a **Microsoft-HealthStateChange**.  |
| `extensionName` | Yes | A bővítmény neve. Ennek **HealthExtension** kell lennie. |
| `extensionSettings` | Yes | Az `healthRuleOverride` alapértelmezett konfigurációra alkalmazandó elemek tömbje. |


## <a name="extensionsettings-element"></a>extensionSettings elem
A bővítmény beállításait tartalmazza.

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| Elem | Kötelező | Leírás |
|:---|:---|:---|
| `schemaVersion` | Igen | A Microsoft által meghatározott karakterlánc, amely az elem várt sémáját jelöli. Jelenleg 1,0-re kell állítani |
| `contentVersion` | No | A felhasználó által az állapot-konfiguráció különböző verzióinak nyomon követésére megadott karakterlánc, ha szükséges. |
| `healthRuleOverrides` | Yes | Az `healthRuleOverride` alapértelmezett konfigurációra alkalmazandó elemek tömbje. |

## <a name="healthrulesoverrides-element"></a>healthRulesOverrides elem
Egy vagy több olyan `healthRuleOverride` elemet tartalmaz, amelyek mindegyike felülbírálást határoz meg.

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| Elem | Kötelező | Leírás |
|:---|:---|:---|
| `scopes` | Igen | Egy vagy több hatókör listája, amelyek meghatározzák azokat a virtuális gépeket, amelyekre ez a felülbírálás vonatkozik. Annak ellenére, hogy a DCR egy virtuális géphez van társítva, a virtuális gépnek az alkalmazandó felülbírálás hatókörén belül kell lennie. |
| `monitors` | Yes | Egy vagy több olyan karakterlánc listája, amely meghatározza, hogy mely figyelők kapják meg ezt a felülbírálást.  |
| `monitorConfiguration` | No | A figyelő konfigurációja, beleértve az állapotokat és azok kiszámításának módját. |
| `alertConfiguration` | No | Riasztás konfigurálása a figyelőhöz. |
| `isEnabled` | No | Meghatározza, hogy a figyelő engedélyezve van-e vagy sem. A figyelő letiltotta a speciális *letiltott* állapotra és állapotokra való váltást, kivéve, ha az újból engedélyezve van. Ha nincs megadva, a figyelő örökli az állapotát a hierarchiában lévő szülő figyelőtől. |


## <a name="scopes-element"></a>hatókörök elem
Minden felülbírálás egy vagy több hatókörrel rendelkezik, amely meghatározza, hogy mely virtuális gépekre legyen alkalmazva a felülbírálás. A hatókör lehet előfizetés, erőforráscsoport vagy egyetlen virtuális gép. Még ha a felülbírálás egy adott virtuális géphez társított DCR-ben van, akkor a rendszer csak akkor alkalmazza az adott virtuális gépre, ha a virtuális gép a felülbírálás egyik hatókörén belül van. Ez lehetővé teszi, hogy a kisebb számú DCR széles körben rendeljen hozzá virtuális gépekhez, de részletes szabályozást biztosít az egyes felülbírálások hozzárendeléséhez a DCR-n belül. Érdemes lehet kis készletet létrehozni a DCR, és társítani azokat a virtuális gépek készletéhez házirend használatával, miközben az állapotfigyelő elemek felülbírálják a virtuális gépek különböző részhalmazait a hatókörök elemet használva.

A következő táblázat példákat mutat be a különböző hatókörökre.

| Hatókör | Példa |
|:---|:---|
| Egyetlen virtuális gép | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| Egy erőforráscsoport összes virtuális gépe | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| Az előfizetésben lévő összes virtuális gép | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| Minden virtuális gép, amelyhez az adatgyűjtési szabály társítva van | `*` |


## <a name="monitors-element"></a>elemek figyelése
Egy vagy több olyan karakterlánc listája, amely meghatározza, hogy az állapot-hierarchiában mely figyelők kapják meg ezt a felülbírálást. Minden elem lehet a figyelő neve vagy neve, amely megfelel egy vagy több olyan figyelőnek, amely a felülbírálást fogja kapni. 

```json
"monitors": [
    "<monitor name>"
 ],
```



A következő táblázat az aktuálisan elérhető figyelők neveit sorolja fel.

| Típus neve | Név | Leírás |
|:----------|:-----|:------------|
| legfelső szintű | legfelső szintű | A virtuális gép állapotát jelképező legfelső szintű figyelő. |
| CPU-kihasználtság | CPU-kihasználtság | CPU-kihasználtsági figyelő. |
| logikai lemezek | logikai lemezek | A Windows rendszerű virtuális gépen lévő összes figyelt lemez állapotának összesítési figyelője. |
| logikai lemezek\|\* | logikai – \| C lemezek:<br>logikai lemezek \| D: | Egy adott lemez Windows rendszerű virtuális gépen való nyomon követési állapotát összesítő figyelő. |
| logikai lemezek – \| \* \| szabad terület | logikai lemezek \| C: \| szabad terület<br>logikai lemezek – \| D: \| szabad terület | Lemezes szabad terület figyelője a Windows rendszerű virtuális gépen. |
| fájlrendszerek | fájlrendszerek | A Linux rendszerű virtuális gépen lévő összes fájlrendszer állapotának összesített figyelője. |
| fájlrendszerek\|\* | fájlrendszerek\|/<br>fájlrendszerek \| /mnt | Egy Linux rendszerű virtuális gép fájlrendszerének nyomon követési állapotának összesítése. |
| fájlrendszerek \| \* \| – szabad terület | fájlrendszerek \| / \| – szabad terület<br>fájlrendszerek \| /mnt \| szabad terület | Lemezes szabad terület figyelője a Linux rendszerű virtuális gépek fájlrendszerén. |
| memória | memória | A virtuális gép memóriájának állapotára vonatkozó összesített figyelő. |
| \|rendelkezésre álló memória | \|rendelkezésre álló memória | A virtuális gépen a rendelkezésre álló memória nyomon követése. |


## <a name="alertconfiguration-element"></a>alertConfiguration elem
Megadja, hogy létre kell-e hozni egy riasztást a figyelőből.

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| Elem | Kötelező | Leírás | 
|:---|:---|:---|
| `isEnabled` | Nem | Ha az értéke TRUE (igaz), a figyelő riasztást hoz létre, amikor kritikus vagy figyelmeztetési állapotra vált, és ha Kifogástalan állapotba tér vissza, a riasztás feloldása történik. Ha hamis vagy nincs megadva, a rendszer nem generál riasztást.  |


## <a name="monitorconfiguration-element"></a>monitorConfiguration elem
Csak az egység figyelőkre vonatkozik. Meghatározza a figyelő konfigurációját, beleértve az állapotokat és azok kiszámításának módját.

A paraméterek határozzák meg az algoritmust a küszöbértékek összehasonlítására szolgáló metrikai érték kiszámításához. Ahelyett, hogy az alapul szolgáló metrika egyik adatmintáján alapul, a figyelő több metrikus mintát értékel ki az időszakból és az időponttól kezdve `lookbackSec` . A rendszer figyelembe veszi az adott időkereten belül fogadott összes mintát, és ha a minták száma nagyobb, mint a `maxSamples` fentinél régebbi minták `maxSamples` figyelmen kívül lesznek hagyva. 

Ha kevesebb minta van a lookback időszakban, mint a `minSamples` , a figyelő az *ismeretlen* állapotba vált, ami azt jelzi, hogy nincs elegendő adat ahhoz, hogy tájékozott döntést hozzon az alapul szolgáló mérőszámok állapotáról. Ha ennél nagyobb számú minta `minSamples` van elérhető, akkor a evaluationType paraméter által meghatározott összesítési függvényt a rendszer a készleten keresztül futtatja egyetlen érték kiszámításához.


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| Elem | Kötelező | Leírás | 
|:---|:---|:---|
| `evaluationFrequencySecs` | Nem | Meghatározza az állapot értékelésének gyakoriságát. A rendszer minden figyelőt kiértékel az ügynök indításakor és a paraméter által később meghatározott rendszeres időközönként. |
| `lookbackSecs`   | No | A lookback ablak mérete másodpercben. |
| `evaluationType` | No | `min` – minimális értéket kell kiállítani a teljes mintából<br>`max` – a teljes mintából maximális értéket kell megadni<br>`avg` – a minták értékének átlagát adja meg<br>`all` – Hasonlítsa össze a készlet minden egyes értékét a küszöbértékek értékével. A figyelő kapcsolók állapota csak akkor, ha a készletben szereplő összes minta megfelel a küszöbértéknek. |
| `minSamples`     | No | Az érték kiszámításához használandó értékek minimális száma. |
| `maxSamples`     | No | Az érték kiszámításához használandó értékek maximális száma. |
| `warningCondition`  | No | A figyelmeztetési feltétel küszöbértéke és összehasonlító logikája. |
| `criticalCondition` | No | A kritikus feltétel küszöbértéke és összehasonlító logikája. |


## <a name="warningcondition-element"></a>warningCondition elem
Meghatározza a figyelmeztetési feltétel küszöbértékét és összehasonlító logikáját. Ha ez az elem nem szerepel, a figyelő soha nem vált a figyelmeztetési állapotra.

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Tulajdonság | Kötelező | Leírás | 
|:---|:---|:---|
| `isEnabled` | Nem | Megadja, hogy engedélyezve van-e a feltétel. Ha **hamis** értékre van állítva, akkor a feltétel le van tiltva, bár a küszöbérték és az operátor tulajdonságai is megadhatók. |
| `threshold` | No | A kiértékelt érték összehasonlítására szolgáló küszöbérték meghatározása. |
| `operator`  | No | Meghatározza az összehasonlító operátort a küszöbérték kifejezésben való használathoz. Lehetséges értékek: >, <, >=, <=, = =. |


## <a name="criticalcondition-element"></a>criticalCondition elem
Meghatározza a kritikus feltétel küszöbértékét és összehasonlító logikáját. Ha ez az elem nem szerepel, a figyelő soha nem fog a kritikus állapotra váltani.

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Tulajdonság | Kötelező | Leírás | 
|:---|:---|:---|
| `isEnabled` | Nem | Megadja, hogy engedélyezve van-e a feltétel. Ha **hamis** értékre van állítva, akkor a feltétel le van tiltva, bár a küszöbérték és az operátor tulajdonságai is megadhatók. |
| `threshold` | No | A kiértékelt érték összehasonlítására szolgáló küszöbérték meghatározása. |
| `operator`  | No | Meghatározza az összehasonlító operátort a küszöbérték kifejezésben való használathoz. Lehetséges értékek: >, <, >=, <=, = =. |

## <a name="sample-data-collection-rule"></a>Minta adatgyűjtési szabály
A vendég figyelését lehetővé tevő minta adatgyűjtési szabály: [virtuális gép engedélyezése Resource Manager-sablon használatával](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template).


## <a name="next-steps"></a>Következő lépések

- További információ az [adatgyűjtési szabályokról](../agents/data-collection-rule-overview.md).
