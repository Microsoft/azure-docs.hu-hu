---
title: Áthelyezés a webes API-kat meghívó webalkalmazásba | Azure
titleSuffix: Microsoft identity platform
description: Útmutató a webes API-kat meghívó webalkalmazások éles környezetbe való áthelyezéséhez.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cf32274a49cb1b790e9d872efe36f2e1cb188d1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675940"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>Webes API-kat meghívó webalkalmazás: áthelyezés éles környezetbe

Most, hogy már tudja, hogyan szerezzen be tokent a webes API-k meghívásához, itt néhány megfontolandó szempontot figyelembe kell venni, amikor az alkalmazást éles környezetbe helyezi.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Következő lépések

További információt a ASP.NET Core Web Apps teljes körű, progresszív oktatóanyagának kipróbálásával foglalkozó témakörben talál. Az oktatóanyag:

- Bemutatja, hogyan lehet a felhasználókat több célközönségbe vagy nemzeti felhőbe, vagy közösségi identitások használatával aláírni.
- Meghívja a Microsoft Graph.
- Több Microsoft API-t hív meg.
- Kezeli a növekményes beleegyezett.
- Meghívja saját webes API-ját.

[ASP.NET Core webalkalmazás-oktatóanyag](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
