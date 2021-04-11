---
title: Az incidensek kezelésének automatizálása az Azure Sentinelben | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható az automatizálási szabályok az incidensek kezelésének automatizálásához, hogy maximalizálja a SOC hatékonyságát és hatékonyságát a biztonsági fenyegetésekre adott válaszként.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 1ff9fbbb6cd4b8827555a6cb1b222ed4eb0a5299
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609363"
---
# <a name="automate-incident-handling-in-azure-sentinel-with-automation-rules"></a>Az incidensek kezelésének automatizálása az Azure Sentinelben automatizálási szabályokkal

Ez a cikk bemutatja, hogy az Azure Sentinel Automation szabályai hogyan használhatók, és hogyan használhatja őket a biztonsági előkészítési, automatizálási és reagálási műveletek megvalósítására, a SOC hatékonyságának növelésére és az idő és erőforrások megtakarítására.

> [!IMPORTANT]
>
> - Az **Automation-szabályok** szolgáltatás jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

## <a name="what-are-automation-rules"></a>Mik azok az Automation-szabályok?

Az Automation-szabályok az Azure Sentinel új koncepciója. Ez a funkció lehetővé teszi a felhasználók számára, hogy központilag kezeljék az incidensek kezelésének automatizálását. Amellett, hogy az incidensekhez (nem csak az előtte lévő riasztásokhoz) rendel hozzá forgatókönyveket, az Automation-szabályok lehetővé teszik a több elemzési szabályra adott válaszok automatizálását, automatikus címkézését, hozzárendelését vagy bezárását, és a végrehajtott műveletek sorrendjét. Az automatizálási szabályok egyszerűsítik az automatizálási használatot az Azure Sentinelben, és lehetővé teszik az incidensek összehangolása folyamataihoz tartozó összetett munkafolyamatok egyszerűsítését.

## <a name="components"></a>Összetevők

Az Automation-szabályok több összetevőből állnak:

### <a name="trigger"></a>Eseményindító

Az Automation-szabályokat egy incidens létrehozásával indítja el a rendszer. 

A felülvizsgálathoz az incidenseket az elemzési szabályok által küldött riasztások alapján hozza létre, amelyeknek négy típusa van, ahogy azt az oktatóanyag az [Azure Sentinel beépített elemzési szabályaival kapcsolatos fenyegetések észlelése](tutorial-detect-threats-built-in.md)terén ismerteti.

### <a name="conditions"></a>Feltételek

A feltételek összetett csoportjai határozzák meg, hogy mikor kell futtatni a műveleteket (lásd alább). Ezek a feltételek általában az incidensek és az entitások attribútumainak állapotán vagy értékein alapulnak, és tartalmazhatnak `AND` / `OR` / `NOT` / `CONTAINS` operátorokat is.

### <a name="actions"></a>Műveletek

A műveletek akkor futtathatók, ha a feltételek (lásd fentebb) teljesülnek. Megadhat számos műveletet egy szabályban, és kiválaszthatja a futtatásuk sorrendjét (lásd alább). Az alábbi műveleteket az automatizálási szabályokkal lehet meghatározni, anélkül, hogy a forgatókönyv [speciális funkcióit](automate-responses-with-playbooks.md)kellene használni:

- Incidens állapotának módosítása, a munkafolyamat naprakészen tartása.

  - Ha a "lezárva" értékre változik, adja meg a [záró okot](tutorial-investigate-cases.md#closing-an-incident) , és adjon hozzá egy megjegyzést. Ez segít a teljesítmény és a hatékonyság nyomon követésében, és a téves pozitív hatás csökkentése érdekében.

- Egy incidens súlyosságának módosítása – az incidensben részt vevő entitások jelenléte, távolléte, értékei vagy attribútumai alapján újraértékelheti és áttekintheti a prioritásokat.

- Incidens kiosztása egy tulajdonoshoz – ez segít az incidensek közvetlen típusait a velük foglalkozó személyeknek, illetve a legtöbb elérhető személynek.

- Címke hozzáadása egy incidenshez – ez akkor hasznos, ha a tárgy, a támadó vagy bármely más közös nevező alapján osztályozza az incidenseket.

Emellett megadhat egy, a forgatókönyv futtatására szolgáló műveletet [is, hogy](tutorial-respond-threats-playbook.md)összetettebb választ lehessen tenni, beleértve a külső rendszerekkel kapcsolatos műveleteket is. Az Automation-szabályokban csak az [incidens trigger](automate-responses-with-playbooks.md#azure-logic-apps-basic-concepts) által aktivált forgatókönyvek használhatók. Meghatározhat egy olyan műveletet, amely több forgatókönyvt is tartalmaz, valamint forgatókönyvek és egyéb műveletek kombinációját, valamint a futtatásuk sorrendjét.

### <a name="expiration-date"></a>Lejárat dátuma

Egy automatizálási szabályban meghatározhat egy lejárati dátumot is. A szabály ezen dátum után le lesz tiltva. Ez hasznos a tervezett, időkorlátos tevékenységek, például a behatolási tesztek által okozott "zaj" incidensek kezeléséhez (azaz zárásához).

### <a name="order"></a>Sorrend

Megadhatja azt a sorrendet, amelyben az Automation-szabályok futnak. A későbbi automatizálási szabályok az incidens állapotát az előző automatizálási szabályoknak megfelelően kiértékelik.

Ha például az "első Automation-szabály" módosította az incidens súlyosságát közepes és alacsony értékre, és a "második Automation-szabály" úgy van definiálva, hogy csak közepes vagy magasabb súlyosságú incidensekre fusson, akkor az incidensen nem fog futni.

## <a name="common-use-cases-and-scenarios"></a>Gyakori használati esetek és forgatókönyvek

### <a name="incident-triggered-automation"></a>Incidens által aktivált automatizálás

Eddig csak a riasztások indíthatnak automatizált választ a forgatókönyvek használatával. Az Automation-szabályokkal az incidensek mostantól aktiválják az automatikus reagálási láncokat, amelyekben új incidensek által aktivált forgatókönyvek is szerepelhetnek ([speciális engedélyek szükségesek](#permissions-for-automation-rules-to-run-playbooks)) az incidensek létrehozásakor. 

### <a name="trigger-playbooks-for-microsoft-providers"></a>Forgatókönyvek elindítása Microsoft-szolgáltatók számára

Az Automation-szabályok lehetővé teszik a Microsoft biztonsági riasztások kezelésének automatizálását, ha ezeket a szabályokat a riasztásokból létrehozott incidensekre alkalmazza. Az Automation-szabályok meghívhatják a forgatókönyveket ([különleges engedélyek szükségesek](#permissions-for-automation-rules-to-run-playbooks)), és az összes adatuk, beleértve a riasztásokat és az entitásokat is, a rájuk vonatkozó információkat továbbítják. Általánosságban elmondható, hogy az Azure Sentinel ajánlott eljárásai az incidensek várólistáját használják a biztonsági műveletek központi elemeként.

A Microsoft biztonsági riasztásai többek között a következők:

- Microsoft Cloud App Security (MCAS)
- Azure AD Identity Protection
- Azure Defender (ASC)
- Defender for IoT (korábban ASC a IoT esetében)
- Microsoft Defender az Office 365-höz (korábbi nevén Office 365 ATP)
- Microsoft Defender végponthoz (korábbi nevén MDATP)
- Microsoft Defender identitáshoz (korábbi nevén Azure ATP)

### <a name="multiple-sequenced-playbooksactions-in-a-single-rule"></a>Több szekvenciális forgatókönyv/művelet egyetlen szabályban

Most már majdnem teljes mértékben vezérelheti a műveletek és a forgatókönyvek végrehajtási sorrendjét egyetlen automatizálási szabályban. Az automatizálási szabályok végrehajtásának sorrendjét is szabályozhatja. Ez lehetővé teszi, hogy nagy mértékben leegyszerűsítse a forgatókönyveit, csökkentse azokat egyetlen feladatra vagy egy kis, egyszerű feladatok végrehajtására, és kombinálja ezeket a kis forgatókönyveket különböző összetett automatizálási szabályokban.

### <a name="assign-one-playbook-to-multiple-analytics-rules-at-once"></a>Egy forgatókönyv kiosztása egyszerre több elemzési szabályhoz

Ha van olyan feladata, amelyet szeretne automatizálni az összes Analytics-szabályra vonatkozóan – tegyük fel, hogy a támogatási jegyet egy külső jegyrendszer keretében hozza létre – egyetlen forgatókönyvt alkalmazhat az Analytics-szabályok bármelyikére vagy mindegyikére – beleértve az összes jövőbeli szabályt is – egy lövéssel. Ez egyszerű, de ismétlődő karbantartási és kitakarítási feladatokat tesz elérhetővé sokkal kevesebb házimunka esetén.

### <a name="automatic-assignment-of-incidents"></a>Incidensek automatikus hozzárendelése

Az incidenseket automatikusan hozzárendelheti a jobb oldali tulajdonoshoz. Ha a SOC olyan elemzővel rendelkezik, amely egy adott platformra specializálódott, akkor a platformmal kapcsolatos incidensek automatikusan hozzárendelhetők az adott elemzőhöz.

### <a name="incident-suppression"></a>Incidensek letiltása

A szabályok segítségével automatikusan megoldhatók az ismert hamis/jóindulatú pozitív események a forgatókönyvek használata nélkül. Például a penetrációs tesztek futtatása, az ütemezett karbantartás vagy a frissítések végrehajtása, illetve az automatizálási eljárások tesztelése során számos hamis pozitív incidenst lehet létrehozni, amelyet a SOC figyelmen kívül hagy. Az időkorlátos automatizálási szabályok automatikusan lezárták ezeket az incidenseket a létrehozásuk során, miközben megjelölik őket a generációjuk okának leírásával.

### <a name="time-limited-automation"></a>Időkorlátos automatizálás

Az Automation-szabályokhoz lejárati dátumokat adhat hozzá. Előfordulhat, hogy az incidensek elnyomása nem indokolja meg az időkorlátos automatizálást. Előfordulhat, hogy egy adott típusú incidenst szeretne hozzárendelni egy adott felhasználóhoz (például egy gyakornokhoz vagy egy tanácsadóhoz) egy adott időszakra vonatkozóan. Ha az időkeret előre ismert, a szabály a relevancia végén letiltható, anélkül, hogy erre kellene emlékeznie.

### <a name="automatically-tag-incidents"></a>Incidensek automatikus címkézése

A kiválasztott feltételek alapján automatikusan hozzáadhat ingyenes szöveges címkéket az incidensekhez, vagy kategorizálhatja azokat.

## <a name="automation-rules-execution"></a>Automatizálási szabályok végrehajtása

Az automatizálási szabályok sorrendben futnak, az Ön által meghatározott sorrend szerint. Az egyes automatizálási szabályok végrehajtása az előző futtatása után történik. Az Automation-szabályokon belül minden művelet szekvenciálisan fut a definiált sorrendben.

A forgatókönyvek esetében a forgatókönyvek megkezdése és a listán szereplő következő művelet között két perces késleltetés áll fenn.

### <a name="permissions-for-automation-rules-to-run-playbooks"></a>Az Automation-szabályok futtatásához szükséges engedélyek

Ha egy Azure Sentinel Automation-szabály egy forgatókönyv futtatását futtatja, egy speciális Azure Sentinel-szolgáltatásfiókot használ, amely kifejezetten engedélyezve van ehhez a művelethez. A fiók használata (a felhasználói fiókkal ellentétben) növeli a szolgáltatás biztonsági szintjét.

Ahhoz, hogy egy Automation-szabály futtasson egy forgatókönyvet, ennek a fióknak explicit engedélyekkel kell rendelkeznie ahhoz az erőforráscsoporthoz, amelyben a forgatókönyv található. Ezen a ponton bármely automatizálási szabály futtathatja az adott erőforráscsoport bármely forgatókönyvét.

Ha automatizálási szabályt konfigurál, és a **futtatási** forgatókönyvek futtatása műveletet ad meg, megjelenik a forgatókönyvek legördülő listája. Azok a forgatókönyvek, amelyekhez az Azure Sentinel nem rendelkezik engedéllyel, nem érhetők el ("szürkén jelenik meg"). Az "a forgatókönyvek **kezelése** " hivatkozásra kattintva engedélyezheti az Azure Sentinel engedélyt a helyszínen található forgatókönyvek erőforráscsoportok számára.

> [!NOTE]
> **Engedélyek egy több-bérlős architektúrában**
>
> Az Automation-szabályok teljes mértékben támogatják a több-munkaterületet és a több-bérlős üzemelő példányokat (több-bérlős esetében az [Azure Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)használatával).
>
> Ezért ha az Azure Sentinel üzembe helyezése több-bérlős architektúrát használ (például MSSP), akkor az egyik bérlőhöz tartozó Automation-szabály egy másik bérlőn futó forgatókönyv futtatására is használható, de a Sentinel futtatásához szükséges engedélyeket a-bérlőn kell meghatározni, nem abban a bérlőben, ahol az Automation-szabályokat meghatározták.

## <a name="creating-and-managing-automation-rules"></a>Automatizálási szabályok létrehozása és kezelése

Az Azure Sentinel felhasználói felületének különböző pontjairól származó automatizálási szabályokat az adott igénytől és használati esettől függően hozhatja létre és kezelheti.

- **Automatizálás panel**

    Az Automation-szabályok központilag kezelhetők az új **Automation** panelen ( **amely a** forgatókönyvek **panelt** váltja fel) az **Automation Rules** (forgatókönyvek) lapon. Innen új Automation-szabályokat hozhat létre, és szerkesztheti a meglévőket is. Az Automation-szabályok húzásával is megváltoztathatja a végrehajtás sorrendjét, és engedélyezheti vagy letilthatja azokat.

    Az **Automation** panelen megtekintheti a munkaterületen definiált összes szabályt, valamint az állapotukat (engedélyezve/letiltva), valamint azokat az elemzési szabályokat, amelyekre a rendszer alkalmazza őket.

    Ha olyan Automation-szabályra van szüksége, amely számos elemzési szabályra érvényes, akkor azt közvetlenül az **Automation** panelen hozza létre. A felső menüben kattintson a **Létrehozás** gombra, és **vegyen fel új szabályt**, amely megnyitja az **új Automation-szabály létrehozása** panelt. Itt teljes körű rugalmasságot biztosít a szabály konfigurálásához: bármely analitikai szabályra (beleértve a jövőbelieket is) alkalmazhatja, és meghatározhatja a feltételek és a műveletek széles körét.

- **Elemzési szabály varázsló**

    Az elemzési szabály varázsló **automatikus válasz** lapján megtekintheti, kezelheti és létrehozhatja azokat az Automation-szabályokat, amelyek érvényesek a varázslóban létrehozott vagy szerkesztett adott elemzési szabályra.

    Ha a **Létrehozás** gombra kattint, és a Szabálytípus (**ütemezett lekérdezési szabály** vagy a **Microsoft incidens-létrehozási szabály**) elemre kattint az **elemzés** panel felső menüjében, vagy ha meglévő analitikai szabályt választ, és a **Szerkesztés** gombra kattint, megnyílik a szabály varázsló. Ha bejelöli az **automatikus válasz** lapot, megjelenik egy **incidens Automation** nevű szakasz, amelyben a szabályhoz jelenleg alkalmazott Automation-szabályok jelennek meg. Kiválaszthat egy meglévő Automation-szabályt a szerkesztéshez, vagy kattintson az **új hozzáadása** lehetőségre egy új létrehozásához.

    Megfigyelheti, hogy az Automation-szabály létrehozásakor az **új automatizálási szabály létrehozása** panel az **elemzési szabály** feltételét nem elérhetőként jeleníti meg, mert ez a szabály már úgy van beállítva, hogy csak a varázslóban szerkesztett elemzési szabályra vonatkozzon. Az összes többi konfigurációs beállítás továbbra is elérhető.

- **Incidensek panel**

    Az **incidensek** panelen is létrehozhat egy Automation-szabályt, hogy egyetlen, ismétlődő incidensre válaszoljanak. Ez akkor hasznos, ha egy [letiltási szabályt](#incident-suppression) hoz létre a "zajos" incidensek automatikus bezárásához. Válasszon ki egy incidenst a várólistából, majd kattintson az **Automation-szabály létrehozása** lehetőségre a felső menüben.

    Megfigyelheti, hogy az **új Automation-szabály létrehozása** panel az incidensből származó értékekkel tölti fel az összes mezőt. A szabály neve megegyezik az incidens nevével, alkalmazza azt az eseményt létrehozó elemzési szabályra, és az incidensben lévő összes elérhető entitást a szabály feltételei szerint használja fel. Emellett alapértelmezés szerint egy letiltási (záró) műveletet is javasol, és a szabály lejárati dátumát javasolja. Igény szerint hozzáadhat vagy eltávolíthat feltételeket és műveleteket, valamint módosíthatja a lejárati dátumot.

## <a name="auditing-automation-rule-activity"></a>Naplózási automatizálási szabály tevékenysége

Érdemes lehet megismernie, hogy mi történt egy adott incidenssel, és hogy egy adott Automation-szabály milyen lehet vagy nem tette meg. Az incidens-krónikák teljes rekordja elérhető a **naplók** panel *biztonsági incidens* táblájában. Az összes Automation-szabály tevékenységének megtekintéséhez használja a következő lekérdezést:

```kusto
SecurityIncident
| where ModifiedBy contains "Automation"
```

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan használható az Automation-szabályok az Azure Sentinel-incidensek várólistájának kezeléséhez és néhány alapvető incidens-kezelési automatizálás megvalósításához.

- További információ a speciális automatizálási lehetőségekről: a [veszélyforrások elleni reagálás automatizálása az Azure sentinelben](automate-responses-with-playbooks.md).
- Az Automation-szabályok és a forgatókönyvek megvalósításával kapcsolatos segítségért tekintse meg az [oktatóanyag: forgatókönyvek használata a fenyegetési válaszok automatizálásához az Azure sentinelben](tutorial-respond-threats-playbook.md)című témakört.
