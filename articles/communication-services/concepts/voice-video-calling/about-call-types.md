---
title: Hang-és video-fogalmak az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg a kommunikációs szolgáltatások hívási típusait.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 00aa24f0ff8c918c645239ca36888c6a214062c8
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028760"
---
# <a name="voice-and-video-concepts"></a>A hang- és videóhívással kapcsolatos alapfogalmak

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Az Azure kommunikációs szolgáltatásaival egy vagy több hang-és videohívásokat indíthat és fogadhat. A hívások más internethez csatlakoztatott eszközökre és a régi telefonokra is felhasználhatók. A kommunikációs szolgáltatások JavaScript-, Android-vagy iOS-alapú ügyféloldali kódtárai segítségével olyan alkalmazásokat hozhat létre, amelyek lehetővé teszik a felhasználók számára, hogy privát beszélgetésekben vagy csoportos megbeszéléseken beszéljenek egymással. Az Azure kommunikációs szolgáltatások támogatják a szolgáltatásokból vagy a robotoktól érkező hívásokat.

## <a name="call-types-in-azure-communication-services"></a>Hívások típusai az Azure kommunikációs szolgáltatásokban

Az Azure kommunikációs szolgáltatásokban többféle típusú hívást végezhet. A hívások típusa határozza meg a jelző sémát, a média forgalmát és a díjszabási modellt.

### <a name="voice-over-ip-voip"></a>Voice over IP (VoIP) 

Ha az alkalmazás egy felhasználója egy internetes vagy adatkapcsolaton keresztül hívja meg az alkalmazás egy másik felhasználóját, a rendszer a hívást a Voice over IP (VoIP) protokollon keresztül kezdeményezi. Ebben az esetben a jelek és az adathordozók is az interneten keresztül jelennek meg.

### <a name="public-switched-telephone-network-pstn"></a>Nyilvános kapcsolású telefonos hálózat (PSTN)

Minden alkalommal, amikor a felhasználók hagyományos telefonszámmal működnek, a hívásokat a PSTN (nyilvános kapcsolású telefonos hálózat) hanghívása könnyíti meg. PSTN-hívások létrehozásához és fogadásához a telefonos funkciókat kell hozzáadnia az Azure kommunikációs szolgáltatások erőforrásaihoz. Ebben az esetben a jelzés és a média az IP-alapú és a PSTN-alapú technológiák kombinációját használja a felhasználók összekapcsolásához.

### <a name="one-to-one-call"></a>Egy-az-egyhez hívás

Az Azure kommunikációs szolgáltatások egy az egyhez típusú hívása akkor történik meg, amikor az egyik felhasználó egy másik felhasználóhoz csatlakozik az egyik ügyfél-kódtár használatával. A hívás akár VoIP vagy PSTN is lehet.

### <a name="group-call"></a>Csoportos hívás

Az Azure kommunikációs szolgáltatások csoportos hívása akkor történik, amikor három vagy több résztvevő csatlakozik egymáshoz. A VoIP-és PSTN-kapcsolattal rendelkező felhasználók bármely kombinációja szerepelhet egy csoportos híváson. Az egy-az-egyhez típusú hívások a híváshoz több résztvevő hozzáadásával válthatnak a csoportos hívásba. Az egyik résztvevő lehet egy robot.

### <a name="supported-video-standards"></a>Támogatott videós szabványok
Támogatjuk a H. 264 (MPEG-4) 

### <a name="video-quality"></a>Videó minősége
A natív (iOS, Android) SDK-k teljes HD 1080p verziójának támogatása. A web (JS) SDK esetében a standard HD 720p szabványt támogatjuk. A minőség a rendelkezésre álló sávszélességtől függ.  

### <a name="rooms-concept"></a>A szobák fogalma
A szobák olyan API-k és SDK-k összessége, amelyek segítségével könnyedén hozzáadhat hang-, videó-, képernyőfelvétel-, PSTN-és SMS-interakciókat webhelyéhez vagy natív alkalmazásához.
Az előzetes verzió ideje alatt a csoport azonosítója segítségével csatlakozhat ugyanahhoz a beszélgetéshez. Tetszőleges számú csoport-azonosítót hozhat létre, és elkülönítheti a felhasználókat a "szobák" használatával. A továbblépés további vezérlőket mutat be a "szobák" körül 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a hívással](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

További információért tekintse át a következő cikkeket:
- Ismerkedjen meg az általános [hívási folyamatokkal](../call-flows.md)
- [A PSTN-megoldás megtervezése](../telephony-sms/plan-solution.md)
- További információ az [ügyféloldali kódtár funkcióinak meghívásáról](../voice-video-calling/calling-sdk-features.md)
