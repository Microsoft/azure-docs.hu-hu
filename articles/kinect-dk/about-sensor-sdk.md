---
title: Tudnivalók az Azure Kinect Sensor SDK-ról
description: Az Azure Kinect Sensor szoftverfejlesztői készletének (SDK), funkcióinak és eszközeinek áttekintése.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Azure, Kinect, RGB, IR, rögzítés, érzékelő, SDK, hozzáférés, mélység, videó, kamera, IMU, mozgás, érzékelő, hang, mikrofon, Matroska, Sensor SDK, Letöltés
ms.openlocfilehash: 17c1b33120eacb5d0c6d3c02e692d1488ef474e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277319"
---
# <a name="about-azure-kinect-sensor-sdk"></a>Tudnivalók az Azure Kinect Sensor SDK-ról

Ez a cikk az Azure Kinect Sensor szoftverfejlesztői készletének (SDK), funkcióinak és eszközeinek áttekintését tartalmazza.

## <a name="features"></a>Funkciók

Az Azure Kinect Sensor SDK többplatformos, alacsony szintű hozzáférést biztosít az Azure Kinect-eszközök konfigurálásához és a hardveres érzékelők streamekhez, beleértve a következőket:

- Részletes kamera-hozzáférés és üzemmód-vezérlés (passzív IR mód, valamint széles és keskeny mező-nézet mélységi mód) 
- RGB kamera-hozzáférés és-vezérlés (például a kitettség és a fehéregyensúly) 
- Mozgásérzékelő (giroszkóp és gyorsulásmérő) hozzáférése 
- Szinkronizált mélység – RGB kamera streaming a kamerák közötti konfigurálható késleltetéssel 
- Külső eszközök szinkronizálásának vezérlése az eszközök közötti konfigurálható késleltetési eltolással 
- A kamera keretének meta-adathozzáférése képfelbontáshoz, időbélyeg-hez stb. 
- Eszköz-kalibrálási adathozzáférés 

## <a name="tools"></a>Eszközök

- Egy [Azure-beli Kinect Viewer](azure-kinect-viewer.md) , amely az eszközök adatfolyamait figyeli és különböző módokat konfigurál.
- Az [Azure Kinect Recorder](azure-kinect-recorder.md) és a lejátszási olvasó API, amely a [Matroska Container formátumot](record-file-format.md)használja.
- Egy Azure Kinect DK [belső vezérlőprogram-frissítési eszköz](azure-kinect-firmware-tool.md).

## <a name="sensor-sdk"></a>Sensor SDK

- [Töltse le az érzékelő SDK](sensor-sdk-download.md)-t.
- Az érzékelő SDK a [githubon nyílt forráskódban](https://github.com/microsoft/Azure-Kinect-Sensor-SDK)érhető el.
- További információ a használatról: [Sensor SDK API-dokumentáció](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/index.html).

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az Azure Kinect Sensor SDK-t, a következőket is elvégezheti:
>[!div class="nextstepaction"]
>[Sensor SDK-kód letöltése](sensor-sdk-download.md)

>[!div class="nextstepaction"]
>[Eszköz megkeresése és megnyitása](find-then-open-device.md)
