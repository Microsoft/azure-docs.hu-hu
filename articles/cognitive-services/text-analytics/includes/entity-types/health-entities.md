---
title: Elnevezett entitások egészségügyi ellátáshoz
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 614d0fe69cee88791559758d5e08dda66672669b
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097267"
---
Az [egészségügyi Text Analytics](../../how-tos/text-analytics-for-health.md) a következő kategóriákban észleli az orvosi fogalmakat.  (Vegye figyelembe, hogy a tároló előzetes verziója csak az angol nyelvű szöveget támogatja, és az egyes tárolók rendszerképében csak egyetlen modell-verzió van megadva.)


| Kategória  | Leírás  |
|---------|---------|
| [ANATÓMIA](#anatomy) | a törzs-és anatómiai rendszerekkel, webhelyekkel, helyekkel vagy régiókkal kapcsolatos információkat rögzítő fogalmak. |
 | [Demográfia](#demographics) | a nemekkel és a korral kapcsolatos információkat rögzítő fogalmak. |
 | [AUDITÁLÁSBAN](#examinations) | a diagnosztikai eljárásokkal és tesztekkel kapcsolatos információkat rögzítő fogalmak. |
 | [GENOMICS](#genomics) | a génekkel és a változatokkal kapcsolatos információkat rögzítő fogalmak. |
 | [EGÉSZSÉGÜGYI](#healthcare) | a felügyeleti eseményekkel, a gondozási környezetekkel és az egészségügyi szakmákkal kapcsolatos információkat rögzítő fogalmak. |
 | [ORVOSI FELTÉTEL](#medical-condition) | a diagnosztizálással, tünetekkel vagy jelekkel kapcsolatos információkat rögzítő fogalmak. |
 | [GYÓGYSZERT](#medication) | a gyógyszerekre vonatkozó információkat rögzítő fogalmak, beleértve a gyógyszerek nevét, osztályait, adagolását és az adminisztrációs útvonalakat. |
 | [SZOCIÁLIS](#social) | fogalmak, amelyek rögzítik az orvosilag releváns közösségi szempontokkal kapcsolatos információkat, például a családi kapcsolatot. |
 | [KEZELÉS](#treatment) | a terápiás eljárásokkal kapcsolatos információkat rögzítő fogalmak. |
  
Az egyes kategóriák két fogalmi csoportot tartalmazhatnak:

* **Entitások** – olyan feltételek, amelyek olyan orvosi fogalmakat rögzítenek, mint például a diagnosztika, a gyógyszer neve vagy a kezelés neve.  A *bronchitis* például a diagnosztika, és az *aszpirin* a gyógyszer neve.  Az ebben a csoportban megjelenő említést UMLS-koncepcióhoz lehet kapcsolni.
* **Attribútumok** – az észlelt entitással kapcsolatos további információkat biztosító kifejezések (például: *súlyos* ) a *bronchitis* vagy a *81 mg* feltétele az *aszpirin* adagja.  Az ebben a kategóriában szereplő említés nem lesz társítva egy UMLS-koncepció-AZONOSÍTÓhoz.

Emellett a szolgáltatás felismeri a különböző fogalmak közötti kapcsolatokat, beleértve az attribútumok és az entitások közötti kapcsolatokat *, például a* *törzs szerkezetének* vagy a *gyógyszert a gyógyszer neve* *és az* entitások közötti kapcsolatok között, például a rövidítések észlelésében.

## <a name="anatomy"></a>Anatómia

### <a name="entities"></a>Entitások

**BODY_STRUCTURE** – törzsrendszer, anatómiai helyek vagy régiók, valamint törzsi webhelyek. Például: ARM, térd, has, orr, máj, fej, légzőszervi rendszer, limfociták.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Példa a törzs szerkezetének entitására.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Kibővített példa a törzs szerkezetének entitására.":::

### <a name="attributes"></a>Attribútumok

**Írásirányt** képező kifejezések, például: Left, laterális, Upper, posterior, amely a törzs szerkezetét jellemzi.

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="Példa irányított attribútumra.":::

### <a name="supported-relations"></a>Támogatott kapcsolatok

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>Demográfia

### <a name="entities"></a>Entitások

**Kor** – minden életkori kifejezés és kifejezés, beleértve a betegeket, a családtagokat és másokat is. Például: 40 éves, 51 yo, 3 hónapos, felnőtt, csecsemő, idős, fiatal, kiskorú, középkorú.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Egy példa az Age entitásra.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Egy másik példa az Age entitásra.":::


**Nemre** vonatkozó feltételek, amelyek felfedik a tárgy nemet. Például: férfi, nő, nő, úriember, Lady.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Példa a nemek közötti entitásokra.":::

### <a name="attributes"></a>Attribútumok

**RELATIONAL_OPERATOR** – a demográfiai entitás és a további információk közötti kapcsolatot kifejező kifejezések.

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="Egy példa a viszonyítási operátorra.":::

## <a name="examinations"></a>Vizsgálatok

### <a name="entities"></a>Entitások

**EXAMINATION_NAME** – diagnosztikai eljárások és tesztek. Például: MRI, EKG, HIV-teszt, hemoglobin, vérlemezkék száma, méretezési rendszerek, például a *Bristol széklet Scale*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Példa vizsga entitásra.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Egy másik példa a vizsga neve entitásra.":::

### <a name="attributes"></a>Attribútumok

**Irány** – a vizsgálatot jellemző irányok.

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="Egy példa egy Direction attribútumra, amely egy vizsga nevű entitást mutat be.":::

**MEASUREMENT_UNIT** – a vizsgálat egysége. Például a *hemoglobin > 9,5 g/DL* értékben a *g/DL* kifejezés a *hemoglobin* -teszt egysége.

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="Példa egy mérési egység attribútumra egy vizsgáztató nevű entitással.":::

**MEASUREMENT_VALUE** – a vizsgálat értéke. Például a *hemoglobin > 9,5 g/DL* értékben a *9,5* kifejezés a *hemoglobin* -teszt értéke.

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="Példa egy mérési név entitást tartalmazó mérési érték attribútumra.":::

**RELATIONAL_OPERATOR** – a vizsgálat és a további információk közötti kapcsolatot kifejező kifejezéseket. Például a megcélzott vizsgálathoz szükséges mérési érték.

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="Egy példa egy relációs operátorra, amely egy vizsgáztató nevű entitást mutat be.":::

**Idő** – a vizsgálat kezdő és/vagy hosszára (időtartamára) vonatkozó időbeli feltételek. Például, ha a teszt bekövetkezett.

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="Egy példa a vizsga nevű entitással rendelkező Time attribútumra.":::

### <a name="supported-relations"></a>Támogatott kapcsolatok

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entitások

**Gén** – a gének összes említése. Például: MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Példa egy gén entitásra.":::

**Variant** – a gén összes variációjának említése. Például: c. 524C>T, (MTRR): r.1462_1557del96
  
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

### <a name="attributes"></a>Attribútumok

**CONDITION_QUALIFIER** az orvosi feltételek leírására szolgáló minőségi feltételeket. A következő alkategóriák minősülnek minősítőknek:

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

A törzs orvosi feltételeit jellemző **irányok** iránya.

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="Példa egy Direction attribútumra egy orvosi feltétel entitással.":::

**Gyakoriság** – milyen gyakran fordul elő vagy fordul elő az orvosi feltételek.

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="Példa egy orvosi feltétel entitást tartalmazó Frequency attribútumra.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="Egy másik példa egy Direction attribútumra, egy tünettel vagy egy aláírási entitással.":::

**MEASUREMENT_UNIT** – az orvosi feltételt jellemző egység. Például *1.5 x2x1 cm daganatban* a *cm* kifejezés a *tumor* mértékegysége. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="Példa az orvosi feltétel entitást tartalmazó mérési egység attribútumra.":::

**MEASUREMENT_VALUE** – az orvosi állapotot jellemző érték. Például *1.5 x2x1 cm-es tumorban* az *1.5 x2x1* a *tumor* mérési értéke. 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="A képernyőképen egy példa látható egy olyan Direction attribútumra, amely egy tünetet vagy egy aláírási entitást mutat be.":::

**RELATIONAL_OPERATOR** – az orvosi feltételekkel kapcsolatos további információk közötti kapcsolatot kifejező kifejezések. Például az idő vagy a mérték értéke. 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="A képernyőfelvétel egy másik példát mutat be egy olyan Direction attribútumra, amely egy tünet vagy egy aláíró entitás.":::

Az orvosi feltételek kezdetének és/vagy hosszának (időtartamának **) időbeli időbeli** feltételei. Például amikor egy tünet elindult (kialakulás) vagy egy betegség előfordulásakor.

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="A képernyőképen egy további példa látható egy Direction attribútumra, amely egy tünet vagy egy aláíró entitás.":::

### <a name="supported-relations"></a>Támogatott kapcsolatok

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>Gyógyszert

### <a name="entities"></a>Entitások

**MEDICATION_CLASS** – a hasonló beavatkozási mechanizmussal, a kapcsolódó művelettel, hasonló kémiai szerkezettel és/vagy az azonos betegség kezelésére használt gyógyszerek készlete. Például: ACE-gátló, opioid, antibiotikumok, fájdalomcsillapítók.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Példa gyógyszert osztályra entitásra.":::

**MEDICATION_NAME** – a gyógyszert említik, beleértve a szerzői jog nélküli márkaneveket és a nem márkanevek nevét. Például: Advil, ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Példa a gyógyszer neve entitásra.":::

### <a name="attributes"></a>Attribútumok

**Adagolás** – a megrendelt gyógyszer mennyisége. Tegyük fel például, hogy *1000 ml*-es nátrium-klorid-megoldás.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Egy példa a gyógyszert dózis-attribútumra.":::

**Gyakoriság** – milyen gyakran kell gyógyszert felvenni.

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="Példa egy gyógyszeres gyakorisági attribútumra.":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="Egy másik példa a gyógyszert Frequency attribútumra.":::

**MEDICATION_FORM** – a gyógyszer formája. Például: megoldás, pirula, kapszula, tabletta, javítás, zselé, beillesztés, hab, spray, drops, Cream, szirup.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Példa egy gyógyszeres űrlap attribútumra.":::

**MEDICATION_ROUTE** – a gyógyszert felügyeleti módszere. Például: szóbeli, hüvelyi, IV, epidurális, helyileg belélegzett.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Egy példa a gyógyszert Route attribútumra.":::

**RELATIONAL_OPERATOR** – a gyógyszerek és a további információk közötti kapcsolatot kifejező kifejezések. Például a szükséges mérési érték.

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="A képernyőképen egy olyan relációs operátori attribútum látható, amely egy gyógyszeres entitást mutat be.":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="A képernyőképen egy, a relációs operátor attribútumának egy másik példája látható egy gyógyszeres entitással.":::

### <a name="supported-relations"></a>Támogatott kapcsolatok

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**

## <a name="social"></a>Közösségi

### <a name="entities"></a>Entitások

**FAMILY_RELATION** – a tárgy rokonainak említése. Például: Atya, lánya, testvér, szülők.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="A képernyőfelvétel egy másik példát mutat be a kezelési idő attribútumra.":::

## <a name="treatment"></a>Kezelés

### <a name="entities"></a>Entitások

**TREATMENT_NAME** – terápiás eljárások. Például a térd-csere sebészet, a csontvelő-transzplantáció, a TAVI, az étrend.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Példa a kezelési név entitásra.":::

### <a name="attributes"></a>Attribútumok

A kezelést jellemző, **irányt** meghatározó kifejezések.

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="A képernyőképen egy példa látható a kezelési irány attribútumra.":::

**Gyakoriság** – milyen gyakran történik a kezelés, vagy ha ez történik.

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="A képernyőképen a kezelési irány attribútum egy másik példája látható.":::
 
**RELATIONAL_OPERATOR** – a kezelés és a további információk közötti kapcsolatot kifejező kifejezések.  Például az előző eljárásból mennyi idő telt el.

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="Példa a kezelési kapcsolatok kezelői attribútumára.":::

A kezelés kezdetére és/vagy hosszára (időtartamára) **vonatkozó időbeli feltételek** . Például az a dátum, amikor a kezelést megadták.

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="A képernyőfelvétel a kezelési idő attribútumára mutat példát.":::

### <a name="supported-relations"></a>Támogatott kapcsolatok

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**
