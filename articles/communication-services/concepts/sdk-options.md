---
title: Ügyféloldali kódtárak és REST API-k az Azure kommunikációs szolgáltatásokhoz
titleSuffix: An Azure Communication Services concept document
description: További információ az Azure Communication Services SDK-król és a REST API-król.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: effd7658bbfe7359e1f99f9452857824c2c45c2f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107890"
---
# <a name="client-libraries-and-rest-apis"></a>Ügyfélkódtárak és REST API-k

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Az Azure kommunikációs szolgáltatások funkcióit koncepcionálisan hat területre szervezték. Néhány területen teljesen nyílt forráskódú SDK-k találhatók. A hívó SDK saját hálózati adaptereket használ, és jelenleg zárt forrású, a csevegési függvénytár pedig zárt forrású függőséget tartalmaz. Az SDK-k mintáit és további technikai részleteit az [Azure kommunikációs szolgáltatások GitHub](https://github.com/Azure/communication)-tárházában tesszük közzé.

## <a name="client-libraries"></a>Ügyfélkódtárak

| Szerelvény               | Protokollok             |Nyitott és lezárt forrás| Névterek                          | Képességek                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Megnyitás            | Azure. erőforráskezelő. Communication | Kommunikációs szolgáltatások erőforrásainak kiépítése és kezelése             |
| Közös                 | REST | Megnyitás               | Azure. Communication. Common          | Alaptípusokat biztosít más SDK-k számára |
| Identitás         | REST | Megnyitás               | Azure. Communication. Identity  | Felhasználók kezelése, hozzáférési tokenek |
| Telefonszámok         | REST | Megnyitás               | Azure. Communication. PhoneNumbers  | Telefonszámok kezelése |
| Csevegés                   | PIHENÉS saját jelzéssel | Megnyitás zárt forrású jelző csomaggal    | Azure. Communication. chat            | Valós idejű szöveg-alapú csevegés hozzáadása az alkalmazásokhoz  |
| SMS                    | REST | Megnyitás              | Azure. Communication. SMS             | SMS-üzenetek küldése és fogadása |
| Hívó                | Saját szállítás | Lezárt |Azure. Communication. Calling         | A hang-, videó-, képernyő-megosztási és egyéb valós idejű adatkommunikációs képességek kihasználása          |

Vegye figyelembe, hogy a Azure Resource Manager, az identitás és az SMS SDK-k a szolgáltatások integrálására összpontosítanak, és sok esetben biztonsági problémák merülnek fel, ha a funkciókat végfelhasználói alkalmazásokba integrálja. A gyakori és csevegési SDK-k alkalmasak a szolgáltatás-és ügyfélalkalmazások számára. A hívó SDK ügyfélalkalmazások számára készült. A szolgáltatási forgatókönyvekre összpontosító SDK fejlesztés alatt áll.

### <a name="languages-and-publishing-locations"></a>Nyelvek és közzétételi helyszínek

Az egyes SDK-csomagok közzétételi helyei alább láthatók.

| Terület           | JavaScript | .NET | Python | Java SE | iOS | Android | Egyéb                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Ugrás a GitHubon keresztül](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Közös         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | N/A      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Identitás | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| Telefonszámok | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| Csevegés           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Hívó        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Referenciadokumentáció     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](/objectivec/communication-services/calling/)      | [docs](/java/api/com.azure.communication.calling)            | -                              |

## <a name="rest-apis"></a>REST API-k

A kommunikációs szolgáltatások API-jai a [docs.microsoft.com](/rest/api/azure/)egyéb Azure REST API-jai mellett is dokumentálva vannak. A dokumentációból megtudhatja, hogyan strukturálhatja HTTP-üzeneteit, és útmutatást nyújt a Poster használatához. Ez a dokumentáció a [githubon](https://github.com/Azure/azure-rest-api-specs)is elérhető a hencegés formátumában.

## <a name="additional-support-details"></a>További támogatási információk

### <a name="ios-and-android-support-details"></a>az iOS és az Android támogatásának részletei

- A kommunikációs szolgáltatások iOS SDK-k az iOS 13 + és a Xcode 11 + verzióját célozzák meg.
- Androidos Java SDK-k cél Android API Level 21 + és Android Studio 4.0 +

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

## <a name="calling-sdk-timeouts"></a>Az SDK időtúllépésének meghívása

A következő időtúllépések érvényesek az SDK-kat hívó kommunikációs szolgáltatásokra:

| Művelet           | Időtúllépés hossza másodpercben |
| -------------- | ---------- |
| Újracsatlakozási/eltávolítási résztvevő | 120 |
| Új modális hívás hozzáadása vagy eltávolítása (videó vagy képernyőfelvétel indítása/leállítása) | 40 |
| Hívási átviteli művelet időtúllépése | 60 |
| 1:1 hívási idő időtúllépése | 85 |
| Csoportos hívás létesítésének időtúllépése | 85 |
| PSTN-hívás létesítésének időtúllépése | 115 |
| 1:1-hívások előléptetése egy csoportos hívás időkorlátja számára | 115 |


## <a name="api-stability-expectations"></a>API-stabilitási elvárások

> [!IMPORTANT]
> Ez a szakasz a REST API-k és az SDK-k **stabil** jelölését ismerteti. A előzetes kiadást, előzetes verziót vagy bétaverziót jelölt API-k **értesítés nélkül** módosíthatók vagy elavulttá válhatnak.

A jövőben előfordulhat, hogy kivonják a kommunikációs szolgáltatások SDK-jai verzióit, és a REST API-kon és a kiadott SDK-kon is bevezetjük a módosításokat. Az Azure kommunikációs szolgáltatásai *általában* két támogatási szabályzatot követnek a szolgáltatási verziók kivonásához:

- A kommunikációs szolgáltatások felületének változása miatt legalább három évig értesítést kap a kód módosításának szükségességéről. Az összes dokumentált REST API és SDK API általában legalább három éves figyelmeztetést biztosít a felületek leszerelése előtt.
- Legalább egy évig értesítést kap, mielőtt frissítenie kell az SDK-szerelvényeket a legújabb alverzióra. Ezeknek a szükséges frissítéseknek semmilyen programkód-módosításra sincs szükségük, mert ugyanazon a főverzión vannak. Ez különösen igaz a hívó és a csevegési kódtárak esetében, amelyek olyan valós idejű összetevőkkel rendelkeznek, amelyek gyakran igényelnek biztonsági és teljesítménybeli frissítéseket. Javasoljuk, hogy frissítse a kommunikációs szolgáltatások SDK-kat.

### <a name="api-and-sdk-decommissioning-examples"></a>Példák az API és az SDK leszerelésére

**Integrálta az SMS-REST API V24 verzióját az alkalmazásba. Az Azure kommunikációs kiadásai v25.**

Az API-k futtatása előtt 3 év figyelmeztetést kap, és a v25-ra való frissítésre kényszerülnek. A frissítéshez szükség lehet a kód módosítására.

**Integrálta a hívó SDK v 2.02 verzióját az alkalmazásba. Az Azure kommunikációs kiadásai: v 2.05.**

Előfordulhat, hogy frissítenie kell a hívó SDK v 2.05 verziójára a v 2.05 kiadásának 12 hónapon belül. Ez az összetevő egyszerű helyettesítése a kód módosítása nélkül, mert a v 2.05 a v2 főverzióban van, és nem változik a változások megszakítása.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse meg a következő SDK-áttekintéseket:

- [Az SDK meghívása – áttekintés](../concepts/voice-video-calling/calling-sdk-features.md)
- [A csevegési SDK áttekintése](../concepts/chat/sdk-features.md)
- [SMS SDK – áttekintés](../concepts/telephony-sms/sdk-features.md)

Ismerkedés az Azure kommunikációs szolgáltatásokkal:

- [Azure kommunikációs erőforrások létrehozása](../quickstarts/create-communication-resource.md)
- [Felhasználói hozzáférési tokenek](../quickstarts/access-tokens.md) előállítása
