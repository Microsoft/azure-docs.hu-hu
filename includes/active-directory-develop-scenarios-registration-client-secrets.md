---
title: fájl belefoglalása
description: fájl belefoglalása a bizalmas ügyfél-forgatókönyvekhez (démon, webalkalmazás, webes API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996006"
---
## <a name="add-a-client-secret-or-certificate"></a>Ügyfél titkos kulcsának vagy tanúsítványának hozzáadása

Akárcsak a bizalmas ügyfélalkalmazások esetében, hozzá kell adnia egy titkos vagy egy tanúsítványt, amely az alkalmazás *hitelesítő adatait* adja meg, így a felhasználó beavatkozása nélkül is hitelesítheti magát.

Az ügyfélalkalmazás regisztrációjának hitelesítő adatait a [Azure Portal](#add-client-credentials-by-using-the-azure-portal) használatával vagy parancssori eszközzel, például a [PowerShell](#add-client-credentials-by-using-powershell)használatával adhatja hozzá.

### <a name="add-client-credentials-by-using-the-azure-portal"></a>Ügyfél hitelesítő adatainak hozzáadása a Azure Portal használatával

Ha hitelesítő adatokat szeretne hozzáadni a bizalmas ügyfélalkalmazás alkalmazásának regisztrálásához, kövesse a rövid útmutató [: alkalmazás regisztrálása a Microsoft Identity platformmal](../articles/active-directory/develop/quickstart-register-app.md) a hozzáadni kívánt hitelesítő adatok típusához:

* [Ügyfél titkos kulcsának hozzáadása](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [Tanúsítvány hozzáadása](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>Ügyfél hitelesítő adatainak hozzáadása a PowerShell használatával

Emellett hitelesítő adatokat is hozzáadhat, amikor az alkalmazást a Microsoft Identity platformon regisztrálja a PowerShell használatával.

Az [Active-Directory-dotnetcore-Daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) kód minta a githubon azt mutatja be, hogyan adhat hozzá alkalmazás-titkos kódot vagy tanúsítványt egy alkalmazás regisztrálásakor:

- Az **ügyfél-titkos** PowerShell-lel való hozzáadásával kapcsolatos részletekért lásd: [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- A **tanúsítvány** PowerShell-lel való hozzáadásával kapcsolatos részletekért lásd: [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
