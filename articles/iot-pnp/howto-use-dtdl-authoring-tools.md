---
title: DTDL-modellek létrehozása és érvényesítése eszköz használatával | Microsoft Docs
description: Telepítse a DTDL-szerkesztőt a Visual Studio Code vagy a Visual Studio 2019-es verzióra, és használja a IoT Plug and Play modellek létrehozásához.
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 59ea0258d6262f652e9ac563bbda5ec89c4c5819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580424"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>A DTDL authoring Tools telepítése és használata

A [digitális Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) MODELLJEI a JSON-fájlok. A Visual Studio Code-hoz vagy a Visual Studio 2019-bővítményhez a modell fájljainak létrehozásához és érvényesítéséhez használhatja a kiterjesztést.

## <a name="install-and-use-the-vs-code-extension"></a>A VS Code bővítmény telepítése és használata

A VS Code-hoz készült DTDL-bővítmény hozzáadja a következő DTDL-szerzői funkciókat:

- A DTDL v2 szintaxisának ellenőrzése.
- IntelliSense, beleértve az automatikus kiegészítést is, hogy segítsen a nyelvi szintaxisban.
- Felületek létrehozása a parancs palettáján.

A DTDL-bővítmény telepítéséhez nyissa meg a [Visual Studio Code DTDL-szerkesztőjét](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl). A **DTDL** a vs Code kiterjesztések nézetében is megkeresheti.

Ha telepítette a bővítményt, használja a DTDL-modell fájljainak a VS Code-ban való létrehozásához:

- A bővítmény szintaxis-ellenőrzést biztosít a DTDL-modell fájljaiban, és kiemeli a hibákat a következő képernyőképen látható módon:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="Modell érvényesítése a VS Code-ban":::

- Az IntelliSense és az automatikus kiegészítés használata a DTDL-modellek szerkesztésekor:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="Modell érvényesítése a VS Code-ban":::

- Hozzon létre egy új DTDL felületet. A következő parancs egy új felülettel rendelkező JSON-fájlt hoz létre. Az illesztőfelület például telemetria, tulajdonságot és parancssori definíciókat tartalmaz.

## <a name="install-and-use-the-visual-studio-extension"></a>A Visual Studio bővítmény telepítése és használata

A Visual Studio 2019 DTDL-bővítménye a következő DTDL-szerzői funkciókat adja:

- A DTDL v2 szintaxisának ellenőrzése.
- IntelliSense, beleértve az automatikus kiegészítést is, hogy segítsen a nyelvi szintaxisban.

A DTDL-bővítmény telepítéséhez nyissa meg a [DTDL nyelvi támogatását a VS 2019-hez](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport). A **DTDL** a **Bővítmények kezelése** a Visual Studióban is megkeresheti.

Ha telepítette a bővítményt, használja a DTDL-modell fájljainak létrehozásához a Visual Studióban:

- A bővítmény szintaxis-ellenőrzést biztosít a DTDL-modell fájljaiban, és kiemeli a hibákat a következő képernyőképen látható módon:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Modell érvényesítése a VS Code-ban":::

- Az IntelliSense és az automatikus kiegészítés használata a DTDL-modellek szerkesztésekor:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="Modell érvényesítése a VS Code-ban":::

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megtanulta, hogyan használhatja a DTDL-bővítményeket a Visual Studio Code-hoz és a Visual Studio 2019-hoz a DTDL-modell fájljainak létrehozásához és érvényesítéséhez. A következő lépés az [Azure IoT Explorer és a modellek és eszközök](./howto-use-iot-explorer.md)használatának megismerése.
