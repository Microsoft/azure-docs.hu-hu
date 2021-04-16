---
title: Microsoft Graph API-k a PIM-hez (előzetes verzió) – Azure AD | Microsoft Docs
description: Információkat nyújt a Microsoft Graph API-k pim Azure AD Privileged Identity Management (előzetes verzió) használatával kapcsolatban.
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
ms.openlocfilehash: ce1900ca1ca04923f98642f0783925f2dd06619b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533582"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Microsoft Graph API-k Privileged Identity Management (előzetes verzió)

A feladatok Privileged Identity Management a Microsoft Graph [API-ját használva](/graph/overview) Azure Active Directory. Ez a cikk a Microsoft Graph API-k Privileged Identity Management.

A Microsoft Graph API-k részleteiért tekintse meg a [Azure AD Privileged Identity Management API-referenciát.](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta&preserve-view=true)

> [!IMPORTANT]
> A /beta verzióban elérhető API Microsoft Graph előzetes verziójúak, és változhatnak. Ezeknek az API-knak az éles alkalmazásokban való használata nem támogatott.

## <a name="required-permissions"></a>Szükséges engedélyek

A Microsoft Graph API-k Privileged Identity Management rendelkeznie kell az alábbi  engedélyek közül egy vagy többel:

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>Engedélyek beállítása

Ahhoz, hogy az alkalmazások Microsoft Graph API-kat hívjanak Privileged Identity Management számára, rendelkezniük kell a szükséges engedélyekkel. A szükséges engedélyek megadásának legegyszerűbb módja az Azure AD hozzájárulási [keretrendszerének használata.](../develop/consent-framework.md)

### <a name="set-permissions-in-graph-explorer"></a>Engedélyek beállítása a Graph Explorerben

Ha a Gráfkezelőt használja a hívások tesztelésére, megadhatja az engedélyeket az eszközben.

1. Jelentkezzen be globális rendszergazdaként a [Graph Explorerbe.](https://developer.microsoft.com/graph/graph-explorer)

1. Kattintson **a Modify permissions (Engedélyek módosítása) elemre.**

    ![Képernyőkép a "Gráfkezelő" lapról a kiválasztott "engedélyek módosítása" művelet használatával.](./media/pim-apis/graph-explorer.png)

1. Jelölje be a jelölőnégyzeteket a kívánt engedélyek mellett. `PrivilegedAccess.ReadWrite.AzureAD` A még nem érhető el a Graph Explorerben.

    ![Graph Explorer – engedélyek módosítása](./media/pim-apis/graph-explorer-modify-permissions.png)

1. Kattintson **az Engedélyek módosítása elemre** az engedélyváltozások alkalmazáshoz.

## <a name="next-steps"></a>Következő lépések

- [Azure AD Privileged Identity Management API-referencia](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta&preserve-view=true)
