---
title: Ügyféloldali kódtárak és REST API-k az Azure kommunikációs szolgáltatásokhoz
titleSuffix: An Azure Communication Services concept document
description: További információ az Azure kommunikációs szolgáltatások ügyféloldali könyvtárairól és a REST API-król.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: d5aba3638ce19c24841fe5b49b19b1139327b790
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578087"
---
# <a name="client-libraries-and-rest-apis"></a>Ügyfélkódtárak és REST API-k

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Az Azure kommunikációs szolgáltatások funkcióit koncepcionálisan hat területre szervezték. Egyes területek teljesen nyílt forráskódú ügyféloldali kódtárakkal rendelkeznek. A hívó ügyféloldali függvénytár saját hálózati adaptereket használ, és jelenleg zárt forrású, a csevegési függvénytár pedig zárt forráskódú függőséget tartalmaz. Az ügyféloldali kódtárak mintáit és további technikai részleteit az [Azure kommunikációs szolgáltatások GitHub](https://github.com/Azure/communication)-tárházában tesszük közzé.

## <a name="client-libraries"></a>Ügyfélkódtárak

| Szerelvény               | Protokollok             |Nyitott és lezárt forrás| Névterek                          | Funkciók                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Megnyitás            | Azure. erőforráskezelő. Communication | Kommunikációs szolgáltatások erőforrásainak kiépítése és kezelése             |
| Közös                 | REST | Megnyitás               | Azure. Communication. Common          | Alaptípusokat biztosít más ügyféloldali kódtárak számára |
| Felügyelet         | REST | Megnyitás               | Azure. Communication. Administration  | A felhasználók, a hozzáférési tokenek és a telefonszámok kezelése, a szabványoknak megfelelő kábítás és a kiszolgálók kiosztása |
| Csevegés                   | PIHENÉS saját jelzéssel | Megnyitás zárt forrású jelző csomaggal    | Azure. Communication. chat            | Valós idejű szöveg-alapú csevegés hozzáadása az alkalmazásokhoz  |
| SMS                    | REST | Megnyitás              | Azure. Communication. SMS             | SMS-üzenetek küldése és fogadása |
| Hívó                | Saját szállítás | Zárt |Azure. Communication. Calling         | A hang-, videó-, képernyő-megosztási és egyéb valós idejű adatkommunikációs képességek kihasználása          |

Vegye figyelembe, hogy a Azure Resource Manager, a felügyelet és az SMS ügyféloldali kódtára a szolgáltatás-integrációra koncentrál, és sok esetben biztonsági probléma merül fel, ha a funkciókat végfelhasználói alkalmazásokba integrálja. A gyakori és a csevegési ügyféloldali kódtárak alkalmasak a szolgáltatás-és ügyfélalkalmazások számára. A hívó ügyféloldali kódtár ügyfélalkalmazások számára készült. A szolgáltatási forgatókönyvekre összpontosító ügyféloldali függvénytár fejlesztés alatt áll.

### <a name="languages-and-publishing-locations"></a>Nyelvek és közzétételi helyszínek

Az egyes ügyféloldali függvénytár-csomagok közzétételi helyei alább láthatók. 


| Terület           | JavaScript | .NET | Python | Java | Swift vagy obj-C | Java (Android) | Egyéb                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.NuGet.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Ugrás a GitHubon keresztül](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Közös         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Common/)    | N.A.      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [Swift a GitHubon keresztül](https://github.com/Azure/azure-sdk-for-ios/releases/tag/1.0.0-beta.1)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Felügyelet | [npm](https://www.npmjs.com/package/@azure/communication-administration)         | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Administration)    | [PyPi](https://pypi.org/project/azure-communication-administration/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-administration)   | -              | -              | -                            |
| Csevegés           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -  | -  | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.NuGet.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Hívó        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [Obj-C a GitHubon keresztül](https://github.com/Azure/Communication/releases/tag/v1.0.0-beta.2)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Referenciadokumentáció     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | 

## <a name="rest-apis"></a>REST API-k

A kommunikációs szolgáltatások API-jai a [docs.microsoft.com](https://docs.microsoft.com/rest/api/azure/)egyéb Azure REST API-jai mellett is dokumentálva vannak. A dokumentációból megtudhatja, hogyan strukturálhatja HTTP-üzeneteit, és útmutatást nyújt a Poster használatához. Ez a dokumentáció a [githubon](https://github.com/Azure/azure-rest-api-specs)is elérhető a hencegés formátumában.

## <a name="additional-support-details"></a>További támogatási információk

### <a name="ios-and-android-support-details"></a>az iOS és az Android támogatásának részletei

- A kommunikációs szolgáltatások iOS-ügyféloldali kódtárai az iOS-es és a Xcode 11 + verzióját célozzák meg.
- Android Java ügyféloldali kódtárak cél Android API Level 21 + és Android Studio 4.0 +

### <a name="net-support-details"></a>.NET-támogatás részletei

A Calling szolgáltatás kivételével a kommunikációs szolgáltatások a .NET Standard 2,0-es verziójának felelnek meg, amely támogatja az alább felsorolt platformokat.

Támogatás a .NET-keretrendszer 4.6.1-es használatával
- Windows 10, 8,1, 8 és 7
- Windows Server 2012 R2, 2012 és 2008 R2 SP1

Támogatás a .NET Core 2,0-on keresztül:
- Windows 10 (1607 +), 7 SP1 +, 8,1
- Windows Server 2008 R2 SP1 +
- Max OS X 10.12 +
- Linux több verzió/disztribúció
- UWP 10.0.16299 (RS3), szeptember 2017
- Unity 2018,1
- Monó 5,4
- Xamarin iOS 10,14
- Xamarin Mac 3,8

## <a name="api-stability-expectations"></a>API-stabilitási elvárások 

> [!IMPORTANT]
> Ez a szakasz útmutatást nyújt a REST API-kkal és az ügyfél-kódtárak **stabil**jelöléssel. A előzetes kiadást, előzetes verziót vagy bétaverziót jelölt API-k **értesítés nélkül**módosíthatók vagy elavulttá válhatnak. Az Azure kommunikációs szolgáltatás jelenleg **nyilvános előzetes**verzióban érhető el, az API-k pedig ilyenként vannak megjelölve.

A jövőben előfordulhat, hogy kivonják a kommunikációs szolgáltatások ügyféloldali kódtárainak verzióit, és a REST API-k és a kiadott ügyféloldali kódtárak feltörési változásait is bevezetjük. Az Azure kommunikációs szolgáltatásai *általában* két támogatási szabályzatot követnek a szolgáltatási verziók kivonásához:

- A kommunikációs szolgáltatások felületének változása miatt legalább három évig értesítést kap a kód módosításának szükségességéről. Az összes dokumentált REST API és ügyféloldali függvénytár API általában legalább három éves figyelmeztetést biztosít a felületek leszerelése előtt.
- Legalább egy évig értesítést kap, mielőtt frissítenie kell az ügyféloldali függvénytár-szerelvényeket a legújabb alverzióra. Ezeknek a szükséges frissítéseknek semmilyen programkód-módosításra sincs szükségük, mert ugyanazon a főverzión vannak. Ez különösen igaz a hívó és a csevegési kódtárak esetében, amelyek olyan valós idejű összetevőkkel rendelkeznek, amelyek gyakran igényelnek biztonsági és teljesítménybeli frissítéseket. Javasoljuk, hogy frissítse a kommunikációs szolgáltatások ügyféloldali kódtárait.

### <a name="api-and-client-library-decommissioning-examples"></a>Példák az API és az ügyféloldali kódtár leszerelésére

**Integrálta az SMS-REST API V24 verzióját az alkalmazásba. Az Azure kommunikációs kiadásai v25.**

Az API-k futtatása előtt 3 év figyelmeztetést kap, és a v25-ra való frissítésre kényszerülnek. A frissítéshez szükség lehet a kód módosítására.

**Integrálta a hívó ügyféloldali kódtár v 2.02 verzióját az alkalmazásba. Az Azure kommunikációs kiadásai: v 2.05.**

Előfordulhat, hogy frissítenie kell a Called Client Library v 2.05 verziójára a v 2.05 kiadásának 12 hónapon belül. Ez az összetevő egyszerű helyettesítése a kód módosítása nélkül, mert a v 2.05 a v2 főverzióban van, és nem változik a változások megszakítása.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse meg a következő ügyféloldali függvénytár-áttekintéseket:

- [Az ügyféloldali kódtár meghívása – áttekintés](../concepts/voice-video-calling/calling-sdk-features.md)
- [Csevegő ügyféloldali kódtár – áttekintés](../concepts/chat/sdk-features.md)
- [SMS ügyféloldali kódtár – áttekintés](../concepts/telephony-sms/sdk-features.md)

Ismerkedés az Azure kommunikációs szolgáltatásokkal:

- [Azure kommunikációs erőforrások létrehozása](../quickstarts/create-communication-resource.md)
- [Felhasználói hozzáférési tokenek](../quickstarts/access-tokens.md) előállítása
