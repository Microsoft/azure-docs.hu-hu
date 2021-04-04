---
title: Azure IoT Central video Analytics-objektum és-mozgásészlelés | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre IoT Central alkalmazást a video Analytics-Object és a Motion Detection alkalmazás sablon használatával a IoT Centralban. Ez a sablon élő videó-elemzéseket és csatlakoztatott kamerákat használ.
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 48808f762536390287bae40e8af3849da20b81c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91874288"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>Video Analytics – objektum-és mozgásérzékelő-észlelési alkalmazás architektúrája

A **video Analytics – Object és Motion Detection** alkalmazás sablonja lehetővé teszi, hogy a IoT-megoldások az élő videó elemzési funkcióit is felkészítsék.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="A video Analytics-objektum és a mozgásérzékelő-összetevők áttekintésének ábrája.":::

A video Analytics megoldás fő összetevői a következők:

## <a name="live-video-analytics-lva"></a>Élő videó-elemzés (LVA)

A LVA platformot biztosít az Edge és a felhőre kiterjedő intelligens video-alkalmazások létrehozásához. A platform lehetővé teszi, hogy intelligens video-alkalmazásokat építsen ki, amelyek a peremhálózat és a felhőre is kiterjednek. A platform lehetővé teszi az élő videók rögzítését, rögzítését, elemzését, valamint az eredmények közzétételét az Azure-szolgáltatások számára videó-vagy videó-elemzéssel. Az Azure-szolgáltatások a felhőben vagy az Edge-ben is futhatnak. A platform segítségével javíthatja a IoT-megoldásokat a video Analytics használatával.

További információ: [Live Video Analytics](https://github.com/Azure/live-video-analytics) a githubon.

## <a name="iot-edge-lva-gateway-module"></a>IoT Edge LVA-átjáró modul

A IoT Edge LVA-átjáró modul a kamerákat új eszközként hozza létre, és közvetlenül a IoT-eszköz ügyfél-SDK-val csatlakoztatja IoT Centralhoz.

Ebben a hivatkozásban az eszközök a szélétől származó szimmetrikus kulcsok használatával csatlakoznak a megoldáshoz. További információ az eszközök kapcsolatáról: [Csatlakozás az Azure-hoz IoT Central](../core/concepts-get-connected.md)

## <a name="media-graph"></a>Médiagrafikon

A Media Graph lehetővé teszi, hogy meghatározza, hol kell rögzíteni az adathordozót, hogyan kell feldolgozni, és hová kell adni az eredményeket. A Media Graph konfigurálásához a kívánt módon csatlakoztathatja az összetevőket vagy a csomópontokat. További információ: [Media Graph](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) a githubon.

## <a name="next-steps"></a>Következő lépések

A következő lépés azt ismerteti, hogyan [helyezhet üzembe egy IoT Central alkalmazást a video Analytics-Object és a Motion Detection alkalmazás sablon használatával](tutorial-video-analytics-deploy.md).
