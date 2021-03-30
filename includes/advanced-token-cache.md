---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 11/05/2020
ms.author: kkrishna
ms.openlocfilehash: 174946667885debc348370ef2c6f93206890e9c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94628017"
---
A MSAL jogkivonat-gyorsítótárazási implementációja lehetővé teszi, hogy a háttérben futó alkalmazások, API-k és szolgáltatások a hozzáférési jogkivonat gyorsítótárának használatával továbbra is a felhasználók nevében járjanak el a távollétében. Ez különösen akkor hasznos, ha a háttérben futó alkalmazásoknak és szolgáltatásoknak továbbra is működniük kell a felhasználó nevében, miután a felhasználó kilépett az előtér-webalkalmazásból.

Napjainkban a legtöbb háttérbeli folyamat [alkalmazás-engedélyeket](/graph/auth/auth-concepts#microsoft-graph-permissions) használ, ha a felhasználó adataival való együttműködésre van szükségük anélkül, hogy a hitelesítés vagy a hitelesítés megtörténjen. Mivel az alkalmazás engedélyei gyakran igényelnek rendszergazdai beleegyezést, ami jogosultságszint-emelést igényel, szükségtelen súrlódás fordul elő, mivel a fejlesztő nem akarta megszerezni az engedélyt azon kívül, hogy a felhasználó eredetileg beleegyezett az alkalmazásához.

Ez a mintakód a GitHubon azt mutatja be, hogyan kerülheti el ezt a szükségtelen súrlódást azáltal, hogy hozzáfér a MSAL jogkivonat-gyorsítótárához a háttérben futó alkalmazásokból:

 [A bejelentkezett felhasználó jogkivonat-gyorsítótárának elérése a háttérben futó alkalmazások, API-k és szolgáltatások között](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)