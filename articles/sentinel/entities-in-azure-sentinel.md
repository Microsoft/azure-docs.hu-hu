---
title: Entitások használata az Azure Sentinelben tárolt adatbesoroláshoz és-elemzéshez | Microsoft Docs
description: Entitás-besorolások (felhasználók, állomásnevek, IP-címek) az Azure Sentinelben lévő adatelemekhez való hozzárendelésével, valamint a különböző forrásokból származó adatok összehasonlítására, elemzésére és összekapcsolására használhatók.
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
ms.openlocfilehash: 43da1af7a3001d7f8e000a878948428a3d63aa4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456247"
---
# <a name="classify-and-analyze-data-using-entities-in-azure-sentinel"></a>Az Azure Sentinel-entitások használatával osztályozhatja és elemezheti az adatelemzést

## <a name="what-are-entities"></a>Mik azok az entitások?

Ha a riasztásokat az Azure Sentinel küldi vagy hozza létre, azok olyan adatelemeket tartalmaznak, amelyekkel a Sentinel felismerheti és osztályozhatja a kategóriákat **entitásként**. Ha az Azure Sentinel tudomásul veszi, hogy milyen típusú entitást képvisel egy adott adatelem, a megfelelő kérdéseket tesz fel az adatforrások teljes skáláján, és képes összehasonlítani az adott elemmel kapcsolatos elemzéseket, és könnyen nyomon követheti a teljes Sentinel-élményt – elemzés, vizsgálat, szervizelés, vadászat stb. Az entitások néhány gyakori példája a felhasználók, a gazdagépek, a fájlok, a folyamatok, az IP-címek és az URL-címek.

### <a name="entity-identifiers"></a>Entitás-azonosítók

Az Azure Sentinel számos különböző típusú entitást támogat. Minden típushoz saját egyedi attribútumok tartoznak, beleértve az egyes entitások azonosítására szolgáló adatokat is. Ezek az attribútumok mezőkként jelennek meg az entitásban, és **azonosítóknak** nevezzük őket. Tekintse meg a támogatott entitások és azonosítóik teljes listáját alább.

#### <a name="strong-and-weak-identifiers"></a>Erős és gyenge azonosítók

Amint azt fentebb említettük, az egyes entitásokhoz mezők vagy mezők halmaza is azonosítható. Ezek a mezők vagy mezők olyan **erős azonosítóknak** is lehetnek, amelyek egyedi módon azonosíthatják az entitásokat anélkül, hogy kétértelműek vagy **gyenge azonosítók** legyenek, ha bizonyos körülmények között azonosítják az entitásokat, de nem garantált, hogy minden esetben egyedileg azonosítják az entitásokat. Sok esetben azonban a gyenge azonosítók kiválasztásával egy erős azonosító hozható létre.

A felhasználói fiókok például több módon is azonosíthatók **fiók** entitásként: egyetlen **erős termékazonosító** , például egy Azure ad-fiók numerikus azonosítójának ( **GUID** -mezője) vagy az egyszerű felhasználónév **(UPN)** értékének, vagy másik lehetőségként a **gyenge azonosítók** kombinációjának használatával, például a **neve** és a **NTDomain** mezőiben. A különböző adatforrások különböző módokon tudják azonosítani ugyanazt a felhasználót. Ha az Azure Sentinel két entitást talál, amelyeket az azonosítójuk alapján azonos entitásként ismer fel, akkor egyetlen entitásba egyesíti a két entitást, hogy megfelelően és következetesen kezelhető legyen.

Ha azonban az egyik erőforrás-szolgáltató olyan riasztást hoz létre, amelyben egy entitás nem eléggé azonosítható – például csak egyetlen **gyenge azonosítót** használ, például a tartománynevet nem tartalmazó felhasználónevet –, akkor a felhasználói entitás nem egyesíthető ugyanazzal a felhasználói fiók más példányaival. Ezek a példányok külön entitásként azonosíthatók, és ezek a két entitások nem egységesek, hanem különállóak maradnak.

Ennek kockázatának csökkentése érdekében győződjön meg arról, hogy az összes riasztási szolgáltató megfelelően azonosítja az entitásokat az általuk előállított riasztásokban. Emellett a felhasználói fiókok entitásait Azure Active Directory is szinkronizálhatja, így létrehozhat egy egyesítő könyvtárat, amely egyesítheti a felhasználói fiókok entitásait.

#### <a name="supported-entities"></a>Támogatott entitások

Az Azure Sentinel jelenleg a következő típusú entitásokat azonosítja:

- Felhasználói fiók
- Gazdagép
- IP-cím
- Kártevő szoftver
- Fájl
- Folyamat
- Felhőbeli alkalmazás
- Tartománynév
- Azure-erőforrás
- Fájlkivonat
- Beállításkulcs
- Beállításazonosító
- Biztonsági csoport
- URL-cím
- IoT-eszköz
- Mailbox
- Levelezési fürt
- Levélüzenet
- E-mailek küldése

Ezek az entitások azonosítói és egyéb releváns információk is megtekinthetők az [entitások hivatkozásában](entities-reference.md).

## <a name="entity-mapping"></a>Entitás-hozzárendelés

Hogyan ismeri fel az Azure Sentinel a riasztásban szereplő adatmennyiséget egy entitás azonosításával?

Nézzük meg, hogyan történik az adatfeldolgozás az Azure Sentinel szolgáltatásban. Az adatok különböző forrásokból, az [Összekötők](connect-data-sources.md), akár a szolgáltatások, az ügynökök vagy a syslog-szolgáltatás, valamint a naplózási továbbító használatával kerülnek betöltésre. Az adatait a rendszer a Log Analytics munkaterület tábláiban tárolja. Ezeket a táblázatokat a rendszer az Ön által meghatározott és engedélyezett elemzési szabályok alapján rendszeres időközönként lekérdezi. Az elemzési szabályok által végrehajtott számos művelet egyike a táblákban lévő adatmezők leképezése az Azure Sentinel által elismert entitásokra. Az elemzési szabályokban definiált leképezések szerint az Azure Sentinel a lekérdezés által visszaadott eredményekből veszi fel a mezőket, felismeri azokat az egyes entitások típusaihoz megadott azonosítók alapján, és alkalmazza őket az azonosítók által azonosított entitás típusára.

Mi a lényege?

Ha az Azure Sentinel képes a különböző típusú adatforrásokból származó riasztásokban lévő entitások azonosítására, és különösen akkor, ha az egyes adatforrásokhoz vagy egy harmadik sémához gyakran használt erős azonosítókat használ, egyszerűen összekapcsolhatók a riasztások és az adatforrások között. Ezek a korrelációs funkciók segítségével gazdag adattárházat hozhatnak létre az entitásokról, és így stabil alapot biztosítanak a biztonsági műveletekhez.

Megtudhatja, hogyan képezhető le az [adatmezők entitásokra](map-data-fields-to-entities.md).

Megtudhatja, [hogy mely azonosítók erősen azonosítják az entitásokat](entities-reference.md).

## <a name="entity-pages"></a>Entitás lapjai

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

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan dolgozhat az entitásokkal az Azure Sentinelben. A megvalósítással kapcsolatos gyakorlati útmutatásért és a szerzett információk felhasználásához tekintse meg a következő cikkeket:

- Az [entitás viselkedés-elemzésének engedélyezése](./enable-entity-behavior-analytics.md) az Azure sentinelben.
- [A biztonsági fenyegetések vadászata](./hunting.md).
