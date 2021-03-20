---
title: Adatcsatornák kezelése a mérőszámok tanácsadójában
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan kezelheti a metrikák Advisorhoz hozzáadott adatcsatornákat.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: mbullwin
ms.openlocfilehash: fb6eaf44967732d3a41ea92b0896540a40f694e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184722"
---
# <a name="how-to-manage-your-data-feeds"></a>Útmutató: az adatcsatornák kezelése

Ismerje meg, hogyan kezelheti az adatcsatornákat a metrikus tanácsadóban. Ez a cikk végigvezeti az adatcsatornáknak a metrikai tanácsadóban való kezelésén.

## <a name="edit-a-data-feed"></a>Adatcsatorna szerkesztése

> [!NOTE]
> Az adatcsatorna létrehozása után a következő adatok nem módosíthatók. 
> * Adatcsatorna azonosítója
> * Létrehozás ideje
> * Dimenzió
> * Source Type (Forrás típusa)
> * Részletesség

Csak az adatcsatorna rendszergazdája teheti lehetővé a módosítását. 

Adatcsatorna szüneteltetése vagy újraaktiválása:

1. Az adatcsatorna-lista lapon kattintson arra a műveletre, amelyet végre szeretne hajtani az adatcsatornán.

2. Az adatcsatorna adatai lapon kattintson az **állapot** kapcsoló gombra.

Adatcsatorna törlése: 

1. Az adatcsatorna-lista lapon kattintson a **Törlés** elemre az adatcsatornán.

2. Az adatcsatorna adatai lapon kattintson a **Törlés** elemre.

A kezdési idő módosításakor újra ellenőriznie kell a sémát. Módosíthatja a **paramétereket a paraméterek szerkesztése** lehetőséggel.

##  <a name="backfill-your-data-feed"></a>Backfill az adatcsatornákat

A  **backfill** gomb kiválasztásával azonnali betöltést válthat ki egy időbélyegzőn, a sikertelen betöltések kijavításához vagy a meglévő adatmennyiség felülbírálásához.
- A kezdési időpont a befogadó.
- A befejezési időpont kizárólagos.
- A anomáliák észlelése csak a kiválasztott tartományon újra aktiválódik.

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="Backfill adatcsatorna":::

## <a name="manage-permission-of-a-data-feed"></a>Adatcsatorna engedélyeinek kezelése

A munkaterület-hozzáférést a metrikai tanácsadó erőforrás vezérli, amely a hitelesítéshez Azure Active Directory használ. Az engedély-vezérlés egy másik rétege lesz alkalmazva a metrikus adatokra.

A metrikák tanácsadója lehetővé teszi, hogy különböző adatcsatornákon lévő személyek különböző csoportjai számára engedélyezzen engedélyeket. A szerepköröknek két típusa van: 

- Rendszergazda: teljes körű engedélyekkel rendelkezik az adatcsatornák kezeléséhez, beleértve a módosítást és a törlést is.
- Viewer: ki férhet hozzá az adatcsatorna írásvédett nézetéhez.
 

## <a name="advanced-settings"></a>Speciális beállítások

Új adatcsatorna létrehozásakor több opcionális speciális beállítás is rendelkezésre áll, ezek módosíthatók az adatcsatorna részletei lapon.

### <a name="ingestion-options"></a>Betöltési beállítások

* **Betöltési idő eltolása**: alapértelmezés szerint a rendszer a megadott részletesség szerint tölti be az adatokat. Például a *napi* időbélyeggel rendelkező metrika egy nappal az időbélyeg után lesz betöltve. Az eltolással késleltetheti a betöltés időpontját *pozitív* számmal, vagy *negatív* számmal is elvégezheti.

* **Maximális Egyidejűség**: akkor adja meg ezt a paramétert, ha az adatforrás támogatja a korlátozott párhuzamosságot. Ellenkező esetben hagyja meg az alapértelmezett beállítást.

* **Újrapróbálkozás** ennyi idő után: Ha az adatfeldolgozás meghiúsult, akkor egy adott időszakon belül automatikusan újrapróbálkozik. Az időszak kezdete az az idő, amikor az első adatfeldolgozás történt. Az időszak hossza a részletességnek megfelelően van definiálva. Ha az alapértelmezett értéket (-1) hagyja, az érték az alábbi részletesség alapján lesz meghatározva.
    
    | Részletesség       | Újrapróbálkozás befejezése után           |
    | :------------ | :--------------- |
    | Napi, egyéni (>= 1 nap), hetente, havonta, évente     | 7 nap          |
    | Óránként, egyéni (< 1 nap)       | 72 óra |

* Minimális **Újrapróbálkozás időköze**: megadhatja a minimális időtartamot, amikor az adatok forrásból való kihúzása újra próbálkozik. Ha az alapértelmezett értéket (-1) hagyja, az újrapróbálkozási időköz az alábbi részletesség szerint lesz meghatározva.
    
    | Részletesség       | Minimális újrapróbálkozási időköz           |
    | :------------ | :--------------- |
    | Napi, egyéni (>= 1 nap), hetente, havonta     | 30 perc          |
    | Óránként, egyéni (< 1 nap)      | 10 perc |
    | Éves | 1 nap          |
 
### <a name="fill-gap-when-detecting"></a>Kitöltési hézag az észleléskor: 

> [!NOTE]
> Ez a beállítás nem befolyásolja az adatforrást, és nem érinti a portálon megjelenített adatdiagramokat. Az automatikus kitöltés csak az anomáliák észlelése során fordul elő.

Egy idősorozat nem folytonos. Ha hiányoznak az adatpontok, a metrikák Advisor a megadott értékkel tölti ki őket, mielőtt a rendszer nagyobb pontosságot észlel a anomáliák észlelése előtt.
A következő lehetőségek közül választhat: 

* Az előző tényleges adatpontból származó érték használata. Alapértelmezés szerint a rendszer ezt használja.
* Egy adott érték használata.

### <a name="action-link-template"></a>Művelet hivatkozásának sablonja: 

A Action link-sablonok használatával előre definiálható http URL-címek határozhatók meg, amelyek a helyőrzők,,, és karakterből állhatnak `%datafeed` `%metric` `%timestamp` `%detect_config` `%tagset` . A sablon használatával egy anomália vagy incidens egy adott URL-címére irányítható át a részletezéshez.

:::image type="content" source="../media/action-link-template.png" alt-text="Művelet csatolása sablon" lightbox="../media/action-link-template.png":::

Miután kitöltötte a művelet hivatkozását **, kattintson a művelet hivatkozásra az** incidensek listájának művelete lehetőségnél, majd az incidens faszerkezetének jobb gombbal kattintson a menü elemre. Cserélje le a művelet csatolása sablonban található helyőrzőket az anomália vagy az incidens megfelelő értékeire.

| Helyőrző | Példák | Megjegyzés |
| ---------- | -------- | ------- |
| `%datafeed` | - | Adatcsatorna azonosítója |
| `%metric` | - | Metrika azonosítója |
| `%detect_config` | - | Konfiguráció AZONOSÍTÓjának észlelése |
| `%timestamp` | - | Egy állandó incidens rendellenességének vagy befejezési idejének időbélyege |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | Egy adott incidens rendellenességének vagy leggyakoribb rendellenességének dimenzió értékei.   <br> A a `filterVal` szögletes zárójelben lévő egyező értékek kiszűrésére szolgál.   |

Példák

* Ha a művelet hivatkozás sablonja `https://action-link/metric/%metric?detectConfigId=%detect_config` ,
  * A művelet hivatkozása a `https://action-link/metric/1234?detectConfigId=2345` metrika `1234` és a konfiguráció észlelése alatt előforduló anomáliák vagy incidensek esetében léphet fel `2345` .

* Ha a művelet hivatkozás sablonja `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]` , 
    * A művelet hivatkozása akkor lenne `https://action-link?Dim1=Val1&Dim2=Val2` , ha az anomália `{ "Dim1": "Val1", "Dim2": "Val2" }` . 
    * A művelet hivatkozása akkor lenne `https://action-link?Dim2=Val2` , ha az anomália `{ "Dim1": "", "Dim2": "Val2" } ` , mert a `[Dim1=***&]` rendszer kihagyja a dimenzió értéke üres karakterláncot. 

* Ha a művelet hivatkozás sablonja `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']` , 
    * A művelet hivatkozása akkor lenne `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'` , ha az anomália `{ "Dim1": "Val1", "Dim2": "Val2" }` , 
    * A művelet hivatkozása akkor lenne, `https://action-link?filter=Name/Dim2 eq 'Val2'` Ha `{ "Dim1": "", "Dim2": "Val2" }` a rendszer kihagyja az adatrendellenességet, mert `[Name/Dim1 eq '***' and ]` a dimenzió értéke üres sztring. 
   
### <a name="data-feed-not-available-alert-settings"></a>"Az adatcsatorna nem érhető el" riasztási beállítások

Az adatcsatorna akkor minősül nem elérhetőnek, ha az adatcsatorna által a betöltés indításakor megadott türelmi időszakon belül nem kerül be adatok a forrásból. Ebben az esetben riasztást vált ki.

A riasztások konfigurálásához először létre kell [hoznia egy hookot](alerts.md#create-a-hook) . A riasztások a beállított hookon keresztül lesznek elküldve.

* **Türelmi időszak**: a türelmi időszak beállításával határozható meg, hogy mikor küldjön riasztást, ha egyetlen adatpont sem kerül betöltésre. A hivatkozási pont az első betöltés időpontja. Ha a betöltés meghiúsul, a metrikák Advisor a részletességgel megadott rendszeres időközönként próbálkozik. Ha továbbra is sikertelen a türelmi időszak, akkor a rendszer riasztást küld.

* **Automatikus késleltetés**: Ha a beállítás értéke nulla, akkor minden *nem elérhető* időbélyeg riasztást indít el. Ha a nullától eltérő beállítás van megadva, a rendszer nem aktiválja a folyamatos időbélyegeket az első, *nem elérhető* időbélyeg után, a megadott beállításnak megfelelően.

## <a name="next-steps"></a>Következő lépések
- [Metrikák konfigurálása és a konfiguráció észlelésének finomhangolása](configure-metrics.md)
- [Anomáliadetektálás beállítása visszajelzés használatával](anomaly-feedback.md)
- [Incidens diagnosztizálása](diagnose-incident.md).
