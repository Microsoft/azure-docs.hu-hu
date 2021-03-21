---
title: Azure AD-hitelesítés & nemzeti felhők | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az alkalmazások regisztrációs és hitelesítési végpontját az országos felhők számára.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: marsma, negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: da1ea6462d22242d23629bcec192d7760314dfed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96444816"
---
# <a name="national-clouds"></a>Országos felhők

Az országos felhők fizikailag elkülönített Azure-példányok. Az Azure ezen régiói úgy vannak kialakítva, hogy meggyőződjenek arról, hogy az adattárolási, a szuverenitási és a megfelelőségi követelmények a földrajzi határokon belül teljesülnek.

A globális felhővel (Azure Active Directory (Azure AD) együtt a következő nemzeti felhőkben is üzembe helyezhetők:  

- Azure Government
- Azure Germany
- Azure China 21Vianet

Az országos felhők egyediek és egy különálló környezetek az Azure Global-től. Fontos, hogy tisztában legyenek a legfontosabb különbségekkel, miközben az alkalmazás fejlesztése ezekben a környezetekben. A különbségek közé tartozik az alkalmazások regisztrálása, a jogkivonatok beszerzése és a végpontok konfigurálása.

## <a name="app-registration-endpoints"></a>Alkalmazás-regisztrációs végpontok

A nemzeti felhők mindegyike külön Azure Portal. Ha egy nemzeti felhőben szeretné integrálni az alkalmazásokat a Microsoft Identity platformmal, akkor külön regisztrálnia kell az alkalmazást a környezetre jellemző összes Azure Portalban.

A következő táblázat felsorolja az egyes nemzeti felhőhöz tartozó alkalmazások regisztrálásához használt Azure AD-végpontok alap URL-címeit.

| Nemzeti felhő | Azure AD-portál végpont |
|----------------|--------------------------|
| Azure AD az USA kormányzati szerveinek | `https://portal.azure.us` |
| Azure AD Germany | `https://portal.microsoftazure.de` |
| 21Vianet által üzemeltetett Azure AD China | `https://portal.azure.cn` |
| Azure AD (globális szolgáltatás) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD-hitelesítési végpontok

Minden nemzeti felhő külön hitelesíti a felhasználókat az egyes környezetekben, és külön hitelesítési végpontokkal rendelkezik.

A következő táblázat felsorolja az egyes nemzeti felhők jogkivonatának beszerzéséhez használt Azure AD-végpontok alap URL-címeit.

| Nemzeti felhő | Azure AD-hitelesítési végpont |
|----------------|-------------------------|
| Azure AD az USA kormányzati szerveinek | `https://login.microsoftonline.us` |
| Azure AD Germany| `https://login.microsoftonline.de` |
| 21Vianet által üzemeltetett Azure AD China | `https://login.chinacloudapi.cn` |
| Azure AD (globális szolgáltatás)| `https://login.microsoftonline.com` |

Az Azure AD-engedélyezési vagy jogkivonat-végpontokra irányuló kéréseket a megfelelő régióspecifikus alap URL-cím használatával lehet megalkotni. Az Azure Germany esetében például:

- Az engedélyezési Common Endpoint értéke `https://login.microsoftonline.de/common/oauth2/v2.0/authorize` .
- A token Common Endpoint értéke `https://login.microsoftonline.de/common/oauth2/v2.0/token` .

Egybérlős alkalmazások esetében az előző URL-címek "Common" értékét cserélje le a bérlői AZONOSÍTÓra vagy a névre. Például: `https://login.microsoftonline.de/contoso.com`.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Ha meg szeretné tudni, hogyan hívhatja meg a Microsoft Graph API-kat egy nemzeti felhőalapú környezetben, lépjen a [Microsoft Graph a nemzeti Felhőbeli üzembe helyezések](/graph/deployments)területen.

> [!IMPORTANT]
> Előfordulhat, hogy a globális szolgáltatás adott régióiban lévő bizonyos szolgáltatások és szolgáltatások nem érhetők el az összes nemzeti felhőben. Ha szeretné megtudni, hogy mely szolgáltatások érhetők el, lépjen az [elérhető termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Ha meg szeretné tudni, hogyan hozhat létre egy alkalmazást a Microsoft Identity platform használatával, kövesse a [Microsoft Authentication Library (MSAL) oktatóanyagot](msal-national-cloud.md). Pontosabban, az alkalmazás bejelentkezik egy felhasználóval, és hozzáférési jogkivonatot kap a Microsoft Graph API meghívásához.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használhatja a [Microsoft Authentication Libraryt (MSAL) egy nemzeti felhőalapú környezetben](msal-national-cloud.md).

Nemzeti felhő dokumentációja:

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Germany](../../germany/index.yml)
