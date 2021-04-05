---
title: Metrikák Advisor – gyakori kérdések
titleSuffix: Azure Cognitive Services
description: A metrikák tanácsadó szolgáltatásával kapcsolatos gyakori kérdések.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0c4c296cb1454ed89eef102732533589b1c8ca0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420959"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Metrikák Advisor – gyakori kérdések

### <a name="what-is-the-cost-of-my-instance"></a>Mi a példányom díja?

A példány használata jelenleg nem jár az előzetes verzióban.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Miért nem hozható létre az erőforrás? A "díjszabási szintje" nem érhető el, és azt mondja, hogy "már létrehozott 1 S0 ehhez az előfizetéshez"?

:::image type="content" source="media/pricing.png" alt-text="Üzenet, ha már létezik egy F0-erőforrás":::

A nyilvános előzetes verzióban a metrikus tanácsadók csak egy példánya hozható létre régiónként egy előfizetésen belül.

Ha már van olyan példánya, amely ugyanabban a régióban lett létrehozva ugyanazzal az előfizetéssel, akkor egy másik régiót vagy egy másik előfizetést is kipróbálhat egy új példány létrehozásához. Meglévő példányt is törölhet, hogy újat hozzon létre.

Ha már törölte a meglévő példányt, de a hiba továbbra is fennáll, várjon körülbelül 20 perccel az erőforrás törlése után, mielőtt új példányt hozna létre.

## <a name="basic-concepts"></a>Alapfogalmak

### <a name="what-is-multi-dimensional-time-series-data"></a>Mi az a többdimenziós idősoros adatsorozat?

Tekintse meg a [többdimenziós metrika](glossary.md#multi-dimensional-metric)  definícióját a szószedetben.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Mennyi adat szükséges ahhoz, hogy a metrikai tanácsadó elindítsa a anomáliák észlelését?

Legalább egy adatpontnál az anomáliák észlelése is aktiválható. Ez azonban nem teszi lehetővé a legjobb pontosságot. A szolgáltatás azt feltételezi, hogy az előző adatpontok ablaka az adatcsatorna létrehozása során "Kitöltés-hézag" szabályként megadott értéket használja.

Azt javasoljuk, hogy az észlelést megelőzően néhány adattal ellátott időbélyeget kelljen használni.
Az adatai részletessége alapján az ajánlott adatmennyiség az alábbiak szerint változik.

| Részletesség | Ajánlott adatmennyiség észleléshez |
| ----------- | ------------------------------------- |
| Kevesebb, mint 5 perc | 4 napos adatmennyiség |
| 5 perc – 1 nap | 28 nap |
| Több mint 1 nap, 31 nap | 4 év |
| 31 napnál hosszabb | 48 éves adatmennyiség |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Miért nem észlelhetők a metrikák tanácsadói a múltbeli adatokból származó rendellenességek?

A metrikai tanácsadó az élő adatfolyam-adatok észlelésére szolgál. A szolgáltatás által visszaadott visszamenőleges adatmennyiség maximális hossza korlátozott, és a rendszer a anomáliák észlelését fogja futtatni. Ez azt jelenti, hogy csak egy adott legkorábbi időbélyegzőt követő adatpontok lesznek a anomáliák észlelési eredményei. A legkorábbi időbélyeg az adatok részletességének függvénye.

Az adatai részletessége alapján a rendellenességek észlelését eredményező korábbi adatmennyiségek hossza az alábbi.

| Részletesség | Az adatanomáliák észleléséhez tartozó korábbi adatmennyiség maximális hossza |
| ----------- | ------------------------------------- |
| Kevesebb, mint 5 perc | Bevezetési idő – 13 óra |
| 5 perc vagy kevesebb, mint 1 óra | Bevezetési idő – 4 nap  |
| 1 órával kevesebb, mint 1 nap | Bevezetési idő – 14 nap  |
| 1 nap | Bevezetési idő – 28 nap  |
| Több mint 1 nap, kevesebb mint 31 nap | Bevezetési idő – 2 év  |
| 31 napnál hosszabb | Bevezetési idő – 24 év   |

### <a name="more-concepts-and-technical-terms"></a>További fogalmak és technikai feltételek

További információkat a [szószedetben](glossary.md) talál.

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>Hogyan az adataik betöltéséhez érvényes lekérdezést írni?  

Ahhoz, hogy a metrikai tanácsadó adatokat tartalmazzon, létre kell hoznia egy lekérdezést, amely az adatok dimenzióit egyetlen időbélyegen adja vissza. A metrikai tanácsadó többször is futtatja ezt a lekérdezést az adatoknak az egyes időbélyegekről való lekéréséhez. 

Vegye figyelembe, hogy a lekérdezésnek az egyes dimenziók kombinációinak legfeljebb egy rekordjában kell visszaadnia egy adott időbélyegzőn. Minden visszaadott rekordnak ugyanazzal az időbélyegzővel kell rendelkeznie. A lekérdezés nem ad vissza duplikált rekordot.

Tegyük fel például, hogy az alábbi lekérdezést hozza létre napi metrika esetén: 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

Ügyeljen arra, hogy az idősorozat helyes részletességét használja. Óradíjas metrika esetén a következőt kell használnia: 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

Vegye figyelembe, hogy ezek a lekérdezések csak egyetlen időbélyegen adják vissza az adatokat, és tartalmazzák a metrikai tanácsadó által betöltött összes dimenzió kombinációt. 

:::image type="content" source="media/query-result.png" alt-text="Egy lekérdezés eredménye egy időbélyeg" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Hogyan észlelni a tüskéket & a dipsot rendellenességként?

Ha a rögzített küszöbértékek előre vannak megadva, akkor a "Hard küszöbérték" manuálisan állítható be a [anomáliák észlelési konfigurációjában](how-tos/configure-metrics.md#anomaly-detection-methods).
Ha nincsenek küszöbértékek, használhatja az AI által működtetett "intelligens észlelést". A részletekért tekintse meg [az észlelési konfiguráció finomhangolása](how-tos/configure-metrics.md#tune-the-detecting-configuration) című témakört.

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Hogyan a rendszeres (szezonális) mintákkal való megfelelőség észlelését rendellenességekként?

Az "intelligens észlelés" képes megtanulni az Adatmintázatot, beleértve az idényjellegű mintákat is. Ezt követően észleli azokat az adatpontokat, amelyek nem felelnek meg a reguláris mintáknak rendellenességeknek. A részletekért tekintse meg [az észlelési konfiguráció finomhangolása](how-tos/configure-metrics.md#tune-the-detecting-configuration) című témakört.

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Hogyan felderíti az egyszerű vonalakat?

Ha az adatai általában meglehetősen instabilak, és nagy mértékben ingadoznak, és ha riasztást szeretne kapni, ha túl stabil, vagy akár egy vonalra vált, akkor a "változási küszöb" beállítás beállítható úgy, hogy az ilyen adatpontok észlelése túl kicsi legyen.
A részletekért tekintse meg a [anomáliák észlelésének konfigurációit](how-tos/configure-metrics.md#anomaly-detection-methods) .

### <a name="how-to-set-up-email-settings-and-enable-alerting-by-email"></a>E-mail-beállítások beállítása és a riasztások engedélyezése e-mailben?

1.  Az előfizetés-rendszergazda vagy erőforráscsoport-rendszergazdai jogosultsággal rendelkező felhasználónak meg kell nyitnia a Azure Portalban létrehozott metrikai tanácsadói erőforrást, és ki kell választania a **hozzáférés-vezérlés (iam)** lapot. 
2.  Válassza a **szerepkör-hozzárendelések hozzáadása** lehetőséget
3.  Válasszon egy szerepkört **Cognitive Services metrikai tanácsadó rendszergazdájának**, válassza ki a fiókját az alábbi képen.
4.  Kattintson a **Save (Mentés** ) gombra, és a rendszer sikeresen felvette a metrikák Advisor-erőforrás rendszergazdájaként. Vegye figyelembe, hogy a fenti műveleteket az előfizetés rendszergazdája vagy az erőforráscsoport rendszergazdája hajthatja végre. 

:::image type="content" source="media/access-control.png" alt-text="Hozzáférés-vezérlés (IAM) menü oldal, a szerepkör-hozzárendelés hozzáadása lehetőséggel, majd egy olyan mező, amely hozzáférést rendel a kiválasztott felhasználóhoz Cognitive Services metrikai tanácsadói rendszergazda hozzáférési szerepkörével, majd a kiválasztott felhasználói felület Save (Mentés) gombjára kattintva megjelenítheti a felhasználók keresésének lépéseit, és adott szintű hozzáférési engedélyeket adhat hozzá." lightbox="media/access-control.png":::


5.  Az engedélyek propagálásához akár egy percet is igénybe vehet. Ezután válassza ki a mérőszámok tanácsadó munkaterületét, majd a bal oldali navigációs panelen válassza az **e-mail beállítás** lehetőséget. Adja meg a szükséges elemeket, különösen az SMTP-hez kapcsolódó adatokat. 
6.  Válassza a **Mentés** lehetőséget, majd az e-mail-konfigurációt. A közel valós idejű riasztásokhoz új hookokat hozhat létre, és előfizethet a metrikus rendellenességekre. 

## <a name="advanced-concepts"></a>Speciális fogalmak

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>Hogyan hoz létre a metrikus Advisor egy incidens fát a többdimenziós mérőszámokhoz?

A metrikák több idősorozatra is bonthatók dimenziók szerint. A metrikát például a `Response latency` csapat tulajdonában lévő összes szolgáltatáshoz figyeli a rendszer. A `Service` Kategória felhasználható dimenzióként a metrika bővítéséhez, így a (z `Response latency` `Service1` `Service2` ) és a (z), stb. felosztásra kerül. Minden szolgáltatás különböző gépeken telepíthető több adatközpontban, így a metrika tovább osztható a és a használatával `Machine` `Data center` .

|Szolgáltatás| Adatközpont| Gép  | 
|----|------|----------------   |
| S1 |  DC1 |   M1 verzióban |
| S1 |  DC1 |   M2 |
| S1 |  DC2 |   M3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   M1 verzióban |
| S2 |  DC1 |   M2 |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

A teljes összegtől kezdődően `Response latency` a mérőszámot a és a érték alapján részletezjük `Service` `Data center` `Machine` . Előfordulhat azonban, hogy több értelme van a szolgáltatás tulajdonosai számára az elérési út használatára `Service`  ->  `Data center`  ->  `Machine` , vagy talán több értelme van az infrastruktúra-mérnököknek az elérési út használatára `Data Center`  ->  `Machine`  ->  `Service` . Mindez a felhasználók egyedi üzleti követelményeitől függ. 

A metrikus tanácsadóban a felhasználók bármilyen elérési utat megadhatnak a hierarchikus topológia egyik csomópontjának a részletezéséhez vagy összesítéséhez. Pontosabban a hierarchikus topológia egy irányított aciklikus gráf, nem pedig fastruktúra. Az összes lehetséges dimenzió kombinációból álló teljes hierarchikus topológia a következőhöz hasonló: 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="hierarchikus topológiai diagram, amely több összekapcsolási csúcspontot és éleket tartalmaz, amelyek több, a (z), DC és M számú dimenzióval rendelkeznek, és a megfelelő számok 1-től 6-ig terjednek." lightbox="media/dimension-combinations-view.png":::

Elméletileg, ha a dimenzióban eltérő értékek szerepelnek, a dimenziónak eltérő értékekkel kell rendelkezniük, `Service` `Ls` és a `Data center` `Ldc` dimenziónak `Machine` `Lm` eltérő értékekkel kell rendelkezniük, akkor `(Ls + 1) * (Ldc + 1) * (Lm + 1)` a hierarchikus topológiában lehetnek dimenzió-kombinációk. 

De általában nem minden dimenzió kombináció érvényes, ami jelentősen csökkentheti az összetettséget. Jelenleg ha a felhasználók maguk összesítik a metrikát, nem korlátozzuk a méretek számát. Ha a metrikai tanácsadó által biztosított kumulatív funkciót kell használnia, a méretek száma nem lehet nagyobb, mint 6. Az idősorozatok számát azonban a mérőszámok méretei szerint kibontva, 10 000-nél kisebbre korlátozzuk.

Az **incidens faszerkezetének** eszköze a diagnosztika oldalon csak azokat a csomópontokat jeleníti meg, amelyekben a teljes topológia helyett anomália észlelhető. Ez segít az aktuális probléma kiépítésében. Emellett előfordulhat, hogy nem jeleníti meg az összes rendellenességet a metrikán belül, hanem a legfontosabb rendellenességeket fogja megjeleníteni a hozzájárulás alapján. Így gyorsan megtalálhatja a rendellenes adatmennyiség következményeit, hatókörét és oldalpár-elérési útját. Ez jelentősen csökkenti a szükséges anomáliák számát, és segít a felhasználóknak megérteni és megtalálni a legfontosabb problémákat. 
 
Ha például egy rendellenesség bekövetkezik `Service = S2 | Data Center = DC2 | Machine = M5` , a rendellenesség eltérése hatással van a szülő csomópontra `Service= S2` , amely szintén a rendellenességet észlelte, de a rendellenesség nem érinti a teljes adatközpontot `DC2` és a összes szolgáltatást `M5` . Az incidens faszerkezete az alábbi képernyőképen fog megjelenni, a legfelső anomáliát a rendszer rögzíti `Service = S2` , a kiváltó ok pedig két olyan elérési úton elemezhető, amely mindkettőt eredményezi `Service = S2 | Data Center = DC2 | Machine = M5` .

 :::image type="content" source="media/root-cause-paths.png" alt-text="5 megcímkézett csúcspont két különböző elérési úttal, amelyek egy S2-vel ellátott, közös csomóponttal vannak összekötve. A legfontosabb anomália a Service = S2-ben van rögzítve, és a kiváltó ok a két elérési út alapján elemezhető, mindkettő a Service = S2-hez vezet | Adatközpont = DC2 | Gép = M5" lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>Következő lépések
- [A metrikai tanácsadó áttekintése](overview.md)
- [A webes portál használata](quickstarts/web-portal.md)