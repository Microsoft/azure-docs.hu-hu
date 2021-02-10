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
ms.openlocfilehash: 894f8b17c3c5e9e3147b66854a5809bf82351fb9
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100012403"
---
# <a name="teams-interoperability"></a>Teams-együttműködés

[!INCLUDE [Private Preview Notice](../includes/private-preview-include.md)]

Az Azure kommunikációs szolgáltatásaival egyéni Meeting-élmények hozhatók létre, amelyek a Microsoft csapatait használják. A kommunikációs szolgáltatási megoldás (ok) felhasználóinak interakcióba léphetnek a csapat résztvevőinek a hang-, videó-és nyomtatómegosztást.

Ez az együttműködési képesség lehetővé teszi, hogy egyéni Azure-alkalmazásokat hozzon létre, amelyek összekapcsolják a felhasználókat a csapat ülésein. Az egyéni alkalmazások felhasználóinak nem kell Azure Active Directory identitásokkal vagy csapatokkal rendelkezniük a képesség megtapasztalása érdekében. Ez ideális megoldás arra, hogy az alkalmazottak (akik ismerik a csapatokat) és a külső felhasználók (egyéni alkalmazási élmény használatával) zökkenőmentes találkozási élményben legyenek. Így az alábbihoz hasonló élmények hozhatók létre:

1. Az alkalmazottak csapatok használatával ütemezhetnek egy értekezletet
2. Az egyéni kommunikációs szolgáltatások alkalmazás a Microsoft Graph API-kat használja az értekezlet részleteinek eléréséhez
3. Az értekezlet részletei az egyéni alkalmazáson keresztül vannak megosztva a külső felhasználókkal
4. A külső felhasználók az egyéni alkalmazással csatlakozhatnak a Teams Meeting szolgáltatáshoz (a kommunikációs szolgáltatások által meghívott ügyféloldali kódtár használatával)

Az ehhez a célra használt magas szintű architektúra a következőképpen néz ki: 

![Architektúra a csapatok együttműködéséhez](./media/call-flows/teams-interop.png)

Míg egyes csapatok olyan funkciókat tartanak fenn, mint a Kiemelt Hand, a közös üzemmód, és a breakout-szobák csak a csapatok felhasználói számára lesznek elérhetők, az egyéni alkalmazás hozzáférhet az értekezlet alapvető hang-, videó-és képernyő-megosztási képességeihez.

Amikor a kommunikációs szolgáltatások felhasználója összekapcsolja a csapatok értekezletét, a hívó ügyfél függvénytárában megadott megjelenítendő név jelenik meg a csapatok felhasználói számára. A kommunikációs szolgáltatások felhasználója más módon lesz kezelve, mint a Teams névtelen felhasználója. Az egyéni alkalmazásnak meg kell fontolnia a felhasználók hitelesítését és más biztonsági intézkedéseket a csapatok üléseinek védelme érdekében. Vegye figyelembe, hogy a névtelen felhasználók hogyan csatlakozhatnak értekezletekhez, és a [csapatok biztonsági útmutató](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) segítségével konfigurálhatják a névtelen felhasználók számára elérhető képességeket.

A kommunikációs szolgáltatások felhasználói csatlakozhatnak az ütemezett csapatok üléseihez, amíg a névtelen illesztések engedélyezve vannak az [értekezlet beállításaiban](/microsoftteams/meeting-settings-in-teams).

## <a name="teams-in-government-clouds-gcc"></a>Csapatok a kormányzati felhőkben (GCC)
Az Azure kommunikációs szolgáltatásokkal való együttműködés jelenleg nem engedélyezett [Microsoft 365 kormányzati felhők (GCC)](/MicrosoftTeams/plan-for-government-gcc) használatával történő központi telepítésre. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hívási alkalmazás csatlakoztatása Teams-értekezlethez](../quickstarts/voice-video-calling/get-started-teams-interop.md)