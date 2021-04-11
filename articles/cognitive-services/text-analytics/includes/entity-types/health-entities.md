---
title: Elnevezett entitások egészségügyi ellátáshoz
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 805c726d33f2050f6f2797c0689069aa5ec4ee71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599319"
---
[Text Analytics az állapotadatok](../../how-tos/text-analytics-for-health.md) feldolgozásához és a strukturálatlan orvosi adatokból származó elemzések kinyeréséhez. A szolgáltatás észleli és felfedi az orvosi fogalmakat, kijelöli a fogalmakat, és összeállítja a fogalmak közötti szemantikai kapcsolatokat, és összekapcsolja őket a közös orvosi ontológiákat.

Az egészségügyi Text Analytics a következő kategóriákban észleli az orvosi fogalmakat. Ebben az előzetes verzióban csak az angol nyelvű szöveg támogatott, és csak egyetlen modell verziója érhető el.

| Kategória  | Leírás  |
|---------|---------|
| [ANATÓMIA](#anatomy) | a törzs-és anatómiai rendszerekkel, helyekkel, helyekkel vagy régiókkal kapcsolatos információkat rögzítő fogalmak. |
 | [Demográfia](#demographics) | a nemekkel és a korral kapcsolatos információkat rögzítő fogalmak. |
 | [AUDITÁLÁSBAN](#examinations) | a diagnosztikai eljárásokkal és tesztekkel kapcsolatos információkat rögzítő fogalmak. |
 | [ÁLTALÁNOS ATTRIBÚTUMOK](#general-attributes) | fogalmak, amelyek további információkat nyújtanak a fenti kategóriákból származó egyéb fogalmakról. |
 | [GENOMICS](#genomics) | a génekkel és a változatokkal kapcsolatos információkat rögzítő fogalmak. |
 | [EGÉSZSÉGÜGYI](#healthcare) | a felügyeleti eseményekkel, a gondozási környezetekkel és az egészségügyi szakmákkal kapcsolatos információkat rögzítő fogalmak. |
 | [ORVOSI FELTÉTEL](#medical-condition) | a diagnosztizálással, tünetekkel vagy jelekkel kapcsolatos információkat rögzítő fogalmak. |
 | [GYÓGYSZERT](#medication) | a gyógyszerekre vonatkozó információkat rögzítő fogalmak, beleértve a gyógyszerek nevét, osztályait, adagolását és az adminisztrációs útvonalakat. |
 | [SZOCIÁLIS](#social) | fogalmak, amelyek rögzítik az orvosilag releváns közösségi szempontokkal kapcsolatos információkat, például a családi kapcsolatot. |
 | [KEZELÉS](#treatment) | a terápiás eljárásokkal kapcsolatos információkat rögzítő fogalmak. |

Alább további információkat és példákat talál.

## <a name="anatomy"></a>Anatómia

### <a name="entities"></a>Entitások

**BODY_STRUCTURE** – törzsrendszer, anatómiai helyek vagy régiók, valamint törzsi webhelyek. Például: ARM, térd, has, orr, máj, fej, légzőszervi rendszer, limfociták.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Példa a törzs szerkezetének entitására.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Kibővített példa a törzs szerkezetének entitására.":::

## <a name="demographics"></a>Demográfia

### <a name="entities"></a>Entitások

**Kor** – minden korcsoport és kifejezés, beleértve a betegeket, a családtagokat és másokat is. Például: 40 éves, 51 yo, 3 hónapos, felnőtt, csecsemő, idős, fiatal, kiskorú, középkorú.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Egy példa az Age entitásra.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Egy másik példa az Age entitásra.":::


**Nemre** vonatkozó feltételek, amelyek felfedik a tárgy nemet. Például: férfi, nő, nő, úriember, Lady.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Példa a nemek közötti entitásokra.":::

## <a name="examinations"></a>Vizsgálatok

### <a name="entities"></a>Entitások

**EXAMINATION_NAME** – diagnosztikai eljárások és tesztek, beleértve a létfontosságú jeleket és a szövegtörzs mértékét. Például: MRI, EKG, HIV-teszt, hemoglobin, vérlemezkék száma, méretezési rendszerek, például a *Bristol széklet Scale*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Példa vizsga entitásra.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Egy másik példa a vizsga neve entitásra.":::

## <a name="general-attributes"></a>Általános attribútumok

### <a name="entities"></a>Entitások

**Dátum** – az orvosi feltételekkel, a vizsgálattal, a kezeléssel, a gyógyszerekkel vagy a felügyeleti eseménnyel kapcsolatos teljes dátum.

**Irány** – a test struktúrájához, orvosi feltételhez, vizsgálathoz vagy kezeléshez kapcsolódó irányok, például: Left, laterális, Upper, posterior.

**Gyakoriság** – leírja, hogy milyen gyakran fordulnak elő az orvosi feltételek, a vizsgálat, a kezelés vagy a gyógyszert kiváltó állapot, illetve előfordulnak-e.

**MEASUREMENT_VALUE** – egy vizsgához vagy orvosi feltételhez kapcsolódó érték.

**MEASUREMENT_UNIT** – egy vizsgálathoz vagy orvosi feltételhez kapcsolódó mérési egység.

**RELATIONAL_OPERATOR** – az entitások és néhány további információ közötti mennyiségi kapcsolatot kifejező kifejezések.

Az orvosi feltétel, a vizsga, a kezelés, a gyógyszer vagy a közigazgatási esemény kezdetének és/vagy hosszának (időtartamának **) időbeli feltételei** . 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entitások

**GENE_OR_PROTEIN** – az emberi gének neveinek és szimbólumainak, valamint a kromoszómák és a fehérjék részeinek minden említése. Például: MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Példa egy gén entitásra.":::

**Variant** – a gén-változatok és a mutációk összes említése. Például: `c.524C>T` , `(MTRR):r.1462_1557del96`
  
## <a name="healthcare"></a>Egészségügy

### <a name="entities"></a>Entitások
  
**ADMINISTRATIVE_EVENT** – az egészségügyi rendszerhez kapcsolódó események, de adminisztratív/félig adminisztratív jellegűek. Például: regisztráció, beléptetés, próbaverzió, tanulmányi bejegyzés, átvitel, kivonás, kórházi ellátás, kórházi tartózkodás. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Példa az egészségügyi események entitására.":::

**CARE_ENVIRONMENT** – olyan környezet vagy hely, ahol a betegek törődnek. Például: vészhelyzeti helyiség, orvosi rendelő, cardio-egység, hospice, kórház.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Ez a képernyőkép egy egészségügyi környezeti entitás példáját mutatja be.":::

**HEALTHCARE_PROFESSION** – az egészségügyi szakember licenccel vagy nem licenccel rendelkezik. Például: fogorvos, patológus, neurológus, radiológus, gyógyszerész, táplálkozási, fizikai terapeuta, csontkovács.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Ez a képernyőkép egy egészségügyi környezeti entitás egy másik példáját mutatja be.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Egy másik példa az egészségügyi környezet entitására.":::

## <a name="medical-condition"></a>Orvosi feltétel

### <a name="entities"></a>Entitások

**Diagnosztika** – betegség, szindróma, mérgezés. Például: mellrák, Alzheimer-kór, HTN, CHF, gerincvelői sérülés.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Példa egy orvosi feltétel entitásra.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Egy másik példa egy orvosi feltétel entitásra.":::

**SYMPTOM_OR_SIGN** – a betegség vagy más Diagnosztizálás szubjektív vagy tárgyilagos bizonyítéka. Például a mellkasi fájdalom, a fejfájás, a szédülés, a kiütések, az ZOKOG, a has lágy, jó székletürítés, jól táplált.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Ilyen például egy orvosi feltétel aláírása vagy tünet entitása.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Egy másik példa egy orvosi feltétel aláírása vagy tünet entitására.":::

**CONDITION_QUALIFIER** – az orvosi feltételek leírására szolgáló minőségi kifejezések. A következő alkategóriák minősülnek minősítőknek:

1.  Időhöz kapcsolódó kifejezések: ezek az idődimenziót minőségileg leíró kifejezések, például hirtelen, akut, krónikus, régi. 
2.  Minőségi kifejezések: azok a feltételek, amelyek leírják az orvosi feltétel "természetét", például égő, éles.
3.  Súlyossági kifejezések: súlyos, enyhe, egy kicsit, nem ellenőrzött.
4.  Extensivity kifejezések: helyi, központi, diffúz.
5.  Sugárzási kifejezések: sugárzók, sugárzások.
6.  Feltétel méretezése: bizonyos esetekben a feltételt egy skála jellemzi, amely az értékek véges rendezett listája. Például a betegek a III. fázis hasnyálmirigy-rákos megbetegedéssel.
7.  Feltétel tanfolyam: olyan kifejezés, amely egy feltétel tanfolyamára vagy előrehaladására vonatkozik, például a fejlesztésre, a romlásra, a felbontásra és a visszaküldetésre. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Példa egy feltétel minősítő attribútumára és egy diagnosztikai entitásra.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Egy másik példa a feltétel minősítő attribútumára és egy diagnosztikai entitásra.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Példa egy feltételt minősítő attribútumra, amely tüneti és gyógyszeres entitásokat is jelez.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Ez a képernyőkép egy, a feltételhez tartozó minősítő attribútum egy másik példáját mutatja be egy diagnosztikai entitással.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Ez a képernyőkép egy további példát mutat be a feltételt minősítő attribútumra egy diagnosztikai entitással.":::

## <a name="medication"></a>Gyógyszert

### <a name="entities"></a>Entitások

**MEDICATION_CLASS** – a hasonló beavatkozási mechanizmussal, a kapcsolódó művelettel, hasonló kémiai szerkezettel és/vagy az azonos betegség kezelésére használt gyógyszerek készlete. Például: ACE-gátló, opioid, antibiotikumok, fájdalomcsillapítók.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Példa gyógyszert osztályra entitásra.":::

**MEDICATION_NAME** – a gyógyszert említik, beleértve a szerzői jog nélküli márkaneveket és a nem márkanevek nevét. Például: ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Példa a gyógyszer neve entitásra.":::

**Adagolás** – a megrendelt gyógyszer mennyisége. Tegyük fel például, hogy *1000 ml*-es nátrium-klorid-megoldás.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Egy példa a gyógyszert dózis-attribútumra.":::

**MEDICATION_FORM** – a gyógyszer formája. Például: megoldás, pirula, kapszula, tabletta, javítás, zselé, beillesztés, hab, spray, drops, Cream, szirup.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Példa egy gyógyszeres űrlap attribútumra.":::

**MEDICATION_ROUTE** – a gyógyszert felügyeleti módszere. Például: szóbeli, hüvelyi, IV, epidurális, helyileg belélegzett.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Egy példa a gyógyszert Route attribútumra.":::

## <a name="social"></a>Közösségi

### <a name="entities"></a>Entitások

**FAMILY_RELATION** – a tárgy rokonainak említése. Például: Atya, lánya, testvér, szülők.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="A képernyőfelvétel egy másik példát mutat be a kezelési idő attribútumra.":::

## <a name="treatment"></a>Kezelés

### <a name="entities"></a>Entitások

**TREATMENT_NAME** – terápiás eljárások. Például a térd-csere sebészet, a csontvelő-transzplantáció, a TAVI, az étrend.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Példa a kezelési név entitásra.":::
