---
title: Több bérlő kezelése felügyelt biztonsági szolgáltatóként az Azure Sentinelben | Microsoft Docs
description: Több bérlő üzembe helyezése és kezelése az Azure Sentinelben felügyelt biztonsági szolgáltatóként (MSSP) az Azure Lighthouse használatával.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: 011ddb883c028a954a8b0683c220bf6341eddb66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578146"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>Több bérlő kezelése az Azure Sentinelben MSSP

Ha Ön felügyelt biztonsági szolgáltató (MSSP), és az [Azure Lighthouse](../lighthouse/overview.md) -t használja a Security Operations Center-(SoC-) szolgáltatások ügyfeleknek való ellátására, az ügyfelek Azure Sentinel-erőforrásait közvetlenül a saját Azure-bérlőből kezelheti, anélkül, hogy csatlakoznia kellene az ügyfél bérlője számára. 

## <a name="prerequisites"></a>Előfeltételek

- [Az Azure Lighthouse előkészítése](../lighthouse/how-to/onboard-customer.md)

- Ahhoz, hogy ez megfelelően működjön, a bérlőnek (a MSSP-bérlőnek) legalább egy előfizetéshez regisztrálnia kell az Azure Sentinel erőforrás-szolgáltatókat. Emellett minden ügyfél bérlője számára regisztrálni kell az erőforrás-szolgáltatókat. Ha már regisztrálta az Azure Sentinelt a bérlőben, és az ügyfeleit, készen áll a kezdésre. A regisztráció ellenőrzéséhez hajtsa végre a következő lépéseket:

    1. Válassza ki az **előfizetéseket** a Azure Portal, majd válassza ki a megfelelő előfizetést a menüből.

    1. Az előfizetés képernyő navigációs menüjének **Beállítások** területén válassza az **erőforrás-szolgáltatók** elemet.

    1. Az ***előfizetés nevéből* | Az erőforrás-szolgáltatók** képernyőn keresse meg és válassza ki a *Microsoft. OperationalInsights* és a *Microsoft. SecurityInsights* elemet, és jelölje be az **állapot** oszlopot. Ha a szolgáltató állapota *NotRegistered*, válassza a **regisztráció** lehetőséget.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="Erőforrás-szolgáltatók keresése":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>Az Azure Sentinel elérése felügyelt bérlők számára

1. A **címtár + előfizetés** területen válassza ki a delegált címtárakat (könyvtár = bérlő), valamint azokat az előfizetéseket, amelyeken az ügyfél Azure Sentinel-munkaterületei találhatók.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="Bérlők és előfizetések kiválasztása":::

1. Nyissa meg az Azure Sentinelt. Ekkor megjelenik a kiválasztott előfizetések összes munkaterülete, és zökkenőmentesen dolgozhat velük, például a saját bérlő bármely munkaterületével.

> [!NOTE]
> A felügyelt munkaterületen belülről nem telepíthet összekötőket az Azure Sentinelben. Összekötő üzembe helyezéséhez közvetlenül be kell jelentkeznie arra a bérlőre, amelyen az összekötőt telepíteni kívánja, majd a szükséges engedélyekkel hitelesítenie kell magát.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan kezelheti zökkenőmentesen több Azure Sentinel-bérlőt. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

