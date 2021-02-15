---
title: Rögzített hosszúságú szövegfájlok feldolgozása a leképezési adatfolyamatokkal Azure Data Factory
description: Megtudhatja, hogyan dolgozhat fel Azure Data Factory rögzített hosszúságú szövegfájlokat a leképezési adatfolyamatok használatával.
author: balakreshnan
ms.service: data-factory
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 2297364e8f1a531b4512f2497bc4d99963d61179
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386203"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Rögzített hosszúságú szövegfájlok feldolgozása Data Factory leképezési adatfolyamatok használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Microsoft Azure Data Factory adatforgalmának leképezésével a rögzített szélességű szövegfájlokból is át lehet alakítani az adatok átalakítását. A következő feladatban definiálunk egy adatkészletet egy szöveges fájlhoz, elválasztó nélkül, majd az alsztringek felosztását a sorszám alapján.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Új folyamat létrehozásához válassza az **+ új folyamat** elemet.

2. Adjon hozzá egy adatfolyam-tevékenységet, amelyet a rendszer a rögzített szélességű fájlok feldolgozására használ:

    ![Rögzített szélességű folyamat](media/data-flow/fwpipe.png)

3. Az adatfolyam tevékenységben válassza az **új leképezési** adatfolyam lehetőséget.

4. Forrás, származtatott oszlop, Select és mosogató átalakítás hozzáadása:

    ![Rögzített szélességű adatfolyam](media/data-flow/fw2.png)

5. Konfigurálja úgy a forrás-átalakítást, hogy egy új adatkészletet használjon, amely a tagolt szöveg típusától függ.

6. Ne állítson be oszlop elválasztó karaktert vagy fejlécet.

   Most beállíthatjuk a mező kiindulási pontjait és hosszát a fájl tartalmához:

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. A forrás-átalakítás **vetítés** lapján egy *Column_1* nevű karakterlánc-oszlopot kell látnia.

8. A származtatott oszlopban hozzon létre egy új oszlopot.

9. Az oszlopokhoz egyszerű neveket adunk, például *col1*.

10. A Kifejezésszerkesztő mezőbe írja be a következőt:

    ```substring(Column_1,1,4)```

    ![származtatott oszlop](media/data-flow/fwderivedcol1.png)

11. Ismételje meg a 10. lépést az összes elemezni kívánt oszlop esetében.

12. Válassza a **vizsgálat** fület a létrehozandó új oszlopok megtekintéséhez:

    ![vizsgálata](media/data-flow/fwinspect.png)

13. Az átalakítás kijelölése lehetőséggel távolítsa el a nem szükséges oszlopokat a transzformációhoz:

    ![átalakítás kiválasztása](media/data-flow/fwselect.png)

14. A fogadó használatával az adatokat egy mappába exportálhatja:

    ![rögzített szélességű fogadó](media/data-flow/fwsink.png)

    A kimenet a következőképpen fog kinézni:

    ![rögzített szélességű kimenet](media/data-flow/fxdoutput.png)

  A rögzített szélességű adat már fel van osztva, és négy karakterből áll, amelyek mindegyike Col1, Col2, Col3, Col4 és így tovább van rendelve. Az előző példa alapján az adatgyűjtés négy oszlopra oszlik.

## <a name="next-steps"></a>Következő lépések

* Hozza létre a többi adatáramlási logikát az adatforgalom- [átalakítások](concepts-data-flow-overview.md)leképezése használatával.
