---
title: Azure AD-alkalmazás-regisztrációhoz tartozó végpontok beszerzése
titleSuffix: Microsoft identity platform
description: Az Azure AD-vel való fejlesztéshez vagy regisztráláshoz használt egyéni alkalmazás hitelesítési végpontjának megkeresése.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ms.openlocfilehash: 2c348bd7a1738742744976c46396f62a3317dc17
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017081"
---
# <a name="how-to-discover-endpoints"></a>Végpontok felderítése

Az alkalmazáshoz tartozó hitelesítési végpontokat a [Azure Portalban](https://portal.azure.com)találja.

1. Jelentkezzen be a <a href="https://portal.azure.com/" target="_blank">Azure Portalba <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Válassza a **Azure Active Directory** lehetőséget.
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk** lehetőséget, majd a felső menüben válassza a **végpontok** lehetőséget.

    Ekkor megjelenik a **végpontok** lap, amely megjeleníti a bérlőhöz tartozó hitelesítési végpontokat.
    
    Használja azt a végpontot, amely az alkalmazás **(ügyfél) azonosítójával** együtt használt hitelesítési protokollnak felel meg az alkalmazásra vonatkozó hitelesítési kérelem kiépítéséhez.

Az **országos felhők** (például az Azure ad China, a Germany és az Egyesült Államok kormánya) saját alkalmazás-regisztrációs portált és Azure ad-hitelesítési végpontokat tartalmazhatnak. További információk a [nemzeti felhők áttekintésében](authentication-national-cloud.md).

## <a name="next-steps"></a>További lépések

A különböző Azure-környezetekben található végpontokkal kapcsolatos további információkért tekintse meg az [országos felhők áttekintését](authentication-national-cloud.md).
