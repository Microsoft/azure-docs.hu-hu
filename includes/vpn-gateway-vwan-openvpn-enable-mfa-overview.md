---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8fba8aa577bcb3b5ef44d57c388a1f1de7494782
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995615"
---
Ha azt szeretné, hogy a felhasználók a hozzáférés megadását megelőzően egy második hitelesítési tényezőt kérjenek, konfigurálhatja az Azure AD Multi-Factor Authentication (MFA) szolgáltatását. Az MFA-t felhasználónként is konfigurálhatja, vagy az MFA-t [feltételes hozzáférés](../articles/active-directory/conditional-access/overview.md)használatával is kihasználhatja.

* Az MFA felhasználónként engedélyezhető díjmentesen. Ha felhasználónként engedélyezi az MFA-t, a rendszer a felhasználót az Azure AD-bérlőhöz kötött összes alkalmazás esetében kéri a második tényező hitelesítésére. Lásd: [1. lehetőség](#peruser) a lépésekhez.
* A feltételes hozzáférés lehetővé teszi, hogy finomabban szabályozható legyen a második tényező előléptetése. Lehetővé teszi az MFA hozzárendelését a VPN-hez, és kizár más, az Azure AD-bérlőhöz kötött alkalmazásokat. A lépésekért lásd a [2. lehetőséget](#conditional) .