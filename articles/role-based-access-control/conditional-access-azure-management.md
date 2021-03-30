---
title: Azure-felügyelethez való hozzáférés kezelése feltételes hozzáféréssel az Azure AD-ben
description: Ismerje meg, hogyan használhatja a feltételes hozzáférést az Azure AD-ben az Azure-felügyelethez való hozzáférés kezeléséhez.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 2a52635dbaa7a76034f3a535b099320a901e8c07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83758775"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Az Azure-felügyelethez való hozzáférés kezelése feltételes hozzáféréssel

> [!CAUTION]
> Győződjön meg arról, hogy az Azure-felügyelethez való hozzáférés kezeléséhez szükséges szabályzat beállítása előtt meg kell ismernie a feltételes hozzáférés működését. Ügyeljen arra, hogy ne hozzon létre olyan feltételeket, amelyek letiltják a saját hozzáférését a portálhoz.

A Azure Active Directory (Azure AD) feltételes hozzáférése a megadott feltételek alapján szabályozza a felhőalapú alkalmazásokhoz való hozzáférést. A hozzáférés engedélyezéséhez olyan feltételes hozzáférési szabályzatokat hozhat létre, amelyek engedélyezik vagy letiltják a hozzáférést attól függően, hogy teljesülnek-e a szabályzat követelményei. 

A felhőalapú alkalmazásokhoz való hozzáférés szabályozása jellemzően feltételes hozzáféréssel végezhető el. Házirendeket is beállíthat az Azure-felügyelethez való hozzáférés szabályozására bizonyos feltételek (például bejelentkezési kockázat, hely vagy eszköz) alapján, valamint a követelmények érvényesítéséhez, például a többtényezős hitelesítéshez.

Az Azure Management szabályzatának létrehozásához válassza a **Microsoft Azure felügyelet** lehetőséget a **Cloud apps** alatt, amikor kiválasztja azt az alkalmazást, amelyre alkalmazni szeretné a szabályzatot.

![Feltételes hozzáférés az Azure felügyeleti szolgáltatásaihoz](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

A létrehozott szabályzat az összes Azure felügyeleti végpontra vonatkozik, beleértve a következőket:

- Azure Portal
- Azure Resource Manager szolgáltató
- Klasszikus Service Management API-k
- Azure PowerShell
- Visual Studio-előfizetések felügyeleti portál
- Azure DevOps
- Azure Data Factory portál

Vegye figyelembe, hogy a házirend Azure PowerShellra vonatkozik, amely meghívja a Azure Resource Manager API-t. Ez nem vonatkozik az [Azure ad powershellre](/powershell/azure/active-directory/install-adv2), amely meghívja a Microsoft Graph.

A Microsoft Azure-kezelés feltételes hozzáférésének engedélyezésére szolgáló minta házirend beállításával kapcsolatos további információkért tekintse meg a [feltételes hozzáférés: többtényezős hitelesítés megkövetelése az Azure Managementhez](../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md)című cikket.
