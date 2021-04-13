---
title: Mélyreható tanulás és gépi tanulás
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan kapcsolódik a Deep learning a Machine learninghez és a mesterséges intelligenciához. A Azure Machine Learning a csalások észlelése, az objektumok észlelése és egyéb lehetőségek mélyreható tanulási modelljeit használja.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 04/12/2021
ms.custom: contperf-fy21q1,contperfq1
ms.openlocfilehash: a9e87796c3557f21bb6192c7d7d0c0925d08d786
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311753"
---
# <a name="deep-learning-vs-machine-learning-in-azure-machine-learning"></a>Mélyreható tanulás és gépi tanulás a Azure Machine Learning

Ez a cikk a részletes tanulást és a gépi tanulást ismerteti, valamint azt, hogy azok hogyan illeszkednek a mesterséges intelligencia tágabb kategóriába. Ismerkedjen meg a Azure Machine Learningre épülő mélyreható tanulási megoldásokkal, például a csalások észlelésével, a hang-és Arcfelismerés, a hangulat elemzése és az idősorozat-előrejelzés használatával.

Az algoritmusok megoldáshoz való kiválasztásával kapcsolatos útmutatásért tekintse meg a [Machine learning algoritmust tartalmazó Cheat lapot](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri).

## <a name="deep-learning-machine-learning-and-ai"></a>Mély tanulás, gépi tanulás és AI

![Kapcsolati diagram: AI vs. Machine learning és Deep learning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Vegye figyelembe a következő definíciókat a Deep learning és a Machine learning és a AI megismerése érdekében:

- A **Deep learning** a gépi tanulás egy olyan részhalmaza, amely mesterséges neurális hálózatokon alapul. A _tanulási folyamat_ _mély_ , mert a mesterséges neurális hálózatok szerkezete több bemeneti, kimeneti és rejtett rétegből áll. Mindegyik réteg egységekből épül fel, amelyek a bemenetet olyan információvá alakítják át, amelyet a következő réteg egy adott prediktív feladat elvégzéséhez fel tud használni. Ennek a struktúrának köszönhetően a gépek saját adatfeldolgozással is megismerhetik.

- A **gépi tanulás** a mesterséges intelligencia olyan részhalmaza, amely technikákat használ (például a mély tanulást), amely lehetővé teszi a gépek számára, hogy a feladatok fejlesztéséhez használják a felhasználói élményt. A _tanulási folyamat_ a következő lépéseken alapul:

   1. Adatcsatorna átalakítása egy algoritmusba. (Ebben a lépésben további információkat adhat meg a modellhez, például a szolgáltatások kinyerésének végrehajtásával.)
   1. Ezekkel az értékekkel betaníthatja a modelleket.
   1. Tesztelje és telepítse a modellt.
   1. Az üzembe helyezett modell használata automatizált prediktív feladathoz. (Más szóval hívja meg és használja az üzembe helyezett modellt a modell által visszaadott előrejelzések fogadásához.)

- A **mesterséges intelligencia (AI)** olyan technika, amely lehetővé teszi a számítógépek számára az emberi intelligenciát. A gépi tanulást is magában foglalja. 
 
A gépi tanulás és a mélyreható tanulási technikák használatával olyan számítógéprendszer és alkalmazások hozhatók létre, amelyek az emberi intelligenciához gyakran társított feladatokat látnak el. Ezek a feladatok a képek felismerését, a beszédfelismerést és a nyelvi fordítást tartalmazzák.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>A Deep learning és a Machine learning módszerei 

Most, hogy áttekintette a Machine learning és a Deep learning áttekintését, vesse össze a két technikát. A gépi tanulásban az algoritmusnak azt kell megadnia, hogyan lehet pontos előrejelzést készíteni a további információk felhasználásával (például a szolgáltatások kinyerésének elvégzésével). A Deep learningben az algoritmus megtudhatja, hogyan készíthet pontos előrejelzést a saját adatfeldolgozásával, a mesterséges neurális hálózati struktúra köszönhetően.

A következő táblázat részletesebben összehasonlítja a két technikát:

| |Minden gépi tanulás |Csak mély tanulás|
|---|---|---|
|  **Adatpontok száma** | Az előrejelzéseket kis mennyiségű adat használatával végezheti el. | Nagy mennyiségű betanítási adattal kell használnia a jóslatok létrehozásához. |
|  **Hardver függőségei** | Az alacsony végű gépeken is működhet. Nincs szükség nagy mennyiségű számítási teljesítményre. | A magas végú gépektől függ. Természeténél fogva nagy számú mátrix-szorzási műveletet végez. A GPU képes hatékonyan optimalizálni ezeket a műveleteket. |
|  **Featurization folyamat** | A funkciók pontos azonosítását és létrehozását igénylik a felhasználók számára. | Megismerheti az adatok magas szintű funkcióit, és saját maga is létrehoz új szolgáltatásokat. |
|  **Tanulási megközelítés** | A tanulási folyamat kisebb lépésekre osztása. Ezután kombinálja az egyes lépések eredményeit egyetlen kimenetre. | Áthalad a tanulási folyamaton a teljes körű probléma megoldásával. |
|  **Végrehajtási idő** | Viszonylag kevés időt vesz igénybe a betanítás, néhány másodperctől néhány órára. | A tanítás általában hosszú időt vesz igénybe, mivel a Deep learning algoritmus számos réteget magában foglal. |
|  **Kimenet** | A kimenet általában numerikus érték, például pontszám vagy besorolás. | A kimenetnek több formátuma is lehet, például egy szöveg, egy pontszám vagy egy hang. |

## <a name="what-is-transfer-learning"></a>Mi az az adatátviteli tanulás?

A Deep learning-modellek betanításához gyakran nagy mennyiségű betanítási adatok, nagy teljesítményű számítási erőforrások (GPU, TPU) és hosszú betanítási idő szükséges. Abban az esetben, ha nem rendelkezik ezekkel a lehetőségekkel, a betanítási folyamatot az *adatátviteli tanulási* módszernek nevezett eljárással teheti meg.

Az átvitel a tanulás olyan technika, amely az egyik probléma megoldását egy másik, de kapcsolódó problémára is kiszolgálja.

A neurális hálózatok szerkezete miatt az első rétegek általában alacsonyabb szintű funkciókat tartalmaznak, míg a rétegek végső készlete magasabb szintű funkciót tartalmaz, amely közelebb áll a szóban forgó tartományhoz. Ha a végső rétegeket új tartományba vagy problémába kívánja használni, jelentősen csökkentheti az új modell betanításához szükséges idő, adatok és számítási erőforrások mennyiségét. Ha például már rendelkezik egy olyan modellel, amely az autókat ismeri fel, ezt a modellt átviheti a Learning használatával is, és felismerheti a teherautók, motorkerékpárok és más típusú járművek használatát.

Ismerje meg, hogyan alkalmazhatja az átvitelt a képbesorolásra a Azure Machine Learning egy nyílt forráskódú keretrendszer használatával: [a Deep learning PyTorch-modell betanítása az adatátviteli tanulással](./how-to-train-pytorch.md?WT.mc_id=docs-article-lazzeri).

## <a name="deep-learning-use-cases"></a>Mély tanulási használati esetek

A mesterséges neurális hálózati struktúra miatt a Deep learning kiemelkedik a strukturálatlan adat, például képek, hang, videó és szöveg mintázatának azonosítására. Emiatt a Deep learning gyorsan átalakítja számos iparágat, például az egészségügyet, az energiát, a pénzügyt és a szállítást. Ezek az iparágak mostantól a hagyományos üzleti folyamatokat gondolják át. 

A Deep learning leggyakoribb alkalmazásai a következő bekezdésekben olvashatók. Azure Machine Learning a modelleket egy nyílt forráskódú keretrendszerből építheti fel, vagy felépítheti a modellt a megadott eszközök használatával.

### <a name="named-entity-recognition"></a>Nevesített entitások felismerése

A nevesített entitások felismerése olyan mély tanulási módszer, amely a szöveg bemenetként való bevitelét és egy előre megadott osztályba alakítását végzi. Ez az új információ lehet postai irányítószám, dátum, termékazonosító. Az adatok egy strukturált sémában tárolhatók, és a címek listájának összeállíthatók, vagy egy Identity Validation Engine viszonyítási céljaként szolgálnak.

### <a name="object-detection"></a>Objektumészlelés

A Deep learning számos objektum-észlelési használati esettel lett alkalmazva. Az objektumok észlelése két részből áll: a képek besorolása, majd a képek honosítása. A rendszerkép _besorolása_ azonosítja a rendszerkép objektumait, például az autókat vagy a személyeket. A rendszerkép _honosítása_ megadja az objektumok adott helyét. 

Az objektumok észlelése már használatban van olyan iparágakban, mint például a játékok, a kiskereskedelem, a turizmus és az önkiszolgáló autók.

### <a name="image-caption-generation"></a>Képfelirat létrehozása

A rendszerkép-feliratozásban, az adott képhez hasonlóan a képfeliratok esetében a rendszernek egy olyan feliratot kell létrehoznia, amely leírja a rendszerkép tartalmát. Ha a fényképekben objektumokat talál és címkéz, a következő lépés a címkék leíró mondatokra való bekapcsolása. 

A képfeliratos alkalmazások általában a párhuzamos neurális hálózatokat használják a rendszerképben lévő objektumok azonosítására, majd egy visszatérő neurális hálózatot használnak a címkék konzisztens mondatokra való bekapcsolásához.

### <a name="machine-translation"></a>Gépi fordítás

A gépi fordítás egy adott nyelv szavait vagy mondatait veszi figyelembe, és automatikusan más nyelvre fordítja őket. A gépi fordítás már régóta, de a Deep learning két konkrét területen látványos eredményeket érhet el: a szöveg automatikus fordítását (és a beszéd szövegének fordítását), valamint a képek automatikus fordítását.

A megfelelő adatátalakítással a neurális hálózat képes értelmezni a szöveg-, hang-és vizuális jeleket. A gépi fordítás használatával azonosíthatók a hangtöredékek a nagyobb hangfájlokban, és szövegként is áthelyezhetők a kimondott szövegből vagy képekből.

### <a name="text-analytics"></a>Szövegelemzés

A mélyreható tanulási módszereken alapuló szöveges elemzések nagy mennyiségű szöveges adat (például orvosi dokumentumok vagy költségek visszaigazolása) elemzését, a minták felismerését, valamint a szervezett és tömör információk létrehozását ismertetik.

A vállalatok mély tanulással végeznek szöveges elemzést a bennfentes kereskedelem és a kormányzati szabályozásoknak való megfelelés észlelése érdekében. Egy másik gyakori példa a biztosítási csalás: a szöveges elemzés gyakran a nagy mennyiségű dokumentum elemzésére szolgál, hogy felismerje a biztosítási követelések csalásának esélyét.

## <a name="artificial-neural-networks"></a>Mesterséges neurális hálózatok

A mesterséges neurális hálózatokat összekapcsolt csomópontok rétegei alkotják. A Deep learning-modellek nagy mennyiségű réteget használó neurális hálózatokat használnak. 

A következő fejezetek a legnépszerűbb mesterséges neurális hálózati tipológiák ismertetik.

### <a name="feedforward-neural-network"></a>Feedforward neurális hálózat

A feedforward neurális hálózat a mesterséges neurális hálózat legegyszerűebb típusa. Egy feedforward-hálózatban az adatok csak egy irányba mozdulnak el a bemeneti rétegből a kimeneti rétegbe. A Feedforward neurális hálózatokat a rendszer a rejtett rétegek sorozatán keresztül alakítja át. Minden réteg idegsejtekből tevődik össze, és minden réteg teljesen csatlakoztatva van a rétegben lévő összes neuronokhoz. Az utolsó teljesen csatlakoztatott réteg (a kimeneti réteg) a generált előrejelzéseket jelöli.

### <a name="recurrent-neural-network-rnn"></a>Ismétlődő neurális hálózat (RNN)

Az ismétlődő neurális hálózatok széles körben használják a mesterséges neurális hálózatot. Ezek a hálózatok elmentik egy réteg kimenetét, és visszaküldik a bemeneti rétegnek a réteg eredményének előrejelzéséhez. Az ismétlődő neurális hálózatok nagy tanulási képességekkel rendelkeznek. Ezek széles körben használatosak olyan összetett feladatokhoz, mint például az idősorozat-előrejelzés, a tanulási kézírás és a nyelv felismerése.

### <a name="convolutional-neural-network-cnn"></a>Egyazon neurális hálózat (CNN)

A többrendszeres neurális hálózat egy különösen hatékony mesterséges neurális hálózat, amely egyedi architektúrát mutat be. A rétegek három dimenzióban vannak rendezve: szélesség, magasság és mélység. Az egyik rétegben lévő neuronok nem a következő rétegben lévő összes neuronokhoz csatlakoznak, hanem csak a réteg neuronok kis régiójához. A végső kimenetet a rendszer a mélységi dimenzió mentén szervezett valószínűségi pontszámok egyetlen vektorára csökkenti. 

A többszintű neurális hálózatok olyan területeken lettek felhasználva, mint a videó-felismerés, a képfelismerés és az ajánlott rendszerek.

### <a name="generative-adversarial-network-gan"></a>Generatív kontradiktórius hálózat (GAN)

A generatív kontradiktórius hálózatok olyan generatív modellek, amelyek valósághű tartalmak, például képek létrehozására lettek kiképezve. Két hálózatból áll, mint a létrehozó és a nem megkülönböztető. Mindkét hálózat egyszerre van betanítva. A betanítás során a generátor véletlenszerű zajt használ az olyan új szintetikus adatmennyiségek létrehozásához, amelyek szorosan hasonlítanak a valós adatmennyiségre. A felhasználó a kimenetet bemenetként veszi át, és valós adatokat használ annak megállapítására, hogy a generált tartalom valós vagy szintetikus. Minden hálózat egymással verseng. A generátor megpróbál olyan szintetikus tartalmat generálni, amely nem különböztethető meg a valós tartalomtól, és a megkülönböztető kísérletet tesz a bemenetek helyes osztályozására valós vagy szintetikusként. A rendszer ezután a kimenetet használja mindkét hálózat súlyozásának frissítéséhez, hogy azok jobban tudják elérni a megfelelő célokat.

A generatív kontradiktórius hálózatok segítségével olyan problémákat lehet megoldani, mint a képek fordítása és az életkor előrehaladtával.

### <a name="transformers"></a>Transformers

A transzformátorok olyan modell-architektúra, amely a sorozatokat, például a szöveg-vagy idősorozat-adatokkal kapcsolatos problémák megoldására alkalmas. [Kódoló és dekóder rétegekből](https://en.wikipedia.org/wiki/Transformer_(machine_learning_model)#Encoder)állnak. A kódoló bemenetet használ, és egy numerikus ábrázoláshoz rendeli, amely olyan adatokat tartalmaz, mint például a környezet. A dekóder adatokat használ a kódolóból egy kimenet, például lefordított szöveg létrehozásához. A különböző kódolókat és dekódereket tartalmazó más architektúrák által elvégezhető transzformátorok a figyelmet alrétegek. Fontos, hogy a bemenet adott részeire összpontosítson, a kontextus fontossága alapján a különböző bemenetekhez képest. Például egy újságcikk összefoglalásakor nem minden mondat vonatkozik a fő ötlet leírására. A cikk legfontosabb szavaira összpontosítva az összefoglalás egyetlen mondatban, a címsorban is elvégezhető.

A transzformátorok olyan természetes nyelvi feldolgozási problémák megoldására használták, mint például a fordítás, a szöveg létrehozása, a kérdések megválaszolásának és a szöveg összegzése.

A Transformers néhány jól ismert implementációja:

- Kétirányú kódolók a transzformátorokból (BERT)
- A generatív előre betanított transzformátor 2 (GPT-2)
- A generatív előre betanított transzformátor 3 (GPT-3)

## <a name="next-steps"></a>Következő lépések

A következő cikkek további lehetőségeket mutatnak be a nyílt forráskódú mély tanulási modellek [Azure Machine Learningban](./index.yml?WT.mc_id=docs-article-lazzeri)való használatára:


- [Kézzel írt számjegyek osztályozása TensorFlow-modell használatával](./how-to-train-tensorflow.md?WT.mc_id=docs-article-lazzeri) 

- [Kézzel írt számjegyek osztályozása TensorFlow-kalkulátor és kerasz használatával](./how-to-train-keras.md?WT.mc_id=docs-article-lazzeri)

- [Kézzel írt számjegyek besorolása egy Láncer-modell használatával](./how-to-set-up-training-targets.md?WT.mc_id=docs-article-lazzeri)
