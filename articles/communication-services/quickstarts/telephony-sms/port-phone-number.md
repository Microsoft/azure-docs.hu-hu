---
title: Rövid útmutató – telefonszám behelyezése az Azure kommunikációs szolgáltatásokba
description: Megtudhatja, hogyan lehet telefonszámot beosztani a kommunikációs szolgáltatások erőforrásaiba
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/20/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: b4357b8d4fe1d7184fc2dcfab2008dc6e0f334fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729924"
---
# <a name="quickstart-port-a-phone-number"></a>Rövid útmutató: telefonszámok portja

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Ismerkedjen meg az Azure kommunikációs szolgáltatásokkal, ha a telefonszámát az Azure kommunikációs szolgáltatások erőforrásaiba helyezi. A Egyesült Államokon alapuló, díjmentes és földrajzi számok a megfelelő hordozhatóságra jogosultak. A telefonszám-típusokkal kapcsolatos további információkért tekintse meg a [telefonszám fogalmi dokumentációját](../../concepts/telephony-sms/plan-solution.md).

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Aktív kommunikációs szolgáltatások erőforrása.](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>Az Azure-erőforrások adatainak összegyűjtése

A portra vonatkozó kérelem kezdeményezése előtt navigáljon a Azure Portal, és válassza ki a kommunikációs szolgáltatások erőforrását. Ha a `Overview` panel ki van választva, kattintson a `JSON View` jobb felső sarokban található hivatkozásra:

:::image type="content" source="./media/number-port/json-view.png" alt-text="A JSON nézet kijelölését bemutató képernyőkép.":::

Jegyezze fel az erőforrás **Azure-azonosítóját** és a nem módosítható **erőforrás-azonosítót**:

:::image type="content" source="./media/number-port/json-properties.png" alt-text="A JSON-tulajdonságok kijelölését bemutató képernyőkép.":::

## <a name="initiate-the-port-request"></a>A port kérésének kezdeményezése

A Egyesült Államokon alapuló, díjmentes és földrajzi számok a megfelelő hordozhatóságra jogosultak. A következő űrlapok egyikével küldje el a port kérelmét:

- Díjmentes telefonszámok esetén: díjmentes [számú portra vonatkozó kérelem](https://aka.ms/acs-port-form-tollfree)
- Az USA-beli földrajzi számok esetében: a földrajzi portszámra vonatkozó [kérelem](https://aka.ms/acs-port-form-geographic)

Ha elkészült, küldje el a befejezett portra vonatkozó kérelem űrlapját a következőre: acsporting@microsoft.com . Győződjön meg arról, hogy az e-mail tárgya az "ACS Port-In kérelem" előtaggal kezdődik.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta a következőket:

> [!div class="checklist"]
> * A kommunikációs szolgáltatások erőforrás-metaadatainak beolvasása
> * Küldési kérelem küldése a telefonszámhoz

> [!div class="nextstepaction"]
> [SMS küldése](../telephony-sms/send.md) 
>  [Ismerkedés a hívással](../voice-video-calling/getting-started-with-calling.md)
