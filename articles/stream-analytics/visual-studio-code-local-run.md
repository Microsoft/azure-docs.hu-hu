---
title: Azure Stream Analytics-feladatok helyi tesztelése a Visual Studio Code használatával a mintaadatok segítségével
description: Ez a cikk bemutatja, hogyan tesztelheti a lekérdezéseket a mintaadatok helyi használatával a Visual Studio Code-hoz készült Azure Stream Analytics eszközökkel.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: how-to
ms.openlocfilehash: bbd83fb3ef3225fc19c48bb4c5962d6559cf32f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97708449"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>A Visual Studio Code segítségével helyileg tesztelheti Stream Analytics lekérdezéseket a mintaadatok használatával

A Visual Studio Code-hoz készült Azure Stream Analytics Tools segítségével helyileg tesztelheti a Stream Analytics-feladatokat a mintaadatok használatával. A lekérdezés eredményei JSON-fájlokban találhatók a projekt **LocalRunOutputs** mappájában.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [.net Core SDK](https://dotnet.microsoft.com/download) -t, és indítsa újra a Visual Studio Code-ot.

* Ebből a rövid útmutatóból megtudhatja [, hogyan](quick-create-visual-studio-code.md) hozhat létre egy stream Analytics feladatot a Visual Studio Code használatával.

## <a name="prepare-sample-data"></a>Mintaadatok előkészítése

Először elő kell készítenie a minta bemeneti adatfájljait. Ha már rendelkezik néhány mintaadatok-fájllal a gépen, akkor kihagyhatja ezt a lépést, és a következőre léphet.

1. A felső sorban kattintson az **adatok előnézete** elemre a bemeneti konfigurációs fájlban. Egyes bemeneti adatok beolvasása a IoT Hubból történik, és az előnézet ablakban jelenik meg. Vegye figyelembe, hogy ez hosszabb időt is igénybe vehet.

2. Az adatmegjelenítést követően kattintson a **Mentés másként** lehetőségre, hogy a helyi fájlba mentse az adatfájlokat.

 ![Élő bemenet megtekintése](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Helyi bemenet megadása

1. Kattintson a **input.js** elemre a stream Analytics projekt bemenetek mappájában. Ezután válassza a **helyi bemenet hozzáadása** lehetőséget a felső sorban.

    ![Helyi bemenet hozzáadása a projektből](./media/quick-create-visual-studio-code/add-input-from-project.png)

    A **CTRL + SHIFT + P** billentyűkombinációval is megnyithatja a parancs-palettát, és megadhatja az **ASA: bemenet hozzáadása** lehetőséget.

   ![Stream Analytics bemenet hozzáadása a VS Code-ban](./media/quick-create-visual-studio-code/add-input.png)

2. Válassza a **helyi bevitel** lehetőséget.

    ![ASA helyi bemenet hozzáadása a Visual Studio Code-ban](./media/vscode-local-run/add-local-input.png)

3. Válassza az **+ új helyi bemenet** lehetőséget.

    ![Új ASA helyi bevitel hozzáadása a Visual Studio Code-ban](./media/vscode-local-run/add-new-local-input.png)

4. Adja meg a lekérdezésben használt beviteli aliast.

    ![Új ASA helyi bemeneti alias hozzáadása](./media/vscode-local-run/new-local-input-alias.png)

5. Az újonnan létrehozott **LocalInput_Input.js** fájljában adja meg a fájl elérési útját, ahol a helyi adatfájl található.

    ![Adja meg a helyi fájl elérési útját a Visual Studióban](./media/vscode-local-run/local-file-path.png)

6. A bemeneti adatok előnézetének megtekintéséhez kattintson az **adatok előnézete** elemre. A rendszer automatikusan észleli az adataihoz tartozó szerializálási típust, ha a JSON-vagy CSV-fájlja. A választóval megtekintheti az adatait **táblázatos** vagy **nyers** formátumban. A következő táblázat a **táblázatos formátumú** adatmennyiségeket szemlélteti:

     ![Az előnézeti helyi adatmegjelenítés táblázatos formátumban](./media/vscode-local-run/local-file-preview-table.png)

    A következő táblázat a **nyers formátumú** adatmennyiségeket szemlélteti:

    ![Helyi adatbázis előzetes előnézete nyers formátumban](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Lekérdezések helyi futtatása

Térjen vissza a lekérdezés-szerkesztőhöz, és válassza a **helyi Futtatás** lehetőséget. Ezután válassza a **helyi bemenet használata** lehetőséget a legördülő listából.

![A lekérdezési szerkesztőben válassza a helyi Futtatás lehetőséget.](./media/vscode-local-run/run-locally.png)

![Helyi bemenet használata](./media/vscode-local-run/run-locally-use-local-input.png)

Az eredmény a jobb oldali ablakban jelenik meg. Kattintson a **Futtatás** gombra a teszteléshez. A **Megnyitás a mappában** lehetőség kiválasztásával megtekintheti az eredmények fájljait a Fájlkezelőben, és további megnyithatja azokat más eszközökkel. Vegye figyelembe, hogy az eredmény fájljai csak JSON formátumban érhetők el.

![Helyi Futtatás eredményének megtekintése](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Következő lépések

* [Azure Stream Analytics feladatok helyi tesztelése élő bevitelsel a Visual Studio Code használatával](visual-studio-code-local-run-live-input.md)

* [Azure Stream Analytics feladatok megismerése a Visual Studio Code-ban (előzetes verzió)](visual-studio-code-explore-jobs.md)
