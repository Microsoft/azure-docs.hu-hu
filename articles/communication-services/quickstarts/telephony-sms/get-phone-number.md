---
title: Rövid útmutató – Telefonszámok kezelése a Azure Communication Services
description: Megtudhatja, hogyan kezelheti a telefonszámokat a Azure Communication Services
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: 19bb79f9a4deaebfacc75918c46a5516d2d398be
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498193"
---
# <a name="quickstart-manage-phone-numbers"></a>Rövid útmutató: Telefonszámok kezelése

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

[!INCLUDE [Bulk Acquisition Instructions](../../includes/phone-number-special-order.md)]

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/phone-numbers-portal.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Azure portal](./includes/phone-numbers-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/phone-numbers-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/phone-numbers-python.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/phone-numbers-js.md)]
::: zone-end

## <a name="troubleshooting"></a>Hibaelhárítás

Gyakori kérdések és problémák:

- A telefonos vásárlás csak az Egyesült Államokon támogatott. Telefonszámok vásárlása esetén győződjön meg a következőről:
  - A társított Azure-előfizetés számlázási címe a következő helyen Egyesült Államok. Jelenleg nem lehet áthelyezni egy erőforrást egy másik előfizetésbe.
  - A Communication Services erőforrás az adatadatok Egyesült Államok van kiépítve. Jelenleg nem lehet áthelyezni az erőforrásokat egy másik adathelyre.

- A telefonszámok felszabadítása után a telefonszám a számlázási ciklus végéig nem lesz felszabadítható vagy újravásárlásra képes.

- Egy Communication Services erőforrás törlésekor az erőforráshoz társított telefonszámok egyszerre automatikusan ki lesznek adva.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőt tanulta meg:

> [!div class="checklist"]
> * Telefonszám vásárlása
> * Telefonszám kezelése
> * Telefonszám kiadása

> [!div class="nextstepaction"]
> [SMS küldése](../telephony-sms/send.md) 
>  [A hívásának első lépések](../voice-video-calling/getting-started-with-calling.md)
