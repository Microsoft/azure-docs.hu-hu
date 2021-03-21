---
title: Webes API-hívás webes API-k áthelyezése az éles környezetbe | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan helyezhet át olyan webes API-t, amely webes API-kat hív meg éles környezetben.
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
ms.openlocfilehash: 0ab5baef925b7c8589dd7852b6ff8058d67ba745
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675872"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Webes API-kat meghívó webes API: áthelyezés éles környezetbe

Miután beszerzett egy jogkivonatot a webes API-k meghívásához, a következő szempontokat érdemes figyelembe venni, amikor az alkalmazást éles környezetben helyezi át.

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a webes API-k saját webes API-ból való meghívásának alapjait, a következő oktatóanyag érdekli, amely leírja a webes API-kat meghívó védett webes API-k létrehozásához használt kódot.

| Sample | Platform | Description |
|--------|----------|-------------|
| [Active-Directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) 1. fejezet | ASP.NET Core webes API, asztali (WPF) | ASP.NET Core webes API-hívások Microsoft Graph, amelyet a Microsoft Identity platform használatával hívhat meg egy WPF-alkalmazásból. |
