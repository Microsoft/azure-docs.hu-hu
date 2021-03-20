---
title: ONNX-modell használata Windows ML-Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Megismerheti, hogyan hozhat létre az Azure Cognitive Servicesből exportált ONNX modellt használó Windows UWP-alkalmazást.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: 58ced0c45d66223ac3e40112126e92a4539db32d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94616076"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Custom Vision ONNX modell használata Windows ML-vel (előzetes verzió)

Megismerheti, hogyan használhatja a Custom Vision Service-ből exportált ONNX modellt Windows ML-gel (előzetes verzió).

Ebből az útmutatóból megtudhatja, hogyan használható a Custom Vision Serviceból exportált ONNX-fájl a Windows ML-vel. A példaként szolgáló UWP-alkalmazást saját betanított rendszerkép-osztályozó használatával fogja használni.

## <a name="prerequisites"></a>Előfeltételek

* Windows 10 1809-es vagy újabb verzió
* Windows SDK Build 17763 vagy újabb verzióra
* Visual Studio 2017 15.7-es vagy újabb verzió engedélyezett __Univerzális Windows-platform fejlesztési__ tevékenységprofillal.
* A fejlesztői mód engedélyezve van a számítógépen. További információ: [az eszköz fejlesztésének engedélyezése](/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>A példa alkalmazásról

A befoglalt alkalmazás egy általános Windows UWP-alkalmazás. Lehetővé teszi, hogy kiválasszon egy rendszerképet a számítógépről, amelyet egy helyileg tárolt besorolási modell dolgoz fel. A modell által visszaadott címkék és pontszámok a kép mellett jelennek meg.

## <a name="get-the-example-code"></a>A példa kódjának letöltése

A példában szereplő alkalmazás a GitHubon elérhető [Cognitive Services ONNX Custom Vision minta](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) tárházban érhető el. Klónozás a helyi gépre, és a Visual Studióban nyissa meg a *SampleOnnxEvaluationApp. SLN* .

## <a name="test-the-application"></a>Az alkalmazás tesztelése

1. A `F5` kulcs használatával indítsa el az alkalmazást a Visual Studióból. Lehet, hogy a rendszer kéri a fejlesztői mód engedélyezését.
1. Az alkalmazás indításakor a gombbal válasszon ki egy képet a pontozáshoz. Az alapértelmezett ONNX modell a különböző típusú planktonok osztályozására van kiképezve.

## <a name="use-your-own-model"></a>Saját modell használata

Saját rendszerkép-osztályozó modell használatához kövesse az alábbi lépéseket:

1. Besoroló létrehozása és betanítása a Custom Vision Service használatával. Ennek módjával kapcsolatos útmutatásért tekintse meg az [osztályozó létrehozása és betanítása](./getting-started-build-a-classifier.md)című témakört. Használja az egyik **kompakt** tartományt **(például általános (Compact))**. 
   * Ha van egy másik tartományt használó meglévő osztályozó, akkor a projekt beállításai között konvertálhatja a **tömörítést** . Ezután a folytatás előtt végezze el a projekt újbóli betanítását.
1. Exportálja a modellt. Váltson a teljesítmény lapra, és válasszon egy **kompakt** tartománnyal betanított iterációt. Kattintson a megjelenő **Exportálás** gombra. Ezután válassza a **ONNX**, majd az **Exportálás** lehetőséget. Amikor a fájl elkészült, kattintson a **Letöltés** gombra. Az exportálási lehetőségekkel kapcsolatos további információkért lásd: [a modell exportálása](./export-your-model.md).
1. Nyissa meg a letöltött *. zip* fájlt, és bontsa ki a *Model. Onnx* fájlt. Ez a fájl tartalmazza az osztályozó modellt.
1. A Visual Studióban lévő Megoldáskezelő kattintson a jobb gombbal az **eszközök** mappára, és válassza a __meglévő elem hozzáadása elemet__. Válassza ki a ONNX-fájlt.
1. A Megoldáskezelő kattintson a jobb gombbal a ONNX fájlra, és válassza a **Tulajdonságok** lehetőséget. Módosítsa a fájl következő tulajdonságait:
   * __Felépítési művelet__  ->  __Tartalom__
   * __Másolás a kimeneti könyvtárba__  ->  __Másolás, ha újabb__
1. Ezután nyissa meg a _Főoldal. XAML. cs_ fájlt, és módosítsa az értékét `_ourOnnxFileName` a ONNX-fájl nevére.
1. A projekt létrehozásához és futtatásához használja a következőt: `F5` .
1. Kattintson a gombra az értékelni kívánt kép kiválasztásához.

## <a name="next-steps"></a>Következő lépések

Egyéb exportálási lehetőségekről és a Custom Vision modell használatáról bővebb információt a következő dokumentumokban talál:

* [A modell exportálása](./export-your-model.md)
* [Exportált Tensorflow-modell használata Android-alkalmazásban](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Exportált CoreML modell használata Swift iOS-alkalmazásban](https://go.microsoft.com/fwlink/?linkid=857726)
* [Exportált CoreML modell használata iOS alkalmazásban Xamarinnal](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

A ONNX-modellek Windows ML-vel történő használatáról további információt a [modell integrálása az alkalmazásba Windows ml](/windows/ai/windows-ml/integrate-model)-ben című témakörben talál.