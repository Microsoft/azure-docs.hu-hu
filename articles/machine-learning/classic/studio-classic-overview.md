---
title: Mire használhatom a Machine Learning Studio (klasszikus) – Azure
description: A Machine Learning Studio (klasszikus) egy fogd és vidd eszköz, amellyel gyorsan készíthet modelleket a használatra kész algoritmusokat és modulokat tartalmazó könyvtárból.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: overview
ms.date: 08/19/2020
ms.openlocfilehash: ad0550dc20edfbc10fb8877ad049c27fc2edf086
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517535"
---
# <a name="what-can-i-do-with-machine-learning-studio-classic"></a>Mire használhatom a Machine Learning Studio (klasszikus)?

**a következőkre vonatkozik:** ![ Ez egy pipa, ami azt jelenti, hogy ez a cikk a Machine Learning Studio (klasszikus) elemre vonatkozik. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasszikus) ![ Ez egy X, ami azt jelenti, hogy ez a cikk Azure Machine learningre vonatkozik.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

A Machine Learning Studio (klasszikus) egy fogd és vidd eszköz, amellyel gépi tanulási modellek hozhatók létre, tesztelhető és helyezhetők üzembe. A Studio (klasszikus) webszolgáltatásként teszi közzé a modelleket, amelyeket egyszerűen felhasználhat egyéni alkalmazások vagy BI-eszközök, például az Excel használatával.


## <a name="studio-classic--interactive-workspace"></a>Studio (klasszikus) interaktív munkaterület

A prediktív elemzési modell kialakításához jellemzően egy vagy több forrásból származó adatok felhasználásával, az adatok átalakításával és elemzésével különböző adatkezelési és statisztikai függvények, valamint az eredmények összessége hozható létre. Az ilyen modellek fejlesztése iteratív folyamat. A különböző függvényeket és paramétereket addig módosítjuk, az eredményeket addig közelítjük, amíg úgy nem véljük, hogy jól betanított, hatékony modellel rendelkezünk.

A Machine Learning Studio (klasszikus) interaktív, vizualizációs munkaterületet biztosít a prediktív elemzési modell egyszerű létrehozásához, teszteléséhez és megismétléséhez. Az **adatkészleteket** _ és elemzési _*_modulokat_*_ egy interaktív vászonra húzhatja, és összekapcsolhatja őket egy _*_kísérlet_*_ létrehozásához, amelyet Machine learning Studio (klasszikus) futtat. A modell felépítésének fejlesztéséhez szerkesztheti a kísérletet, szükség esetén mentheti egy másolatát, majd újrafuttathatja. Ha elkészült, átalakíthatja a _*_betanítási kísérletet_*_ egy _*_prediktív kísérletbe_*_, majd közzéteheti azt _ *_webszolgáltatásként_**, hogy a modell mások számára is elérhető legyen.

A prediktív elemzési modell létrehozásához nincs szükség programozásra, valamint az adatkészletek és modulok vizuális összekapcsolására.

![Machine Learning Studio (klasszikus) diagram: kísérletek létrehozása, számos forráshoz tartozó adatolvasás, pontszámmal ellátható adatmennyiség, írási modellek.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-ml-studio-classic-overview-diagram"></a>Az ML Studio (klasszikus) áttekintési diagram letöltése
Töltse le a **Microsoft ml Studio (klasszikus) képességek áttekintése** diagramot, és tekintse át a Machine learning Studio (klasszikus) képességeinek magas szintű nézetét. Hogy mindig kéznél legyen, kinyomtathatja a diagramot tabloid méretben (11 x 17-es méret).

**A diagram letöltése: [Microsoft Machine learning Studio (klasszikus) képességek áttekintése](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
 ![ A Microsoft Machine Learning Studio (klasszikus) képességeinek áttekintése](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>A Studio (klasszikus) kísérlet összetevői
A kísérletek adathalmazokból állnak, amelyek adatokat szolgáltatnak az elemzési modulok számára; az elemzési modulok pedig összekapcsolódva prediktív elemzési modellt alkotnak. Az érvényes kísérletek a következő jellemzőkkel rendelkeznek:

* A kísérlet tartalmaz legalább egy adathalmazt és egy modult
* Az adathalmazok csak a modulokhoz csatlakoztathatók
* A modulok csatlakoztathatók adathalmazokhoz és más modulokhoz is
* A modulok összes bemeneti portjának rendelkeznie kell valamilyen kapcsolattal az adatfolyamhoz
* Minden modul összes szükséges paraméterét meg kell adni

A kísérleteket létrehozhatja nulláról, vagy egy meglévő mintakísérletet sablonként használva További információkért lásd: [Copy example experiments to create new machine learning experiments](sample-experiments.md) (Új Machine Learning-kísérletek létrehozása példakísérletek másolásával).

A kísérlet létrehozásával kapcsolatos példát a következő témakörben talál: [kísérlet létrehozása Machine learning Studio (klasszikus)](create-experiment.md).

A prediktív elemzési megoldások létrehozásának részletes ismertetését lásd: [prediktív megoldás fejlesztése Machine learning Studio (klasszikus)](tutorial-part1-credit-risk.md)használatával.

### <a name="datasets"></a>Adathalmazok
Az adatkészlet olyan adatokat tartalmaz, amelyeket Machine Learning Studioba (klasszikus) töltöttek fel, hogy használhatók legyenek a modellezési folyamatban. A Machine Learning Studio (klasszikus) számos minta adatkészletet tartalmaz, amelyekkel kísérletezni tud, és szükség esetén több adatkészletet is feltölthet. Néhány példa a mintaadathalmazokra:

* **Különböző autók fogyasztási adatai** – Hengerszám, lóerő stb. alapján azonosított autók üzemanyag-fogyasztási adatai.
* **Mellrákkal kapcsolatos adatok** – Mellrák-diagnosztikai adatok.
* **Erdőtüzek adatai** – Az Északkelet-Portugáliában előfordult erdőtüzek kiterjedése.

Kísérlet létrehozásakor a vászon bal oldalán elérhető adatkészletek listájából választhat.

A Machine Learning Studio (klasszikus) részét képező minta adatkészletek listáját a következő témakörben tekintheti meg: [a mintaadatok-készletek használata Machine learning Studio (klasszikus)](use-sample-datasets.md).

### <a name="modules"></a>Modulok
A modulok az adatokon végezhető algoritmusok. A Machine Learning Studio (klasszikus) számos modult tartalmaz, amelyek az adatok beáramlása funkciótól kezdve a képzés, a pontozás és az érvényesítési folyamatokig terjednek. Néhány példa a mellékelt modulokra:

* [Convert to ARFF][convert-to-arff] (Átalakítás ARFF-formátumba) – Szerializált .NET-adathalmazok átalakítása attribútum-relációs fájlformátumra (ARFF).
* [Compute Elementary Statistics][elementary-statistics] (Alapvető statisztikai számítások) – Alapvető statisztikai számítások, például átlag, szórás stb. kiszámítása.
* [Linear Regression][linear-regression] (Lineáris regresszió) – Online, grádiens módszeren alapuló lineáris regressziós modell létrehozása.
* [Score Model][score-model] (Pontszámmodell) – Betanított osztályozási vagy regressziós modell pontozása.

Kísérlet létrehozásakor a vászon bal oldalán elérhető modulok listájából választhat.

A modul rendelkezhet a belső algoritmusok konfigurálásához használható paraméterek készletével. Amikor kiválaszt egy modult a vásznon, a modul paraméterei megjelennek a **Tulajdonságok** panelen a vászontól jobbra. A modell hangolásához módosíthatja a paramétereket a panelen.

Ha segítségre van lehetősége az elérhető gépi tanulási algoritmusok nagy könyvtára között, tekintse meg a következőt: [algoritmusok kiválasztása a Microsoft Machine learning Studio (klasszikus)](../how-to-select-algorithms.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>A prediktív elemzési webszolgáltatások telepítése
Miután a prediktív elemzési modell elkészült, üzembe helyezheti webszolgáltatásként közvetlenül Machine Learning Studio (klasszikus) webhelyről. További információ erről a folyamatról: [Azure Machine learning webszolgáltatások üzembe helyezése](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Következő lépések
A prediktív elemzés és a gépi tanulás alapjaival megismerheti a [lépésenkénti](create-experiment.md) útmutatót, valamint [a minták kialakítását](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: /azure/machine-learning/studio-module-reference/convert-to-arff
[elementary-statistics]: /azure/machine-learning/studio-module-reference/compute-elementary-statistics
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[score-model]: /azure/machine-learning/studio-module-reference/score-model