---
title: Újdonságok az Azure AD-ben Microsoft 365 Governmentben? | Microsoft Docs
description: Ismerkedjen meg az Azure Active Directory (Azure AD) változásaival a Microsoft 365 Government Cloud-példányon, ami hatással lehet a szolgáltatásra.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.reviewer: sumitp
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0efc4bc8f89b0fbefbba171d80a3f8a1ed5e7f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89318930"
---
# <a name="whats-new-for-azure-active-directory-in-microsoft-365-government"></a>A Microsoft 365 Government Azure Active Directory újdonságai

Módosítottuk a Azure Active Directory (Azure AD) szolgáltatást a Microsoft 365 Government Cloud-példányon, amely a következő szolgáltatásokat használó ügyfelekre vonatkozik:

- Microsoft Azure Government

- Microsoft 365 Government – magas GCC

- Microsoft 365 Government – DoD

Ez a cikk a Microsoft 365 Government – GCC-ügyfelekre nem vonatkozik.

## <a name="changes-to-the-initial-domain-name"></a>A kezdeti tartománynév módosításai

A szervezet kezdeti regisztrálása során egy Microsoft 365 Government Online szolgáltatásra vonatkozóan a rendszer megkérte, hogy válassza ki a szervezet tartománynevét `<your-domain-name>.onmicrosoft.com` . Ha már rendelkezik a. com utótaggal rendelkező tartománynévvel, semmi sem változik.

Ha azonban új Microsoft 365 Government-szolgáltatásra jelentkezik be, a rendszer arra kéri, hogy válasszon egy tartománynevet az `.us` utótag használatával. Így lesz `<your-domain-name>.onmicrosoft.us` .

>[!Note]
>Ez a változás nem vonatkozik a Cloud Service Providers (CSP) által kezelt ügyfelekre.

## <a name="changes-to-portal-access"></a>A portál elérésének módosításai

Frissítettük a Microsoft Azure Government, Microsoft 365 Government – GCC High és Microsoft 365 Government – DoD portál-végpontokat, ahogy azt a [végpont-hozzárendelési táblázat](#endpoint-mapping)mutatja.

Korábban az ügyfelek bejelentkezhetnek a Worldwide Azure (portal.azure.com) és az Office 365 (portal.office.com) portálok használatával. Ezzel a frissítéssel a felhasználóknak most be kell jelentkezniük az adott Microsoft Azure Government, Microsoft 365 Government-GCC High és a Microsoft 365 Government-DoD portál használatával.

## <a name="endpoint-mapping"></a>Végpont-hozzárendelés

A következő táblázat az összes ügyfél végpontját tartalmazza:

| Name | Végpont részletei |
|------|------------------|
| Portálok |Microsoft Azure Government: https://portal.azure.us<p>Microsoft 365 Government – magas GCC: https://portal.office365.us<p>Microsoft 365 Government – DoD: https://portal.apps.mil |
| Azure Active Directory Authority végpont | https://login.microsoftonline.us |
| Microsoft Graph API Microsoft 365 Government számára – magas GCC | https://graph.microsoft.us |
| Microsoft Graph API Microsoft 365 Government számára – DoD | https://dod-graph.microsoft.us |
| Azure Government Services-végpontok | Részletekért lásd: [Azure Government fejlesztői útmutató](../../azure-government/documentation-government-developer-guide.md) |
| Microsoft 365 Government – magas szintű GCC-végpontok | Részletekért lásd: [Office 365 Egyesült Államok kormányának GCC magas végpontja](/office365/enterprise/office-365-u-s-government-gcc-high-endpoints) |
| Microsoft 365 Government – DoD | Részletekért lásd: [Office 365 US government DoD-végpontok](/office365/enterprise/office-365-u-s-government-dod-endpoints) |

## <a name="next-steps"></a>Következő lépések

További információért lásd a következő cikkeket:

- [Mi az az Azure Government?](../../azure-government/documentation-government-welcome.md)

- [Azure Government HRE-szolgáltató végpontjának frissítése](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

- [Az USA kormányzati felhőben Microsoft Graph végpontok](https://developer.microsoft.com/graph/blogs/new-microsoft-graph-endpoints-in-us-government-cloud/)

- [Office 365 Egyesült államokbeli GCC High és DoD](/office365/servicedescriptions/office-365-platform-service-description/office-365-us-government/gcc-high-and-dod)