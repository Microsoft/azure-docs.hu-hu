---
title: Rövid útmutató – telefonszámok kezelése az Azure kommunikációs szolgáltatásokkal
description: Ismerje meg, hogyan kezelheti a telefonszámokat az Azure kommunikációs szolgáltatásokkal
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: e28b8581342824032921ad75e0297b3604fc0bb4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629345"
---
# <a name="quickstart-manage-phone-numbers"></a>Gyors útmutató: telefonszámok kezelése

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Ismerkedjen meg az Azure kommunikációs szolgáltatásokkal a Azure Portal vagy a kommunikációs szolgáltatások telefonszámait tartalmazó ügyféloldali kódtár használatával a telefonszámok kezeléséhez.

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

- Az USA-beli vásárlás csak az Egyesült Államokban támogatott. A telefonszámok megvásárlásához a következőket kell tennie:
  - A társított Azure-előfizetés számlázási címe a Egyesült Államok található. Jelenleg nem helyezhető át egy erőforrás egy másik előfizetésre.
  - A kommunikációs szolgáltatások erőforrása a Egyesült Államok adatterületen van kiépítve. Az erőforrások jelenleg nem helyezhetők át másik adathelyre.

- Ha telefonszámot szabadít fel, a telefonszámot a rendszer nem szabadítja fel, illetve nem tudja újra megvásárolni a számlázási időszak végéig.

- A kommunikációs szolgáltatások erőforrásának törlésekor a rendszer automatikusan felszabadítja az adott erőforráshoz tartozó telefonszámokat.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta a következőket:

> [!div class="checklist"]
> * Telefonszám vásárlása
> * Telefon számának kezelése
> * Telefonszám felszabadítása

> [!div class="nextstepaction"]
> [SMS küldése](../telephony-sms/send.md) 
>  [Ismerkedés a hívással](../voice-video-calling/getting-started-with-calling.md)
