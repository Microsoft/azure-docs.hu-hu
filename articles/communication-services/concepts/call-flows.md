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
ms.openlocfilehash: cc8e0edd1109162f0b426be31eb875ba8465d091
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490772"
---
# <a name="call-flow-basics"></a>Folyamat alapjai

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Az alábbi szakasz áttekintést nyújt az Azure kommunikációs szolgáltatások hívási folyamatairól. A jelek és a média folyamatai a felhasználók által kezdeményezett hívások típusától függenek. Ilyenek például az egy-az-egyhez típusú VoIP, az egy-az-egyhez PSTN, valamint a VoIP-és PSTN-kapcsolattal rendelkező résztvevők kombinációját tartalmazó csoportos hívások. Tekintse át a [hívások típusait](./voice-video-calling/about-call-types.md).

## <a name="about-signaling-and-media-protocols"></a>Tudnivalók a jelzésekről és a média protokollokról

Társ-társ vagy csoportos hívás létrehozásakor a rendszer két protokollt használ a színfalak mögött – HTTP (REST) az adathordozók jelzéséhez és SRTP.

Az ügyféloldali kódtárak vagy az ügyféloldali kódtárak és a kommunikációs szolgáltatások jelzői közötti jelzések a HTTP REST (TLS) protokollal kezelhetők. A Real-Time Media Traffic (RTP) esetében a User Datagram Protocol (UDP) használata javasolt. Ha a tűzfal megakadályozza az UDP használatát, az ügyféloldali kódtár a Transmission Control Protocol (TCP) adathordozót fogja használni.

Vizsgáljuk meg a jeleket és a média protokollokat különböző forgatókönyvekben.

## <a name="call-flow-cases"></a>Folyamat eseteinek hívása

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>1. eset: az a VoIP, amelyben két eszköz közötti közvetlen kapcsolat lehetséges

Az egy-az-egyhez típusú VoIP-vagy videohívások esetén a forgalom a legközvetlenebb útvonalat részesíti előnyben. A "közvetlen elérési út" azt jelenti, hogy ha két ügyfél-függvénytár közvetlenül elér egymással, közvetlen kapcsolatot létesít. Ez általában akkor fordulhat elő, ha két ügyféloldali függvénytár ugyanabban az alhálózatban van (például egy alhálózatban 192.168.1.0/24), vagy kettőt, ha az egyes alhálózatokban élő eszközök látják egymást (az 10.10.0.0/16 és a 192.168.1.0/24 alhálózatban található ügyféloldali kódtárak).

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="A felhasználók és a kommunikációs szolgáltatások közötti közvetlen VOIP-hívást bemutató ábra.":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>2. eset: az eszközök közötti közvetlen kapcsolat nem lehetséges, de a NAT-eszközök közötti kapcsolat lehetséges

Ha két eszköz olyan alhálózatokban található, amelyek nem tudnak megérkezni egymáshoz (például Alice működik egy kávézóból, és Bob működik a saját otthoni irodájában), de a NAT-eszközök közötti kapcsolat lehetséges, az ügyféloldali kódtár kapcsolatot létesít a NAT-eszközök használatával.

Alice számára a Coffee Shop NAT-je lesz, és Bob számára a hazai iroda NAT-je lesz. Alice eszköze elküldi a NAT külső címeit, és Bob is ugyanezt teszi. Az ügyfél-kódtárak megtudhatják a külső címeket egy olyan KÁBÍTó (munkamenet-bejárási segédprogramok a NAT-hoz) szolgáltatáshoz, amelyet az Azure kommunikációs szolgáltatásai ingyenesen biztosítanak. Az Alice és Bob közötti kézfogást kezelő logika beágyazva van az Azure kommunikációs szolgáltatásokban biztosított ügyféloldali kódtárak közé. (Nincs szükség további konfigurációra)

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="A KÁBÍTÁSi kapcsolatokat használó VOIP-hívást ábrázoló ábra.":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>3. eset: VoIP, ahol sem a közvetlen, sem a NAT-kapcsolatok nem lehetségesek

Ha egy vagy mindkét ügyfél egy szimmetrikus NAT mögött található, egy külön felhőalapú szolgáltatás szükséges az adathordozó továbbításához a két ügyfél kódtára között. Ezt a szolgáltatást kapcsolja be (a NAT-kapcsolaton keresztüli átjárással), és a kommunikációs szolgáltatások is biztosítanak. A kommunikációs szolgáltatások által meghívott ügyféloldali függvénytár automatikusan az észlelt hálózati feltételek alapján kapcsolja be a szolgáltatásokat. A Microsoft TURN Service használatát külön kell fizetni.

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

Ha az ügyféloldali kódtár nem tud az UDP protokollt használni a tűzfal korlátozásai miatt, a rendszer kísérletet tesz a Transmission Control Protocol (TCP) használatára. Vegye figyelembe, hogy a Media Processor összetevőhöz UDP szükséges, így ha ez történik, a kommunikációs szolgáltatások bekapcsolják a szolgáltatást, hogy a rendszer a TCP-t UDP-re fordítja. Ebben az esetben a díjak kiváltására akkor kerül sor, ha a funkciók kézi letiltására nem kerül sor.

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="A kommunikációs szolgáltatások TCP-feldolgozási folyamatát ábrázoló diagram.":::

### <a name="case-5-communication-services-client-library-and-microsoft-teams-in-a-scheduled-teams-meeting"></a>5. eset: a kommunikációs szolgáltatások ügyféloldali könyvtára és a Microsoft Teams egy ütemezett csapat ülésén

A riasztás a jelző vezérlőn keresztül zajlik. Az adathordozó a média processzorán keresztül áramlik. A jelző vezérlő és a média processzora a kommunikációs szolgáltatások és a Microsoft Teams között van megosztva.

:::image type="content" source="./media/call-flows/teams-communication-services-meeting.png" alt-text="A kommunikációs szolgáltatások ügyféloldali függvénytárát és a Teams-ügyfelet bemutató diagram egy ütemezett csapat ülésén.":::



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a hívással](../quickstarts/voice-video-calling/getting-started-with-calling.md)

A következő dokumentumok érdekesek lehetnek:

- További információ a [hívások típusairól](../concepts/voice-video-calling/about-call-types.md)
- További információ az [ügyfél-kiszolgáló architektúráról](./client-and-server-architecture.md)
- További információ a [Call flow-topológiák](./detailed-call-flows.md) használatáról
