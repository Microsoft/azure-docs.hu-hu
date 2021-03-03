---
title: Együttműködési csoportokat tárgyaló csapatok
titleSuffix: An Azure Communication Services concept document
description: Csatlakozás a csapatok üléseihez
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 10/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d369e976ab1ba4e33f5eb21edb92054678f9040f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101655734"
---
# <a name="teams-interoperability"></a>Teams-együttműködés

> [!IMPORTANT]
> A [csapat bérlői együttműködésének](../concepts/teams-interop.md)engedélyezéséhez vagy letiltásához fejezze be [ezt az űrlapot](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Az Azure kommunikációs szolgáltatásaival egyéni Meeting-élmények hozhatók létre, amelyek a Microsoft csapatait használják. A kommunikációs szolgáltatási megoldás (ok) felhasználóinak interakcióba léphetnek a csapat résztvevőinek hang-, videó-, csevegési és nyomtatómegosztást.

A csapatok együttműködési képessége lehetővé teszi, hogy egyéni alkalmazásokat hozzon létre, amelyek összekapcsolják a felhasználókat a csapat ülésein. Az egyéni alkalmazások felhasználóinak nem kell Azure Active Directory identitásokkal vagy csapatokkal rendelkezniük a képesség megtapasztalása érdekében. Ez ideális megoldás arra, hogy az alkalmazottak (akik ismerik a csapatokat) és a külső felhasználók (egyéni alkalmazási élmény használatával) zökkenőmentes találkozási élményben legyenek. Például:

1. Az alkalmazottak csapatok használatával ütemezhetnek egy értekezletet 
1. Az értekezlet részletei az egyéni alkalmazáson keresztül vannak megosztva a külső felhasználókkal.
   * **Graph API használata** Az egyéni kommunikációs szolgáltatási alkalmazás a Microsoft Graph API-kat használja az értekezlet részleteinek megosztásához. 
   * **Egyéb beállítások használata** Az értekezletre mutató hivatkozás például átmásolható a naptárból a Microsoft Teams-ben.
1. A külső felhasználók az egyéni alkalmazással csatlakozhatnak a Teams Meeting szolgáltatáshoz (a kommunikációs szolgáltatások meghívásával és a csevegési ügyféloldali kódtárak használatával)

Az ehhez a célra használt magas szintű architektúra a következőképpen néz ki: 

![Architektúra a csapatok együttműködéséhez](./media/call-flows/teams-interop.png)

Noha bizonyos csapatok olyan funkciókat is biztosítanak, mint a Kiemelt Hand, a közös üzemmód, és a breakout-szobák csak a csapatok felhasználói számára lesznek elérhetők, az egyéni alkalmazás az alapvető hang-, videó-, csevegési és képernyőfelvételi képességekhez fog hozzáférni. A Meeting chat elérhetővé válik az egyéni alkalmazás felhasználója számára, amíg a hívása folyamatban van. Nem tudnak üzeneteket küldeni vagy fogadni a csatlakozás előtt vagy a hívás elhagyása után. 

Amikor a kommunikációs szolgáltatások felhasználója összekapcsolja a csapatok értekezletét, a hívó ügyfél függvénytárában megadott megjelenítendő név jelenik meg a csapatok felhasználói számára. A kommunikációs szolgáltatások felhasználója más módon lesz kezelve, mint a Teams névtelen felhasználója.  Az egyéni alkalmazásnak meg kell fontolnia a felhasználók hitelesítését és más biztonsági intézkedéseket a csapatok üléseinek védelme érdekében. Vegye figyelembe, hogy a névtelen felhasználók hogyan csatlakozhatnak értekezletekhez, és a [csapatok biztonsági útmutató](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) segítségével konfigurálhatják a névtelen felhasználók számára elérhető képességeket.

A kommunikációs szolgáltatások csapatának együttműködési szolgáltatása jelenleg privát előzetes verzióban érhető el. Ha általánosan elérhető, a kommunikációs szolgáltatások felhasználói a "külső hozzáférésű felhasználók"-ként lesznek kezelve. További információ a külső hozzáférésről a [szervezeten kívüli személyekkel a Microsoft Teams szolgáltatásban, csevegéssel és együttműködéssel](/microsoftteams/communicate-with-users-from-other-organizations).

A kommunikációs szolgáltatások felhasználói csatlakozhatnak az ütemezett csapatok üléseihez, amíg a névtelen illesztések engedélyezve vannak az [értekezlet beállításaiban](/microsoftteams/meeting-settings-in-teams).

## <a name="teams-in-government-clouds-gcc"></a>Csapatok a kormányzati felhőkben (GCC)
Az Azure kommunikációs szolgáltatásokkal való együttműködés jelenleg nem kompatibilis a [Microsoft 365 Government-Felhőkkel (GCC)](/MicrosoftTeams/plan-for-government-gcc) használó csapatok üzembe helyezésével. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hívási alkalmazás csatlakoztatása Teams-értekezlethez](../quickstarts/voice-video-calling/get-started-teams-interop.md)