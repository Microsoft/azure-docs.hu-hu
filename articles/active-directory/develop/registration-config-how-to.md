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
ROBOTS: NOINDEX
ms.openlocfilehash: 778523869715916bf1e4c32af59ea7a0081df91b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103311"
---
# <a name="how-to-discover-endpoints"></a>Végpontok felderítése

Az alkalmazáshoz tartozó hitelesítési végpontokat a [Azure Portalban](https://portal.azure.com)találja.

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Válassza a **Azure Active Directory** lehetőséget.
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk** lehetőséget, majd a felső menüben válassza a **végpontok** lehetőséget.

    Ekkor megjelenik a **végpontok** lap, amely megjeleníti a bérlőhöz tartozó hitelesítési végpontokat.
    
    Használja azt a végpontot, amely az alkalmazás **(ügyfél) azonosítójával** együtt használt hitelesítési protokollnak felel meg az alkalmazásra vonatkozó hitelesítési kérelem kiépítéséhez.

Az **országos felhők** (például az Azure ad China, a Germany és az Egyesült Államok kormánya) saját alkalmazás-regisztrációs portált és Azure ad-hitelesítési végpontokat tartalmazhatnak. További információk a [nemzeti felhők áttekintésében](authentication-national-cloud.md).

## <a name="next-steps"></a>Következő lépések

A különböző Azure-környezetekben található végpontokkal kapcsolatos további információkért tekintse meg az [országos felhők áttekintését](authentication-national-cloud.md).
