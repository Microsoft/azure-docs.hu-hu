---
title: Modell exportálása a Mobile-Custom Vision Serviceba
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan exportálhatja a modellt a mobil alkalmazások létrehozásához vagy a helyileg futtatott, valós idejű besoroláshoz való használatra.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: be28a10f8a96d21d64d08cfacee4543c065168ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85391757"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>A modell exportálása mobileszközökön való használatra

Custom Vision Service lehetővé teszi az osztályozók exportálását offline állapotba. Az exportált besorolást beillesztheti egy alkalmazásba, és helyileg futtathatja egy eszközön a valós idejű besoroláshoz.

## <a name="export-options"></a>Exportálási beállítások

A Custom Vision Service a következő exportálásokat támogatja:

* __Tensorflow__ __Android__ rendszerhez.
* A __IOS11__ __CoreML__ .
* __ONNX__ a __Windows ml__-hez.
* A __[jövőkép AI fejlesztői csomagja](https://azure.github.io/Vision-AI-DevKit-Pages/)__.
* __Docker-tároló__ Windows, Linux vagy ARM architektúrához. A tároló tartalmaz egy Tensorflow modellt és egy szolgáltatási kódot a Custom Vision API használatához.

> [!IMPORTANT]
> Custom Vision Service csak a __kompakt__ tartományokat exportálja. A kompakt tartományok által generált modellek a mobileszközök valós idejű besorolásának korlátaira vannak optimalizálva. A kompakt tartománnyal létrehozott osztályozók valamivel kevésbé pontosak, mint egy standard tartomány, amely azonos mennyiségű betanítási adattal rendelkezik.
>
> Az osztályozók fejlesztésével kapcsolatos információkért tekintse meg az [osztályozó dokumentum továbbfejlesztése](getting-started-improving-your-classifier.md) című témakört.

## <a name="convert-to-a-compact-domain"></a>Átalakítás kompakt tartományba

> [!NOTE]
> Az ebben a szakaszban ismertetett lépések csak akkor érvényesek, ha már van olyan modellje, amely nem kompakt tartományra van beállítva.

Meglévő modell tartományának átalakításához hajtsa végre a következő lépéseket:

1. Az [Egyéni jövőkép webhelyén](https://customvision.ai)válassza a __Kezdőlap__ ikont a projektek listájának megtekintéséhez.

    ![A Kezdőlap ikon és a projektek lista képe](./media/export-your-model/projects-list.png)

1. Válasszon ki egy projektet, majd kattintson a lap jobb felső sarkában található __fogaskerék__ ikonra.

    ![A fogaskerék ikon képe](./media/export-your-model/gear-icon.png)

1. A __tartományok__ szakaszban válassza ki az egyik __kompakt__ tartományt. A módosítások mentéséhez kattintson a __módosítások mentése__ gombra. 

    > [!NOTE]
    > A jövőkép AI fejlesztői csomag esetében a projektet az __általános (Compact)__ tartománnyal kell létrehozni, és az **exportálási képességek** szakaszban meg kell adnia a **jövőkép AI fejlesztői csomag** lehetőséget.

    ![Tartományok kiválasztásának képe](./media/export-your-model/domains.png)

1. Az oldal tetején válassza a __betanítás__ az új tartomány használatával lehetőséget.

## <a name="export-your-model"></a>A modell exportálása

A modell átképzés utáni exportálásához kövesse az alábbi lépéseket:

1. Lépjen a **teljesítmény** lapra, és válassza az __Exportálás__ lehetőséget. 

    ![Az Exportálás ikon képe](./media/export-your-model/export.png)

    > [!TIP]
    > Ha az __exportálási__ bejegyzés nem érhető el, akkor a kiválasztott iteráció nem használ kompakt tartományt. A lap __iterációk__ szakaszával olyan iterációt választhat ki, amely egy kompakt tartományt használ, majd válassza az __Exportálás__ lehetőséget.

1. Válassza ki a kívánt exportálási formátumot, majd válassza az __Exportálás__ lehetőséget a modell letöltéséhez.

## <a name="next-steps"></a>Következő lépések

Integrálja az exportált modellt egy alkalmazásba az alábbi cikkek vagy minták egyikének feltárásával:

* [A Tensorflow-modell használata a Python használatával](export-model-python.md)
* [A ONNX-modell használata a Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Tekintse meg az [iOS-alkalmazásban a CoreML modellhez](https://go.microsoft.com/fwlink/?linkid=857726) készült mintát, amely valós idejű képbesorolást biztosít a Swift-mel.
* Tekintse meg az Android [-alkalmazásokban a Tensorflow modellhez](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) készült mintát az androidos valós idejű képbesoroláshoz.
* Tekintse meg a [CoreML modelt a Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) valós idejű képbesorolást biztosító modelljét egy Xamarin iOS-alkalmazásban.
