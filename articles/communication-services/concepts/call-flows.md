---
title: Folyamatok meghívása az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg az Azure kommunikációs szolgáltatások hívási folyamatait.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8f7bfd63d858fb409286268c318c9f66474e3d53
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110916"
---
# <a name="call-flow-basics"></a>Folyamat alapjai

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Az alábbi szakasz áttekintést nyújt az Azure kommunikációs szolgáltatások hívási folyamatairól. A jelek és a média folyamatai a felhasználók által kezdeményezett hívások típusától függenek. Ilyenek például az egy-az-egyhez típusú VoIP, az egy-az-egyhez PSTN, valamint a VoIP-és PSTN-kapcsolattal rendelkező résztvevők kombinációját tartalmazó csoportos hívások. Tekintse át a [hívások típusait](./voice-video-calling/about-call-types.md).

## <a name="about-signaling-and-media-protocols"></a>Tudnivalók a jelzésekről és a média protokollokról

Társ-társ vagy csoportos hívás létrehozásakor a rendszer két protokollt használ a színfalak mögött – HTTP (REST) az adathordozók jelzéséhez és SRTP.

Az SDK-k vagy az SDK-k, valamint a kommunikációs szolgáltatások jelzői közötti jelzések a HTTP REST (TLS) protokollal kezelhetők. A Real-Time Media Traffic (RTP) esetében a User Datagram Protocol (UDP) használata javasolt. Ha a tűzfal megakadályozza az UDP használatát, az SDK a Transmission Control Protocol (TCP) adathordozót fogja használni.

Vizsgáljuk meg a jeleket és a média protokollokat különböző forgatókönyvekben.

## <a name="call-flow-cases"></a>Folyamat eseteinek hívása

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>1. eset: az a VoIP, amelyben két eszköz közötti közvetlen kapcsolat lehetséges

Az egy-az-egyhez típusú VoIP-vagy videohívások esetén a forgalom a legközvetlenebb útvonalat részesíti előnyben. A "közvetlen elérési út" azt jelenti, hogy ha két SDK közvetlenül is elérhet egymással, közvetlen kapcsolatot létesít. Ez általában akkor fordulhat elő, ha két SDK ugyanabban az alhálózatban található (például egy alhálózatban 192.168.1.0/24), vagy kettőt, ha az egyes alhálózatokban élő eszközök képesek egymásnak megtekinteni (az 10.10.0.0/16 és a 192.168.1.0/24 alhálózatban található SDK-k is elérhetők egymástól).

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="A felhasználók és a kommunikációs szolgáltatások közötti közvetlen VOIP-hívást bemutató ábra.":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>2. eset: az eszközök közötti közvetlen kapcsolat nem lehetséges, de a NAT-eszközök közötti kapcsolat lehetséges

Ha két eszköz olyan alhálózatokban található, amelyek nem tudnak elérni egymáshoz (például Alice működik egy kávézóból, és Bob működik a saját otthoni irodájában), de a NAT-eszközök közötti kapcsolat lehetséges, akkor az ügyféloldali SDK-k NAT-eszközökön keresztül csatlakoznak a kapcsolathoz.

Alice számára a Coffee Shop NAT-je lesz, és Bob számára a hazai iroda NAT-je lesz. Alice eszköze elküldi a NAT külső címeit, és Bob is ugyanezt teszi. Az SDK-k az Azure kommunikációs szolgáltatások által ingyenesen elérhető, elkábító (munkamenet-bejárási segédprogramok: NAT) szolgáltatás külső címeit tanulják meg. Az Alice és Bob közötti kézfogást kezelő logika beágyazva van az Azure kommunikációs szolgáltatások által biztosított SDK-k közé. (Nincs szükség további konfigurációra)

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="A KÁBÍTÁSi kapcsolatokat használó VOIP-hívást ábrázoló ábra.":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>3. eset: VoIP, ahol sem a közvetlen, sem a NAT-kapcsolatok nem lehetségesek

Ha az ügyféleszközök egyike szimmetrikus NAT mögött van, egy különálló felhőalapú szolgáltatásra van szükség, amely továbbítja az adathordozót a két SDK között. Ezt a szolgáltatást kapcsolja be (a NAT-kapcsolaton keresztüli átjárással), és a kommunikációs szolgáltatások is biztosítanak. A kommunikációs szolgáltatások által meghívott SDK automatikusan az észlelt hálózati feltételek alapján kapcsolja be a szolgáltatásokat. A Microsoft TURN Service használatát külön kell fizetni.

:::image type="content" source="./media/call-flows/about-voice-case-3.png" alt-text="A KAPCSOLÁSi kapcsolatokat használó VOIP-hívást ábrázoló ábra.":::

### <a name="case-4-group-calls-with-pstn"></a>4. eset: csoportos hívások a PSTN-sel

A PSTN-hívások és az adathordozók egyaránt az Azure kommunikációs szolgáltatások telefonos erőforrását használják. Ez az erőforrás más szolgáltatókhoz kapcsolódik.

A PSTN adathordozó-forgalma a Media Processor nevű összetevőn keresztül áramlik.

:::image type="content" source="./media/call-flows/about-voice-pstn.png" alt-text="A kommunikációs szolgáltatásokkal rendelkező PSTN-csoportok hívását bemutató ábra.":::

> [!NOTE]
> A média-feldolgozással jól ismertek, a média processzora szintén vissza van állítva a felhasználói ügynöknek, az [RFC 3261 SIP: munkamenet-kezdeményező protokollban](https://tools.ietf.org/html/rfc3261)definiált módon, ami azt jelenti, hogy a Microsoft és a Carrier hálózatok közötti hívások kezelése során kodekeket fordíthat. Az Azure kommunikációs szolgáltatások jelző vezérlője a Microsoft által a SIP-proxyk ugyanazon RFC-ben történő megvalósítása.

A csoportos hívások, a média és a jelzések mindig az Azure kommunikációs szolgáltatások hátterén keresztül áramlanak. Az összes résztvevő hang-és/vagy videója a Media Processor összetevőben van keverve. A csoportos hívás összes tagja elküldi a hang-és/vagy a videó streameket a média-processzorba, amely vegyes adatfolyamokat ad vissza.

A csoportos hívások alapértelmezett valós idejű protokollja a User Datagram Protocol (UDP).

> [!NOTE]
> A Media Processor többpontos vezérlő egységként (MCU) vagy szelektív továbbítási egységként (SFU) működhet

:::image type="content" source="./media/call-flows/about-voice-group-calls.png" alt-text="A kommunikációs szolgáltatásokon belüli UDP-adatfolyamok folyamatát ábrázoló diagram.":::

Ha az SDK tűzfal korlátozásai miatt nem tud az UDP protokollt használni az adathordozón, a rendszer kísérletet tesz a Transmission Control Protocol (TCP) használatára. Vegye figyelembe, hogy a Media Processor összetevőhöz UDP szükséges, így ha ez történik, a kommunikációs szolgáltatások bekapcsolják a szolgáltatást, hogy a rendszer a TCP-t UDP-re fordítja. Ebben az esetben a díjak kiváltására akkor kerül sor, ha a funkciók kézi letiltására nem kerül sor.

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="A kommunikációs szolgáltatások TCP-feldolgozási folyamatát ábrázoló diagram.":::

### <a name="case-5-communication-services-sdk-and-microsoft-teams-in-a-scheduled-teams-meeting"></a>5. eset: a kommunikációs Services SDK és a Microsoft Teams egy ütemezett csapat ülésén

A riasztás a jelző vezérlőn keresztül zajlik. Az adathordozó a média processzorán keresztül áramlik. A jelző vezérlő és a média processzora a kommunikációs szolgáltatások és a Microsoft Teams között van megosztva.

:::image type="content" source="./media/call-flows/teams-communication-services-meeting.png" alt-text="Diagram, amely a kommunikációs szolgáltatások SDK-t és a Teams-ügyfelet mutatja egy ütemezett csapat ülésén.":::



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a hívással](../quickstarts/voice-video-calling/getting-started-with-calling.md)

A következő dokumentumok érdekesek lehetnek:

- További információ a [hívások típusairól](../concepts/voice-video-calling/about-call-types.md)
- További információ az [ügyfél-kiszolgáló architektúráról](./client-and-server-architecture.md)
- További információ a [Call flow-topológiák](./detailed-call-flows.md) használatáról
