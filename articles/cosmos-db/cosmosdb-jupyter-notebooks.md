---
title: A beépített Jupyter notebookok támogatásának bemutatása Azure Cosmos DB (előzetes verzió)
description: Megtudhatja, hogyan használhatja a Azure Cosmos DB beépített Jupyter notebook-támogatását a lekérdezések interaktív futtatásához.
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 4f57d2793a28f4bbb201764c67af82f31d0b9333
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100369798"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>A Azure Cosmos DB beépített Jupyter notebookok támogatása (előzetes verzió)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A Jupyter Notebook egy nyílt forráskódú webalkalmazás, amely az élő kódot, egyenleteket, vizualizációkat és leíró szövegeket tartalmazó dokumentumok létrehozását és megosztását teszi lehetővé. 

Azure Cosmos DB beépített Jupyter jegyzetfüzetek közvetlenül integrálva vannak a Azure Portalba és a Azure Cosmos DB-fiókjába, így kényelmes és könnyen használható. A fejlesztők, az adatszakértők, a mérnökök és az elemzők az ismerős Jupyter notebookok használatával végezhetik el az adatfeltárást, az adattisztítást, az adatátalakítást, a numerikus szimulációkat, a statisztikai modellezést, az adatvizualizációt és a gépi tanulást.

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Jupyter notebook-vizualizációk Azure Cosmos DB":::

A Azure Cosmos DB a C# és a Python jegyzetfüzeteket is támogatja minden API-hoz, beleértve a Core (SQL), Cassandra, Gremlin, Table és API-t a MongoDB. A notebookon belül kihasználhatja a beépített parancsok és szolgáltatások előnyeit, amelyek megkönnyítik Azure Cosmos DB erőforrások létrehozását, az adatok feltöltését és a lekérdezési adatok megjelenítését Azure Cosmos DBokban. 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="A Jupyter notebookok támogatása Azure Cosmos DB":::

## <a name="benefits-of-jupyter-notebooks"></a>A Jupyter notebookok előnyei

A Jupyter notebookok eredetileg a Pythonban és az R-ben írt adatelemzési alkalmazásokhoz lettek kifejlesztve. A különböző típusú projektekben azonban különböző módokon használhatók, többek között:

**Adatvizualizáció:** A Jupyter notebookok lehetővé teszik, hogy egy megosztott jegyzetfüzet formájában jelenítse meg az adathalmazt, amely egy adatkészletet grafikus formában jelenít meg. Vizualizációkat hozhat létre, interaktív módosításokat hajthat végre a megosztott kódban és az adatkészletben, és megoszthatja az eredményeket.

**Kód megosztása:** Az olyan szolgáltatások, mint például a GitHub, a kódok megosztására szolgálnak, de nagyrészt nem interaktívak. A Jupyter Notebook megtekintheti a kódot, végrehajthatja és megjelenítheti az eredményeket közvetlenül a Azure Portal.

**Élő interakciók kóddal:** Egy Jupyter Notebook programkódja dinamikus; szerkesztheti, és a frissítéseket Növekményesen futtathatja valós időben. Olyan felhasználói vezérlőket (például csúszkákat vagy szövegbeviteli mezőket) is beágyazhat, amelyek bemeneti forrásként használatosak a kód, bemutatók vagy fogalmak igazolásához (Pócs).

**A kód mintáinak és az adatfeltárás eredményeinek dokumentálása:** Ha egy kódrészlettel rendelkezik, és azt szeretné elmagyarázni, hogyan működik, akkor beágyazhatja Jupyter Notebookba. A dokumentációval egyidejűleg is hozzáadhat interaktivitást.

**Beépített parancsok a Azure Cosmos DBhoz:** Azure Cosmos DB beépített mágikus parancsai megkönnyítik a fiókkal való kommunikációt. Használhatja a (z)%% upload és%% SQL parancsokat is az adatok tárolóba való feltöltéséhez és az [SQL API-szintaxis](sql-query-getting-started.md)használatával történő lekérdezéséhez. Nem kell további egyéni kódokat írnia.

**Minden egy helyen lévő környezetben:** A Jupyter-jegyzetfüzetek kód, Rich Text, images, videos, animáció, matematikai egyenletek, ábrázolások, térképek, interaktív számok, widgetek és grafikus felhasználói felületek egyetlen dokumentumba való egyesítésére használhatók.

## <a name="components-of-a-jupyter-notebook"></a>Egy Jupyter Notebook összetevői

A Jupyter notebookok többféle típusú összetevőt tartalmazhatnak, amelyek mindegyike különálló blokkokba vagy cellákba rendezhető:

**Szöveg és HTML:** A HTML létrehozásához a Markdown szintaxisában szereplő egyszerű szöveg vagy szöveg, amely bármikor beszúrható a dokumentumba. A CSS-stílus a beágyazott vagy a jegyzetfüzet létrehozásához használt sablonba is felvehető.

**Kód és kimenet:** A Jupyter notebookok támogatják a Python és a C# kódokat. A végrehajtott kód eredményei közvetlenül a kód blokkolása után jelennek meg, és a kódrészletek többször is végrehajthatók tetszőleges sorrendben.

**Vizualizációk:** Létrehozhat grafikát és diagramokat a kódban olyan modulok használatával, mint például a Matplotlib, a Plot, a bokeh és mások. A kimenethez hasonlóan ezek a vizualizációk a generált kód mellett jelennek meg. A kimenethez hasonlóan ezek a vizualizációk a generált kód mellett jelennek meg.

**Multimédia:** Mivel a Jupyter-jegyzetfüzetek webes technológiára épülnek, a weblapok által támogatott összes típust megjeleníthetik. A jegyzetfüzetben HTML-elemekként is megadhatja őket, vagy programozott módon létrehozhatja őket a modul használatával `IPython.display` .

**Adatértékek:** Az adatok importálhatók az Azure Cosmos-tárolóból, illetve a lekérdezések eredményei egy Jupyter Notebook programozott módon. Azure Cosmos DBban lévő adatok feltöltéséhez és lekérdezéséhez használjon beépített Magic-parancsokat. 

## <a name="next-steps"></a>Következő lépések

A Azure Cosmos DB beépített Jupyter-jegyzetfüzetek megkezdéséhez tekintse meg a következő cikkeket:

* [Jegyzetfüzetek engedélyezése Azure Cosmos-fiókban](enable-notebooks.md)
* [Ismerkedés a notebook Samples-galériával](https://cosmos.azure.com/gallery.html)
* [A Python notebook funkcióinak és parancsainak használata](use-python-notebook-features-and-commands.md)
* [C# Notebook-funkciók és-parancsok használata](use-csharp-notebook-features-and-commands.md)
* [Jegyzetfüzetek importálása GitHub-tárházból](import-github-notebooks.md)