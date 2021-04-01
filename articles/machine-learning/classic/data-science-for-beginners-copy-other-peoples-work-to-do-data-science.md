---
title: Adatelemzési példák másolása – ML Studio (klasszikus) – Azure
description: 'Az adatelemzés kereskedelmi titka: másokkal is elvégezheti a munkáját. A Azure AI Gallery gépi tanulási példákat szerezhet be.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 2410e5fed48b7ab206360debc3bbeb174b3943cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517059"
---
# <a name="copy-other-peoples-work-to-do-data-science"></a>Más emberek munkájának lemásolása az adatelemzéshez
## <a name="video-5-data-science-for-beginners-series"></a>5. videó: az adatelemzés kezdőknek sorozata
Az adatelemzés egyik kereskedelmi titka, hogy más személyeket is feldolgozzon Önnek. A saját gépi tanulási kísérlethez használni kívánt Azure AI Galleryban talál egy fürtözési algoritmust.

> [!IMPORTANT]
> **Cortana Intelligence Gallery** átnevezték **Azure AI Gallery**. Ennek eredményeképpen az ebben az átiratban szereplő szöveg és képek kis mértékben eltérnek a videótól, amely az előző nevet használja.
>

Ha ki szeretné próbálni a sorozatot, tekintse meg őket. [Ugrás a videók listájára](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-copy-other-peoples-work-to-do-data-science/player]
>
>

## <a name="other-videos-in-this-series"></a>További videók ebben a sorozatban
Az *adatelemzés kezdőknek* az adatelemzés öt rövid videóban való bevezetésének első lépései.

* 1. videó: [az 5 kérdés adatelemzési válasz](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 perc 14 mp)*
* 2. videó: [készen áll](data-science-for-beginners-is-your-data-ready-for-data-science.md) az adatelemzésre? *(4 perc 56 mp)*
* 3. videó: [Kérdezzen rá az adatválaszra](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 perc 17 mp)*
* 4. videó: [Válasz Megjósolása egy egyszerű modellel](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 perc 42 mp)*
* 5. videó: más személyek munkájának másolása az adatelemzéshez

## <a name="transcript-copy-other-peoples-work-to-do-data-science"></a>Átirat: az adatelemzés elvégzése más személyeknek
Üdvözöljük az "adatelemzés kezdőknek" című sorozat ötödik videójában.

Ebben az esetben érdemes felderíteni egy olyan példát, amely kiindulási pontként használható a saját munkája során. Ebben a videóban a legtöbbet hozhatja ki, ha először tekinti meg a sorozat korábbi videóit.

Az adatelemzés egyik kereskedelmi titka, hogy más személyeket is feldolgozzon Önnek.

## <a name="find-examples-in-the-azure-ai-gallery"></a>Példák keresése a Azure AI Galleryban

A Microsoft [Azure Machine learning Studio (klasszikus)](https://azure.microsoft.com/services/machine-learning-studio/)nevű felhőalapú szolgáltatással rendelkezik. Egy olyan munkaterületet biztosít, amelyben különböző gépi tanulási algoritmusokkal kísérletezik, és a megoldás kidolgozása után webszolgáltatásként is elindítható.

Ennek a szolgáltatásnak egy része a **[Azure AI Gallery](https://gallery.azure.ai/)**. Olyan erőforrásokat tartalmaz, mint például Azure Machine Learning Studio (klasszikus) kísérletek vagy modellek gyűjteménye, amelyeket a felhasználók felkészítettek és mások számára is felhasználtak. Ezek a kísérletek nagyszerű módot biztosítanak a mások gondolkodási és nehéz munkájának kihasználása érdekében, hogy megismerkedjen a saját megoldásaival. Mindenki szívesen megkeresi őket.

![Azure AI katalógus](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/azure-ai-gallery.png)

Ha a fent látható **kísérletek** elemre kattint, a katalógusban megjelenik a legutóbbi és népszerű kísérletek száma. A többi kísérletet megkeresheti a képernyő tetején található **Tallózás** gombra kattintva, és Itt megadhatja a keresési kifejezéseket, és kiválaszthatja a keresési szűrőket.

## <a name="find-and-use-a-clustering-algorithm-example"></a>Példa fürtözött algoritmus megkeresésére és használatára
Tehát tegyük fel például, hogy látni szeretné, hogyan működik a fürtözés, így a **"fürtözéses söpörés"** kísérletekre keres rá.

![Csoportosítási kísérletek keresése](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/search-for-clustering-experiments.png)

Íme egy érdekes, hogy valaki hozzájárult a gyűjteményhez.

![Fürtözési kísérlet](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/clustering-experiment.png)

Kattintson a kísérletre, és egy olyan weboldalt kap, amely leírja, hogy a közreműködő milyen munkát végzett, és hogy milyen eredményeket tartalmaz.

![A fürtözési kísérlet leírásának lapja](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/clustering-experiment-description-page.png)

Figyelje **meg a Megnyitás a Studióban (klasszikus)** című hivatkozást.

![Megnyitás a Studióban (klasszikus) gomb](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/open-in-studio.png)

Rákattintok, és a **Azure Machine learning Studio (klasszikus)** lehetőségre van szükségem. Létrehoz egy másolatot a kísérletről, és a saját munkaterületen helyezi el. Ez magában foglalja a közreműködő adatkészletét, az összes feldolgozást, az összes felhasznált algoritmust, valamint azt, hogy ezek hogyan mentik el az eredményeket.

![Katalógusbeli kísérlet megnyitása Machine Learning Studio (klasszikus) – fürtözési algoritmus – példa](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/cluster-experiment-open-in-studio.png)

És most már van egy kiindulási pontom. Saját maguk is felcserélhetik saját adataikat, és Megtehetem a modellem saját csípését. Ez egy futó indítást tesz elérhetővé, és lehetővé teszi, hogy felépítem azon személyek munkájára, akik valóban tudják, mit csinálnak.

## <a name="find-experiments-that-demonstrate-machine-learning-techniques"></a>Gépi tanulási technikákat bemutató kísérletek keresése
Más kísérletek is vannak a [Azure AI Galleryban](https://gallery.azure.ai) , amelyek kifejezetten az adatelemzéshez szükséges példákat biztosítanak a felhasználók számára. Például van egy kísérlet a katalógusban, amely bemutatja, hogyan kell kezelni a hiányzó értékeket ([a hiányzó értékek kezelésének módszerei](https://gallery.azure.ai/Experiment/Methods-for-handling-missing-values-1)). A szolgáltatás 15 különböző módon mutatja be az üres értékek behelyettesítését, és az egyes módszerek előnyeit és használatát mutatja be.

![A Gallery-kísérletek megnyitása Machine Learning Studio (klasszikus) – hiányzó értékek metódusai](./media/data-science-for-beginners-copy-other-peoples-work-to-do-data-science/experiment-methods-for-handling-missing-values.png)

[Azure AI Gallery](https://gallery.azure.ai) a saját megoldásai számára kiindulási pontként használható munkakísérletek keresése.

Tekintse meg a többi videót a "kezdő adatelemzés" Microsoft Azure Machine Learning Studio (klasszikus).

## <a name="next-steps"></a>Következő lépések
* [Próbálja ki az első adatelemzési kísérletet Azure Machine Learning Studio (klasszikus)](create-experiment.md)
* [Bevezetés a Machine Learning on Microsoft Azure](../overview-what-is-azure-ml.md)