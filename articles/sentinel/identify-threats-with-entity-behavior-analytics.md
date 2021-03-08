---
title: A felhasználók és az entitások viselkedésének elemzésével (UEBA) kapcsolatos fejlett veszélyforrások azonosítása az Azure Sentinelben | Microsoft Docs
description: Hozzon létre viselkedési alapkonfigurációkat az entitások (felhasználók, állomásnevek, IP-címek) számára, és használja őket a rendellenes viselkedés észlelésére és a nulla napos speciális állandó fenyegetések (APT) azonosítására.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: bf7a17d96d31fd4214d5465a5739acc9ce9a9d53
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455501"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>A felhasználók és az entitások viselkedésének elemzésével (UEBA) kapcsolatos fejlett veszélyforrások azonosítása az Azure Sentinelben

> [!IMPORTANT]
>
> - A UEBA és az Entity Pages szolgáltatás mostantól **általánosan elérhető** **_Az Azure Sentinel_** földrajzi és régióiban.

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>Mi a felhasználói és az entitások viselkedésének elemzése (UEBA)?

### <a name="the-concept"></a>A koncepció

Azonosíthatja a szervezeten belüli fenyegetéseket és azok lehetséges hatásait – akár egy feltört entitást, akár rosszindulatú bennfenteset –, mindig időigényes és munkaigényes folyamat. A riasztások és a pontok összekapcsolása, valamint az aktív vadászat révén a lehető legkevesebb mennyiségű időt és erőfeszítést kell kiszámítani, és a kifinomult fenyegetések egyszerűen kikerülik a felderítést. Különösen a megfoghatatlan fenyegetések, például a zéró napi, a megcélozott és a speciális állandó fenyegetések lehetnek a legveszélyesebbek a szervezet számára, így az észlelésük is kritikus fontosságú.

Az Azure Sentinel UEBA funkciója kiküszöböli az elemzők számítási feladatainak rabszolgamunka, és a beavatkozások bizonytalanságát, és magas megbízhatóságú, gyakorlati intelligenciát biztosít, így a vizsgálatra és a szervizelésre koncentrálhat.

Mivel az Azure Sentinel az összes csatlakoztatott adatforrásból gyűjt naplókat és riasztásokat, elemzi őket, és létrehozza a szervezet entitásai (például felhasználók, gazdagépek, IP-címek és alkalmazások) alapszintű viselkedési profilját az idő és a társ-csoport horizonton. Az Azure Sentinel számos különböző technikát és gépi tanulási funkciót használ, így azonosíthatja a rendellenes tevékenységeket, és meghatározhatja, hogy az eszköz biztonsága sérült-e. Nem csak ez, de képes az egyes eszközök relatív érzékenységére, az eszközök társ csoportjai azonosítására és az adott sérült eszköz (a "Blast RADIUS") lehetséges hatásának kiértékelésére is. Ezeket az információkat felhasználva hatékonyan rangsorolhatja a nyomozást és az incidensek kezelését. 

### <a name="architecture-overview"></a>Az architektúra áttekintése

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="Entitás viselkedésének elemzési architektúrája":::

### <a name="security-driven-analytics"></a>Biztonsági vezérelt elemzés

A Gartner UEBA-megoldásokhoz készült paradigma ihlette az Azure Sentinel "külső" megközelítést biztosít a következő három képkocka alapján:

- **Használati esetek:** A megfelelő támadási vektorok és forgatókönyvek rangsorolása a biztonsági kutatások alapján, összhangban a (z)&CK-keretrendszerével, technikákkal és altechnikákkal, amelyek különböző entitásokat tesznek elérhetővé az áldozatok, az elkövetők vagy a kimutatási pontok számára a kill láncban; Az Azure Sentinel kifejezetten az egyes adatforrások által biztosított legértékesebb naplókra koncentrál.

- **Adatforrások:** Az Azure Sentinel mindenekelőtt az Azure-adatforrások támogatásával, a fenyegetési forgatókönyveknek megfelelő adatok biztosításához pedig a harmadik féltől származó adatforrásokat is kijelöli.

- **Elemzés:** A különböző gépi tanulási (ML-) algoritmusok használatával az Azure Sentinel rendellenes tevékenységeket azonosít, és egyértelműen és tömör módon mutatja be a kontextust a kontextus-gazdagítás formájában, néhány példa alább látható.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="Viselkedés-elemzések kívül – megközelítés":::

Az Azure Sentinel olyan összetevőket mutat be, amelyek segítenek a biztonsági elemzők számára, hogy egyértelmű képet kapjanak a rendellenes tevékenységekről a kontextusban, valamint a felhasználó alapkonfigurációjának profiljához képest. A felhasználó (vagy egy gazdagép vagy egy címe) által végrehajtott műveleteket a rendszer kontextus szerint értékeli ki, ahol a "true" (igaz) érték egy azonosított rendellenességet jelez:
- földrajzi helyeken, eszközökön és környezeteken keresztül.
- az idő-és gyakorisági horizontokon (a felhasználó saját előzményeihez képest).
- a társak viselkedéséhez képest.
- a szervezet viselkedéséhez képest.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="Entitás környezete":::


### <a name="scoring"></a>Pontozás

Minden tevékenység a "vizsgálat prioritási pontszáma" kifejezéssel van kiértékelve, amely meghatározza, hogy egy adott felhasználó milyen valószínűséggel rendelkezik egy adott tevékenységgel, a felhasználó és a társai viselkedési tanulása alapján. A legrendellenesebb módon azonosított tevékenységek a legmagasabb pontszámot kapják (0-10-es skálán).

Tekintse meg, hogyan használja a viselkedés-elemzést [Microsoft Cloud app Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) a működésének példáját.

## <a name="entity-pages"></a>Entitás lapjai

Tudjon meg többet az [Azure Sentinel-entitásokról](entities-in-azure-sentinel.md) , és tekintse meg a [támogatott entitások és azonosítók](entities-reference.md)teljes listáját.

Ha a keresés, a riasztás vagy a vizsgálat során bármely entitást (jelenleg a felhasználókra és gazdagépekre korlátozódik) keres, kiválaszthatja az entitást, és az entitások **oldalára** is áthelyezheti az adott entitás hasznos információit tartalmazó adatlapot. Az ezen a lapon megjelenő információk típusai közé tartoznak az entitás alapvető tudnivalói, a jelen entitással kapcsolatos jelentős események ütemezése, valamint az entitás viselkedésére vonatkozó megállapítások.
 
Az entitások lapjai három részből állnak:
- A bal oldali panel tartalmazza az entitás azonosítására szolgáló adatokat, amelyeket az adatforrásokból, például a Azure Active Directoryból, a Azure Monitorból, a Azure Security Centerból és a Microsoft Defenderből gyűjtöttek össze.

- A középső panelen az entitással kapcsolatos jelentős események (például riasztások, könyvjelzők és tevékenységek) grafikus és szöveges ütemezése látható. A tevékenységek a Log Analyticsből származó jelentős események összesítései. A tevékenységeket felderítő lekérdezéseket a Microsoft Security Research Teams fejlesztette ki.

- A jobb oldali panel az entitás viselkedési elemzéseit mutatja be. Ezek az ismeretek segítenek a rendellenességek és a biztonsági fenyegetések gyors azonosításában. Az észleléseket a Microsoft biztonsági kutatói csapatai dolgozzák ki, és az anomáliák észlelési modelljein alapulnak.

### <a name="the-timeline"></a>Az idősor

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Entitás lapok idővonala":::

Az idősor az entitások oldalának az Azure Sentinelben való viselkedésének elemzéséhez való hozzájárulásának jelentős részét képezi. Egy olyan történetet mutat be az entitásokkal kapcsolatos eseményekről, amelyek segítenek megérteni az entitás tevékenységeit egy adott időszakon belül.

Kiválaszthatja az **időtartományt** több előre definiált lehetőség közül (például az *elmúlt 24 órában*), vagy megadhatja bármely egyéni időkeretre. Emellett olyan szűrőket is beállíthat, amelyek korlátozzák az ütemtervben lévő információkat bizonyos típusú eseményekre vagy riasztásokra.

Az idősor a következő típusú elemeket tartalmazza:

- Riasztások – minden olyan riasztás, amelyben az entitás **leképezett entitásként** van definiálva. Vegye figyelembe, hogy ha a szervezete [Egyéni riasztásokat](./tutorial-detect-threats-custom.md)hozott létre az Analytics-szabályok használatával, győződjön meg arról, hogy a szabályok entitás-megfeleltetése megfelelően lett végrehajtva.

- Könyvjelzők – az oldalon megjelenő adott entitást tartalmazó könyvjelzők.

- Tevékenységek – az entitáshoz kapcsolódó jelentős események összesítése. 
 
### <a name="entity-insights"></a>Entitás-felismerések
 
Az entitás-elemzések a Microsoft biztonsági kutatói által meghatározott lekérdezések, amelyek segítenek az elemzőknek a hatékonyabb és hatékony vizsgálatában. Az elemzések az entitások oldalának részeként jelennek meg, és értékes biztonsági információkat biztosítanak a gazdagépekről és a felhasználókról táblázatos adatok és diagramok formájában. Az itt látható információk azt jelzik, hogy nem kell Log Analytics. Az elemzések között szerepelnek a bejelentkezések, a csoportok kiegészítései, a rendellenes események és egyebek, valamint a rendellenes viselkedést észlelő speciális ML-algoritmusok is. 

Az elemzések a következő adatforrásokon alapulnak:
- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (Azure Sentinel UEBA)
- Szívverés (Azure Monitor ügynök)
- CommonSecurityLog (Azure Sentinel)

### <a name="how-to-use-entity-pages"></a>Az entitások lapjainak használata

Az entitások lapjai több használati forgatókönyv részét képezik, és az incidensek kezelése, a vizsgálati gráf, a könyvjelzők vagy közvetlenül **az entitás-** keresési oldalon, az Azure Sentinel főmenüjében is elérhetők.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Entitás lap használati esetei":::

## <a name="data-schema"></a>Adatséma

### <a name="behavior-analytics-table"></a>Viselkedés-elemzési táblázat

| Mező                     | Leírás                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | a bérlő egyedi AZONOSÍTÓjának száma                                      |
| SourceRecordId            | az EBA-esemény egyedi azonosítószáma                                   |
| TimeGenerated             | a tevékenység előfordulási időbélyege                              |
| TimeProcessed             | a tevékenység az EBA-motor általi feldolgozásának időbélyege            |
| ActivityType              | a tevékenység magas szintű kategóriája                                 |
| ActionType                | a tevékenység normalizált neve                                     |
| Felhasználónév                  | a tevékenységet kezdeményező felhasználó felhasználóneve                    |
| UserPrincipalName         | a tevékenységet kezdeményező felhasználó teljes felhasználóneve               |
| EventSource               | az eredeti eseményt biztosító adatforrás                        |
| SourceIPAddress           | Az IP-cím, amelyből a tevékenységet kezdeményezték                        |
| SourceIPLocation          | az az ország, amelyből a tevékenységet kezdeményezték, az IP-címről dúsított |
| SourceDevice              | a tevékenységet kezdeményező eszköz állomásneve                  |
| DestinationIPAddress      | A tevékenység céljának IP-címe                            |
| DestinationIPLocation     | az IP-címről kibővített tevékenység céljának országa     |
| DestinationDevice         | a céleszköz neve                                           |
| **UsersInsights**         | az érintett felhasználók környezetfüggő gyarapítása                            |
| **DevicesInsights**       | az érintett eszközök környezetfüggő dúsítása                          |
| **ActivityInsights**      | a tevékenységek kontextus szerinti elemzése a profilkészítés alapján              |
| **InvestigationPriority** | anomália pontszám, 0-10 között (0 = jóindulatú, 10 = nagyon rendellenes)         |
|

A **UsersInsights**, a **DevicesInsights** és a **ActivityInsights** témakörben hivatkozott környezetfüggő bővítések teljes készletét a UEBA-bővítési [dokumentációban](ueba-enrichments.md)tekintheti meg.

### <a name="querying-behavior-analytics-data"></a>Viselkedés-elemzési adatbázis lekérdezése

A [KQL](/azure/data-explorer/kusto/query/)használatával lekérdezheti a viselkedési elemzési táblázatot.

Ha például egy olyan felhasználó összes olyan esetét szeretné megkeresni, amely nem tudott bejelentkezni egy Azure-erőforrásba, ahol a felhasználó először próbál csatlakozni egy adott országhoz, és az adott országból érkező kapcsolatok még nem gyakoriak a felhasználó társainál, a következő lekérdezést használhatja:

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>Felhasználói egyenrangú metaadatok – tábla és jegyzetfüzet

A felhasználói társak metaadatai fontos kontextust biztosítanak a fenyegetések észleléséhez, az incidensek kivizsgálásához és a potenciális fenyegetések vadászatához. A biztonsági elemzők megfigyelheti a felhasználói társainak szokásos tevékenységeit annak megállapítására, hogy a felhasználó tevékenységei szokatlanok-e a saját társaihoz képest.

Az Azure Sentinel kiszámítja és rangsorolja a felhasználó társait a felhasználó Azure AD biztonsági csoportjának tagsága, levelezési listája és satöbbi alapján, valamint a 1-20-as számú társat a **UserPeerAnalytics** táblában tárolja. Az alábbi képernyőképen látható a UserPeerAnalytics tábla sémája, és a felhasználó Kendall Collins első nyolc rangsorolt társa jelenik meg. Az Azure Sentinel a *Frequency-inverz Document Frequency* (TF-IDF) algoritmust használja a besorolás kiszámításához: minél kisebb a csoport, annál nagyobb a súly. 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="Képernyőfelvétel a felhasználói társak metaadat-táblájáról":::

A felhasználói társak metaadatainak megjelenítéséhez használhatja az Azure Sentinel GitHub-adattárában elérhető [Jupyter notebookot](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) . A jegyzetfüzet használatával kapcsolatos részletes utasításokért tekintse meg az [interaktív elemzés – felhasználói biztonsági metaadatokat](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) tartalmazó jegyzetfüzetet.

### <a name="permission-analytics---table-and-notebook"></a>Engedély-elemzés – tábla és jegyzetfüzet

Az engedélyek elemzése segít meghatározni, hogy egy támadó milyen hatással lehet egy szervezeti eszköz veszélyeztetése. Ez a hatás az objektum "Blast RADIUS" néven is ismert. A biztonsági elemzők ezeket az információkat a nyomozások és az incidensek kezelésének rangsorolására használhatják.

Az Azure Sentinel meghatározza az adott felhasználó által az Azure-erőforrásokhoz biztosított közvetlen és tranzitív hozzáférési jogokat az Azure-előfizetések kiértékelésével, amelyet a felhasználó közvetlenül vagy csoportokon vagy egyszerű szolgáltatásokon keresztül érhet el. Ezt az információt, valamint a felhasználó Azure AD biztonsági csoportjának tagságának teljes listáját a **UserAccessAnalytics** táblában tárolja a rendszer. Az alábbi képernyőképen egy minta sor látható az Alex Johnson felhasználója számára a UserAccessAnalytics táblában. A **forrásoldali entitás** a felhasználói vagy egyszerű szolgáltatásnév, a **célként megadott entitás** pedig az az erőforrás, amelyhez a forrás entitás hozzáfér. A **hozzáférési szint** és a **hozzáférési típus** értékei a célként megadott entitás hozzáférés-vezérlési modelljétől függenek. Láthatja, hogy Alex közreműködői hozzáféréssel rendelkezik az Azure-előfizetés *contoso Hotels-bérlőhöz*. Az előfizetés hozzáférés-vezérlési modellje az Azure RBAC.   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="Képernyőfelvétel a felhasználói hozzáférés elemzési táblázatáról":::

A [Jupyter notebookot](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (a fent említett jegyzetfüzetet) az Azure Sentinel GitHub-adattárból is használhatja az engedélyek elemzési adatainak megjelenítéséhez. A jegyzetfüzet használatával kapcsolatos részletes utasításokért tekintse meg az [interaktív elemzés – felhasználói biztonsági metaadatokat](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) tartalmazó jegyzetfüzetet.

### <a name="hunting-queries-and-exploration-queries"></a>Vadászati lekérdezések és kutatási lekérdezések

Az Azure Sentinel a BehaviorAnalytics tábla alapján a vadászati lekérdezéseket, a feltárási lekérdezéseket és a munkafüzeteket is tartalmazza. Ezek az eszközök a rendellenes viselkedést jelző, konkrét használati esetekre összpontosító, dúsított adathalmazokat mutatnak be. 

További információ [a vadászatról és a vizsgálati gráfról](./hunting.md) az Azure sentinelben.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan működik az Azure Sentinel az entitások viselkedésének analitikai képességei. A megvalósítással kapcsolatos gyakorlati útmutatásért és a szerzett információk felhasználásához tekintse meg a következő cikkeket:

- Az [entitás viselkedés-elemzésének engedélyezése](./enable-entity-behavior-analytics.md) az Azure sentinelben.
- [A biztonsági fenyegetések vadászata](./hunting.md).
