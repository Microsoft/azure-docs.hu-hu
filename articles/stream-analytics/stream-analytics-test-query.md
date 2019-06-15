---
title: Azure Stream Analytics-feladat, mintaadatokkal tesztelése
description: Ez a cikk ismerteti, hogyan használhatja az Azure Portalon egy Azure Stream Analytics-feladat, a minta bemenet tesztelése, és töltse fel a mintaadatokat.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: fca76b632e9bcc27ed762886eaea696a5696ad3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761910"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>A Stream Analytics lekérdezési mintaadatokkal tesztelése

Az Azure Stream Analytics használatával indítása és feladat leállítása nélkül tölthet fel adatokat, valamint mintalekérdezések az Azure Portalon.

## <a name="upload-sample-data-and-test-the-query"></a>Mintaadatok feltöltése és a lekérdezés tesztelése

1. Jelentkezzen be az Azure portálra. 

2. Keresse meg a meglévő Stream Analytics-feladatot, és válassza ki azt.

3. A Stream Analytics a feladat oldal, a a **Feladattopológia** szakaszban kattintson **lekérdezés** a lekérdezés szerkesztő ablak megnyitásához. 

4. Bemeneti mintaadatok a lekérdezés teszteléséhez kattintson a jobb gombbal bármelyik a bemeneteket.  Válassza ki **fájlból mintaadatok feltöltése**. Az adatok JSON-, CSV-vagy AVRO szerializálni kell. Minta beviteli UTF-8 kódolású legyen, és nem tömöríti. Csak a vessző (,) elválasztó a portálon a fürt megosztott kötetei szolgáltatás bemenet tesztelése esetén támogatott.

    ![Stream analytics query editor testovat dotaz](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Válassza a feltöltés befejeződése után **tesztelése** a lekérdezést a mintaadatokkal tesztelheti.

    ![Stream analytics-lekérdezés szerkesztő teszt mintaadatok](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. A teszt eredménye későbbi használatra van szükség, ha a lekérdezés kimenete a böngészőt a letöltés eredményeit mutató hivatkozást tartalmazó jelenik meg. 

7. Iteratív módosítsa a lekérdezést, és a tesztelés közben ismételt használatával ellenőrizheti, hogyan változik a kimenet.

   ![Stream Analytics query editor sample output](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Amikor lekérdezést használ több kimenetek, az eredményeket külön lapokra jelennek meg, és könnyedén válthat közöttük.

8. Miután meggyőződött a böngészőben megjelenített eredményekre **mentése** a lekérdezést. Ezután **Start** a feladatot, és biztosítani, hogy a bejövő események feldolgozására.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
