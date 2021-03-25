---
title: Rövid útmutató – SMS-üzenet küldése
titleSuffix: An Azure Communication Services quickstart
description: Útmutató SMS-üzenet küldéséhez az Azure kommunikációs szolgáltatásokkal.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 70dce109ec751a5c14de539b9c7461857cedb941
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110353"
---
# <a name="quickstart-send-an-sms-message"></a>Gyors útmutató: SMS-üzenet küldése

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

> [!IMPORTANT]
> Az SMS-üzeneteket Egyesült Államok telefonszámokról lehet elküldeni és fogadni. A kommunikációs szolgáltatások SMS-je még nem támogatja a más földrajzi területeken található telefonszámokat.
> További információ: **[telefonszámok típusai](../../concepts/telephony-sms/plan-solution.md)**.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET SDK](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript SDK](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python SDK](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java SDK](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>Hibaelhárítás

Az SMS-kézbesítéssel kapcsolatos problémák elhárításához engedélyezheti a kézbesítési [jelentéskészítést Event Grid](./handle-sms-events.md) a kézbesítési adatok rögzítéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan küldhet SMS-üzeneteket az Azure kommunikációs szolgáltatásokkal.

> [!div class="nextstepaction"]
> [Előfizetés SMS-eseményekre](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Telefonszám-típusok](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [További információ az SMS-ről](../../concepts/telephony-sms/concepts.md)
