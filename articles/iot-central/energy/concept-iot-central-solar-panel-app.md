---
title: Építészeti fogalmak az Azure IoT Centralban – napelemes panel | Microsoft Docs
description: Ez a cikk az Azure IoT Central napelem-figyelő alkalmazás architektúrájának főbb fogalmait ismerteti.
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 1efb693e1ad9b9e9bfdbeafa27e9cc75c645baf4
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834289"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central – napelemes alkalmazások architektúrája

Ez a cikk áttekintést nyújt a napelemes figyelési alkalmazás sablonjának architektúráról. Az alábbi ábra az Azure napelemes alkalmazásának általánosan használt architektúráját mutatja be IoT Central platform használatával.

> [!div class="mx-imgBorder"]
> ![intelligens fogyasztásmérő architektúrája](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Az architektúra az alábbi összetevőkből áll. Nem minden alkalmazáshoz szükséges az itt felsorolt összes összetevő.

## <a name="solar-panels-and-connectivity"></a>Napelemek és kapcsolatok

A napelemes panelek a megújuló energiaforrások egyik jelentős forrása. A napelemek típusától és beállítástól függően az átjárók vagy más köztes eszközök és a szabadalmaztatott rendszerek használatával is csatlakoztatható. Előfordulhat, hogy az eszközök csatlakoztatásához létre kell hoznia IoT Central Device Bridge eszközt, amely nem csatlakoztatható közvetlenül. A IoT Central Device Bridge egy nyílt forráskódú megoldás, és [itt](../core/howto-build-iotc-device-bridge.md)találja a teljes részleteket. 

## <a name="iot-central-platform"></a>IoT Central platform
Az Azure IoT Central egy olyan platform, amely leegyszerűsíti a IoT-megoldás felépítését, és segít csökkenteni a IoT-kezelés, a műveletek és a fejlesztés terheit és költségeit. A IoT Central használatával könnyedén csatlakoztathatók, figyelhetők és kezelhetők a eszközök internetes hálózata (IoT) eszközei a skálán. A napelemek IoT Centralhoz való csatlakoztatása után az alkalmazás-sablon beépített funkciókat használ, mint például az eszközök modelljei, parancsai és irányítópultok. Az alkalmazás sablonja a IoT Central tárolót is használja a meleg elérési utakhoz, például a közel valós idejű mérési adatok figyeléséhez, elemzéséhez, szabályaihoz és vizualizációhoz.


## <a name="extensibility-options-to-build-with-iot-central"></a>Bővíthetőségi lehetőségek a IoT Central létrehozásához
A IoT Central platform két bővíthetőségi lehetőséget kínál: folyamatos adatexportálást (CDE) és API-kat. Az ügyfelek és a partnerek választhatnak ezek között a lehetőségek közül, hogy testre szabják az adott igényeknek megfelelő megoldásokat. Például az egyik partner konfigurálta az Azure Data Lake Storage (ADLS) CDE. A ADLS használják a hosszú távú adatmegőrzésre és más, a lassú elérésű útvonalak tárolási helyzetére, például a kötegelt feldolgozásra, a naplózásra és a jelentéskészítésre. 

## <a name="next-steps"></a>Következő lépések

* Most, hogy megismerte az architektúrát, [hozzon létre ingyen a Solar panel alkalmazást](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* További információ a IoT Centralről: [IoT Central áttekintése](../index.yml)
