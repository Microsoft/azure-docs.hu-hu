---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d7ac457041474f4e774414b1d5e6f9ed09dc856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179216"
---
<!-- this tells how to get the connection string for your hub -->
<!-- This assumes the user is looking at his hub in the portal. -->

Miután létrehozta a hubot, kérje le a hub-hoz tartozó kapcsolatok karakterláncát. Ez az eszközök és alkalmazások hubhoz való csatlakoztatására szolgál. 

1. Kattintson a központra, és tekintse meg a beállításokat tartalmazó IoT Hub ablaktáblát, és így tovább. Kattintson a **megosztott elérési házirendek** elemre.
   
2. A **Megosztott elérési szabályzatok** alatt válassza ki az **iothubowner** elemet. 

3. A **megosztott elérési kulcsok** területen másolja a használni kívánt **kapcsolati sztringet – az elsődleges kulcsot** később.

    ![A kapcsolati karakterlánc lekérésének megjelenítése](./media/iot-hub-include-find-connection-string/iot-hub-get-connection-string.png)

    További információkért tekintse meg az IoT Hub fejlesztői útmutató [hozzáférés-vezérléssel](../articles/iot-hub/iot-hub-devguide-security.md) foglalkozó részét.
