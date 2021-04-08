---
title: Az Azure Monitor az autoskálázás beállításainak ismertetése
description: Az autoskálázási beállítások részletes részletezése és működésük. Virtual Machines, Cloud Services, Web Apps
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: a914f6d71c013acea8dfde0f6578985bc009bb26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100617588"
---
# <a name="understand-autoscale-settings"></a>Ismerkedés az automatikus méretezési beállításokkal
Az automatikus skálázási beállítások segítségével gondoskodhat arról, hogy megfelelő mennyiségű erőforrást futtasson az alkalmazás változó terhelésének kezeléséhez. Az automatikus skálázási beállításokat beállíthatja úgy, hogy a terhelést vagy teljesítményt jelző mérőszámok alapján induljon el, vagy egy ütemezett dátumon és időpontban aktiválódik. Ez a cikk részletesen ismerteti az autoskálázási beállítások anatómiáját. A cikk a beállítások sémájával és tulajdonságaival kezdődik, majd végigvezeti a különböző konfigurálható profilok típusain. Végül a cikk bemutatja, hogyan értékeli ki az Azure-ban az autoscale funkció az adott időpontban végrehajtandó profilt.

## <a name="autoscale-setting-schema"></a>Az autoskálázási beállítás sémája
Az autoskálázási beállítás sémájának szemléltetéséhez a következő autoskálázási beállítás lesz használatban. Fontos megjegyezni, hogy ez az autoskálázási beállítás a következő:
- Egy profil. 
- Két metrikai szabály ebben a profilban: egy a kiskálázáshoz, egy pedig a méretezéshez.
  - A kibővített szabály akkor aktiválódik, ha a virtuálisgép-méretezési csoport átlagos CPU-mérőszáma nagyobb, mint 85% az elmúlt 10 percben.
  - A skálázási szabály akkor aktiválódik, ha a virtuálisgép-méretezési csoport átlaga kevesebb, mint 60% az elmúlt percben.

> [!NOTE]
> Egy beállítás több profillal is rendelkezhet. További információért lásd a [profilok](#autoscale-profiles) szakaszt. Egy profilhoz több kibővíthető szabály is tartozhat, és a méretezési szabályok definiálva vannak. A kiértékelésének módjával kapcsolatban tekintse meg a [próbaverzió](#autoscale-evaluation) szakaszt.

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| Section | Elem neve | Leírás |
| --- | --- | --- |
| Beállítás | ID (Azonosító) | Az autoskálázási beállítás erőforrás-azonosítója. Az autoskálázási beállítások egy Azure Resource Manager erőforrás. |
| Beállítás | name | Az autoskálázási beállítás neve. |
| Beállítás | location | Az autoskálázási beállítás helye Ez a hely nem lehet azonos a méretezni kívánt erőforrás helyétől. |
| properties | targetResourceUri | A méretezni kívánt erőforrás erőforrás-azonosítója. Erőforráshoz csak egy méretezési beállítás adható meg. |
| properties | profilok | Az autoskálázási beállítás egy vagy több profilból áll. Minden alkalommal, amikor az autoskálázási motor fut, egy profilt hajt végre. |
| profil | name | A profil neve. Bármely olyan nevet választhat, amely segít azonosítani a profilt. |
| profil | Kapacitás. maximum | A maximális kapacitás megengedett. Gondoskodik arról, hogy a profil végrehajtásakor az autoskálázás ne méretezi az erőforrást ezen szám felett. |
| profil | Kapacitás. minimum | A minimális kapacitás megengedett. Gondoskodik arról, hogy a profil végrehajtásakor az autoskálázás ne méretezi az erőforrást ennél a számnál. |
| profil | Kapacitás. default | Ha probléma merül fel az erőforrás-metrika (ebben az esetben a "vmss1" CPU) beolvasásakor, és a jelenlegi kapacitás az alapértelmezett érték alatt van, az autoskálázás az alapértelmezett értékre áll. Ez biztosítja az erőforrás rendelkezésre állását. Ha az aktuális kapacitás már magasabb az alapértelmezett kapacitásnál, az autoskálázás nem méretezhető a-ben. |
| profil | szabályok | Az automatikus skálázás automatikusan méretezi a maximális és a minimális kapacitást a profil szabályainak használatával. Egy profilban több szabály is megadható. Jellemzően két szabály létezik: egyet a kiskálázás időpontjának meghatározásához, a másikat pedig a méretezés időpontjának meghatározásához. |
| szabály | metricTrigger | Meghatározza a szabály metrikai feltételét. |
| metricTrigger | metricName | A metrika neve. |
| metricTrigger |  metricResourceUri | A metrikát kibocsátó erőforrás azonosítója. A legtöbb esetben ez ugyanaz, mint a méretezni kívánt erőforrás. Bizonyos esetekben eltérő lehet. A virtuálisgép-méretezési csoport például méretezhető a tárolási üzenetsor üzeneteinek száma alapján. |
| metricTrigger | timeGrain | A metrika mintavételezésének időtartama. Például a **TimeGrain = "PT1M"** érték azt jelenti, hogy a metrikákat 1 percenként kell összesíteni a statisztikai elemben megadott összesítési módszer használatával. |
| metricTrigger | statisztika | Az összesítési metódus a timeGrain időszakon belül. Például a **statisztika = "Average"** és a **TIMEGRAIN = "PT1M"** érték azt jelenti, hogy a metrikákat 1 percenként kell összesíteni, az átlag kiszámításával. Ez a tulajdonság határozza meg a metrika mintavételezésének módját. |
| metricTrigger | timeWindow | A metrikák visszakeresésének időtartamát. Például a **timeWindow = "PT10M"** érték azt jelenti, hogy minden alkalommal, amikor az autoscale fut, lekérdezi a metrikákat az elmúlt 10 percben. Az időablak lehetővé teszi, hogy a metrikák normalizálva legyenek, és elkerülhető legyen az átmeneti tüskékre való reagálás. |
| metricTrigger | timeAggregation | A mintául szolgáló mérőszámok összesítéséhez használt összesítési módszer. Például a **TimeAggregation = "Average"** értéknek összesíteni kell a mintavételezési metrikákat az átlag kiszámításával. Az előző esetben végezze el az 10 1 perces mintákat, és az átlagot. |
| szabály | scaleAction | A szabály metricTrigger elindításához végrehajtandó művelet. |
| scaleAction | irány | "Növelje" a méretezéshez, vagy "csökkentse" a méretezést a alkalmazásban.|
| scaleAction | érték | Mennyit növelheti vagy csökkentheti az erőforrás kapacitását. |
| scaleAction | cooldown | Az a várakozási idő, ameddig a méretezési művelet a méretezés előtt újra meg nem telik. Ha például a **cooldown = "PT10M"**, az autoskálázás nem próbálkozik újra a méretezéssel újabb 10 percre. A cooldown a példányok hozzáadását vagy eltávolítását követően a mérőszámok stabilizálását teszi lehetővé. |

## <a name="autoscale-profiles"></a>Autoskálázási profilok

Az autoskálázási profilok három típusa létezik:

- **Normál profil:** A leggyakoribb profil. Ha nem kell a hét napjának megfelelően méreteznie az erőforrást, vagy egy adott napon, használhat egy normál profilt. Ezt a profilt ezután olyan metrikai szabályokkal konfigurálhatja, amelyek megszabják, hogy mikor érdemes felskálázást végezni, és mikor kell méretezni Csak egy normál profilt kell definiálni.

    A cikkben korábban használt példa egy normál profilt mutat be. Vegye figyelembe, hogy az erőforrás statikus példányok száma számára is beállítható egy profil beállítása.

- **Rögzített dátum profil:** Ez a profil különleges esetekhez használható. Tegyük fel például, hogy egy fontos esemény következik be, amely a 2017 (PST) december 26-án jelenik meg. Azt szeretné, hogy az erőforrás minimális és maximális kapacitása ettől a naptól eltérő legyen, de továbbra is méretezhető ugyanazon mérőszámok esetében. Ebben az esetben adjon hozzá egy rögzített dátumú profilt a beállítások listájához. A profil úgy van konfigurálva, hogy csak az esemény napján fusson. Az autoscale bármely más napra a normál profilt használja.

    ```json
    "profiles": [
        {
            "name": " regularProfile",
            "capacity": {
                ...
            },
            "rules": [
                {
                ...
                },
                {
                ...
                }
            ]
        },
        {
            "name": "eventProfile",
            "capacity": {
            ...
            },
            "rules": [
                {
                ...
                }, 
                {
                ...
                }
            ],
            "fixedDate": {
                "timeZone": "Pacific Standard Time",
                "start": "2017-12-26T00:00:00",
                "end": "2017-12-26T23:59:00"
            }
        }
    ]
    ```
    
- **Ismétlődési profil:** Ez a profil lehetővé teszi annak biztosítását, hogy ezt a profilt mindig a hét egy adott napján használják. Az ismétlődési profilok csak kezdési idővel rendelkeznek. Addig futnak, amíg a következő ismétlődési profil vagy a rögzített dátum profil értéke indítás értékre van állítva. Egy olyan autoskálázási beállítás, amely csak egy ismétlődési profillal rendelkezik, futtatja ezt a profilt, még akkor is, ha egy adott beállításban van definiálva egy normál profil. A következő két példa szemlélteti a profil használatának módját:

    **1. példa: hétköznapok és hétvégék**
    
    Tegyük fel, hogy a hétvégén a maximális kapacitás 4. Mivel a hétköznapok nagyobb terhelést várnak, a maximális kapacitás 10. Ebben az esetben a beállítás két ismétlődési profilt fog tartalmazni, amelyek közül az egyik a hétvégén, a másik pedig hétköznapokon fut.
    A beállítás a következőképpen néz ki:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    Az előző beállítás azt mutatja, hogy minden ismétlődési profilnak van ütemezése. Ez az ütemterv határozza meg, hogy a profil mikor induljon el. A profil leáll, ha ideje egy másik profil futtatására.

    Az előző beállításban például a "weekdayProfile" beállítás a hétfő 12:00 ÓRAKOR való indulásra van beállítva. Ez azt jelenti, hogy ez a profil hétfőn, a 12:00-kor fut. 12:00 ÓRAKOR folytatódik, amikor a "weekendProfile" futtatása ütemezve van a futás megkezdésére.

    **2. példa: munkaidő**
    
    Tegyük fel, hogy a munkaidőn belül egy metrikai küszöbértéket szeretne használni (9:00 – 5:00 PM), és egy másikat minden más alkalommal. A beállítás a következőképpen fog kinézni:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    Az előző beállítás azt mutatja, hogy a "businessHoursProfile" a hétfő 9:00 órakor kezdődik, és továbbra is 5:00 PM. Ekkor kezdődik a "nonBusinessHoursProfile" futtatása. A "nonBusinessHoursProfile" a 9:00-es keddig fut, majd a "businessHoursProfile" függvény újra átveszi. Ez a péntek 5:00 ÓRAKOR ismétlődik. Ezen a ponton a "nonBusinessHoursProfile" egészen hétfő 9:00 ÓRAKOR fut.
    
> [!Note]
> Az Azure Portal a felhasználói felület az ismétlődési profilok befejezési idejét kényszeríti, és az ismétlődési profilok között megkezdi az autoskálázási beállítás alapértelmezett profiljának futtatását.
    
## <a name="autoscale-evaluation"></a>Az autoscale kiértékelése
Mivel az autoskálázási beállítások több profillal rendelkezhetnek, és az egyes profilok több metrikai szabállyal is rendelkezhetnek, fontos megérteni, hogyan történik az autoskálázási beállítás kiértékelése. Minden alkalommal, amikor az autoskálázási feladatot futtatja, az a megfelelő profil kiválasztásával kezdődik. Ezután az autoscale kiértékeli a profilban szereplő minimális és maximális értékeket és a metrikák szabályait, és eldönti, hogy szükséges-e méretezési művelet.

### <a name="which-profile-will-autoscale-pick"></a>Melyik profil automatikusan méretezi a kivételezést?

Az autoscale a következő műveletsort használja a profil kiválasztásához:
1. Először minden olyan rögzített dátumú profilt keres, amely már konfigurálva van. Ha van, az autoscale futtatja azt. Ha több rögzített dátumú profilt kellene futtatni, az autoscale kiválasztja az elsőt.
2. Ha nincsenek rögzített dátumú profilok, az autoscale megtekinti az ismétlődési profilokat. Ha a rendszer ismétlődési profilt talál, azt futtatja.
3. Ha nincsenek rögzített dátum-vagy ismétlődési profilok, az autoscale futtatja a normál profilt.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Hogyan értékeli az autoscale több szabályt?

Miután az autoscale meghatározza, hogy melyik profilt szeretné futtatni, kiértékeli a profil összes kibővíthető szabályát (ezek a szabályok az **irány = "növekedés"**).

Ha egy vagy több kibővíthető szabály van kiváltva, az autoscale kiszámítja az egyes szabályok **scaleAction** által meghatározott új kapacitást. Ezt követően a szolgáltatás rendelkezésre állásának biztosítása érdekében a kapacitások maximális mennyiségét kiméretezi.

Tegyük fel például, hogy van egy virtuálisgép-méretezési csoport, amelynek a jelenlegi kapacitása 10. Két kibővített szabály létezik: az egyik, amely 10 százalékkal növeli a kapacitást, és eggyel növeli a kapacitást 3 számmal. Az első szabály a 11 új kapacitását eredményezi, a második szabály pedig 13 kapacitást eredményezne. A szolgáltatás rendelkezésre állásának biztosítása érdekében az autoscale kiválasztja a maximális kapacitást eredményező műveletet, így a második szabály van kiválasztva.

Ha nincs kibővíthető kibővített szabály, az autoscale kiértékeli az összes skálázási szabályt (szabályok: **Direction = "csökkenés"**). Az autoscale művelet csak akkor vesz igénybe méretezési műveletet, ha az összes méretezési szabály aktiválva van.

Az autoscale kiszámítja az egyes szabályok **scaleAction** által meghatározott új kapacitást. Ezután kiválasztja a méretezési műveletet, amely a szolgáltatás rendelkezésre állásának biztosítása érdekében a maximális kapacitást eredményezi.

Tegyük fel például, hogy van egy virtuálisgép-méretezési csoport, amelynek a jelenlegi kapacitása 10. Két méretezési szabály létezik: az egyik, amely 50 százalékkal csökkenti a kapacitást, és egy, a kapacitást 3 számmal csökkenti. Az első szabály az 5 új kapacitását eredményezi, a második szabály pedig 7 kapacitást eredményezne. A szolgáltatás rendelkezésre állásának biztosítása érdekében az autoscale kiválasztja a maximális kapacitást eredményező műveletet, így a második szabály van kiválasztva.

## <a name="next-steps"></a>Következő lépések
További információ az autoskálázásról:

* [Az automatikus méretezés áttekintése](./autoscale-overview.md)
* [Általános mérőszámok Azure Monitor](./autoscale-common-metrics.md)
* [Ajánlott eljárások az Azure Monitor automatikus skálázásához](./autoscale-best-practices.md)
* [E-mailek és webhookok riasztási értesítéseinek küldése az autoscale műveletekkel](./autoscale-webhook-email.md)
* [REST API méretezése](/rest/api/monitor/autoscalesettings)

