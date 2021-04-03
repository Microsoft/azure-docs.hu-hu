---
title: A Designer modul hibáinak elhárítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan olvashatja el és elháríthatja az automatizált modul hibakódját a Azure Machine Learning Designerben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: troubleshooting
author: likebupt
ms.author: keli19
ms.date: 11/25/2020
ms.openlocfilehash: b917e3fc93c59de85c5236c18e31d7bbc9d891f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98065473"
---
# <a name="exceptions-and-error-codes-for-the-designer"></a>A tervezőhöz tartozó kivételek és hibakódok

Ez a cikk a Azure Machine Learning Designerben található hibaüzeneteket és kivételi kódokat ismerteti, hogy segítsen a gépi tanulási folyamatok hibaelhárításában.

A hibaüzenetet a tervezőben a következő lépésekkel érheti el:  

- Válassza ki a hibás modult, lépjen a **kimenetek és naplók** lapra, ahol megtalálhatja a részletes naplót a **azureml-naplók** kategóriában található **70_driver_log.txt** fájlban.

- A modul részletes hibája a **module_statistics** kategóriában található error_info.jsban is ellenőrizhető.

Az alábbiakban a Designer moduljainak hibakódja látható.

## <a name="error-0001"></a>0,001-es hiba  
 Kivétel történik, ha egy vagy több megadott oszlop nem található.  

 Ez a hibaüzenet akkor jelenik meg, ha egy modulhoz oszlop van kiválasztva, de a kijelölt oszlop (ok) nem szerepel a bemeneti adatkészletben. Ez a hiba akkor fordulhat elő, ha kézzel írt be egy oszlopnevet, vagy ha az oszlop választója olyan javasolt oszlopot adott meg, amely nem szerepelt az adatkészletben a folyamat futtatásakor.  

**Megoldás:** Tekintse át a kivételt tartalmazó modult, és ellenőrizze, hogy az oszlopnév vagy a nevek helyesek-e, és hogy a hivatkozott oszlopok léteznek-e.  

|Kivételek üzenetei|
|------------------------|
|Egy vagy több megadott oszlop nem található.|
|Nem található a (z) "{column_id}" nevű vagy indexű oszlop.|
|A (z) "{column_id}" nevű vagy indexű oszlop nem létezik a következőben: "{arg_name_missing_column}".|
|A (z) "{column_id}" nevű vagy indexű oszlop nem létezik a következőben: "{arg_name_missing_column}", de létezik a következőben: "{arg_name_has_column}".|
|A (z) "{column_names}" nevű vagy indexű oszlopok nem találhatók.|
|A (z) "{column_names}" nevű vagy indexű oszlopok nem léteznek a következőben: "{arg_name_missing_column}".|
|A (z) "{column_names}" nevű vagy indexű oszlopok nem szerepelnek a (z) {arg_name_missing_column} alkalmazásban, de a (z) "{arg_name_has_column}" nem létezik.|


## <a name="error-0002"></a>0002 hiba  
 Kivétel történik, ha egy vagy több paramétert nem lehetett elemezni, vagy a megadott típusról át kell alakítani a cél metódus típusának megfelelően.  

 Ez a hiba akkor fordul elő Azure Machine Learning amikor bemenetként ad meg egy paramétert, és az érték típusa eltér a várt típustól, és az implicit konverzió nem hajtható végre.  

**Megoldás:** Ellenőrizze a modul követelményeit, és határozza meg, hogy melyik értéktípus szükséges (karakterlánc, egész szám, dupla stb.)  

|Kivételek üzenetei|
|------------------------|
|Nem sikerült elemezni a paramétert.|
|Nem sikerült elemezni a (z) {arg_name_or_column} paramétert.|
|Nem sikerült a (z) {arg_name_or_column} paraméter konvertálása a (z) {to_type} értékre.|
|Nem sikerült konvertálni a (z) {arg_name_or_column} paramétert a (z) {from_type} helyről a (z) {to_type} értékre.|
|Nem sikerült a (z) {arg_name_or_column} "{arg_value}" paraméterérték konvertálása a (z) "{from_type}" értékről a (z) "{to_type}" értékre.|
|Nem sikerült átalakítani a (z) {arg_value} értéket a (z) {arg_name_or_column} oszlopban a (z) {from_type} oszlopból a (z) {to_type} értékre a megadott "{FMT Package}" formátum használatával.|


## <a name="error-0003"></a>0003 hiba  
 Kivétel történik, ha egy vagy több bemenet null értékű vagy üres.  

 Ez a hibaüzenet akkor jelenik meg Azure Machine Learning, ha a modulhoz tartozó bemenetek vagy paraméterek null értékűek vagy üresek.  Ez a hiba akkor fordulhat elő, ha például nem adott meg értéket a paraméterhez. Akkor is előfordulhat, ha olyan adatkészletet választott, amely hiányzó értékeket tartalmaz, vagy egy üres adatkészlet.  

**Megoldás:**

+ Nyissa meg a kivételt okozó modult, és győződjön meg arról, hogy az összes bemenet meg van adva. Győződjön meg arról, hogy az összes szükséges bemenet meg van adva. 
+ Győződjön meg arról, hogy az Azure Storage-ból betöltött adatok elérhetők, és hogy a fiók neve vagy kulcsa nem módosult.  
+ A hiányzó vagy a null értékek bevitele a bemeneti adatok között.
+ Ha egy adatforráson lekérdezést használ, ellenőrizze, hogy az adatok visszaadása a várt formátumban történik-e. 
+ Helyesírás-és egyéb változások keresése az adatok specifikációjában.
  
|Kivételek üzenetei|
|------------------------|
|Egy vagy több bemenet null értékű vagy üres.|
|A (z) {Name} bemenet null értékű vagy üres.|


## <a name="error-0004"></a>0004 hiba  
 Kivétel történik, ha a paraméter értéke kisebb vagy egyenlő, mint a megadott érték.  

 Ezt a hibaüzenetet Azure Machine Learning fogja kapni, ha az üzenetben szereplő paraméter egy olyan határ érték alá esik, amely ahhoz szükséges, hogy a modul feldolgozza az adatfeldolgozást.  

**Megoldás:** Nyissa meg újra a modult, és módosítsa a paramétert úgy, hogy az a megadott értéknél nagyobb legyen.  

|Kivételek üzenetei|
|------------------------|
|A paraméternek nagyobbnak kell lennie, mint a határ értéke.|
|A (z) {arg_name} paraméter értékének nagyobbnak kell lennie, mint {lower_boundary}.|
|A (z) {arg_name} paraméter "{actual_value}" értékkel rendelkezik, amelynek nagyobbnak kell lennie, mint {lower_boundary}.|


## <a name="error-0005"></a>0,005-es hiba  
 Kivétel történik, ha a paraméter egy adott értéknél kisebb.  

 Ezt a hibaüzenetet Azure Machine Learning fogja kapni, ha az üzenetben szereplő paraméter értéke alacsonyabb, vagy egyenlő a modul által az adat feldolgozásához szükséges határ értékével.  

**Megoldás:** Nyissa meg újra a modult, és módosítsa a paramétert úgy, hogy az a megadott értéknél nagyobb vagy azzal egyenlő legyen.  

|Kivételek üzenetei|
|------------------------|
|A paraméternek nagyobbnak vagy egyenlőnek kell lennie a határ értékével.|
|A (z) {arg_name} paraméter értékének nagyobbnak vagy egyenlőnek kell lennie a következővel: {lower_boundary}.|
|A (z) {arg_name} paraméter értéke {Value}, amely nem lehet nagyobb, mint {lower_boundary}.|


## <a name="error-0006"></a>0006 hiba  
 Kivétel történik, ha a paraméter nagyobb vagy egyenlő, mint a megadott érték.  

 Ezt a hibaüzenetet Azure Machine Learning fogja kapni, ha az üzenetben szereplő paraméter nagyobb vagy egyenlő, mint a modul által az adat feldolgozásához szükséges határ érték.  

**Megoldás:** Nyissa meg újra a modult, és módosítsa a paramétert úgy, hogy az a megadott értéknél kisebb legyen.  

|Kivételek üzenetei|
|------------------------|
|A paraméterek nem egyeznek. Az egyik paraméternek kisebbnek kell lennie, mint egy másiknak.|
|A (z) "{arg_name}" paraméter értékének kisebbnek kell lennie a (z) "{upper_boundary_parameter_name}" paraméternél.|
|A (z) {arg_name} paraméter értéke {Value}, amelynek kisebbnek kell lennie, mint {upper_boundary_parameter_name}.|


## <a name="error-0007"></a>0007 hiba  
 Kivétel történik, ha a paraméter egy adott értéknél nagyobb.  

 Ezt a hibaüzenetet Azure Machine Learning, ha a modul tulajdonságainál nagyobb értéket adott meg, mint a megengedettnél. Megadhat például olyan adatmennyiséget, amely kívül esik a támogatott dátumok tartományán, vagy jelezheti, hogy öt oszlop használható, ha csak három oszlop érhető el. 

 Ez a hiba akkor is megjelenhet, ha két olyan adathalmazt ad meg, amelyeknek valamilyen módon meg kell egyezniük. Ha például oszlopokat kíván átnevezni, és index alapján adja meg az oszlopokat, a megadott nevek számának meg kell egyeznie az oszlopok oszlopainak számával. Egy másik példa lehet egy olyan matematikai művelet, amely két oszlopot használ, ahol az oszlopoknak azonos számú sort kell tartalmazniuk. 

**Megoldás:**

 + Nyissa meg a szóban forgó modult, és tekintse át a numerikus tulajdonságok beállításait.
 + Győződjön meg arról, hogy bármelyik paraméter értéke az adott tulajdonság értékének támogatott tartománya alá esik.
 + Ha a modul több bemenetet is igénybe vesz, ügyeljen arra, hogy a bemenetek azonos méretűek legyenek.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Győződjön meg arról, hogy az adatkészlet vagy az adatforrás módosult-e. Előfordulhat, hogy egy olyan érték, amely az adat egy korábbi verziójával működött, az oszlopok száma, az oszlop adattípusa vagy az adat mérete után sikertelen lesz.  

|Kivételek üzenetei|
|------------------------|
|A paraméterek nem egyeznek. Az egyik paraméternek kisebbnek vagy egyenlőnek kell lennie egy másikkal.|
|A (z) {arg_name} paraméter értékének kisebbnek vagy egyenlőnek kell lennie a (z) {upper_boundary_parameter_name} paraméter értékével.|
|A (z) {arg_name} paraméter "{actual_value}" értékkel rendelkezik, amely nem lehet kisebb, mint {upper_boundary}.|
|A (z) {actual_value} paraméter "{arg_name}" értéke nem lehet kisebb, mint a (z) {upper_boundary_parameter_name} érték {upper_boundary} értéke.|
|A (z) {arg_name} paraméter {actual_value} értéke nem lehet kisebb, mint {upper_boundary_meaning} érték {upper_boundary}.|


## <a name="error-0008"></a>0008 hiba  
 Kivétel történik, ha a paraméter nem a tartományon belül van.  

 Ezt a hibaüzenetet Azure Machine Learning fogja kapni, ha az üzenetben szereplő paraméter kívül esik a modul által az adat feldolgozásához szükséges korlátokon.  

 Ez a hiba például akkor jelenik meg, ha a [sorok hozzáadása](add-rows.md) lehetőséggel megpróbál összekapcsolni két olyan adatkészletet, amelyek eltérő számú oszloppal rendelkeznek.  

**Megoldás:** Nyissa meg újra a modult, és módosítsa a paramétert a megadott tartományon belülre.  

|Kivételek üzenetei|
|------------------------|
|A paraméter értéke nem a megadott tartományba esik.|
|A (z) {arg_name} paraméter értéke nem a tartományban van.|
|A (z) {arg_name} paraméter értékének a következő tartományba kell esnie: [{lower_boundary}, {upper_boundary}].|
|A (z) {arg_name} paraméter értéke nem a tartományban van. ok|


## <a name="error-0009"></a>0009 hiba  
 Kivétel történik, ha az Azure Storage-fiók neve vagy a tároló neve helytelenül van megadva.  

Ez a hiba akkor fordul elő Azure Machine Learning Designerben, ha megad egy Azure Storage-fiók paramétereit, de a név vagy a jelszó nem oldható fel. A jelszóval vagy a fiók nevével kapcsolatos hibák több okból is megtörténhetnek:

 + A fiók típusa nem megfelelő. Néhány új fióktípus nem támogatott a Machine Learning Designerben való használathoz. További részletek: [adatok importálása](import-data.md) .
 + A helytelen fióknevet adta meg
 + A fiók már nem létezik
 + A Storage-fiók jelszava helytelen vagy megváltozott
 + Nem adta meg a tároló nevét, vagy a tároló nem létezik
 + Nem adta meg teljes mértékben a fájl elérési útját (a blob elérési útját).
   

**Megoldás:**

Ilyen problémák gyakran akkor fordulnak elő, amikor megpróbál kézzel megadnia a fiók nevét, jelszavát vagy tárolójának elérési útját. Javasoljuk, hogy az [adatimportálási](import-data.md) modulhoz az új varázslót használja, amely segít megkeresni és megtekinteni a neveket.

Győződjön meg arról is, hogy a fiók, a tároló vagy a blob törölve lett-e. Egy másik Azure Storage segédprogrammal ellenőrizze, hogy helyesen adta-e meg a fióknevet és a jelszót, valamint hogy létezik-e a tároló. 

Azure Machine Learning nem támogatja néhány újabb fióktípus használatát. Például az új "forró" vagy "hideg" tárolási típusok nem használhatók gépi tanuláshoz. Mind a klasszikus Storage-fiókok, mind a Storage-fiókok "általános célú"-ként lettek létrehozva.

Ha meg van adva egy blob teljes elérési útja, ellenőrizze, hogy az elérési út **tároló-vagy blobname** van-e megadva, és hogy a tároló és a blob is létezik-e a fiókban.  

 Az elérési út nem tartalmazhat kezdő perjelet. A **/Container/blob** például helytelen, és **tárolóként vagy blobként** kell megadni.  


|Kivételek üzenetei|
|------------------------|
|Az Azure Storage-fiók neve vagy a tároló neve helytelen.|
|Helytelen az Azure Storage-fióknév ({account_name}) vagy a (z) {container_name} nevű tároló neve. a rendszer a tároló nevét vagy blobját várta.|


## <a name="error-0010"></a>0010 hiba  
 Kivétel történik, ha a bemeneti adatkészletek olyan oszlopnevek, amelyeknek egyezniük kell, de nem.  

 Ezt a hibaüzenetet Azure Machine Learning fogja kapni, ha az üzenetben szereplő oszlop indexe eltérő oszlopnevek tartalmaz a két bemeneti adatkészletben.  

**Megoldás:** Használja a [metaadatok szerkesztése](edit-metadata.md) lehetőséget, vagy módosítsa az eredeti adatkészletet úgy, hogy az azonos oszlopnevet adjon meg a megadott oszlop-indexhez.  

|Kivételek üzenetei|
|------------------------|
|A bemeneti adatkészletekben a megfelelő indexszel rendelkező oszlopok nevei eltérőek.|
|Az oszlopnevek nem egyeznek a bemeneti adatkészletek ({DataSet1 elemet} és {dataset2}) {col_index} oszlopához (nulla-alapú).|


## <a name="error-0011"></a>0011 hiba  
 Kivétel történik, ha az átadott oszlop beállított argumentuma nem vonatkozik egyetlen adatkészlet-oszlopra sem.  

 Ezt a hibaüzenetet Azure Machine Learning fogja kapni, ha a megadott oszlop nem egyezik a megadott adatkészlet egyik oszlopával sem.  

 Ezt a hibát akkor is megteheti, ha még nem jelölt ki oszlopot, és legalább egy oszlopra van szükség ahhoz, hogy a modul működjön.  

**Megoldás:** Módosítsa az oszlop kijelölését a modulban, hogy az az adatkészlet oszlopaira vonatkozzon.  

 Ha a modulhoz ki kell választania egy adott oszlopot, például egy felirat oszlopot, ellenőrizze, hogy a jobb oldali oszlop van-e kiválasztva.  

 Ha nem megfelelő oszlopok vannak kijelölve, távolítsa el, majd futtassa újra a folyamatot.  

|Kivételek üzenetei|
|------------------------|
|A megadott oszlop nem vonatkozik egyetlen adatkészlet-oszlopra sem.|
|A megadott "{column_set}" oszlop nem vonatkozik egyetlen adatkészlet-oszlopra sem.|


## <a name="error-0012"></a>0012 hiba  
 Kivétel történik, ha az osztály példánya nem hozható létre az átadott argumentumok készletével.  

**Megoldás:** Ez a hiba nem hajtható végre a felhasználó számára, és egy későbbi kiadásban elavulttá válik.  

|Kivételek üzenetei|
|------------------------|
|Nem betanított modell, először a modell betanítása.|
|Nem betanított modell ({arg_name}), használja a betanított modellt.|


## <a name="error-0013"></a>0013 hiba  
 Kivétel történik, ha a tanuló a modulnak átadott értéket adta meg érvénytelen típusként.  

 Ez a hiba akkor fordul elő, ha egy betanított modell nem kompatibilis a kapcsolódó pontozási modullal. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Megoldás:**

Határozza meg a betanítási modul által létrehozott tanuló típusát, és határozza meg a tanuló számára megfelelő pontozási modult. 

Ha a modellt a speciális képzési modulok bármelyikével betanítják, a betanított modellt csak a megfelelő speciális pontozási modulhoz kapcsolja össze. 


|Modell típusa|Betanítási modul| Pontozási modul|
|----|----|----|
|bármely osztályozó|[Modell betanítása](train-model.md) |[Relevanciamodell](score-model.md)|
|bármely regressziós modell|[Modell betanítása](train-model.md) |[Relevanciamodell](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|Kivételek üzenetei|
|------------------------|
|Érvénytelen típusú tanuló lett átadva.|
|A (z) {arg_name} tanuló típusa érvénytelen.|
|A (z) {arg_name} tanulónak érvénytelen a (z) {learner_type} típusa.|
|Érvénytelen típusú tanuló lett átadva. Kivétel üzenete: {exception_message}|


## <a name="error-0014"></a>0014 hiba  
 Kivétel történik, ha az oszlop egyedi értékeinek száma nagyobb az engedélyezettnél.  

 Ez a hiba akkor fordul elő, ha egy oszlop túl sok egyedi értéket tartalmaz, például egy azonosító oszlopot vagy egy szöveges oszlopot. Ez a hiba akkor fordulhat elő, ha azt adja meg, hogy egy oszlop kategorikus adatként legyen kezelve, de túl sok egyedi érték szerepel az oszlopban a feldolgozás befejezésének engedélyezéséhez. Ez a hiba akkor is megjelenhet, ha a két bemenetben lévő egyedi értékek száma nem egyezik.   

Az egyedi értékek hibája nagyobb az engedélyezettnél, ha **a következő** feltételekkel találkozik:

- Egy oszlop több mint 97%-a egyedi értékek, ami azt jelenti, hogy szinte az összes kategória eltér egymástól.
- Az egyik oszlop több mint 1000 egyedi értéket tartalmaz.

**Megoldás:**

Nyissa meg a hibát generáló modult, és azonosítsa a bemenetként használt oszlopokat. Egyes modulok esetében kattintson a jobb gombbal az adatkészlet bemenetére, és válassza a **Megjelenítés** lehetőséget az egyes oszlopokra vonatkozó statisztikák beszerzéséhez, beleértve az egyedi értékek számát és a terjesztését.

A csoportosításhoz vagy kategorizáláshoz használni kívánt oszlopok esetében hajtsa végre a lépéseket az oszlopok egyedi értékei számának csökkentése érdekében. Az oszlop adattípusától függően a különböző módokon is csökkentheti az adattípust. 

Olyan azonosító oszlopok esetében, amelyek nem értelmezhető funkciók a modellek betanítása során, a [metaadatok szerkesztése](../algorithm-module-reference/edit-metadata.md) lehetőséggel megjelölheti az oszlopot **egyértelmű szolgáltatásként** , és a modell betanítása során nem lesz használatban. 

A szöveges oszlopok esetében használhatja a [funkció kivonatolását](../algorithm-module-reference/feature-hashing.md) , vagy [kinyerheti az N-Gram funkciókat a szöveges modulból](../algorithm-module-reference/extract-n-gram-features-from-text.md) a szöveges oszlopok előfeldolgozásához.
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Nem található a forgatókönyvnek megfelelő megoldás? A témakörben visszajelzést adhat, amely tartalmazza a hibát generáló modul nevét, valamint az oszlop adattípusát és a kardinálisát. Az információkat a gyakori forgatókönyvek részletesebb hibaelhárítási lépéseinek biztosítására fogjuk használni.   

|Kivételek üzenetei|
|------------------------|
|Az oszlop egyedi értékeinek mennyisége nagyobb az engedélyezettnél.|
|A (z) "{column_name}" oszlop egyedi értékeinek száma nagyobb, mint az engedélyezett.|
|A (z) "{column_name}" oszlop egyedi értékeinek száma meghaladja a (z) {korlátozás} rekordos számát.|


## <a name="error-0015"></a>0015 hiba  
 Kivétel történik, ha az adatbázis-kapcsolatok sikertelenek voltak.  

 Ez a hibaüzenet akkor jelenik meg, ha helytelen SQL-fióknevet, jelszót, adatbázis-kiszolgálót vagy adatbázisnevet ad meg, vagy ha az adatbázissal vagy a kiszolgálóval kapcsolatos problémák miatt nem lehet kapcsolódni az adatbázishoz.  

**Megoldás:** Ellenőrizze, hogy a fiók neve, jelszava, adatbázis-kiszolgálója és adatbázisa helyesen van-e megadva, és hogy a megadott fiók megfelelő szintű engedélyekkel rendelkezik-e. Ellenőrizze, hogy az adatbázis jelenleg elérhető-e.  

|Kivételek üzenetei|
|------------------------|
|Hiba történt az adatbázis-kapcsolatok létrehozásakor.|
|Hiba történt az adatbázis-kapcsolatok létrehozásakor: {connection_str}.|


## <a name="error-0016"></a>0016 hiba  
 Kivétel történik, ha a modulnak átadott bemeneti adatkészleteknek kompatibilis oszlop típusúnak kell lenniük, de nem.  

 Ez a hibaüzenet akkor jelenik meg Azure Machine Learning, ha a két vagy több adatkészletben átadott oszlopok típusai nem kompatibilisek egymással.  

**Megoldás:** [Metaadatok szerkesztése](edit-metadata.md) vagy az eredeti bemeneti adatkészlet módosítása<!--, or use [Convert to Dataset](convert-to-dataset.md)--> annak biztosítása érdekében, hogy az oszlopok típusai kompatibilisek legyenek.  

|Kivételek üzenetei|
|------------------------|
|A bemeneti adatkészletekben a megfelelő indexszel rendelkező oszlopok nem kompatibilis típusokkal rendelkeznek.|
|A (z) {first_col_names} oszlopok nem kompatibilisek a betanítási és a tesztelési adatértékek között.|
|A (z) {first_col_names} és a (z) {second_col_names} oszlopok nem kompatibilisek.|
|Az oszlop típusú elemek nem kompatibilisek a bemeneti adatkészletek ({first_dataset_names} és {second_dataset_names}) "{first_col_names}" oszlopában (nulla alapú).|


## <a name="error-0017"></a>0017 hiba  
 Kivétel történik, ha egy kiválasztott oszlop olyan adattípust használ, amelyet az aktuális modul nem támogat.  

 Előfordulhat például, hogy ezt a hibaüzenetet Azure Machine Learning, ha az oszlop kijelölése olyan adattípusú oszlopot tartalmaz, amelyet a modul nem tud feldolgozni, például egy matematikai művelet sztring oszlopát, vagy egy olyan pontszám oszlopot, amelyben a kategorikus funkció oszlop szükséges.  

**Megoldás:**
 1. Azonosítsa a problémát okozó oszlopot.
 2. Tekintse át a modul követelményeit.
 3. Módosítsa az oszlopot úgy, hogy az megfeleljen a követelményeknek. Előfordulhat, hogy a következő modulok közül többet kell használnia a módosítások elvégzéséhez az oszloptól és a megkísérelt konverziótól függően:
    + A [metaadatok szerkesztése](edit-metadata.md) lehetőséggel módosíthatja az oszlopok adattípusát, vagy megváltoztathatja az oszlop használatát a szolgáltatástól a numerikus értékre, a kategorikus és a nem kategorikus típusra, és így tovább.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Végső megoldásként előfordulhat, hogy módosítania kell az eredeti bemeneti adatkészletet.

> [!TIP]
> Nem található a forgatókönyvnek megfelelő megoldás? A témakörben visszajelzést adhat, amely tartalmazza a hibát generáló modul nevét, valamint az oszlop adattípusát és a kardinálisát. Az információkat a gyakori forgatókönyvek részletesebb hibaelhárítási lépéseinek biztosítására fogjuk használni. 

|Kivételek üzenetei|
|------------------------|
|Az aktuális típusú oszlop nem dolgozható fel. A modul nem támogatja a típust.|
|A (col_type} típusú oszlop nem dolgozható fel. A modul nem támogatja a típust.|
|A (z) {col_type} típusú "{col_name}" oszlop nem dolgozható fel. A modul nem támogatja a típust.|
|A (z) {col_type} típusú "{col_name}" oszlop nem dolgozható fel. A modul nem támogatja a típust. Paraméter neve: {arg_name}.|


## <a name="error-0018"></a>0018 hiba  
 Kivétel történik, ha a bemeneti adatkészlet érvénytelen.  

**Megoldás:** Ez a hiba Azure Machine Learning több kontextusban is megjelenhet, így nincs egyetlen megoldás. Általánosságban a hiba azt jelzi, hogy a modulba bemenetként megadott adatok helytelen számú oszlopot tartalmaz, vagy az adattípus nem felel meg a modul követelményeinek. Például:  

-   A modulhoz címke típusú oszlopra van szükség, de az oszlop nem jelölésként van megjelölve, vagy még nem jelölt ki felirat oszlopot.  
  
-   A modul megköveteli, hogy az adat legyen kategorikus, de az adatai numerikusak.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Az adatformátum nem megfelelő.  
  
-   Az importált adatok érvénytelen karaktereket, helytelen értékeket vagy tartományon kívüli értékeket tartalmaznak.  
-   Az oszlop üres, vagy túl sok hiányzó értéket tartalmaz.  

 A követelmények és az adatok módjának megállapításához tekintse át az adatkészletet bemenetként használó modul súgóját.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Kivételek üzenetei|
|------------------------|
|Az adatkészlet érvénytelen.|
|a (DataSet1 elemet}) érvénytelen adatértéket tartalmaz.|
|a ({DataSet1 elemet} és {dataset2}) konzisztens columnwise kell lennie.|
|a (DataSet1 elemet) {} érvénytelen adatértéket tartalmaz, {Reason}.|
|{DataSet1 elemet} {invalid_data_category} tartalmaz. {troubleshoot_hint}|
|{DataSet1 elemet} érvénytelen, {Reason}. {troubleshoot_hint}|


## <a name="error-0019"></a>0019 hiba  
 Kivétel történik, ha az oszlopnak rendezett értékeket kell tartalmaznia, de nem.  

 Ezt a hibaüzenetet Azure Machine Learning fogja kapni, ha a megadott oszlop értékei nem sorrendben vannak.  

**Megoldás:** Rendezze az oszlop értékeit a bemeneti adatkészlet manuális módosításával, majd futtassa újra a modult.  

|Kivételek üzenetei|
|------------------------|
|Az oszlopban lévő értékek nincsenek rendezve.|
|A (z) "{col_index}" oszlop értékei nem rendezettek.|
|A (z) "{DataSet}" adatkészlet "{col_index}" oszlopának értékei nem rendezettek.|
|A (z) "{arg_name}" argumentumban szereplő értékek nem "{sorting_order}" sorrendben vannak rendezve.|


## <a name="error-0020"></a>0020 hiba  
 Kivétel történik, ha a modulnak átadott egyes adatkészletekben lévő oszlopok száma túl kicsi.  

 Ez a hibaüzenet akkor jelenik meg Azure Machine Learning, ha nincs elég oszlop kiválasztva egy modulhoz.  

**Megoldás:** Nyissa meg újra a modult, és győződjön meg arról, hogy az oszlop választója megfelelő számú oszlopot jelölt ki.  

|Kivételek üzenetei|
|------------------------|
|A bemeneti adatkészlet oszlopainak száma kevesebb, mint az engedélyezett minimum.|
|A (z) "{arg_name}" bemeneti adatkészletben lévő oszlopok száma kisebb, mint a minimálisan megengedett.|
|A bemeneti adatkészletben lévő oszlopok száma kisebb, mint a (z) {required_columns_count} oszlop (ok) számára megengedett minimum.|
|A (z) "{arg_name}" bemeneti adatkészletben lévő oszlopok száma kisebb, mint az engedélyezett minimum {required_columns_count} oszlop.|


## <a name="error-0021"></a>0021 hiba  
 Kivétel történik, ha a modulnak átadott egyes adatkészletek sorainak száma túl kicsi.  

 Ez a hiba akkor látható, ha az adatkészletben nincs elegendő sor a megadott művelet végrehajtásához Azure Machine Learning. Előfordulhat például, hogy ez a hiba akkor jelenik meg, ha a bemeneti adatkészlet üres, vagy ha olyan műveletet próbál végrehajtani, amely a minimálisan szükséges sorok érvényességét igényli. Ezek a műveletek magukban foglalhatják a statisztikai módszerek, bizonyos dobozolási és a számokkal való tanulás alapján történő csoportosítást vagy besorolást.  

**Megoldás:**

 + Nyissa meg a hibát visszaadó modult, és keresse meg a bemeneti adatkészletet és a modul tulajdonságait. 
 + Ellenőrizze, hogy a bemeneti adatkészlet nem üres-e, és hogy van-e elegendő sornyi adat a modul súgójában ismertetett követelmények teljesítéséhez.  
 + Ha az adatok külső forrásból töltődnek be, győződjön meg arról, hogy az adatforrás elérhető, és nincs olyan hiba vagy változás az adatdefinícióban, amely miatt az importálási folyamat kevesebb sort kap.
 + Ha olyan műveletet végez, amely hatással lehet az adatok típusára vagy az értékek számára, például tisztításra, felosztásra vagy csatlakozási műveletekre, ellenőrizze a műveletek kimenetét a visszaadott sorok számának megállapításához.  

|Kivételek üzenetei|
|------------------------|
|A bemeneti adatkészletben lévő sorok száma kevesebb, mint az engedélyezett minimum.|
|A bemeneti adatkészletben lévő sorok száma kisebb, mint a (z) {required_rows_count} sor minimuma.|
|A bemeneti adatkészletben lévő sorok száma kisebb, mint a (z) {required_rows_count} sor minimuma. ok|
|A (z) "{arg_name}" bemeneti adatkészletben szereplő sorok száma kisebb, mint az engedélyezett minimum {required_rows_count} sor.|
|A (z) {arg_name} bemeneti adatkészletben lévő sorok száma {actual_rows_count}, amely kisebb, mint a minimálisan megengedett {required_rows_count} sor.|
|A (z) {arg_name} bemeneti adatkészletben levő "{row_type}" sorok száma {actual_rows_count}, amely kisebb, mint a minimálisan megengedett {required_rows_count} sor.|


## <a name="error-0022"></a>0022 hiba  
 Kivétel történik, ha a bemeneti adatkészlet kiválasztott oszlopainak száma nem egyezik a várt számmal.  

 Ez a hiba Azure Machine Learning akkor fordulhat elő, ha az alárendelt modulnak vagy műveletnek adott számú oszlopot vagy bemenetet kell használnia, és túl kevés vagy túl sok oszlopot vagy bemenetet adott meg. Például:  

-   Egyetlen címkét tartalmazó oszlopot vagy kulcs oszlopot kell megadnia, és véletlenül több oszlopot is kiválasztott.  
  
-   Átnevezi az oszlopokat, de több vagy kevesebb nevet adott meg, mint amennyi oszlop.  
  
-   A forrás vagy a cél oszlopainak száma módosult, vagy nem egyezik a modul által használt oszlopok számával.  
  
-   A bemenetek értékeinek vesszővel tagolt listáját adta meg, de az értékek száma nem egyezik, vagy több bemenet nem támogatott.  

**Megoldás:** Nyissa meg újra a modult, és ellenőrizze az oszlop kijelölését, és győződjön meg arról, hogy a megfelelő számú oszlop van kiválasztva. Ellenőrizze a felsőbb rétegbeli modulok kimeneteit, valamint az alsóbb rétegbeli műveletek követelményeit.  

 Ha az egyik olyan oszlop kijelölési beállítást használta, amely több oszlopot is kijelölhet (oszlop indexek, minden funkció, összes numerikus elem stb.), ellenőrizze a kijelölés által visszaadott oszlopok pontos számát.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Ellenőrizze, hogy a felsőbb rétegbeli oszlopok száma vagy típusa nem módosult-e.  

 Ha ajánlási adatkészletet használ a modell betanításához, ne feledje, hogy az ajánló korlátozott számú oszlopot vár, amely a felhasználó-elem párokhoz vagy a felhasználói elemek rangsorolásához tartozik. Távolítsa el a további oszlopokat a modell vagy a felosztási javaslat adatkészletének betanítása előtt. További információ: [adatok felosztása](split-data.md).  

|Kivételek üzenetei|
|------------------------|
|A bemeneti adatkészletben lévő kijelölt oszlopok száma nem egyezik a várt számmal.|
|A bemeneti adatkészletben lévő kijelölt oszlopok száma nem egyenlő a következővel: {expected_col_count}.|
|A (z) {selection_pattern_friendly_name} oszlop-kiválasztási minta a bemeneti adatkészletben lévő kijelölt oszlopok számát adja meg, amely nem egyenlő a következővel: {expected_col_count}.|
|A (z) "{selection_pattern_friendly_name}" oszlop kiválasztási mintájának a bemeneti adatkészletben megadott {expected_col_count} oszlop (oka) t kell megadnia, de a (z) {selected_col_count} oszlop (ok) ténylegesen meg van adva.|


## <a name="error-0023"></a>0023 hiba  

Kivétel fordul elő, ha a bemeneti adatkészlet cél oszlopa nem érvényes az aktuális oktatói modulhoz.  

Ez a hiba Azure Machine Learning akkor fordul elő, ha a cél oszlop (a modul paraméterei közül kiválasztva) nem érvényes adattípusú, az összes hiányzó értéket tartalmazza, vagy nem a vártnak megfelelően.  

**Megoldás:** Nyissa meg újra a modul bemenetét a címke/cél oszlop tartalmának vizsgálatához. Győződjön meg arról, hogy nem rendelkezik minden hiányzó értékkel. Ha a modulnak meg kell győződnie arról, hogy a cél oszlop kategorikus, akkor győződjön meg arról, hogy a cél oszlopban több különböző érték szerepel.  

|Kivételek üzenetei|
|------------------------|
|A bemeneti adatkészlet nem támogatott célként megadott oszlopot tartalmaz.|
|A bemeneti adatkészlet nem támogatja a (z) {column_index} célként megadott oszlopot.|
|A bemeneti adatkészlet nem támogatja a (z) {column_index} nevű cél oszlopot a (z) {learner_type} típusú tanulók számára.|


## <a name="error-0024"></a>0024 hiba  
Kivétel történik, ha az adatkészlet nem tartalmaz felirat oszlopot.  

 Ez a hiba Azure Machine Learning akkor következik be, amikor a modulhoz címke oszlop szükséges, és az adatkészlet nem tartalmaz felirat oszlopot. Például egy pontszámmal rendelkező adatkészlet kiértékelése általában megköveteli, hogy a kiszámítási pontosság mérőszámai címkével ellátott oszlop legyen jelen.  

Azt is megteheti, hogy egy felirat típusú oszlop szerepel az adatkészletben, de a Azure Machine Learning nem észleli megfelelően.

**Megoldás:**

+ Nyissa meg a hibát generáló modult, és állapítsa meg, hogy van-e felirat típusú oszlop. Az oszlop neve vagy adattípusa nem számít, feltéve, hogy az oszlop egyetlen eredményt (vagy függő változót) tartalmaz, amelyet előre meg szeretne jósolni. Ha nem biztos abban, hogy melyik oszlop rendelkezik a címkével, keresse meg az általános nevet, például az  *osztályt* vagy a *célt*. 
+  Ha az adatkészlet nem tartalmaz felirat oszlopot, akkor lehetséges, hogy a Label oszlop explicit módon vagy véletlenül eltávolította a felsőbb rétegbeli adatokat. Az is előfordulhat, hogy az adatkészlet nem egy felsőbb rétegbeli pontozási modul kimenete.
+ Ha explicit módon meg szeretné jelölni az oszlopot feliratként, vegye fel a [metaadatok szerkesztése](edit-metadata.md) modult, és kapcsolja össze az adatkészletet. Válassza ki a csak a címke oszlopot, és válassza a **címkék** lehetőséget a **mezők** legördülő listából. 
+ Ha a hibás oszlop van kiválasztva címkeként, a **mezőkben** lévő metaadatok kijavításához jelölje be a **címke törlése jelölőnégyzetet** . 
  
|Kivételek üzenetei|
|------------------------|
|Nincs felirat oszlop az adatkészletben.|
|Nincs felirat típusú oszlop a (z) {dataset_name} alkalmazásban.|


## <a name="error-0025"></a>0,025-es hiba  
 Kivétel történik, ha az adatkészlet nem tartalmaz pontszám oszlopot.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha a kiértékelési modellbe való bevitel nem tartalmaz érvényes pontszám-oszlopokat. A felhasználó például megpróbál kiértékelni egy adatkészletet, mielőtt a megfelelő betanított modellel szerezte volna, vagy a pontszám oszlop explicit módon el lett dobva. Ez a kivétel akkor is előfordulhat, ha a két adatkészlet pontszám oszlopai nem kompatibilisek. Előfordulhat például, hogy egy lineáris regressor pontosságát próbálja összehasonlítani egy bináris osztályozó használatával.  

**Megoldás:** Tekintse át a kiértékelési modell bemenetét, és vizsgálja meg, hogy egy vagy több pontszám oszlopot tartalmaz-e. Ha nem, az adatkészlet nem lett kiváltva, vagy a pontszám oszlopait egy felsőbb rétegbeli modulban eldobta.  

|Kivételek üzenetei|
|------------------------|
|Nincs pontszám oszlop az adatkészletben.|
|Nincs pontszám oszlop a (z) {dataset_name} elemben.|
|A (z) {dataset_name} "{learner_type}" által létrehozott pontszám-oszlop nem található. A megfelelő típusú tanuló használatával szerzi be az adatkészletet.|


## <a name="error-0026"></a>0026 hiba  
 Kivétel történik, ha az azonos nevű oszlopok nem engedélyezettek.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha több oszlopnak ugyanaz a neve. Ennek a hibának az az egyik módja, ha az adatkészlet nem rendelkezik fejléc-sorral, és az oszlopnevek automatikusan hozzá vannak rendelve: Col0, Col1 stb.  

**Megoldás:** Ha az oszlopok neve megegyezik, szúrja be a [metaadatok szerkesztése](edit-metadata.md) modult a bemeneti adatkészlet és a modul között. Az átnevezni kívánt oszlopok kiválasztásához használja az oszlop választóját a [metaadatok szerkesztése](edit-metadata.md) elemre, írja be az új neveket az **új oszlopnevek** szövegmezőbe.  

|Kivételek üzenetei|
|------------------------|
|Az egyenlő oszlopnevek az argumentumokban vannak megadva. A modul nem engedélyezi az egyenlő oszlopnevek nevét.|
|A (z) {arg_name_1} és a (z) {arg_name_2} argumentumban szereplő egyenlő oszlopnevek nem engedélyezettek. Adjon meg különböző neveket.|


## <a name="error-0027"></a>0027 hiba  
 Kivétel fordul elő abban az esetben, ha két objektumnak azonos méretűnek kell lennie, de nem.  

 Ez gyakori hiba a Azure Machine Learningban, és számos feltétel okozhatja.  

**Megoldás:** Nincs konkrét megoldás. A feltételeket azonban a következőképpen tekintheti meg:  

-   Oszlopok átnevezése esetén győződjön meg arról, hogy az egyes listák (a bemeneti oszlopok és az új nevek listája) azonos számú elemmel rendelkeznek.  
  
-   Ha két adatkészletet csatlakoztat vagy ÖSSZEFŰZ, győződjön meg róla, hogy ugyanazzal a sémával rendelkeznek.  
  
-   Ha két olyan adatkészletet hoz létre, amelyek több oszloppal rendelkeznek, győződjön meg arról, hogy a kulcs oszlopai azonos adattípussal rendelkeznek, és válassza az **Ismétlődések engedélyezése és az oszlopok megőrzése a kijelölésben** lehetőséget.  

|Kivételek üzenetei|
|------------------------|
|Az átadott objektumok mérete inkonzisztens.|
|A (z) "{friendly_name1}" mérete nem konzisztens a (z) "{friendly_name2}" méretével.|


## <a name="error-0028"></a>0028 hiba  
 Kivétel keletkezik abban az esetben, ha az oszlop duplikált oszlopnevek tartalmaz, és nem engedélyezett.  

 Ez a hiba Azure Machine Learning akkor következik be, amikor az oszlopnevek duplikálva vannak; Ez nem egyedi.  

**Megoldás:** Ha bármely oszlop neve azonos, adja hozzá a [metaadatok szerkesztése](edit-metadata.md) a bemeneti adatkészletbe és a hibát növelő modulba. Az átnevezni kívánt oszlopok kiválasztásához használja az oszlop-választót a [metaadatok szerkesztése](edit-metadata.md) elemre, majd írja be az új oszlopok nevét az **új oszlopnevek** szövegmezőbe. Több oszlop átnevezése esetén ügyeljen arra, hogy az **új oszlopokban** megadott értékek egyediek legyenek.  

|Kivételek üzenetei|
|------------------------|
|Az oszlop a duplikált oszlop neve (ke) t tartalmazza.|
|A (z) {duplicated_name} név duplikált.|
|A (z) {duplicated_name} név duplikálva van a következőben: {arg_name}.|
|A (z) {duplicated_name} név duplikált. Részletek: {details}|


## <a name="error-0029"></a>0029 hiba  
 Kivétel fordul elő abban az esetben, ha a rendszer érvénytelen URI-értéket ad át.  

 Ez a hiba Azure Machine Learning esetén akkor fordul elő, ha a rendszer érvénytelen URI-t ad át.  Ez a hibaüzenet akkor jelenik meg, ha az alábbi feltételek bármelyike teljesül:  

-   Az olvasási vagy írási Azure Blob Storage számára megadott nyilvános vagy SAS URI-azonosító hibát tartalmaz.  
  
-   Az SAS időtartománya lejárt.  
  
-   A webes URL-cím HTTP-forráson keresztül egy fájlt vagy egy visszacsatolási URI-t jelöl.  
  
-   A HTTP-n keresztüli webes URL-cím helytelenül formázott URL-címet tartalmaz.  
  
-   A távoli forrás nem tudja feloldani az URL-címet.  

**Megoldás:** Nyissa meg újra a modult, és ellenőrizze az URI formátumát. Ha az adatforrás HTTP-n keresztüli webes URL-cím, ellenőrizze, hogy a kívánt forrás nem fájl vagy visszacsatolási URI (localhost).  

|Kivételek üzenetei|
|------------------------|
|A rendszer érvénytelen URI-t adott át.|
|A (z) {invalid_url} URI érvénytelen.|


## <a name="error-0030"></a>0030 hiba  
 Kivétel keletkezik abban az esetben, ha a fájl letöltése nem lehetséges.  

 Ez a kivétel Azure Machine Learning akkor következik be, amikor nem lehet letölteni egy fájlt. Ez a kivétel akkor fog megjelenni, ha egy HTTP-forrásból való olvasási kísérlet három (3) újrapróbálkozás után meghiúsult.  

**Megoldás:** Győződjön meg arról, hogy a HTTP-forrás URI-ja helyes, és hogy a hely jelenleg elérhető az interneten keresztül.  

|Kivételek üzenetei|
|------------------------|
|Nem sikerült letölteni a fájlt.|
|Hiba történt a következő fájl letöltése közben: {file_url}.|


## <a name="error-0031"></a>0031 hiba  
 Kivétel történik, ha a készlet oszlopainak száma kisebb a szükségesnél.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha a kiválasztott oszlopok száma kisebb a szükségesnél.  Ez a hibaüzenet akkor jelenik meg, ha nincs kiválasztva a minimálisan szükséges oszlopok száma.  

**Megoldás:** Adjon hozzá további oszlopokat az oszlop kiválasztásához az **oszlop választójának** használatával.  

|Kivételek üzenetei|
|------------------------|
|A set oszlop oszlopainak száma kisebb a szükségesnél.|
|Legalább {required_columns_count} oszlopot meg kell adni a (z) {arg_name} bemeneti argumentumhoz.|
|Legalább {required_columns_count} oszlopot meg kell adni a (z) {arg_name} bemeneti argumentumhoz. A megadott oszlopok tényleges száma {input_columns_count}.|


## <a name="error-0032"></a>0032-es hiba  
 Kivétel történik, ha az argumentum nem szám.  

 Ezt a hibaüzenetet akkor kapja meg Azure Machine Learning, ha az argumentum egy dupla vagy NaN.  

**Megoldás:** Módosítsa a megadott argumentumot érvényes érték használatára.  

|Kivételek üzenetei|
|------------------------|
|Az argumentum nem szám.|
|a (z) "{arg_name}" nem szám.|


## <a name="error-0033"></a>0033-es hiba  
 Kivétel történik, ha az argumentum végtelen.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha az argumentum végtelen. Ez a hibaüzenet akkor jelenik meg, ha az argumentum `double.NegativeInfinity` vagy vagy `double.PositiveInfinity` .  

**Megoldás:** Módosítsa a megadott argumentumot érvényes értékre.  

|Kivételek üzenetei|
|------------------------|
|Az argumentumnak véges kell lennie.|
|a (z) "{arg_name}" nem véges.|
|A (z) {column_name} oszlop végtelen értékeket tartalmaz.|


## <a name="error-0034"></a>0034-es hiba  
 Kivétel történik, ha egynél több minősítés létezik egy adott felhasználói elemmel rendelkező pár esetében.  

 Ez a hiba Azure Machine Learning fordul elő abban az esetben, ha egy felhasználói Item pár több minősítési értékkel rendelkezik.  

**Megoldás:** Győződjön meg arról, hogy a felhasználó-Item pár csak egy minősítési értéket rendelkezik.  

|Kivételek üzenetei|
|------------------------|
|Egynél több minősítés létezik az adatkészletben lévő érték (ek) hez.|
|Egynél több minősítés van a (z) {User} felhasználóhoz és a (z) {Item} elemmel a minősítési előrejelzési adattáblában.|
|Egynél több minősítés a ({user}) és a ({Item}) {DataSet} objektumban.|


## <a name="error-0035"></a>0035-es hiba  
 Kivétel történik, ha egy adott felhasználó vagy elem számára nem adtak meg szolgáltatásokat.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha egy ajánlási modellt próbál használni a pontozáshoz, de nem található a szolgáltatás vektora.  

**Megoldás:**

A Matchbox-ajánló rendelkezik bizonyos követelményekkel, amelyeket az elemek vagy a felhasználói szolgáltatások használatakor kell teljesíteni.  Ez a hiba azt jelzi, hogy hiányzik egy szolgáltatás-vektor a bemenetként megadott felhasználó vagy elem számára. Győződjön meg arról, hogy a szolgáltatások egy vektora elérhető az egyes felhasználók vagy elemek esetében.  

 Ha például a felhasználó korát, helyét vagy bevételét használó funkciókkal, de most olyan új felhasználók számára szeretne pontszámokat létrehozni, akik nem voltak a betanítás során, meg kell adnia egy megfelelő szolgáltatást (azaz az életkort, a helyet és a bevételi értékeket) az új felhasználók számára, hogy megfelelő előrejelzéseket lehessen készíteni számukra. 

 Ha nem rendelkezik ezekkel a felhasználókkal, érdemes lehet a megfelelő funkciók létrehozásához.  Ha például nem rendelkezik egyéni felhasználói kor-vagy bevételi értékekkel, a felhasználók egy csoportjára vonatkozóan megközelítő értékeket is létrehozhat. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > A megoldás nem alkalmazható az adott esetre? Szívesen küld visszajelzést erről a cikkről, és információt nyújt a forgatókönyvről, beleértve a modult és a sorok számát az oszlopban. Ezeket az információkat a jövőben részletesebb hibaelhárítási lépések megadására fogjuk használni.

|Kivételek üzenetei|
|------------------------|
|Nem adtak meg szolgáltatásokat a szükséges felhasználóhoz vagy elemhez.|
|{Required_feature_name} funkció szükséges, de nincs megadva.|


## <a name="error-0036"></a>0036-es hiba  
 Kivétel történik, ha egy adott felhasználóhoz vagy elemhez több szolgáltatás-vektor van megadva.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha egy szolgáltatás vektora egynél többször van definiálva.  

**Megoldás:** Győződjön meg arról, hogy a szolgáltatás vektora egynél többször van definiálva.  

|Kivételek üzenetei|
|------------------------|
|Egy felhasználó vagy elem duplikált funkció-definíciója.|


## <a name="error-0037"></a>0037-es hiba  
 Kivétel történik, ha több címke oszlop van megadva, és csak egy engedélyezett.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha egynél több oszlop van kiválasztva az új címke oszlopnak. A legtöbb felügyelt tanulási algoritmushoz meg kell adni egy oszlopot, amely célként vagy címkével van megjelölve.  

**Megoldás:** Ügyeljen arra, hogy egyetlen oszlopot jelöljön ki az új címke oszlopként.  

|Kivételek üzenetei|
|------------------------|
|Több címkét tartalmazó oszlop van megadva.|
|Több címke oszlop van megadva a következőben: {dataset_name}.|


## <a name="error-0039"></a>0039-es hiba  
 Kivétel történik, ha egy művelet meghiúsult.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha egy belső művelet nem hajtható végre.  

**Megoldás:** Ezt a hibát számos feltétel okozta, és nincs konkrét orvoslás.  
 A következő táblázat a hiba általános üzeneteit tartalmazza, amelyeket a feltétel adott leírása követ. 

 Ha nem áll rendelkezésre adat, a [Microsoft Q&egy kérdést a visszajelzések küldéséhez](/answers/topics/azure-machine-learning-studio-classic.html) , és információt nyújt a hibát eredményező modulokról és a kapcsolódó feltételekről.

|Kivételek üzenetei|
|------------------------|
|A művelet sikertelen volt.|
|Hiba történt a művelet végrehajtása közben: {failed_operation}.|
|Hiba történt a művelet végrehajtása közben: {failed_operation}. Ok: "{Reason}".|


## <a name="error-0042"></a>0042-es hiba  
 Kivétel történik, ha az oszlopot nem lehet más típusúra konvertálni.  

 Ez a hiba Azure Machine Learning akkor következik be, amikor nem lehetséges az oszlop konvertálása a megadott típusra.  Ez a hibaüzenet akkor jelenik meg, ha egy modulhoz egy adott adattípus szükséges, például a DateTime, a Text, a lebegőpontos szám vagy egész érték, de a meglévő oszlopokat nem lehet a szükséges típusra konvertálni.  

Kiválaszthat például egy oszlopot, és megpróbálhatja a numerikus adattípusra konvertálni a matematikai műveletekben való használathoz, és ha az oszlop érvénytelen értékeket tartalmaz, ezt a hibaüzenetet kapja. 

Ez a hiba akkor fordulhat elő, ha olyan oszlopot próbál használni, amely lebegőpontos számokat vagy sok egyedi értéket tartalmaz kategorikus oszlopként. 

**Megoldás:**

+ Nyissa meg a hibát generáló modul Súgó lapját, és ellenőrizze az adattípus követelményeit.
+ Tekintse át az oszlopok adattípusait a bemeneti adatkészletben.
+ Az úgynevezett séma nélküli adatforrásokból származó információk vizsgálata.
+ A hiányzó értékeket vagy speciális karaktereket tartalmazó adatkészletben keresse meg a kívánt adattípusra való átalakítást blokkoló adatokat. 
    + A numerikus adattípusoknak konzisztenseknek kell lenniük: például a lebegőpontos számok egy egész számú oszlopban való keresésével.
    + Keressen szöveges karakterláncokat vagy NA értékeket egy Number oszlopban. 
    + A logikai értékeket a szükséges adattípustól függően megfelelő ábrázolásra lehet konvertálni.
    + Nem Unicode karakterek, tabulátorok vagy vezérlőkarakterek szöveg oszlopainak vizsgálata
    + A DateTime típusú adatmennyiségnek konzisztensnek kell lennie a modellezési hibák elkerüléséhez, de a tisztítás számos formátum miatt bonyolult lehet. Érdemes használni <!--the [Execute R Script](execute-r-script.md) or -->[Python parancsfájl](execute-python-script.md) -modulok végrehajtása a tisztítás végrehajtásához.  
+ Ha szükséges, módosítsa a bemeneti adatkészlet értékeit, hogy az oszlop sikeresen konvertálható legyen. A módosítás magában foglalhatja a dobozolási, a csonkítás vagy a kerekítési műveletet, a kiugró értékek megszüntetését vagy a hiányzó értékek imputálási. A Machine learningben a következő cikkekben talál néhány gyakori Adatátalakítási forgatókönyvet:
    + [Hiányzó adatok törlése](clean-missing-data.md)
    + [Adatok normalizálása](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> A feloldás nem egyértelmű, vagy nem alkalmazható az adott esetre? Szívesen küld visszajelzést erről a cikkről, és információt nyújt a forgatókönyvről, beleértve a modult és az oszlop adattípusát. Ezeket az információkat a jövőben részletesebb hibaelhárítási lépések megadására fogjuk használni.  

|Kivételek üzenetei|
|------------------------|
|Nem engedélyezett a konverzió.|
|A ({Type1} típusú oszlop nem konvertálható {Type2} típusú oszlopra.|
|A (z) {Type1} típusú "{col_name1}" oszlop nem konvertálható a (z) {Type2} típusú oszlopba.|
|A (z) {Type1} típusú "{col_name1}" oszlop nem alakítható át {Type2} típusú "{col_name2}" oszlopra.|


## <a name="error-0044"></a>0044-es hiba  
 Kivétel történik, ha a meglévő értékekből nem lehet származtatni az oszlop típusát.  

 Ez a hiba Azure Machine Learning akkor következik be, amikor nem lehet következtetni egy oszlop vagy oszlop típusára egy adatkészletben. Ez általában akkor fordul elő, ha két vagy több olyan adatkészletet fűz össze, amelyek különböző típusú elemeket tartalmaznak. Ha Azure Machine Learning nem tud megállapítani egy olyan közös típust, amely egy oszlop vagy oszlop összes értékének az információ elvesztése nélkül való ábrázolására képes, ez a hibaüzenetet eredményezi.  

**Megoldás:** Győződjön meg arról, hogy a két adatkészletben található adott oszlopban lévő összes érték azonos típusú (numerikus, logikai, kategorikus, karakterlánc, dátum stb.), vagy kényszerített lehet ugyanarra a típusra.  

|Kivételek üzenetei|
|------------------------|
|Az oszlop elemének típusa nem származtatható.|
|A (z) "{column_name}" oszlop elemének típusa nem származtatható – az összes elem null hivatkozás.|
|A (z) "{dataset_name}" adatkészlet "{column_name}" oszlopához tartozó elemtípus nem származtatható – az összes elem null hivatkozás.|


## <a name="error-0045"></a>0045-es hiba  
 Kivétel keletkezik, ha a forrás vegyes típusú elemei miatt nem lehet oszlopot létrehozni.  

 Ez a hiba akkor jön létre a Azure Machine Learningban, ha a két adathalmaz összevont elemeinek típusa eltérő.  

**Megoldás:** Győződjön meg arról, hogy az adott oszlopban lévő összes érték azonos típusú (numerikus, logikai, kategorikus, string, Date stb.).  

|Kivételek üzenetei|
|------------------------|
|Vegyes elem típusú oszlop nem hozható létre.|
|A (z) "{column_id}" azonosítójú oszlop nem hozható létre kevert elem típusú típusok esetén:<br />A (z) [{row_1}, {column_id}] adattípusa "{type_1}". <br />A (z) [{row_2}, {column_id}] adattípusa "{type_2}".|
|A (z) "{column_id}" azonosítójú oszlop nem hozható létre kevert elem típusú típusok esetén:<br />A (z) {chunk_id_1} adattömbbe való beírás a következő: {type_1}. <br />A (z) {chunk_id_2} adattömbbe írja be a (z) {type_2} értéket, amelynek mérete a következő: {chunk_size}.|


## <a name="error-0046"></a>0046-es hiba  
 Kivétel történik, ha a megadott elérési úton nem lehet könyvtárat létrehozni.  

 Ez a hiba Azure Machine Learning akkor következik be, amikor nem lehet könyvtárat létrehozni a megadott elérési úton. Ez a hibaüzenet akkor jelenik meg, ha a kaptár-lekérdezés kimeneti könyvtárának bármelyik része helytelen vagy nem érhető el.  

**Megoldás:** Nyissa meg újra a modult, és ellenőrizze, hogy a könyvtár elérési útja helyesen van-e formázva, és hogy elérhető-e az aktuális hitelesítő adatokkal.  

|Kivételek üzenetei|
|------------------------|
|Adjon meg egy érvényes kimeneti könyvtárat.|
|A (z) {Path} könyvtár nem hozható létre. Adjon meg érvényes elérési utat.|


## <a name="error-0047"></a>0047-es hiba  
 Kivétel történik, ha a modulnak átadott egyes adatkészletekben a szolgáltatás oszlopainak száma túl kicsi.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha a betanításhoz megadott bemeneti adatkészlet nem tartalmazza az algoritmus által igényelt oszlopok minimális számát. Jellemzően vagy az adatkészlet üres, vagy csak képzési oszlopokat tartalmaz.  

**Megoldás:** Nyissa meg újra a bemeneti adatkészletet, és győződjön meg arról, hogy egy vagy több további oszlop található a Label (címke) oszlop mellett.  

|Kivételek üzenetei|
|------------------------|
|A bemeneti adatkészlet funkció oszlopainak száma kevesebb, mint a minimálisan megengedett érték.|
|A bemeneti adatkészletben szereplő szolgáltatások oszlopainak száma kisebb, mint a (z) {required_columns_count} oszlop megengedett minimuma.|
|A (z) "{arg_name}" bemeneti adatkészletben szereplő szolgáltatási oszlopok száma kisebb, mint az engedélyezett minimum {required_columns_count} oszlop.|


## <a name="error-0048"></a>0048-es hiba  
 Kivétel keletkezik abban az esetben, ha egy fájlt nem lehet megnyitni.  

 Ez a hiba Azure Machine Learning akkor következik be, amikor nem lehet fájlt megnyitni olvasásra vagy írásra. A következő okok miatt előfordulhat, hogy ezt a hibaüzenetet kapja:  

-   A tároló vagy a fájl (blob) nem létezik.  
  
-   A fájl vagy tároló hozzáférési szintje nem teszi lehetővé a fájl elérését  
  
-   A fájl túl nagy az olvasáshoz vagy a helytelen formátumhoz  

**Megoldás:** Nyissa meg újra a modult és az olvasni kívánt fájlt.  

 Ellenőrizze, hogy a tároló és a fájl neve helyes-e.  

 A klasszikus Azure portál vagy egy Azure Storage-eszköz használatával ellenőrizze, hogy rendelkezik-e a fájl eléréséhez szükséges engedéllyel.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Kivételek üzenetei|
|------------------------|
|Nem lehet megnyitni egy fájlt.|
|Hiba történt a következő fájl megnyitásakor: {file_name}.|
|Hiba történt a következő fájl megnyitásakor: {file_name}. Tárolási kivételre vonatkozó üzenet: {Exception}.|


## <a name="error-0049"></a>0049-es hiba  
 Kivétel keletkezik abban az esetben, ha nem lehet fájlt elemezni.  

 Ez a hiba Azure Machine Learning akkor következik be, amikor egy fájlt nem lehet elemezni. Ez a hibaüzenet akkor jelenik meg, ha az [adatok importálása](import-data.md) modulban kiválasztott fájlformátum nem egyezik a fájl tényleges formátumával, vagy ha a fájl nem felismerhető karaktert tartalmaz.  

**Megoldás:** Nyissa meg újra a modult, és javítsa ki a fájlformátum kijelölését, ha az nem egyezik meg a fájl formátumával. Ha lehetséges, vizsgálja meg a fájlt annak ellenőrzéséhez, hogy az nem tartalmaz-e szabálytalan karaktereket.  

|Kivételek üzenetei|
|------------------------|
|Nem lehet elemezni egy fájlt.|
|Hiba történt a (file_format} fájl elemzése közben.|
|Hiba történt a (z) {file_format} fájl elemzése közben: {file_name}.|
|Hiba történt a (file_format} fájl elemzése közben. Ok: {failure_reason}.|
|Hiba történt a (z) {file_format} fájl elemzése közben: {file_name}. Ok: {failure_reason}.|


## <a name="error-0052"></a>0052-es hiba  
 Kivétel történik, ha az Azure Storage-fiók kulcsa helytelenül van megadva.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha az Azure Storage-fiók eléréséhez használt kulcs helytelen. Előfordulhat például, hogy ez a hiba akkor jelenik meg, ha az Azure Storage-kulcsot a vágólapra másolta és beillesztette, vagy ha rossz kulcsot használt.  

 További információ az Azure Storage-fiókok kulcsának beszerzéséről: [tároló-hozzáférési kulcsok megtekintése, másolása és újragenerálása](../../storage/common/storage-account-create.md).  

**Megoldás:** Nyissa meg újra a modult, és győződjön meg arról, hogy az Azure Storage-kulcs helyes a fiókhoz; szükség esetén másolja újra a kulcsot a klasszikus Azure portálról.  

|Kivételek üzenetei|
|------------------------|
|Az Azure Storage-fiók kulcsa helytelen.|


## <a name="error-0053"></a>0053-es hiba  
 Kivétel fordul elő abban az esetben, ha nincsenek felhasználói funkciók vagy elemek Matchbox-javaslatokhoz.  

 Ez a hiba a Azure Machine Learning akkor jön létre, ha nem található a szolgáltatás vektora.  

**Megoldás:** Győződjön meg arról, hogy a bemeneti adatkészletben megtalálható a szolgáltatás vektora.  

|Kivételek üzenetei|
|------------------------|
|A felhasználói szolgáltatások vagy/vagy elemek megadása kötelező, de nincs megadva.|


## <a name="error-0056"></a>0056-es hiba  
 Kivétel történik, ha egy művelethez kiválasztott oszlopok sértik a követelményeket.  

 Ez a hiba Azure Machine Learning akkor következik be, amikor oszlopokat választ egy olyan művelethez, amelyhez az oszlopnak egy adott adattípussal kell rendelkeznie. 

 Ez a hiba akkor is előfordulhat, ha az oszlop a megfelelő adattípusú, de a használt modulhoz az oszlopnak a funkció, a címke vagy a kategorikus oszlop is jelöléssel kell rendelkeznie.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Megoldás:**

1.  Tekintse át a jelenleg kijelölt oszlopok adattípusát. 

2. Győződjön meg arról, hogy a kijelölt oszlopok kategorikus, címke vagy szolgáltatás oszlopokat használnak-e.  
  
3.  Tekintse át annak a modulnak a súgóját, amelyben az oszlopot kiválasztva megtudhatja, hogy vannak-e konkrét követelmények az adattípus vagy az oszlop használata esetén.  
  
3.  Módosítsa az oszlop típusát a művelet időtartamára a [metaadatok szerkesztése](edit-metadata.md) paranccsal. Ügyeljen arra, hogy az oszlop típusát visszaállítsa az eredeti értékre, a [metaadatok](edit-metadata.md)másik példányát használva, ha az alsóbb rétegbeli műveletekhez szükség van.  

|Kivételek üzenetei|
|------------------------|
|Egy vagy több kijelölt oszlop nem engedélyezett kategóriába tartozik.|
|A (z) {col_name} nevű oszlop nem engedélyezett kategóriába tartozik.|


## <a name="error-0057"></a>0057-es hiba  
 Kivétel keletkezik, amikor egy már létező fájlt vagy blobot próbál létrehozni.  

 Ez a kivétel akkor fordul elő, ha az [adatexportálási](export-data.md) modul vagy más modul használatával menti a folyamat eredményeit Azure Machine learning az Azure Blob Storage-ba, de megpróbál létrehozni egy már létező fájlt vagy blobot.   

**Megoldás:**

 Ez a hiba csak akkor fog megjelenni **, ha** korábban már beállította a tulajdonságot az **Azure Blob Storage írási módjára** . A tervezés szerint ez a modul hibát jelez, ha olyan blobhoz próbál meg írni egy adatkészletet, amely már létezik.

 - Nyissa meg a modul tulajdonságait, és módosítsa az **Azure Blob Storage írási mód** tulajdonságát a **felülíráshoz**.
 - Másik lehetőségként beírhatja egy másik cél blob vagy fájl nevét is, és megadhat egy olyan blobot, amely még nem létezik.  

|Kivételek üzenetei|
|------------------------|
|A fájl vagy a blob már létezik.|
|A (z) "{file_path}" fájl vagy blob már létezik.|


## <a name="error-0058"></a>0058-es hiba  
 Ez a hiba Azure Machine Learning akkor fordul elő, ha az adatkészlet nem tartalmazza a várt felirat oszlopot.  

 Ez a kivétel akkor is előfordulhat, ha a megadott címke oszlop nem egyezik a tanuló által várt adatokkal vagy adattípussal, vagy helytelen értékekkel rendelkezik. Ez a kivétel például akkor jön létre, amikor egy valós értékű címke oszlopot használ a bináris osztályozó betanításakor.  

**Megoldás:** A megoldás a használt tanulótól vagy trénertől, valamint az adatkészlet oszlopainak adattípusaitól függ. Először ellenőrizze a Machine learning-algoritmus vagy a betanítási modul követelményeit.  

 Nyissa meg újra a bemeneti adatkészletet. Ellenőrizze, hogy a felvenni kívánt oszlop megfelelő adattípussal rendelkezik-e a létrehozandó modellhez.  

 Ellenőrizze a hiányzó értékek bemeneteit, és szükség esetén távolítsa el vagy cserélje le őket.  

 Ha szükséges, adja hozzá a [metaadatok szerkesztése](edit-metadata.md) modult, és győződjön meg arról, hogy a Label (címke) oszlop címkéként van megjelölve.  

|Kivételek üzenetei|
|------------------------|
|A Label oszlop értékei és a mutató címkéje oszlop értékei nem hasonlíthatók össze.|
|A Label oszlop nem a várt módon szerepel a következőben: {dataset_name}.|
|A Label oszlop nem a várt módon szerepel a következőben: {dataset_name}, {Reason}.|
|A felirat "{column_name}" oszlopa nem várt a következőben: "{dataset_name}".|
|A felirat "{column_name}" oszlopa nem várt a következőben: "{dataset_name}", {Reason}.|


## <a name="error-0059"></a>0059-es hiba  
 Kivétel történik, ha egy oszlop választójában megadott oszlop-index nem elemezhető.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha az oszlop Választójának használatakor megadott oszlop-index nem elemezhető.  Ez a hibaüzenet akkor jelenik meg, ha az oszlop indexének formátuma érvénytelen, és nem elemezhető.  

**Megoldás:** Módosítsa az oszlop indexét érvényes index érték használatára.  

|Kivételek üzenetei|
|------------------------|
|Egy vagy több megadott oszlop indexe vagy index tartománya nem elemezhető.|
|Nem lehetett elemezni a (z) {column_index_or_range} oszlop indexét vagy tartományát.|


## <a name="error-0060"></a>0060-es hiba  
 Kivétel történik, ha a tartományon kívüli oszlop tartománya meg van adva egy oszlop-választóban.  

 Ez a hiba Azure Machine Learning akkor következik be, amikor egy tartományon kívüli oszlop van megadva az oszlop-választóban. Ez a hibaüzenet akkor jelenik meg, ha az oszlop-választóban lévő oszlop tartománya nem felel meg az adatkészlet oszlopainak.  

**Megoldás:** Módosítsa az oszlop-kiválasztó oszlopának tartományát az adatkészlet oszlopainak megfelelő értékre.  

|Kivételek üzenetei|
|------------------------|
|Érvénytelen vagy a tartományon kívüli oszlop indexelési tartománya van megadva.|
|A (z) "{column_range}" oszlop érvénytelen vagy a megengedett tartományon kívül esik.|


## <a name="error-0061"></a>0061-es hiba  
 Kivétel lép fel, amikor olyan sort ad hozzá egy adattáblához, amely eltérő számú oszloppal rendelkezik a táblánál.  

 Ez a hiba Azure Machine Learning akkor fordul elő, amikor egy olyan sort próbál hozzáadni egy adatkészlethez, amely az adatkészlettől eltérő számú oszloppal rendelkezik.  Ez a hibaüzenet akkor jelenik meg, ha az adatkészletbe felvett sor különböző számú oszlopot tartalmaz a bemeneti adatkészletből.  Ha az oszlopok száma eltérő, a sor nem fűzhető hozzá az adatkészlethez.  

**Megoldás:** Módosítsa a bemeneti adatkészletet úgy, hogy az azonos számú oszlopot adjon hozzá a sorhoz, vagy módosítsa a hozzáadott sort, hogy az azonos számú oszlop legyen az adatkészletben.  

|Kivételek üzenetei|
|------------------------|
|Az összes táblának azonos számú oszlopot kell tartalmaznia.|
|A (z) "{chunk_id_1}" adattömb oszlopai eltérnek a (z) "{chunk_id_2}" adattömb méretével: {chunk_size}.|
|A (z) "{filename_1}" fájlban (Count = {column_count_1}) lévő oszlopok száma eltér a (z) "{filename_2}" fájltól (Count = {column_count_2}).|


## <a name="error-0062"></a>0062-es hiba  
 Kivétel keletkezik, amikor két modellt próbál összehasonlítani különböző tanulói típusokkal.  

 Ez a hiba a Azure Machine Learningban akkor jön létre, amikor két különböző pontozású adathalmazra vonatkozó értékelési metrikákat nem lehet összehasonlítani. Ebben az esetben nem lehet összehasonlítani a két pontszámmal rendelkező adatkészletek előállításához használt modellek hatékonyságát.  

**Megoldás:** Győződjön meg arról, hogy a pontozásos eredményeket azonos típusú gépi tanulási modell (bináris besorolás, regresszió, többosztályos besorolás, javaslat, fürtözés, rendellenességek észlelése stb.) alapján állítja elő. Minden összehasonlítandó modellnek ugyanazzal a tanulói típussal kell rendelkeznie.  

|Kivételek üzenetei|
|------------------------|
|Minden modellnek ugyanazzal a tanulói típussal kell rendelkeznie.|
|Inkompatibilis tanuló típusa: {actual_learner_type}. A várt tanuló típusok: {expected_learner_type_list}.|


## <a name="error-0064"></a>0064-es hiba  
 Kivétel történik, ha az Azure Storage-fiók neve vagy a tárolási kulcs helytelenül van megadva.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha az Azure Storage-fiók neve vagy a tárolási kulcs helytelenül van megadva. Ez a hibaüzenet akkor jelenik meg, ha helytelen fióknevet vagy jelszót ad meg a Storage-fiókhoz. Ez akkor fordulhat elő, ha manuálisan adja meg a fiók nevét vagy jelszavát. Ez akkor is előfordulhat, ha a fiókot törölték.  

**Megoldás:** Ellenőrizze, hogy a fiók neve és jelszava helyesen van-e megadva, és létezik-e a fiók.  

|Kivételek üzenetei|
|------------------------|
|Az Azure Storage-fiók neve vagy a Storage-kulcs helytelen.|
|Helytelen az Azure Storage-fiók neve ({account_name}) vagy a fióknév.|


## <a name="error-0065"></a>0065-es hiba  
 Kivétel történik, ha az Azure-Blob neve helytelenül van megadva.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha az Azure-Blob neve helytelenül van megadva.  A következő hibaüzenet jelenik meg:  

-   A blob nem található a megadott tárolóban.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   Csak a tároló lett megadva [adatimportálási](import-data.md) kérelem forrásaként, ha a formátum Excel vagy CSV a kódolással. a tárolóban lévő összes blob tartalmának összefűzése nem engedélyezett ezekkel a formátumokkal.  
  
-   A SAS URI-ja nem tartalmazza az érvényes blob nevét.  

**Megoldás:** Tekintse át a kivételt eldobott modult. Győződjön meg arról, hogy a megadott blob létezik a Storage-fiók tárolójában, és hogy az engedélyek lehetővé teszik a blob megtekintését. Ellenőrizze, hogy a bevitel **ContainerName/filename formátumú-** e, ha az Excel vagy a CSV kódolási formátummal rendelkezik. Ellenőrizze, hogy az SAS URI tartalmazza-e az érvényes blob nevét.  

|Kivételek üzenetei|
|------------------------|
|Az Azure Storage-blob neve helytelen.|
|Az Azure Storage-blob neve ({blob_name}) helytelen.|
|A (z) "{blob_name_prefix}" előtaggal rendelkező Azure Storage-blob neve nem létezik.|
|Nem található az Azure Storage-Blobok a (z) {container_name} tárolóban.|
|Nem található olyan Azure Storage-blob, amelynek a helyettesítő elérési útja "{blob_wildcard_path}".|


## <a name="error-0066"></a>0066-es hiba  
 Kivétel történik, ha egy erőforrást nem sikerült feltölteni egy Azure-Blobba.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha egy erőforrást nem sikerült feltölteni egy Azure-Blobba.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Mindkettő ugyanazon Azure Storage-fiókba kerül, mint a bemeneti fájlt tartalmazó fiók.  

**Megoldás:** Nyissa meg újra a modult. Ellenőrizze, hogy az Azure-fiók neve, a tárolási kulcs és a tároló helyes-e, és hogy a fióknak van-e írási engedélye a tárolóba.  

|Kivételek üzenetei|
|------------------------|
|Az erőforrás nem tölthető fel az Azure Storage-ba.|
|A (z) "{source_path}" fájl nem tölthető fel az Azure Storage-ba "{dest_path}" néven.|


## <a name="error-0067"></a>0067-es hiba  
 Kivétel történik, ha egy adatkészlet eltérő számú oszloppal rendelkezik a vártnál.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha egy adatkészlet eltérő számú oszloppal rendelkezik a vártnál.  Ez a hibaüzenet akkor jelenik meg, ha az adatkészlet oszlopainak száma eltér a végrehajtás során a modul által várt oszlopok számától.  

**Megoldás:** Módosítsa a bemeneti adatkészletet vagy a paramétereket.  

|Kivételek üzenetei|
|------------------------|
|Váratlan számú oszlop szerepel a DataTable adattáblában.|
|Váratlan számú oszlop szerepel a következő adatkészletben: "{dataset_name}".|
|A rendszer a (z) "{expected_column_count}" oszlopot várta, de a rendszer a (z) "{actual_column_count}" oszlopot találta helyette.|
|A (z) {dataset_name} bemeneti adatkészletben a rendszer "{expected_column_count}" oszlopot várt, de "{actual_column_count}" oszlopot talált helyette.|


## <a name="error-0068"></a>0068-es hiba  
 Kivétel történik, ha a megadott kaptár-parancsfájl helytelen.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha szintaktikai hibák vannak a kaptár QL parancsfájljában, vagy ha a kaptár-értelmező hibát észlel a lekérdezés vagy a parancsfájl végrehajtása közben.  

**Megoldás:**

A struktúra hibaüzenetét általában visszaküldi a rendszer a hibanaplóba, így az adott hiba alapján műveleteket hajthat végre. 

+ Nyissa meg a modult, és ellenőrizze a hibákat a lekérdezésben.  
+ Ellenőrizze, hogy a lekérdezés megfelelően működik-e Azure Machine Learning a Hadoop-fürt kaptár-konzolján való bejelentkezéssel és a lekérdezés futtatásával.  
+ Próbálja meg egy külön sorban elhelyezni a megjegyzéseket a kaptár-parancsfájlban, szemben a végrehajtható utasítások és megjegyzések egyetlen sorba való keverésével.  

### <a name="resources"></a>Források

A Machine learninghez készült kaptár-lekérdezésekkel kapcsolatos segítségért tekintse meg a következő cikkeket:

+ [Struktúra-táblák létrehozása és adatok betöltése az Azure Blob Storage](../team-data-science-process/move-hive-tables.md)
+ [A táblákban lévő adatelemzés struktúra-lekérdezésekkel](../team-data-science-process/explore-data-hive-tables.md)
+ [Funkciók létrehozása az adatokhoz egy Hadoop-fürtben Hive-lekérdezések segítségével](../team-data-science-process/create-features-hive.md)
+ [SQL-felhasználók számára készült kaptár (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Kivételek üzenetei|
|------------------------|
|A struktúra parancsfájlja helytelen.|


## <a name="error-0069"></a>0069-es hiba  
 Kivétel történik, ha a megadott SQL-parancsfájl helytelen.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha a megadott SQL-parancsfájl szintaktikai problémákkal rendelkezik, vagy ha a parancsfájlban megadott oszlopok vagy táblák érvénytelenek. 

 Ez a hibaüzenet akkor jelenik meg, ha az SQL-motor hibát észlel a lekérdezés vagy a parancsfájl végrehajtása során. Az SQL-hibaüzenetet általában visszaküldi a rendszer a hibanaplóba, így az adott hiba alapján műveleteket hajthat végre.  

**Megoldás:** Tekintse át újra a modult, és vizsgálja meg az SQL-lekérdezés hibáit.  

 Ellenőrizze, hogy a lekérdezés megfelelően működik-e az Azure ML-n keresztül, ha közvetlenül az adatbázis-kiszolgálóra jelentkezik be, és futtatja a lekérdezést.  

 Ha a modul kivétele szerint egy SQL által generált üzenet jelenik meg, akkor a jelentett hiba alapján végezze el a műveletet. Előfordulhat például, hogy a hibaüzenetek időnként konkrét útmutatást tartalmaznak a valószínű hibára vonatkozóan:
+ *Nincs ilyen oszlop vagy hiányzó adatbázis*, ami azt jelzi, hogy helytelen az oszlopnév beírása. Ha biztos benne, hogy az oszlop neve helyes, az oszlop azonosítójának bejelöléséhez használjon szögletes zárójeleket vagy idézőjeleket.
+ *SQL-logikai hiba \<SQL keyword\> a közelében*, ami azt jelzi, hogy szintaktikai hiba történt a megadott kulcsszó előtt

  
|Kivételek üzenetei|
|------------------------|
|Az SQL-parancsfájl helytelen.|
|A (z) "{sql_query}" SQL-lekérdezés helytelen.|
|A (z) "{sql_query}" SQL-lekérdezés helytelen. Kivételi üzenet: {Exception}.|


## <a name="error-0070"></a>0070-es hiba  
 Kivétel történik, ha nem létező Azure-táblához próbál hozzáférni.  

 Ez a hiba Azure Machine Learning akkor fordul elő, amikor megpróbál hozzáférni egy nem létező Azure-táblához. Ez a hibaüzenet akkor jelenik meg, ha olyan táblát ad meg az Azure Storage-ban, amely nem létezik az Azure Table Storageba való olvasáskor vagy annak írásakor. Ez akkor fordulhat elő, ha nem írja be a kívánt tábla nevét, vagy ha a cél neve és a tárolási típusa nem egyezik. Előfordulhat például, hogy egy táblából kíván olvasni, de ehelyett egy blob nevét adta meg.  

**Megoldás:** Nyissa meg újra a modult annak ellenőrzéséhez, hogy a tábla neve helyes-e.  

|Kivételek üzenetei|
|------------------------|
|Az Azure-tábla nem létezik.|
|A (z) {table_name} Azure-tábla nem létezik.|


## <a name="error-0072"></a>0072-es hiba  
 A kapcsolat időtúllépése esetén kivétel történik.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha a kapcsolódás időtúllépés miatt megszakad. Ez a hibaüzenet akkor jelenik meg, ha az adatforrással vagy a céllal kapcsolatban jelenleg kapcsolati problémák léptek fel, például lassú internetkapcsolat, vagy ha az adathalmaz nagy méretű és/vagy az adatok olvasására szolgáló SQL-lekérdezés bonyolult feldolgozást végez.  

**Megoldás:** Állapítsa meg, hogy jelenleg van-e probléma az Azure Storage vagy az Internet lassú kapcsolataival.  

|Kivételek üzenetei|
|------------------------|
|A kapcsolat időtúllépése megtörtént.|


## <a name="error-0073"></a>0073-es hiba  
 Kivétel történik, ha hiba történik egy oszlop más típusúra konvertálásakor.  

 Ez a hiba Azure Machine Learning akkor következik be, amikor nem lehetséges az oszlop konvertálása más típusra.  Ez a hibaüzenet akkor jelenik meg, ha egy modulnak egy adott típusra van szüksége, és az oszlopot nem lehet az új típusra konvertálni.  

**Megoldás:** Módosítsa a bemeneti adatkészletet úgy, hogy az oszlop a belső kivétel alapján legyen konvertálható.  

|Kivételek üzenetei|
|------------------------|
|Az oszlop konvertálása nem sikerült.|
|Nem sikerült az oszlop konvertálása {target_type} értékre.|


## <a name="error-0075"></a>0075-es hiba  
Kivétel történik, ha egy adatkészlet kvantálásakor érvénytelen dobozolási függvényt használ a rendszer.  

Ez a hiba Azure Machine Learning akkor következik be, amikor nem támogatott metódussal próbál meg raktárhelyeket használni, vagy ha a paraméterek kombinációi érvénytelenek.  

**Megoldás:**

Az eseményhez tartozó hibakezelés a Azure Machine Learning egy korábbi verziójában lett bevezetve, amely a dobozolási metódusok további testreszabását tette lehetővé. Jelenleg az összes dobozolási-módszer egy legördülő lista egy kiválasztásán alapul, így a továbbiakban nem lehet ezt a hibát megszerezni.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

|Kivételek üzenetei|
|------------------------|
|Érvénytelen dobozolási függvény van használatban.|


## <a name="error-0077"></a>0077-es hiba  
 Kivétel történik, ha az ismeretlen blob-írási mód lett átadva.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha érvénytelen argumentumot ad át a blob-fájl célhelyének vagy forrásának specifikációi.  

**Megoldás:** Szinte minden olyan modulban, amely az Azure Blob Storage-ba vagy onnan exportálja az adatok importálását és exportálását, legördülő lista használatával rendeli hozzá az írási módot vezérlő paramétereket. ezért nem lehet átadni egy érvénytelen értéket, és ez a hiba nem jelenik meg. Ez a hiba egy későbbi kiadásban elavulttá válik.  

|Kivételek üzenetei|
|------------------------|
|Nem támogatott blob írási mód.|
|Nem támogatott blob-írási üzemmód: {blob_write_mode}.|


## <a name="error-0078"></a>0078-es hiba  
 Kivétel történik, ha az [importáláshoz](import-data.md) használt http-beállítás egy 3xx-állapotkódot kap, amely átirányítást jelez.  

 Ez a hiba Azure Machine Learning akkor következik be, amikor az [adatimportálás](import-data.md) http-beállítása egy 3xx (301, 302, 304 stb.) kap, amely az átirányítás állapotát jelzi. Ez a hibaüzenet akkor jelenik meg, ha olyan HTTP-forráshoz próbál csatlakozni, amely egy másik lapra irányítja át a böngészőt. Biztonsági okokból a webhelyek átirányítása nem engedélyezett a Azure Machine Learning adatforrásaként.  

**Megoldás:** Ha a webhely megbízható webhely, adja meg közvetlenül az átirányított URL-címet.  

|Kivételek üzenetei|
|------------------------|
|A HTTP-átirányítás nem engedélyezett.|


## <a name="error-0079"></a>0079-es hiba  
 Kivétel történik, ha az Azure Storage-tároló neve helytelenül van megadva.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha az Azure Storage-tároló neve helytelenül van megadva. Ezt a hibaüzenetet akkor kapja meg, ha a tárolót és a blobot (fájl) nem adta meg **a blob elérési útja alapján az** Azure Blob Storageba való íráskor.  

**Megoldás:** Tekintse át újra az [adatexportálási](export-data.md) modult, és ellenőrizze, hogy a blob megadott elérési útja tartalmazza-e a tárolót és a fájlnevet is a **tároló/fájlnév** formátumban.  

|Kivételek üzenetei|
|------------------------|
|Az Azure Storage-tároló neve helytelen.|
|Az Azure Storage-tároló neve ({container_name}) helytelen; a rendszer a tároló nevét vagy blobját várta.|


## <a name="error-0080"></a>0080-es hiba  
 Kivétel történik, ha az összes hiányzó értékkel rendelkező oszlop nem engedélyezett a modulban.  

 Ez a hiba akkor jelenik meg Azure Machine Learningban, ha a modul által felhasznált oszlopok közül egy vagy több tartalmazza az összes hiányzó értéket. Ha például egy modul minden oszlophoz összesíti a statisztikát, nem tud adatokat tartalmazó oszlopon működni. Ilyen esetekben a modul végrehajtása ezzel a kivétellel megszűnik.  

**Megoldás:** Nyissa meg újra a bemeneti adatkészletet, és távolítson el minden olyan oszlopot, amely tartalmazza az összes hiányzó értéket.  

|Kivételek üzenetei|
|------------------------|
|Az összes hiányzó értékkel rendelkező oszlopok nem engedélyezettek.|
|A (col_index_or_name} oszlop minden hiányzó értékkel rendelkezik.|


## <a name="error-0081"></a>0081-es hiba  
 Kivétel keletkezik a PCA modulban, ha a csökkenteni kívánt méretek száma egyenlő a bemeneti adatkészletben lévő funkciók oszlopainak számával, amely legalább egy ritka funkció oszlopot tartalmaz.  

 Ez a hiba Azure Machine Learning a következő feltételek teljesülése esetén jön létre: (a) a bemeneti adatkészlet legalább egy ritka oszlopával rendelkezik, és (b) a kért dimenziók végső száma megegyezik a bemeneti dimenziók számával.  

**Megoldás:** Vegye figyelembe, hogy a kimenetben lévő méretek száma kevesebb, mint a bemeneti dimenziók száma. Ez a PCA alkalmazásaiban jellemző.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Kivételek üzenetei|
|------------------------|
|A ritka funkció típusú oszlopokat tartalmazó adatkészlet esetében a méretek számának kisebbnek kell lennie, mint a szolgáltatás oszlopainak száma.|


## <a name="error-0082"></a>0082-es hiba  
 Kivétel történik, ha egy modellt nem sikerült deszerializálni.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha egy mentett gépi tanulási modellt vagy átalakítót nem lehet betölteni a Azure Machine Learning futtatókörnyezet újabb verziójával a megszakítási változás eredményeképpen.  

**Megoldás:** A modellt vagy átalakítót előállító betanítási folyamatot újra kell futtatni, és a modellt vagy az átalakítást újra kell menteni.  

|Kivételek üzenetei|
|------------------------|
|A modellt nem sikerült deszerializálni, mert valószínűleg egy régebbi szerializálási formátummal lett szerializálva. Kérjük, végezze el a modell újratanítását és újramentését.|


## <a name="error-0083"></a>0083-es hiba  
 Kivétel történik, ha a képzéshez használt adatkészlet nem használható konkrét típusú tanulók számára.  

 Ez a hiba akkor jön létre a Azure Machine Learningban, ha az adatkészlet nem kompatibilis a betanított tanulóval. Előfordulhat például, hogy az adatkészlet tartalmaz legalább egy hiányzó értéket az egyes sorokban, és ennek eredményeképpen a teljes adatkészlet kimarad a betanítás során. Más esetekben a gépi tanulási algoritmusok, például a anomáliák észlelése nem vár feliratokat, és ezt a kivételt kidobják, ha a címkék szerepelnek az adatkészletben.  

**Megoldás:** Tekintse át a bemeneti adatkészletre vonatkozó követelmények ellenőrzését szolgáló tanuló dokumentációját. Vizsgálja meg az oszlopokat, hogy megjelenjenek az összes szükséges oszlop.  

|Kivételek üzenetei|
|------------------------|
|A betanításhoz használt adatkészlet érvénytelen.|
|{data_name} a betanításhoz érvénytelen adatértéket tartalmaz.|
|{data_name} a betanításhoz érvénytelen adatértéket tartalmaz. A tanuló típusa: {learner_type}.|
|{data_name} a betanításhoz érvénytelen adatértéket tartalmaz. A tanuló típusa: {learner_type}. Ok: {Reason}.|
|Nem sikerült alkalmazni a (z) {action_name} műveletet a (z) {data_name} betanítási művelethez. Ok: {Reason}.|


## <a name="error-0084"></a>0084-es hiba  
 Kivétel történik az R-parancsfájlokból előállított pontszámok kiértékelése során. Ez jelenleg nem támogatott.  

 Ez a hiba Azure Machine Learning fordul elő, ha az egyik modul használatával próbálja kiértékelni a modelleket egy, a pontszámokat tartalmazó R-szkriptből származó kimenettel.  

**Megoldás:**

|Kivételek üzenetei|
|------------------------|
|Az egyéni modell által előállított pontszámok kiértékelése jelenleg nem támogatott.|


## <a name="error-0085"></a>0085-es hiba  
 Kivétel történik, ha a parancsfájl kiértékelése hibát jelez.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha szintaktikai hibákat tartalmazó egyéni parancsfájlt futtat.  

**Megoldás:** Tekintse át a kódot egy külső szerkesztőben, és ellenőrizze a hibákat.  

|Kivételek üzenetei|
|------------------------|
|Hiba történt a parancsfájl kiértékelése során.|
|A parancsfájl kiértékelése során a következő hiba történt, további információért tekintse meg a kimeneti naplót:<br />A (----------) {script_language} értelmező hibaüzenetének megkezdése----------<br />üzenetet<br />A (----------) {script_language} értelmező hibaüzenetének vége----------|


## <a name="error-0090"></a>0090-es hiba  
 Kivétel történik, ha a struktúra-tábla létrehozása sikertelen.  

 Ez a hiba Azure Machine Learning akkor fordul elő, ha [adatexportálási](export-data.md) vagy más lehetőséget használ az HDInsight-fürtre történő adatmentéshez, és a megadott struktúra-tábla nem hozható létre.  

**Megoldás:** Ellenőrizze a fürthöz társított Azure Storage-fiók nevét, és ellenőrizze, hogy ugyanazt a fiókot használja-e a modul tulajdonságainál.  

|Kivételek üzenetei|
|------------------------|
|Nem sikerült létrehozni a kaptár-táblázatot. HDInsight-fürt esetén győződjön meg arról, hogy a fürthöz társított Azure Storage-fiók neve ugyanaz, mint amit a Module paraméteren keresztül továbbított.|
|Nem sikerült létrehozni a (z) {table_name} struktúra-táblázatot. HDInsight-fürt esetén győződjön meg arról, hogy a fürthöz társított Azure Storage-fiók neve ugyanaz, mint amit a Module paraméteren keresztül továbbított.|
|Nem sikerült létrehozni a (z) {table_name} struktúra-táblázatot. HDInsight-fürt esetén győződjön meg arról, hogy a fürthöz társított Azure Storage-fiók neve {cluster_name}.|


## <a name="error-0102"></a>0102-es hiba  
 Nem lehet kibontani a ZIP-fájlt.  

 Ez a hiba Azure Machine Learning akkor következik be, amikor egy. zip kiterjesztésű tömörített csomagot importál, de a csomag nem zip-fájl, vagy a fájl nem támogatott ZIP-formátumot használ.  

**Megoldás:** Győződjön meg arról, hogy a kiválasztott fájl érvényes. zip-fájl, és hogy az egyik támogatott tömörítési algoritmus használatával lett tömörítve.  

 Ha az adatkészletek tömörített formátumban való importálásakor ezt a hibaüzenetet kapja, ellenőrizze, hogy az összes foglalt fájl használja-e a támogatott fájlformátumok egyikét, és hogy Unicode formátumú-e.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Próbálja meg beolvasni a kívánt fájlokat egy új tömörített tömörített mappába, és próbálkozzon újra az egyéni modul hozzáadásával.  

|Kivételek üzenetei|
|------------------------|
|A megadott ZIP-fájl formátuma nem megfelelő.|


## <a name="error-0105"></a>0105-es hiba  
 Ez a hiba akkor jelenik meg, ha egy modul definíciós fájlja nem támogatott paraméter-típust tartalmaz  
  
 Ez a hiba a Azure Machine Learning akkor jön létre, amikor létrehoz egy egyéni modul XML-definícióját, és a definícióban szereplő paraméter vagy argumentum típusa nem egyezik a támogatott típussal.  
  
**Megoldás:** Győződjön meg arról, hogy az egyéni modul XML-definíciós fájljában található bármely **ARG** -elem Type tulajdonsága támogatott típusú.  
  
|Kivételek üzenetei|  
|------------------------|  
|A paraméter típusa nem támogatott.|  
|A megadott "" paraméter típusa nem támogatott {0} .|  


## <a name="error-0107"></a>0107-es hiba  
 Ha egy modul definíciós fájlja nem támogatott kimeneti típust határoz meg  
  
 Ez a hiba akkor jön létre Azure Machine Learning, ha egy egyéni modul XML-definíciójában lévő kimeneti port típusa nem egyezik a támogatott típussal.  
  
**Megoldás:** Győződjön meg arról, hogy az egyéni modul XML-definíciós fájljának Type tulajdonsága támogatott típusú.  
  
|Kivételek üzenetei|  
|------------------------|  
|A kimeneti típus nem támogatott.|  
|A megadott "{output_type}" kimeneti típus nem támogatott.|  


## <a name="error-0125"></a>0125-es hiba  
 Akkor fordul elő, ha több adatkészlet sémája nem egyezik.  

**Megoldás:**

|Kivételek üzenetei|
|------------------------|
|Az adatkészlet sémája nem egyezik.|


## <a name="error-0127"></a>0127-es hiba  
 A képképpont mérete meghaladja az engedélyezett korlátot  

 Ez a hiba akkor fordul elő, ha lemezképeket olvas be a besoroláshoz, és a lemezképek nagyobbak, mint amennyit a modell képes kezelni.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Kivételek üzenetei|
|------------------------|
|A képképpont mérete meghaladja az engedélyezett korlátot.|
|A (z) {file_path} fájl képpontjának mérete meghaladja az engedélyezett korlátot: {size_limit}.|


## <a name="error-0128"></a>0128-es hiba  
 A kategorikus oszlopok feltételes valószínűségének száma meghaladja a korlátot.  

**Megoldás:**

|Kivételek üzenetei|
|------------------------|
|A kategorikus oszlopok feltételes valószínűségének száma meghaladja a korlátot.|
|A kategorikus oszlopok feltételes valószínűségének száma meghaladja a korlátot. A (z) {column_name_or_index_1} és a (z) {column_name_or_index_2} oszlop a problémás pár.|


## <a name="error-0129"></a>0129-es hiba  
 Az adatkészlet oszlopainak száma meghaladja az engedélyezett korlátot.  

**Megoldás:**

|Kivételek üzenetei|
|------------------------|
|Az adatkészlet oszlopainak száma meghaladja az engedélyezett korlátot.|
|A (z) {dataset_name} adatkészletben lévő oszlopok száma meghaladja az engedélyezett értéket.|
|A (z) {dataset_name} adatkészletben lévő oszlopok száma meghaladja a (z) {component_name} megengedett korlátját.|
|A (z) "{dataset_name}" adatkészletben lévő oszlopok száma meghaladja a (z) {limit_columns_count} megengedett "{component_name}" korlátot.|


## <a name="error-0134"></a>0134-es hiba
Kivétel keletkezik, ha hiányzik a Label oszlop, vagy nem áll rendelkezésre elegendő számú címkézett sor.  

Ez a hiba akkor fordul elő, ha a modulhoz címke oszlop szükséges, de nem tartalmaz egyet az oszlop kiválasztásakor, vagy a Label (címke) oszlopban túl sok érték hiányzik.

Ez a hiba akkor is megjelenhet, ha egy korábbi művelet megváltoztatja az adatkészletet úgy, hogy a nem megfelelő sorok elérhetők legyenek egy alsóbb rétegbeli művelet számára. Tegyük fel például, hogy a **partíció és a minta** modul egy kifejezését használja az adatkészlet értékek szerinti felosztásához. Ha a kifejezéshez nem található egyezés, akkor a partícióból származó egyik adatkészlet üres lenne.

Megoldás: 

 Ha címkét tartalmazó oszlopot is tartalmaz az oszlop kijelölésekor, de nem ismeri fel, a [metaadatok szerkesztése](edit-metadata.md) modullal megjelölheti a címkét oszlopként.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Ezután a [tiszta hiányzó](clean-missing-data.md) adatmodul használatával eltávolíthatja a hiányzó értékekkel rendelkező sorokat a Label (címke) oszlopban. 

 Ellenőrizze a bemeneti adatkészleteket, és győződjön meg arról, hogy érvényes adatokat tartalmaznak, és hogy elegendő sor van a művelet követelményeinek kielégítéséhez. Számos algoritmus hibaüzenetet küld, ha néhány minimális számú adatra van szükségük, de az adat csak néhány sort tartalmaz, vagy csak egy fejlécet.

|Kivételek üzenetei|
|------------------------|
|Kivétel keletkezik, ha hiányzik a Label oszlop, vagy nem áll rendelkezésre elegendő számú címkézett sor.|
|Kivétel történik, ha a címke oszlop hiányzik vagy kevesebb, mint {required_rows_count} címkézett sor.|
|Kivétel történik, ha a (dataset_name} adatkészletben hiányzik a Label oszlop, vagy kevesebb, mint {required_rows_count} címkézett sor.|


## <a name="error-0138"></a>0138-es hiba  
 A memória kimerült, nem lehet befejezni a modul futtatását. Az adatkészlet leegyszerűsítése segíthet enyhíteni a problémát.  

 Ez a hiba akkor fordul elő, ha a futó modul több memóriát igényel, mint amennyi az Azure-tárolóban elérhető. Ez akkor fordulhat elő, ha nagyméretű adatkészlettel dolgozik, és az aktuális művelet nem fér bele a memóriába.  

**Megoldás:** Ha nagyméretű adatkészletet próbál beolvasni, és a művelet nem hajtható végre, az adatkészlet leegyszerűsítése segíthet.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Kivételek üzenetei|
|------------------------|
|A memória kimerült, nem lehet befejezni a modul futtatását.|
|A memória kimerült, nem lehet befejezni a modul futtatását. Részletek: {details}|


## <a name="error-0141"></a>0141-es hiba  
 Kivétel történik, ha a kijelölt numerikus oszlopok száma és a kategorikus és sztring oszlopok egyedi értékei túl kicsik.  

 Ez a hiba Azure Machine Learning akkor következik be, amikor nincs elég egyedi érték a kijelölt oszlopban a művelet végrehajtásához.  

**Megoldás:** Egyes műveletek statisztikai műveleteket hajtanak végre a szolgáltatáson és a kategorikus oszlopokon, és ha nincs elég érték, a művelet meghiúsulhat, vagy érvénytelen eredményt adhat vissza. Ellenőrizze az adatkészletet, és tekintse meg, hogy hány érték szerepel a funkció és a címke oszlopaiban, és határozza meg, hogy a végrehajtani kívánt művelet statisztikailag érvényes-e.  

 Ha a forrás-adatkészlet érvényes, akkor azt is megteheti, hogy egy felsőbb rétegbeli adatmanipuláció vagy metaadat-művelet módosította-e az adatokat, és eltávolított néhány értéket.  

 Ha a felsőbb rétegbeli műveletek között felosztás, mintavételezés vagy újraszámítás történt, ellenőrizze, hogy a kimenetek tartalmazzák-e a sorok és az értékek várt számát.  

|Kivételek üzenetei|
|------------------------|
|A kijelölt numerikus oszlopok és a kategorikus és sztring oszlopok egyedi értékeinek száma túl kicsi.|
|A kategorikus és sztring oszlopokban szereplő kijelölt numerikus oszlopok és egyedi értékek teljes számát (jelenleg {actual_num}) legalább {lower_boundary} értékre kell állítani.|


## <a name="error-0154"></a>0154-es hiba  
 Kivétel történik, ha a felhasználó a nem kompatibilis oszlop típusú kulcs oszlopokban próbál meg csatlakozni az adattípushoz.

|Kivételek üzenetei|
|------------------------|
|A kulcs oszlop elemeinek típusai nem kompatibilisek.|
|A kulcs oszlop elemeinek típusai nem kompatibilisek. (bal: {keys_left}; jobb: {keys_right})|


## <a name="error-0155"></a>0155-es hiba  
 Kivétel történik, ha az adatkészlet oszlopainak neve nem karakterlánc.

|Kivételek üzenetei|
|------------------------|
|A dataframe oszlop nevének karakterlánc típusúnak kell lennie. Az oszlopnevek nem karakterláncok.|
|A dataframe oszlop nevének karakterlánc típusúnak kell lennie. A (column_names} oszlopnevek nem karakterláncok.|


## <a name="error-0156"></a>0156-es hiba  
 Kivétel történik, ha nem sikerült beolvasni az Azure SQL Databaseból származó adatokból.

|Kivételek üzenetei|
|------------------------|
|Nem sikerült az adatok beolvasása a Azure SQL Databaseból.|
|Nem sikerült az adatok beolvasása a következő Azure SQL Databaseról: {detailed_message} DB: {database_server_name}: {database_name} lekérdezés: {sql_statement}|


## <a name="error-0157"></a>0157-es hiba  
 Az adattár nem található.

|Kivételek üzenetei|
|------------------------|
|Az adattár adatai érvénytelenek.|
|Az adattár adatai érvénytelenek. Nem sikerült beolvasni a (z) {datastore_name} AzureML-adattárt a (z) {workspace_name} munkaterületen.|


## <a name="error-0158"></a>0158-es hiba
 A rendszer akkor dobja el, ha egy átalakítási könyvtár érvénytelen.

|Kivételek üzenetei|
|------------------------------------------------------------|
|A megadott TransformationDirectory érvénytelen.|
|A (z) {arg_name} TransformationDirectory érvénytelen. Ok: {Reason}. Futtassa újra az átalakítási fájlt létrehozó betanítási kísérletet. Ha a betanítási kísérlet törölve lett, hozza létre újra és mentse az átalakító fájlt.|
|A (z) {arg_name} TransformationDirectory érvénytelen. Ok: {Reason}. {troubleshoot_hint}|


## <a name="error-0159"></a>0159-es hiba
 Kivétel történik, ha a modul modelljének könyvtára érvénytelen. 

|Kivételek üzenetei|
|------------------------------------------------------------|
|A megadott ModelDirectory érvénytelen.|
|A (z) {arg_name} ModelDirectory érvénytelen.|
|A (z) {arg_name} ModelDirectory érvénytelen. Ok: {Reason}.|
|A (z) {arg_name} ModelDirectory érvénytelen. Ok: {Reason}. {troubleshoot_hint}|


## <a name="error-1000"></a>1000-es hiba  
Belső függvénytár-kivétel.  

Ez a hiba a nem kezelt belső motor hibáinak rögzítésére szolgál. Ezért előfordulhat, hogy a hiba oka eltérő lehet a hibát generáló modultól függően.  

Ha további segítségre van szüksége, javasoljuk, hogy tegye közzé a hibához tartozó részletes üzenetet a [Azure Machine learning fórumnak](/answers/topics/azure-machine-learning.html), a forgatókönyv leírásával együtt, beleértve a bemenetként használt adatokat is. Ez a visszajelzés segít rangsorolni a hibákat, és azonosítani a legfontosabb problémákat a további munkához.  

|Kivételek üzenetei|
|------------------------|
|Függvénytár-kivétel.|
|Függvénytár-kivétel: {Exception}.|
|Ismeretlen függvénytár-kivétel: {Exception}. {customer_support_guidance}.|


## <a name="execute-python-script-module"></a>Python parancsfájl-modul végrehajtása

Keressen **azureml_main** a **Python parancsfájl-modul végrehajtása** **70_driver_logsban** , és megtalálhatja, hogy melyik sor hiba miatt történt. Például a "fájl"/tmp/tmp01_ID/user_script. másolt ", a 17. sor azureml_main" "azt jelzi, hogy a hiba a Python-szkript 17 sorában történt.
