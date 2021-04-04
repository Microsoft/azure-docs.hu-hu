---
title: A metrikák Advisor-példányának konfigurálása a webes portál használatával
titleSuffix: Azure Cognitive Services
description: A metrikai tanácsadó példányának konfigurálása és az anomáliák észlelési eredményeinek finomhangolása.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 7923dad3d47122c0ceb04d1240736e2b66a0dd64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "92048253"
---
# <a name="how-to-configure-metrics-and-fine-tune-detecting-configuration"></a>Útmutató: mérőszámok konfigurálása és Finomhangolás észlelése – konfiguráció

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a metrikák Advisor-példányát a webes portál használatával. Egy adott adatcsatorna metrikáinak megkereséséhez nyissa meg az **adatcsatornák** lapot, és válassza ki az egyik hírcsatornát. Ekkor megjelenik a hozzá társított metrikák listája.

:::image type="content" source="../media/metrics/select-metric.png" alt-text="Metrika kiválasztása" lightbox="../media/metrics/select-metric.png":::

Kattintson az egyik metrika nevére a részletek megjelenítéséhez. Ebben a részletes nézetben a képernyő jobb felső sarkában található legördülő listával válthat egy másik metrikára ugyanabban az adatcsatornában.

Amikor először tekinti meg a mérőszámok részleteit, betöltheti az idősorozatot a metrikák tanácsadójának kiválasztásával, vagy megadhatja az egyes dimenziók számára felvenni kívánt értékeket. 

Kiválaszthatja az időtartományokat is, és módosíthatja az oldal elrendezését.

> [!NOTE]
> - A kezdési időpont a befogadó.
> - A befejezési időpont kizárólagos. 

Az **incidensek** lapra kattintva megtekintheti a rendellenességeket, és megtalálhatja az [incidens hubhoz](diagnose-incident.md)mutató hivatkozást.

## <a name="tune-the-detecting-configuration"></a>Az észlelési konfiguráció hangolása

Egy metrika egy vagy több észlelési konfigurációt is alkalmazhat. Minden metrika esetében van egy alapértelmezett konfiguráció, amelyet a figyelési igényeknek megfelelően szerkeszthet vagy hozzáadhat.

### <a name="tune-the-configuration-for-all-series-in-current-metric"></a>Az aktuális metrika összes adatsorozatának konfigurációjának hangolása

A rendszer ezt a konfigurációt alkalmazza a metrika összes adatsorára, kivéve azokat, amelyek külön konfigurációval rendelkeznek. Alapértelmezés szerint a rendszer a metrikai szintű konfigurációt alkalmazza az adatok bevezetéséhez, és a bal oldali panelen jelenik meg. A felhasználók közvetlenül módosíthatják a metrikus szintű konfigurációt a metrika lapon. 

További paraméterek, például **irány** és **érvényes anomália** használható a konfiguráció további finomhangolásához. A különböző észlelési módszerek is kombinálhatók. 

:::image type="content" source="../media/configuration-combination.png" alt-text="Konfiguráció kombinációja" lightbox="../media/configuration-combination.png":::

### <a name="tune-the-configuration-for-a-specific-series-or-group"></a>Egy adott adatsorozat vagy csoport konfigurációjának hangolása

A csoport szintjének konfigurálásához kattintson a metrikus szintű konfigurációs beállítások alatt látható **Speciális konfiguráció** elemre. Az **+** ebben az ablakban található ikonra kattintva hozzáadhat egy adott adatsorozathoz vagy adatsorozat-csoporthoz tartozó konfigurációt. A paraméterek hasonlóak a metrikai szintű konfigurációs paraméterekhez, de előfordulhat, hogy az adatsorozatok egy csoportjának azonosításához legalább egy dimenzió értéket kell megadnia egy csoport szintű konfigurációhoz. És adja meg az adatsorozat-szintű konfiguráció összes dimenziójának értékét egy adott adatsor azonosításához. 

Ezt a konfigurációt a rendszer az adatsorozatok vagy az adott adatsorozatok csoportjára alkalmazza a metrikai szintű konfiguráció helyett. A csoport feltételeinek beállítása után mentse azt.

:::image type="content" source="../media/advanced-configuration.png" alt-text="Speciális konfiguráció" lightbox="../media/advanced-configuration.png":::

### <a name="anomaly-detection-methods"></a>Anomáliák észlelési módszerei

A metrikai tanácsadó több anomália-észlelési módszert is kínál. A gombra kattintva használhatja az egyiket, vagy kombinálhatja őket logikai operátorok használatával **+** . 

**Intelligens észlelés** 

Az intelligens észlelést a gépi tanulás látja el, amely a múltbeli adatok mintáit tanulja meg, és a későbbi észleléshez használja őket. Ha ezt a módszert használja, az **érzékenység** az észlelési eredmények hangolásának legfontosabb paramétere. Húzással kisebb vagy nagyobb értékre húzhatja a vizualizációt az oldal jobb oldalán. Válasszon egyet, amely megfelel az adatainak, és mentse azt. 


Az intelligens észlelési mód esetében az érzékenység és a határ verziószáma a anomáliák észlelési eredményének finomhangolására szolgál.

Az érzékenység hatással lehet az egyes pontok várható érték-tartományának szélességére. Ha növeli a határértéket, a várt értéktartomány szigorúbb lesz, és a rendszer több rendellenességet fog jelenteni:

:::image type="content" source="../media/metrics/smart-detection-high-sensitivity.png" alt-text="Intelligens észlelés magas érzékenységgel":::

Az érzékenység kikapcsolásakor a várt értéktartomány szélesebb lesz, és a rendszer kevesebb rendellenességet fog jelenteni:

:::image type="content" source="../media/metrics/smart-detection-low-sensitivity.png" alt-text="Intelligens észlelés alacsony érzékenységgel":::

**Küszöbérték módosítása** 

A változás küszöbértékét általában akkor kell használni, ha a metrikus adatok általában egy bizonyos tartomány körül maradnak. A küszöbérték a **százalékos változásnak** megfelelően van beállítva. A **változási küszöb** mód képes észlelni a helyzetekben mutatkozó rendellenességeket:

* Az adatai általában stabilak és zökkenőmentesek. A rendszer értesítést szeretne kapni, ha ingadozások vannak.
* Az adatai általában meglehetősen instabilak, és sokat ingadoznak. Értesítést szeretne kapni, ha túl stabil vagy lapos lesz.

Használja ezt a módot a következő lépésekkel:

1. Ha beállítja a mérőszámok és az idősorozatok anomália-észlelési konfigurációját, válassza a **küszöbérték módosítása** lehetőséget a rendellenesség észlelése módszernél.
    
    :::image type="content" source="../media/metrics/change-threshold.png" alt-text="küszöbérték módosítása":::

2. Válassza ki a **tartományt** vagy a **Range** paramétert a forgatókönyv alapján.

    Ha szeretné felderíteni az ingadozásokat, válassza ki **a tartományt**. Például az alábbi beállításokkal minden olyan adatpontot, amely a 10%-ot az előzőhöz képest változtatja, a rendszer kiugró adatként észleli.
    :::image type="content" source="../media/metrics/out-of-the-range.png" alt-text="tartományon kívüli paraméter":::

    Ha az adataiban lévő vonalakat szeretné felderíteni, válassza **a tartomány** lehetőséget. Például az alábbi beállításokkal minden olyan adatpontot, amely az előzőhöz képest 0,01%-ot megváltoztat, a rendszer kiugró adatként észleli. Mivel a küszöbérték olyan kicsi (0,01%), amely az adatokat kiugróként észleli az adatsorokat.

    :::image type="content" source="../media/metrics/in-the-range.png" alt-text="A Range paraméterben":::

3. Állítsa be a változás százalékos arányát, amely anomáliának számít, és a korábban rögzített adatpontokat az összehasonlításhoz használja a rendszer. Ez az összehasonlítás mindig az aktuális adatpont és az előtte lévő egyetlen adatpont között van.
    
    Az **irány** csak akkor érvényes, ha a **tartományon kívüli** módot használja:
    
    * Az **észlelés beállításával** csak a rendellenességek észlelhetők (az aktuális adatpontok) – (összehasonlító adatpont) > **+** küszöbérték százalékos arányát.
    * Az **észlelés beállításával** a csak a rendellenességek észlelése (aktuális adatpont) – (az adatpont összehasonlítása) < a **-** küszöbérték százalékos arányát.
 
**Rögzített küszöbérték**

 A rögzített küszöbérték egy alapszintű módszer a anomáliák észleléséhez. Megadhat egy felső és/vagy alsó korlátot a várt értéktartomány meghatározásához. A határ kiesése során a rendszer anomália-ként azonosítja az összes pontot. 


## <a name="preset-events"></a>Előre beállított események

Időnként a várt események és előfordulások (például ünnepek) rendellenes adatbevitelt is létrehozhatnak. Az előre beállított események használatával jelzőket adhat hozzá az anomália-észlelési kimenethez a megadott időpontokban. Ezt a szolgáltatást az adatcsatorna előkészítését követően kell konfigurálni. Minden metrika csak egy előre beállított esemény-konfigurációval rendelkezhet.

> [!Note]
> A beállított események konfigurációjában figyelembe kell venni az időpontokat a rendellenességek észlelése során, és megváltoztathatják az eredményeket. A rendszer a konfiguráció mentése után betöltött adatpontokra alkalmazza. 

Kattintson az **előre definiált esemény beállítása** gombra a metrikák legördülő lista mellett az egyes mérőszámok részletei lapon.
 
:::image type="content" source="../media/metrics/preset-event-button.png" alt-text="előre beállított esemény gomb":::

A megjelenő ablakban konfigurálja a beállításokat a használatnak megfelelően. A konfiguráció használatához győződjön meg arról, hogy az **ünnepi esemény engedélyezése** beállítás be van jelölve. 

A **nyaralási esemény** szakasz segít letiltani a munkaszüneti időszakban észlelt felesleges rendellenességeket. A **stratégia** lehetőségre két lehetőség közül választhat:

* **Nyaralás mellőzése**: az összes rendellenességet és riasztást elkerüli a kiértékelési időszakban.
* **Nyaralás hétvége**: kiszámítja az adott hétvégi napok átlagos várható értékeit a nyaralás előtt, és a rendellenességek állapotát kikapcsolja ezekből az értékekből.

Több más érték is konfigurálható:

|Beállítás  |Leírás  |
|---------|---------|
|**Válasszon ki egy dimenziót országként**     | Válassza ki az ország adatait tartalmazó dimenziót. Például egy országkód.         |
|**Országkód-hozzárendelés**     | A standard [országkód](https://wikipedia.org/wiki/ISO_3166-1_alpha-2)és a kiválasztott dimenzió ország-adattípusai közötti leképezés.        |
|**Üdülési lehetőségek**    | Függetlenül attól, hogy az összes munkaszünetet figyelembe kell venni, csak a PTO (fizetett idő), vagy csak a nem PTO ünnepek.         |
|**Kibontás napja**    |  A nyaralás előtti és utáni érintett napok.        |


A **ciklus eseményei** szakasz bizonyos helyzetekben felhasználható, hogy az adatokban ciklikus minták használatával csökkentse a szükségtelen riasztásokat. Például: 

- Több mintázattal vagy ciklussal rendelkező mérőszámok, mint például a heti és a havi minta. 
- Azok a metrikák, amelyek nem rendelkeznek egyértelmű mintázattal, de az adatok az év során (YoY), a hónap (MoM), a hét (WoW) vagy a nap folyamán (DoD) is hasonlóak.
 
Nem minden beállítás választható minden részletességhez. A részletességgel elérhető lehetőségek a következők:

| Részletesség | YoY | MoM | WoW | DoD |
|:-|:-|:-|:-|:-|
| Éves | X | X | X | X |
| Havonta | X | X | X | X |
| Heti | ✔ | X | X | X |
| Napi | ✔ | ✔ | ✔ | X |
| Óránként | ✔ | ✔ | ✔ | ✔ |
| Aprólékosan | X | X | X | X |
| Másodszor | X | X | X | X |
| Egyéni | ✔ | ✔ | ✔ | ✔ |

X – nem érhető el.  
✔ Érhető el.
  
\* Ha a másodpercben egyéni részletességet használ, csak akkor érhető el, ha a metrika hosszabb, mint egy óra, és kevesebb mint egy nap.

A ciklus esemény a rendellenességek csökkentése érdekében használatos, ha egy ciklikus mintát követnek, de a rendszer anomáliát küld, ha több adatpont nem követi a mintát. A **szigorú mód** lehetővé teszi a anomália jelentéskészítését, ha még egy adatpont sem követi a mintát. 

:::image type="content" source="../media/metrics/preset-events.png" alt-text="előre beállított esemény konfigurációja":::

## <a name="view-recent-incidents"></a>Legutóbbi incidensek megtekintése

A metrikai tanácsadó a betöltött idősoros adatokon észlelt rendellenességeket észleli. Azonban nem minden rendellenességet kell kibővíteni, mert előfordulhat, hogy nem nagy hatással vannak rá. A rendszer az összesítést az incidensekre vonatkozó, a kapcsolódó csoportokba tartozó rendellenességek alapján hajtja végre. Ezek az incidensek a metrikák részletei lap **incidens** lapján tekinthetők meg. 

Kattintson egy incidensre, és lépjen az **incidensek elemzése** oldalra, ahol további részleteket is megtudhat. Kattintson az **incidensek kezelése az új incidens központban** lehetőségre az [incidens hub](diagnose-incident.md) oldalának megkereséséhez, ahol megtalálhatja az összes incidenst az adott metrika alatt. 

## <a name="subscribe-anomalies-for-notification"></a>Előfizetési rendellenességek az értesítéshez

Ha értesítést szeretne kapni, amikor valamilyen rendellenesség észlelhető, előfizethet a metrikára vonatkozó riasztásokra egy Hook használatával. További információ: [riasztások konfigurálása és értesítések beszerzése Hook használatával](alerts.md) .


## <a name="next-steps"></a>Következő lépések 
- [Riasztások konfigurálása és értesítések fogadása beavatkozási pont segítségével](alerts.md)
- [Anomáliadetektálás beállítása visszajelzés használatával](anomaly-feedback.md)
- [Incidens diagnosztizálása](diagnose-incident.md).

