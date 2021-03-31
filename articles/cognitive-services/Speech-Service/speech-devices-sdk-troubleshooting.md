---
title: A Speech Devices SDK – Speech szolgáltatás hibáinak megoldása
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Speech Devices SDK használatakor felmerülő problémák megoldásához nyújt segítséget.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "74815562"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>A Beszédeszközök SDK hibaelhárítása

Ez a cikk a Speech Devices SDK használatakor felmerülő problémák megoldásához nyújt segítséget.

## <a name="certificate-failures"></a>Tanúsítvány meghibásodása

Ha a beszédfelismerési szolgáltatás használatakor hibaüzenetet kap, győződjön meg arról, hogy az eszköz rendelkezik a megfelelő dátummal és időponttal:

1. Válassza a **Beállítások lehetőséget**. A **rendszerben** válassza a **dátum & idő** lehetőséget.

    ![A beállítások területen válassza a dátum & idő lehetőséget.](media/speech-devices-sdk/qsg-12.png)

1. Tartsa meg az **automatikus dátum &** a kiválasztott idő beállítást. Az **időzóna kiválasztása** területen válassza ki az aktuális időzónát.

    ![Dátum-és időzóna beállításainak kiválasztása](media/speech-devices-sdk/qsg-13.png)

    Amikor azt látja, hogy a fejlesztői készlet ideje megfelel a számítógépen beállított időpontnak, a fejlesztői csomag csatlakozik az internethez.

## <a name="next-steps"></a>Következő lépések

* [A kibocsátási megjegyzések áttekintése](devices-sdk-release-notes.md)
