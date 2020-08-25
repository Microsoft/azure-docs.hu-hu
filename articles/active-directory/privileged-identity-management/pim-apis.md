---
title: A PIM-hez Microsoft Graph API-k (előzetes verzió) – Azure AD | Microsoft Docs
description: Információt nyújt a Microsoft Graph API-k használatáról Azure AD Privileged Identity Management (PIM) (előzetes verzió).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95b0b026b75b9b77c94451245ac4f18d487fc2e4
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783518"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Privileged Identity Management API-k Microsoft Graph (előzetes verzió)

A Azure Active Directory [Microsoft Graph API](/graph/overview) -k használatával Privileged Identity Management feladatokat hajthat végre. Ez a cikk a Privileged Identity Management Microsoft Graph API-k használatára vonatkozó fontos fogalmakat ismerteti.

A Microsoft Graph API-kkal kapcsolatos részletekért tekintse meg a [Azure ad PRIVILEGED Identity Management API-referenciát](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta).

> [!IMPORTANT]
> A Microsoft Graph/Beta verziója alatti API-k előzetes verzióban érhetők el, és változhatnak. Az API-k üzemi alkalmazásokban való használata nem támogatott.

## <a name="required-permissions"></a>Szükséges engedélyek

A Privileged Identity Management Microsoft Graph API-k meghívásához a következő engedélyek **közül egyet vagy többet** kell megadnia:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Engedélyek beállítása

Ahhoz, hogy az alkalmazások meg tudják hívni a Privileged Identity Management Microsoft Graph API-jait, rendelkeznie kell a szükséges engedélyekkel. A szükséges engedélyek megadásának legegyszerűbb módja az [Azure ad-beli engedélyezési keretrendszer](../develop/consent-framework.md)használata.

### <a name="set-permissions-in-graph-explorer"></a>Engedélyek beállítása a Graph Explorerben

Ha a Graph Explorer segítségével teszteli a hívásokat, megadhatja az engedélyeket az eszközben.

1. Jelentkezzen be a [Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer) globális rendszergazdaként.

1. Kattintson az **engedélyek módosítása**elemre.

    ![Graph Explorer – engedélyek módosítása](./media/pim-apis/graph-explorer.png)

1. Jelölje be a jelölőnégyzeteket a felvenni kívánt engedélyek mellett. `PrivilegedAccess.ReadWrite.AzureAD` még nem érhető el a Graph Explorerben.

    ![Graph Explorer – engedélyek módosítása](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Kattintson az **engedélyek módosítása** elemre az engedély módosításának alkalmazásához.

## <a name="next-steps"></a>Következő lépések

- [Azure AD Privileged Identity Management API-hivatkozás](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta)