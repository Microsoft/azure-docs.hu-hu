---
title: Építészeti fogalmak az Azure IoT Centralban – intelligens fogyasztásmérő | Microsoft Docs
description: Ez a cikk az Azure IoT Central Energy app-sablon architektúrájának főbb fogalmait ismerteti
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 48dd95acf725e57d63f85c54c97b641935b049b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832368"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Azure IoT Central – intelligens fogyasztásmérő alkalmazás architektúrája

Ez a cikk áttekintést nyújt az intelligens fogyasztásmérő monitorozási alkalmazás sablonjának architektúráról. Az alábbi ábra egy általánosan használt architektúrát mutat be az Azure-beli Smart Meter-alkalmazáshoz IoT Central platform használatával.

> [!div class="mx-imgBorder"]
> ![intelligens fogyasztásmérő architektúrája](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Az architektúra az alábbi összetevőkből áll. Előfordulhat, hogy egyes megoldások nem igénylik az itt felsorolt összes összetevőt.

## <a name="smart-meters-and-connectivity"></a>Intelligens mérőórák és kapcsolat 

Egy intelligens fogyasztásmérő az összes energiaellátási eszköz egyik legfontosabb eszköze. Az energiafogyasztással kapcsolatos adatokat rögzíti és közli a segédprogramokkal a figyelési és más használati esetekre, például a számlázásra és az igényre adott válaszra vonatkozóan. A mérési típus alapján az átjárók vagy más köztes eszközök vagy rendszerek, például az Edge-eszközök és a Head-end rendszerek használatával kapcsolódhat IoT Centralhoz. Hozzon létre IoT Central Device Bridge-et az eszközök csatlakoztatásához, amelyek nem csatlakoztathatók közvetlenül. A IoT Central Device Bridge egy nyílt forráskódú megoldás, és [itt](../core/howto-build-iotc-device-bridge.md)találja a teljes részleteket. 

## <a name="iot-central-platform"></a>IoT Central platform

Az Azure IoT Central egy olyan platform, amely leegyszerűsíti a IoT-megoldás felépítését, és segít csökkenteni a IoT-kezelés, a műveletek és a fejlesztés terheit és költségeit. A IoT Central használatával könnyedén csatlakoztathatók, figyelhetők és kezelhetők a eszközök internetes hálózata (IoT) eszközei a skálán. Az intelligens mérőszámok IoT Centralhoz csatlakoztatása után az alkalmazás-sablon beépített funkciókat használ, mint például az eszközök modelljei, parancsai és irányítópultok. Az alkalmazás sablonja a IoT Central tárolót is használja a meleg elérési utakhoz, például a közel valós idejű mérési adatok figyeléséhez, elemzéséhez, szabályaihoz és vizualizációhoz. 

## <a name="extensibility-options-to-build-with-iot-central"></a>Bővíthetőségi lehetőségek a IoT Central létrehozásához
A IoT Central platform két bővíthetőségi lehetőséget kínál: folyamatos adatexportálást (CDE) és API-kat. Az ügyfelek és a partnerek választhatnak ezek között a lehetőségek közül, hogy testre szabják az adott igényeknek megfelelő megoldásokat. Például az egyik partner konfigurálta az Azure Data Lake Storage (ADLS) CDE. A ADLS használják a hosszú távú adatmegőrzésre és más, a lassú elérésű útvonalak tárolási helyzetére, az ilyen kötegelt feldolgozásra, a naplózásra és a jelentéskészítésre. 

## <a name="next-steps"></a>Következő lépések

* Most, hogy megismerte az architektúrát, [ingyenes Smart Meter-alkalmazást készíthet](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* További információ a IoT Centralről: [IoT Central áttekintése](../index.yml)
