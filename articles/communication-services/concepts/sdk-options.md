---
title: SDK-k és REST API-k az Azure kommunikációs szolgáltatásokhoz
titleSuffix: An Azure Communication Services concept document
description: További információ az Azure Communication Services SDK-król és a REST API-król.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 92324d68eabfb1885a482a7f539140f93be77596
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605195"
---
# <a name="sdks-and-rest-apis"></a>SDK-k és REST API-k

Az Azure kommunikációs szolgáltatások funkcióit koncepcionálisan hat területre szervezték. A legtöbb terület teljes mértékben nyílt forráskódú SDK-kkal rendelkezik, amelyek olyan közzétett REST API-kkal vannak beprogramozva, amelyeket közvetlenül az interneten keresztül használhat. A hívó SDK saját hálózati adaptereket használ, és jelenleg zárt forrású. Az SDK-k mintáit és további technikai részleteit az [Azure kommunikációs szolgáltatások GitHub](https://github.com/Azure/communication)-tárházában tesszük közzé.

## <a name="rest-apis"></a>REST API-k
A kommunikációs szolgáltatások API-jai a [docs.microsoft.com](/rest/api/azure/)egyéb Azure REST API-jai mellett is dokumentálva vannak. A dokumentációból megtudhatja, hogyan strukturálhatja HTTP-üzeneteit, és útmutatást nyújt a Poster használatához. Ez a dokumentáció a [githubon](https://github.com/Azure/azure-rest-api-specs)is elérhető a hencegés formátumában.


## <a name="sdks"></a>SDK-k

| Szerelvény | Névterek| Protokollok | Képességek |
|------------------------|-------------------------------------|---------------------------------|--------------------------------------------------------------------------------------------|
| Azure Resource Manager | Azure. erőforráskezelő. Communication | [REST](https://docs.microsoft.com/rest/api/communication/communicationservice)| Kommunikációs szolgáltatások erőforrásainak kiépítése és kezelése|
| Közös | Azure. Communication. Common| REST | Alaptípusokat biztosít más SDK-k számára |
| Identitás | Azure. Communication. Identity| [REST](https://docs.microsoft.com/rest/api/communication/communicationidentity)| Felhasználók kezelése, hozzáférési tokenek|
| Telefonszámok _(bétaverzió)_| Azure. Communication. PhoneNumbers| [REST](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)| Telefonszámok beolvasása és kezelése |
| Csevegés | Azure. Communication. chat| [Pihenés](https://docs.microsoft.com/rest/api/communication/) saját jelzéssel | Valós idejű szöveg-alapú csevegés hozzáadása az alkalmazásokhoz |
| SMS| Azure. Communication. SMS | [REST](https://docs.microsoft.com/rest/api/communication/sms)| SMS-üzenetek küldése és fogadása|
| Hívó| Azure. Communication. Calling | Saját szállítás | Hang-, videó-, képernyő-megosztási és egyéb valós idejű adatkommunikációs képességek használata |

A Azure Resource Manager, identitás és SMS SDK-k a szolgáltatások integrálására összpontosítanak, és sok esetben biztonsági probléma merül fel, ha a funkciókat végfelhasználói alkalmazásokba integrálja. A gyakori és csevegési SDK-k alkalmasak a szolgáltatás-és ügyfélalkalmazások számára. A hívó SDK ügyfélalkalmazások számára készült. A szolgáltatási forgatókönyvekre összpontosító SDK fejlesztés alatt áll.


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


## <a name="rest-api-throttles"></a>REST API szabályozások
Bizonyos REST API-k és a hozzájuk tartozó SDK-módszerek szabályozási korlátokkal rendelkeznek, ezért szem előtt kell lennie. Ha túllépi a szabályozási korlátot, a rendszer  `429 - Too Many Requests` hibaüzenetet küld. Ezek a korlátok az [Azure-támogatásra vonatkozó kéréssel](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)növelhetők.

| API                                                                                                                          | Szabályozás            |
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [Az összes keresés a telefonszámon csomag API-k](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)         | 4 kérelem/nap      |
| [Telefonszám vásárlása](https://docs.microsoft.com/rest/api/communication/phonenumberadministration/purchasesearch) | 1 hónap vásárlása  |
| [SMS küldése](https://docs.microsoft.com/rest/api/communication/sms/send)                                                       | 200 kérelem/perc |


## <a name="sdk-platform-support-details"></a>SDK-platform támogatása – részletek

### <a name="ios-and-android"></a>iOS és Android 

- A kommunikációs szolgáltatások iOS SDK-k az iOS 13 + és a Xcode 11 + verzióját célozzák meg.
- Androidos Java SDK-k cél Android API Level 21 + és Android Studio 4.0 +

### <a name="net"></a>.NET 

A hívása kivételével a kommunikációs szolgáltatások a .NET Standard 2,0-es verzióit célozzák meg, amely az alább felsorolt platformokat támogatja.

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
> Ez a szakasz a REST API-k és az SDK-k **stabil** jelölését ismerteti. A előzetes kiadást, előzetes verziót vagy bétaverziót jelölt API-k **értesítés nélkül** módosíthatók vagy elavulttá válhatnak.

A jövőben előfordulhat, hogy kivonják a kommunikációs szolgáltatások SDK-jai verzióit, és a REST API-kon és a kiadott SDK-kon is bevezetjük a módosításokat. Az Azure kommunikációs szolgáltatásai *általában* két támogatási szabályzatot követnek a szolgáltatási verziók kivonásához:

- A kommunikációs szolgáltatások felületének változása miatt legalább három évig értesítést kap a kód módosításának szükségességéről. Az összes dokumentált REST API és SDK API általában legalább három éves figyelmeztetést biztosít a felületek leszerelése előtt.
- Legalább egy évig értesítést kap, mielőtt frissítenie kell az SDK-szerelvényeket a legújabb alverzióra. Ezeknek a szükséges frissítéseknek semmilyen programkód-módosításra sincs szükségük, mert ugyanazon a főverzión vannak. Ez különösen igaz a hívó és a csevegési kódtárak esetében, amelyek olyan valós idejű összetevőkkel rendelkeznek, amelyek gyakran igényelnek biztonsági és teljesítménybeli frissítéseket. Javasoljuk, hogy frissítse a kommunikációs szolgáltatások SDK-kat.

### <a name="api-and-sdk-decommissioning-examples"></a>Példák az API és az SDK leszerelésére

**Integrálta az SMS-REST API V24 verzióját az alkalmazásba. Az Azure kommunikációs kiadásai v25.**

Az API-k működése előtt három évig figyelmeztetést kap, és a v25-re való frissítésre kényszerül. A frissítéshez szükség lehet a kód módosítására.

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
