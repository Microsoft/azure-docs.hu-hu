---
title: 'Webszolgáltatás bemenete/kimenete: modul leírása'
description: Megtudhatja, hogyan használhatja a Azure Machine Learning Designer webszolgáltatási moduljait a bemenetek és kimenetek kezeléséhez.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: 475ad3de8e0a7636a14949d4fcd8a5ec2812ad5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421124"
---
# <a name="web-service-input-and-web-service-output-modules"></a>Webszolgáltatás bemeneti és webszolgáltatási kimeneti moduljai

Ez a cikk a Azure Machine Learning Designer webszolgáltatás bemeneti és webszolgáltatási kimeneti moduljait ismerteti.

A webszolgáltatás bemeneti modulja csak a **DataFrameDirectory** típusú bemeneti porthoz tud csatlakozni. A webszolgáltatás kimeneti modulja csak **DataFrameDirectory** típusú kimeneti portról csatlakoztatható. A modul fájában található két modult a **webszolgáltatás** kategóriájában találja. 

A webszolgáltatás bemeneti modulja azt jelzi, hogy a felhasználói adatok hol jutnak be a folyamatba. A webszolgáltatás kimeneti modulja azt jelzi, hogy a rendszer hol adja vissza a felhasználói adatokat egy valós idejű következtetési folyamat során.

## <a name="how-to-use-web-service-input-and-output"></a>A webszolgáltatás bemenetének és kimenetének használata

Amikor [valós idejű következtetési folyamatot hoz létre](../tutorial-designer-automobile-price-deploy.md#create-a-real-time-inference-pipeline) a betanítási folyamatból, a webszolgáltatás bemeneti és webszolgáltatás-kimeneti moduljai automatikusan hozzáadódnak a folyamathoz, ahol a felhasználói adatok belépnek a folyamatba, és az adatok visszaadása történik. 

> [!NOTE]
> A valós idejű következtetések automatikus generálása egy szabály-alapú, legalkalmasabb folyamat. Nincs garancia a pontosságra. 

Manuálisan is hozzáadhatja vagy eltávolíthatja a webszolgáltatás bemeneti és webszolgáltatás-kimeneti modulját, hogy megfeleljen az igényeinek. Győződjön meg arról, hogy a valós idejű következtetési folyamatnak legalább egy webszolgáltatás-bemeneti modulja és egy webszolgáltatás-kimeneti modulja van. Ha több webszolgáltatás-bemeneti vagy webszolgáltatás-kimeneti modulja van, ügyeljen arra, hogy egyedi névvel rendelkezzenek. A nevet megadhatja a modul jobb oldali paneljén.

A valós idejű következtetéseket manuálisan is létrehozhatja a webszolgáltatás bemeneti és webszolgáltatás-kimeneti moduljainak a nem elküldött folyamathoz való hozzáadásával.

> [!NOTE]
> A folyamat típusát a rendszer az első elküldésekor határozza meg. A beküldés előtt ügyeljen arra, hogy a webszolgáltatás bemeneti és webszolgáltatási kimeneti moduljait is hozzáadja.

Az alábbi példa bemutatja, hogyan hozhat létre manuálisan valós idejű következtetési folyamatot a Python parancsfájl végrehajtása modulból. 

![Példa](media/module/web-service-input-output-example.png)
   
A folyamat elküldése és a Futtatás sikeres befejezése után üzembe helyezheti a valós idejű végpontot.
   
> [!NOTE]
>  Az előző példában az **adatok manuális megadása** a webszolgáltatás bemeneti adatsémáját adja meg, és a valós idejű végpont üzembe helyezéséhez szükséges. Általában mindig csatlakoztatnia kell egy modult vagy adatkészletet ahhoz a porthoz, ahol a **webszolgáltatás bemenete** csatlakozik az Adatséma biztosításához.
   
## <a name="next-steps"></a>Következő lépések
További információ [a valós idejű végpont üzembe helyezéséről](../tutorial-designer-automobile-price-deploy.md#deploy-the-real-time-endpoint).

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .