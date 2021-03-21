---
title: Mobile App – webes API-k hívása éles környezetben | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó mobil alkalmazást. (Alkalmazások előkészítése éles környezethez)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 08243fd06de289941d8e6a9197ccb349614af056
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675957"
---
# <a name="prepare-mobile-apps-for-production"></a>Mobile apps for Products előkészítése

Ez a cikk részletesen ismerteti a mobileszköz minőségének és megbízhatóságának javítását, mielőtt az éles környezetbe helyezi őket.

## <a name="handle-errors"></a>Hibakezelés

Amikor előkészíti a Mobile apps-t éles környezetben, több hiba is előfordulhat. Az Ön által kezelt fő esetek a beavatkozási hibák és tartalékok. Egyéb feltételek, amelyeket érdemes figyelembe vennie a nem hálózati helyzetek, a szolgáltatási kimaradások, a rendszergazdai belefoglalási követelmények és az egyéb forgatókönyv-specifikus esetekben.

Minden Microsoft Authentication Library (MSAL) típushoz megtalálhatja a hibakódokat és a wiki-tartalmakat, amelyek a hibák állapotának kezelését ismertetik:

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Következő lépések

További minták kipróbálásához tekintse meg az [asztali és mobil nyilvános ügyfélalkalmazások](sample-v2-code.md#desktop-and-mobile-public-client-apps)című témakört.