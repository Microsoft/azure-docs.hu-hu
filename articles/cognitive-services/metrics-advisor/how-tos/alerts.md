---
title: Metrikai tanácsadói riasztások konfigurálása
titleSuffix: Azure Cognitive Services
description: A metrikák Advisor-riasztások konfigurálása az e-mailekhez, a webes és az Azure-DevOps tartozó hookok használatával.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 30d8fdf99da7a4854db0985bed6256ecd6f7a366
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420920"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>Útmutató: riasztások konfigurálása és értesítések beszerzése Hook használatával

Miután a metrikai tanácsadó észlelte a anomáliát, a riasztási beállítások alapján riasztási értesítés lesz aktiválva egy Hook használatával. A riasztási beállítások több észlelési konfigurációval is használhatók, a riasztási szabály testreszabásához különböző paraméterek állnak rendelkezésre.

## <a name="create-a-hook"></a>Hook létrehozása

A metrikai tanácsadó három különböző típusú hookot támogat: e-mail-hookot, webhookot és Azure-DevOps. Kiválaszthatja az adott forgatókönyvhöz használhatót.       

### <a name="email-hook"></a>E-mail Hook

> [!Note]
> A metrikai tanácsadó erőforrás-rendszergazdáinak be kell állítania az e-mail-beállításokat, és be kell állítani az SMTP-hez kapcsolódó adatokat a metrikai TANÁCSADÓBA, mielőtt a rendszer elküldi a Az erőforráscsoport rendszergazdájának vagy az előfizetés rendszergazdájának legalább egy *Cognitive Services metrikai Advisor rendszergazdai* szerepkört kell rendelnie a metrikai tanácsadó erőforrás hozzáférés-vezérlés lapján. [További információ az e-mail beállítások konfigurálásáról](../faq.md#how-to-set-up-email-settings-and-enable-alerting-by-email).

E-mail Hook létrehozásához a következő paraméterek érhetők el: 

Az e-mail-Hook az a csatorna, amellyel a rendszer az **e-mailben** megadott e-mail-címekre küldi el az anomáliák riasztásait. Kétféle riasztási e-mail lesz elküldve: *az adatcsatornán nem elérhető* riasztások és az *incidensek jelentései* , amelyek egy vagy több rendellenességet tartalmaznak. 

|Paraméter |Leírás  |
|---------|---------|
| Név | Az e-mail Hook neve |
| E-mail cím| E-mail-címek, amelyek riasztást küldenek|
| Külső hivatkozás | Opcionális mező, amely lehetővé teszi a testreszabott átirányítást, például a hibaelhárítási megjegyzésekhez. |
| Testreszabott anomáliák riasztási címe | A title sablon támogatja a,,,,,,, `${severity}` `${alertSettingName}` `${datafeedName}` `${metricName}` `${detectConfigName}` `${timestamp}` `${topDimension}` `${incidentCount}` , `${anomalyCount}`

Miután rákattintott **az OK gombra**, a rendszer létrehoz egy e-mail-hookot. Bármelyik riasztási beállításban használhatja a anomáliák riasztások fogadására. 

### <a name="web-hook"></a>Webes Hook

> [!Note]
> * Használja a **post** kérelem metódusát.
> * A kérelem törzse a következőhöz hasonló:  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * Egy webhook létrehozásakor vagy módosításakor az API-t egy üres kérelem törzsének tesztje fogja hívni. Az API-nak egy 200 HTTP-kódot kell visszaadnia.

A webes Hook a metrikai tanácsadó szolgáltatásból elérhető összes információ belépési pontja, és a riasztások indításakor meghívja a felhasználó által megadott API-t. Az összes riasztást webhookon keresztül lehet elküldeni.

Webhook létrehozásához hozzá kell adnia a következő információkat:

|Paraméter |Leírás  |
|---------|---------|
|Végpont     | A riasztás aktiválásakor meghívható API-címe.        |
|Felhasználónév/jelszó | Az API-címnek való hitelesítéshez. Ha nincs szükség hitelesítésre, hagyja üresen ezt a feketét.         |
|Fejléc     | Egyéni fejlécek az API-hívásban.        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="webhook-létrehozási ablak.":::

Ha a rendszer egy webhookon keresztül küld értesítést, a következő API-k segítségével kérheti le a riasztás részleteit. Állítsa be a *időbélyegzőt* és a *alertSettingGuid* az API-szolgáltatásban, amelyet a rendszer leküld, majd használja a következő lekérdezéseket: 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

### <a name="azure-devops"></a>Azure DevOps

A metrikai tanácsadó Emellett támogatja a munkaelemek automatikus létrehozását az Azure DevOps a problémák/hibák követéséhez, ha bármilyen rendellenesség észlelhető. Minden riasztás az Azure DevOps hookokon keresztül küldhető el.

Az Azure DevOps Hook létrehozásához hozzá kell adnia a következő információkat

|Paraméter |Leírás  |
|---------|---------|
| Név | A Hook neve |
| Szervezet | Az a szervezet, amelyhez a DevOps tartozik |
| Project | A DevOps adott projektje. |
| Hozzáférési jogkivonat |  A DevOps való hitelesítéshez használt jogkivonat. | 

> [!Note]
> Írási engedélyeket kell megadnia, ha azt szeretné, hogy a metrikai tanácsadó munkaelemeket hozzon létre az anomália-riasztások alapján. A hookok létrehozása után bármelyik riasztási beállításban használhatja őket. A Hook- **Beállítások** lapon kezelheti a horgokat.

## <a name="add-or-edit-alert-settings"></a>Riasztási beállítások hozzáadása vagy szerkesztése

A metrikák részletei lapon megkeresheti a **riasztási beállítások** szakaszt a metrikák részletei lap bal alsó sarkában. Felsorolja az összes olyan riasztási beállítást, amely a kiválasztott észlelési konfigurációra vonatkozik. Új észlelési konfiguráció létrehozásakor nincs riasztási beállítás, és a rendszer nem küld riasztásokat.  
A riasztási beállítások módosításához használhatja a **Hozzáadás**, **Szerkesztés** és **Törlés** ikonokat is.

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="Riasztási beállítások menüelem":::

Kattintson a **Hozzáadás** vagy **Szerkesztés** gombra a riasztási beállítások hozzáadására vagy szerkesztésére szolgáló ablak beszerzéséhez.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="Riasztási beállítások hozzáadása vagy szerkesztése":::

**Riasztási beállítás neve**: a riasztási beállítás neve. Ekkor megjelenik a riasztás e-mail címe.

**Hookok**: a riasztások küldésére szolgáló horgok listája.

A fenti képernyőképen feltüntetett szakasz egy észlelési konfiguráció beállításait tartalmazza. Különböző riasztási beállításokat adhat meg a különböző észlelési konfigurációkhoz. Válassza ki a cél konfigurációt az ablak harmadik legördülő listájának használatával. 

### <a name="filter-settings"></a>Beállítások szűrése 

A következő egy észlelési konfiguráció szűrési beállításai.

A (z) **riasztása** 4 lehetőséggel rendelkezik a rendellenességek szűréséhez:

* Az **összes adatsorozat rendellenességei**: a riasztásban minden rendellenesség szerepelni fog.         
* **Adatsorozat-csoportba tartozó rendellenességek**: adatsorozatok szűrése dimenzió értékek alapján. Meghatározott értékek beállítása egyes dimenziók esetében. Az anomáliák csak akkor lesznek felszámítva a riasztásban, ha az adatsorozat megfelel a megadott értéknek.       
* **Anomáliák a kedvenc adatsorozatában**: csak a kedvencként megjelölt adatsorozat lesz belefoglalva a riasztásba.        |
* Az **összes sorozat legfontosabb n-ban észlelt rendellenességek**: Ez a szűrő arra az esetre szól, ha csak az a sorozat szerepel, amelynek értéke a legfontosabb n. Visszatekintünk néhány időbélyeget, és megvizsgáljuk, hogy az időbélyegzőn belüli adatsorozat értéke felül van-e az első N. Ha a "Top n" szám nagyobb, mint a megadott szám, akkor a rendszer az anomáliát fogja tartalmazni egy riasztásban.        |

A **szűrő anomália lehetőség** egy további szűrő a következő beállításokkal:

- **Súlyosság** : az anomália csak akkor jelenik meg, ha az anomália súlyossága a megadott tartományon belül van.
- **Késleltetés** : a riasztások kiváltásakor ideiglenesen állítsa le a riasztásokat a következő N pontokban (időszak) lévő rendellenességeknél.
    - **szundítás típusa** : Ha az **adatsorozat** értékre van állítva, akkor az elindított anomália csak az adatsorozatot jeleníti meg. A **metrika** esetében az egyik aktivált anomália a metrika összes adatsorát kikapcsolja.
    - **késleltetési szám** : az a pontok száma (pont), amelynél a Szundi meghalad.
    - **Alaphelyzetbe állítás** : Ha bejelöli, az aktivált anomália csak a következő n egymást követő rendellenességeket fogja késleltetni. Ha a következő adatpontok egyike nem anomália, a rendszer visszaállítja a késleltetést ebből a pontból. Ha nincs kiválasztva, az egyik aktivált anomália a következő n pont (időszak) késleltetését választja, még akkor is, ha az egymást követő adatpontok nem rendellenességek.
- **érték** (nem kötelező): szűrés érték alapján. A rendszer csak a feltételnek megfelelő pont értékeket fogja tartalmazni. Ha egy másik metrika megfelelő értékét használja, a két metrika dimenziójának nevének konzisztensnek kell lennie.

A nem kiszűrt rendellenességek riasztásban lesznek elküldve.

### <a name="add-cross-metric-settings"></a>Több mérőszámos beállítások hozzáadása

További szakasz hozzáadásához kattintson a riasztási beállítások lapon a **+ több mérőszám hozzáadása** lehetőségre.

Az **operátor** választó az egyes szakaszok logikai kapcsolata, amely meghatározza, hogy riasztást küldenek-e.


|Operátor  |Leírás  |
|---------|---------|
|ÉS     | Csak akkor küldjön riasztást, ha egy sorozat megfelel az egyes riasztási szakaszoknak, és az összes adatpont anomália. Ha a metrikák különböző dimenziós névvel rendelkeznek, a rendszer soha nem aktiválja a riasztást.         |
|OR     | Küldje el a riasztást, ha legalább egy szakasz rendellenességeket tartalmaz.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="Több riasztás beállítási szakaszának operátora":::

## <a name="next-steps"></a>Következő lépések

- [Anomáliadetektálás beállítása visszajelzés használatával](anomaly-feedback.md)
- [Incidens diagnosztizálása](diagnose-incident.md).
- [Metrikák konfigurálása és a konfiguráció észlelésének finomhangolása](configure-metrics.md)