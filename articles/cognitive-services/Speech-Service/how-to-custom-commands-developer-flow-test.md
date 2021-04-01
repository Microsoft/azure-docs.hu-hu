---
title: Custom Commands-alkalmazás tesztelése
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megismerheti az egyéni parancsok alkalmazásának tesztelésének különböző módszereit.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c0bd21f55fee4d8487826deae23093ede293c8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95021813"
---
# <a name="test-your-custom-commands-application"></a>Egyéni parancsok alkalmazás tesztelése

Ebből a cikkből megismerheti az egyéni parancsok alkalmazásának tesztelésének különböző módszereit.

## <a name="test-in-the-portal"></a>Tesztelés a portálon

A tesztelés a portálon a legegyszerűbb és leggyorsabb módszer annak ellenőrzéséhez, hogy az egyéni parancssori alkalmazás a várt módon működik-e. Az alkalmazás sikeres betanítása után kattintson a `Test` gombra a tesztelés megkezdéséhez.

> [!div class="mx-imgBorder"]
> ![Tesztelés a portálon](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>Tesztelés a Windows Voice Assistant-ügyféllel

A Windows Voice Assistant-ügyfél egy Windows megjelenítési alaprendszer (WPF) alkalmazás a C#-ban, amely megkönnyíti az interakciók tesztelését a robottal egy egyéni ügyfélalkalmazás létrehozása előtt.

Az eszköz el tudja fogadni az egyéni parancssori alkalmazás AZONOSÍTÓját. Lehetővé teszi, hogy tesztelje a feladat befejezését, vagy az egyéni parancsok szolgáltatás által futtatott parancs-és vezérlési forgatókönyvet.

Az ügyfél beállításához a pénztári [Windows Voice Assistant-ügyfelet](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf).

> [!div class="mx-imgBorder"]
> ![WVAC-profil létrehozása](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Tesztelés a Speech SDK-kompatibilis ügyfélalkalmazások használatával 
A Speech szoftverfejlesztői készlet (SDK) számos Speech Service-képességet tesz elérhetővé, amely lehetővé teszi a beszédfelismerést támogató alkalmazások fejlesztését. Számos programozási nyelven és platformon is elérhető.

Univerzális Windows-platform-(UWP-) ügyfélalkalmazás beállítása a Speech SDK-val, és az egyéni parancssori alkalmazással való integrálása:  
- [Útmutató: integrálás egy ügyfélalkalmazás használatával a Speech SDK-val](./how-to-custom-commands-setup-speech-sdk.md)
- [Útmutató: tevékenység küldése ügyfélalkalmazás számára](./how-to-custom-commands-send-activity-to-client.md)
- [Útmutató: webes végpontok beállítása](./how-to-custom-commands-setup-web-endpoints.md)

Más programozási nyelvekhez és platformokhoz:
- [Beszédfelismerési SDK programozási nyelvek, platformok, forgatókönyvek kapacitása](./speech-sdk.md)
- [A hangsegéd mintáinak kódjai](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Minták megtekintése a GitHubon](https://aka.ms/speech/cc-samples)