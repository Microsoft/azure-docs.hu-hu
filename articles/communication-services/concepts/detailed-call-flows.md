---
title: Flow-topológiák meghívása az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg az Azure kommunikációs szolgáltatások hívási folyamatának topológiáit.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 12/11/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5ffc655cebb19b755b990171ab662576ac541793
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174291"
---
# <a name="call-flow-topologies"></a>Folyamat-topológiák hívása
Ez a cikk az Azure kommunikációs szolgáltatások hívási folyamat topológiáit ismerteti. Ez egy nagyszerű cikk, amelyből megtudhatja, hogy a vállalati ügyfelek hogyan integrálják a kommunikációs szolgáltatásokat a felügyelt hálózaton belül. A kommunikációs szolgáltatások hívási folyamatainak bevezetéséhez tekintse meg a [folyamatok fogalmi dokumentációját](./call-flows.md).

## <a name="background"></a>Háttér

### <a name="network-concepts"></a>Hálózati fogalmak

A hívási folyamat topológiáinak áttekintése előtt definiálunk néhány, a dokumentumban szereplő kifejezést.

Az **ügyfél hálózata** minden Ön által kezelt hálózati szegmenst tartalmaz. Ilyen lehet például a vezetékes és vezeték nélküli hálózatok az irodában, illetve az irodák, az adatközpontok és az internetszolgáltatók között.

Az ügyfél hálózata általában számos olyan hálózati területet tartalmaz, amelyeken tűzfalak és/vagy proxykiszolgálók vannak érvényben, amelyek a szervezet biztonsági házirendjeit érvényesítik. Javasoljuk, hogy [átfogó hálózati értékelést](/microsoftteams/3-envision-evaluate-my-environment) végezzen a kommunikációs megoldás optimális teljesítményének és minőségének biztosítása érdekében.

A **kommunikációs szolgáltatások hálózata** az Azure kommunikációs szolgáltatásokat támogató hálózati szegmens. Ezt a hálózatot a Microsoft felügyeli, és a világ minden pontján a legtöbb ügyfél hálózatához hasonló élek vannak elosztva. Ez a hálózat felelős a továbbítási, a csoportos hívások adathordozó-feldolgozásának, valamint a gazdag valós idejű média-kommunikációt támogató egyéb összetevőkért.

### <a name="types-of-traffic"></a>Forgalmi típusok

A kommunikációs szolgáltatások elsősorban két típusú forgalomra épülnek: **valós idejű adathordozók** és **jelzések**.

A valós **idejű adathordozók** továbbítása a valós idejű átviteli protokoll (RTP) használatával történik. Ez a protokoll támogatja a hang-, videó-és képernyő-megosztási adatátvitelt. Ezek az adatok érzékenyek a hálózati késéssel kapcsolatos problémákra. Habár lehetséges a valós idejű adathordozók továbbítása TCP vagy HTTP protokollon keresztül, javasoljuk, hogy a nagy teljesítményű végfelhasználói élmény támogatásához használja az UDP-t átviteli rétegként szolgáló protokollként. Az RTP-en keresztül továbbított adathordozó-adattartalom biztonságos a SRTP használatával.

A kommunikációs szolgáltatási megoldás felhasználói a saját eszközein keresztül csatlakoznak a szolgáltatásokhoz. Az eszközök és a kiszolgálók közötti kommunikációt a rendszer a **jelzéssel** kezeli. Például: a Hívás kezdeményezése és a valós idejű csevegés az eszközök és a szolgáltatás közötti jelzéssel támogatott. A legtöbb jelzési forgalom HTTPS-REST-t használ, bár bizonyos helyzetekben a SIP a megjeles forgalmi protokollként is használható. Habár az ilyen típusú forgalom kevésbé érzékeny a késésre, az alacsony késésű jelzések a megoldás felhasználóinak kellemes felhasználói élményt nyújtanak.

### <a name="interoperability-restrictions"></a>Együttműködési korlátozások

A kommunikációs szolgáltatásokon keresztül áramló média a következőképpen korlátozódik:

**Harmadik féltől származó adathordozó-továbbítók.** A harmadik féltől származó adathordozó-továbbítókkal való együttműködés nem támogatott. Ha az egyik adathordozó-végpont a kommunikációs szolgáltatás, akkor az adathordozó csak a Microsoft-natív média-továbbítókat továbbítja, beleértve a Microsoft Teams és a Skype vállalati verziókat támogatókat is.

Egy külső gyártótól származó munkamenet-vezérlő (SBC) a PSTN-sel való határán le kell fejeznie az RTP/RTCP streamet, amely a SRTP használatával védett, és nem továbbítja a következő ugráshoz. Ha a folyamatot a következő ugrásra továbbítja, előfordulhat, hogy nem értelmezhető.

**Harmadik féltől származó SIP-proxykiszolgálók.** Egy olyan kommunikációs szolgáltatás, amely egy harmadik féltől származó SBC-vel és/vagy átjáróval rendelkező SIP-párbeszédpanelt jelez, a Microsoft natív SIP-proxyn (akárcsak a csapatoknál) haladhat át. A harmadik féltől származó SIP-proxykkal való együttműködés nem támogatott.

**Harmadik féltől származó B2BUA (vagy SBC).** A kommunikációs szolgáltatások a PSTN-be irányuló és onnan érkező adatfolyamait egy harmadik féltől származó SBC szakítja meg. Együttműködés harmadik féltől származó SBC-vel a kommunikációs szolgáltatások hálózatán belül (ha egy harmadik féltől származó SBC két kommunikációs szolgáltatási végpontot közvetít) nem támogatott.

### <a name="unsupported-technologies"></a>Nem támogatott technológiák

**VPN-hálózat.** A kommunikációs szolgáltatások nem támogatják a virtuális magánhálózatok átvitelét. Ha a felhasználók VPN-ügyfeleket használnak, az ügyfélnek meg kell osztania és továbbítania kell a média forgalmát egy nem VPN-kapcsolaton keresztül, ahogy [azt a Lync-adathordozónak a VPN-alagút megkerülésének engedélyezése](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210) című rész adja

*Megjegyzés. Bár a cím a Lynct jelzi, az Azure kommunikációs szolgáltatásokra és csapatokra is érvényes.*

**Csomagok formálói.** A csomagok képmetszője, a csomagok vizsgálata vagy a csomagszűrő eszközök nem támogatottak, és jelentősen csökkenthetik a minőséget.

### <a name="call-flow-principles"></a>Folyamatok alapelveinek meghívása

Négy általános alapelv létezik a kommunikációs szolgáltatások hívási folyamatainak alátámasztására:

* A **kommunikációs szolgáltatások csoportos hívásának első résztvevője határozza meg azt a régiót, amelyben a hívást futtatták**. Bizonyos topológiákban kivételek vannak a szabály alól, amelyek az alábbiakban olvashatók.
* **A kommunikációs szolgáltatások hívásának támogatásához használt adathordozó-végpontot a rendszer a média feldolgozási igényei alapján választja ki**, és nem érinti a hívások résztvevői számára. A pont-pont típusú hívás például használhat egy Felhőbeli adathordozó-végpontot, amely átíráshoz vagy rögzítéshez is feldolgozza az adathordozókat, míg a két résztvevővel való hívás nem használ adathordozó-végpontokat. A csoportos hívások az adathordozó-végpontot fogják használni a keveréshez és az útválasztáshoz. Ez a végpont a hívást futtató régió alapján van kiválasztva. Az ügyfél és az adathordozó-végpont között továbbított adathordozó-forgalom közvetlenül irányítható, vagy az Azure-ban átviteli továbbítást is használhat, ha az ügyfél hálózati tűzfalának korlátozásai igénylik.
* A **társközi hívások média-forgalma az elérhető legközvetlenebb útvonalat veszi igénybe**, feltételezve, hogy a hívás nem igényel adathordozó-végpontot a felhőben. Az előnyben részesített útvonal közvetlenül a távoli társ (ügyfél) számára érhető el. Ha a közvetlen útvonal nem érhető el, egy vagy több átviteli továbbító továbbítja a forgalmat. A média forgalmának nem kell keresztirányú kiszolgálónak lennie, mint a csomagszűrés, a VPN-kiszolgálók vagy más olyan függvények, amelyek késleltetik a feldolgozást és csökkentik a végfelhasználói élményt.
* **A forgalom jelzése mindig a felhasználóhoz legközelebb eső kiszolgálóra kerül.**

Ha többet szeretne megtudni a kiválasztott adathordozó-útvonal részleteiről, tekintse meg a [folyamatok fogalmi dokumentációját](./call-flows.md).


## <a name="call-flows-in-various-topologies"></a>Folyamatok meghívása különböző topológiákban

### <a name="communication-services-internet"></a>Kommunikációs szolgáltatások (Internet)

Ezt a topológiát olyan ügyfelek használják, akik a felhőből származó kommunikációs szolgáltatásokat használják a helyszíni telepítések, például a SIP-interfész nélkül. Ebben a topológiában a kommunikációs szolgáltatások felé irányuló és onnan érkező forgalom az interneten keresztül folyik.

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Azure kommunikációs szolgáltatások topológiája.":::

*1. ábra – kommunikációs szolgáltatások topológiája*

A fenti ábrán látható nyilak iránya tükrözi a kommunikáció kezdeményezési irányát, amely hatással van a vállalati körzetek kapcsolatára. Az UDP for Media esetében az első csomag (ok) fordított irányt eredményezhet, de előfordulhat, hogy ezek a csomagok egészen addig le lesznek tiltva, amíg a csomagok nem áramlanak.

Folyamat leírása:
* 2. folyamat – a felhasználó kommunikációs szolgáltatásainak felhasználói felületének részeként egy, az ügyfél hálózatán keresztül kezdeményezett folyamatot jelöl. Ilyen folyamat például a DNS és a társ-társ adathordozó átvitele.
* 2. folyamat – a távoli mobil kommunikációs szolgáltatások felhasználója által kezdeményezett folyamatot jelöli, VPN-kapcsolattal az ügyfél-hálózattal.
* 3. folyamat – a távoli mobil kommunikációs szolgáltatások felhasználója által kezdeményezett folyamatot jelenti a kommunikációs szolgáltatások végpontjai számára.
* 4. folyamat – a kommunikációs szolgáltatásoknak a felhasználó által kezdeményezett folyamatát jelöli.
* 5. folyamat – egy társközi adatfolyamot képvisel egy kommunikációs szolgáltatás felhasználója és egy másik között az ügyfél hálózaton belül.
* 6. folyamat – egy társközi adatfolyamot képvisel a távoli mobil kommunikációs szolgáltatások felhasználója és egy másik, az interneten keresztül futó távoli mobil kommunikációs szolgáltatás felhasználója között.

### <a name="use-case-one-to-one"></a>Használati eset: egy-az-egyhez

Az egy-az-egyhez típusú hívások olyan közös modellt használnak, amelyben a hívó az IP-címek/portok, beleértve a helyi, a továbbítási és a reflexív (az ügyfél nyilvános IP-címét, a továbbító által látható) álló készletét fogja beszerezni. A hívó elküldi ezeket a jelölteket a hívott félnek; a hívott fél hasonló jelölteket szerez be, és elküldi őket a hívónak. Az elkábított kapcsolat-ellenőrzési üzenetek segítségével megtalálhatja, hogy melyik hívó vagy más néven használt média-elérési utak működnek, és a legjobb munkaútvonal van kiválasztva. Az adathordozót (azaz az SRTP használatával védett RTP/RTCP-csomagokat) a kiválasztott pályázó pár használatával küldi el a rendszer. Az átviteli továbbító üzembe helyezése az Azure kommunikációs szolgáltatások részeként történik.

Ha a helyi IP-cím/port vagy a visszaható jelöltek kapcsolattal rendelkeznek, akkor az ügyfelek közötti közvetlen útvonal lesz kiválasztva a média számára. Ha az ügyfelek mind az ügyfél hálózaton vannak, akkor a közvetlen elérési utat kell kiválasztani. Ehhez közvetlen UDP-kapcsolat szükséges az ügyfél hálózaton belül. Ha az ügyfelek nomád Felhőbeli felhasználók, a NAT/tűzfaltól függően a média közvetlen kapcsolatot is használhat.

Ha az ügyfél belső hálózaton van, és egy ügyfél külső (például egy mobil felhőalapú felhasználó), akkor nem valószínű, hogy a helyi vagy a reflexív jelöltek közötti közvetlen kapcsolat működik. Ebben az esetben lehetőség van arra, hogy az egyik ügyfél (például a belső ügyfél továbbítson egy továbbító jelöltet az Azure-beli átviteli továbbítótól, a külső ügyfélnek képesnek kell lennie elkábító/RTP/RTCP-csomagok küldésére az átviteli továbbító felé). Egy másik lehetőség a belső ügyfél, amelyet a Mobile Cloud Client által kapott továbbító jelöltnek küld. Bár az adathordozók UDP-kapcsolata kifejezetten ajánlott, a TCP protokoll támogatott.

**Magas szintű lépések:**
1.  A kommunikációs szolgáltatások felhasználója A 2. flow használatával oldja fel az URL-tartománynevet (DNS).
2.  Az A felhasználó egy Media relay-portot foglal le a Teams Transport relayen a flow 4 használatával.
3.  A kommunikációs szolgáltatások "A" felhasználó A 4. folyamattal a kommunikációs szolgáltatások felé irányuló "meghívó" üzenetet küld.
4.  A kommunikációs szolgáltatások értesíti a B felhasználót a flow 4 használatával.
5.  A B felhasználó egy Media relay-portot oszt ki a Teams Transport relayen a flow 4 használatával.
6.  A "B" felhasználó a "válasz" kifejezést a 4. folyamattal rendelkező ICE-jelöltek használatával küldi el, amelyet a rendszer A flow 4 használatával továbbít vissza a felhasználónak.
7.  Az A felhasználó és a B felhasználó meghívja a jég kapcsolati teszteket, és kijelöli a legjobb elérhető adathordozó-útvonalat (lásd az alábbi diagramokat a különböző használati esetekben).
8.  Mindkét felhasználó telemetria küld a kommunikációs szolgáltatásoknak a 4. folyamat használatával.

### <a name="customer-network-intranet"></a>Ügyfél hálózata (intranet)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="Az ügyfél hálózatán belüli forgalom.":::

*2. ábra – az ügyfél hálózatán belül*

A 7. lépésben a társközi Media Flow 5 van kiválasztva.

Ez a médiaadatfolyam-továbbítás kétirányú. A flow 5 iránya azt jelzi, hogy az egyik oldal kezdeményezi a kommunikációt egy csatlakozási perspektívából. Ebben az esetben nem számít, hogy melyik irányt használja a rendszer, mert mindkét végpont az ügyfél hálózatán belül van.

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>Ügyfél hálózata külső felhasználó felé (a média továbbítása a Team Transport Relay használatával)

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="Egy-egy hívási folyamat egy továbbítón keresztül.":::

*3. ábra – a külső felhasználó felé irányuló ügyfél-hálózat (az Azure Transport Relay által továbbított média)*

A 7. lépésben a 4. lépés (az ügyfél hálózatról a kommunikációs szolgáltatásokba) és a flow 3 (a távoli mobil kommunikációs szolgáltatások felhasználója és az Azure kommunikációs szolgáltatások) beállítás van kiválasztva.

Ezeket a folyamatokat a Teams Transport Relay továbbítja az Azure-on belül.

Ez a médiaadatfolyam-továbbítás kétirányú. Az irány jelzi, hogy melyik oldal kezdeményezi a kommunikációt egy csatlakozási perspektívából. Ebben az esetben ezek a folyamatok a jeleket és az adathordozókat használják, különböző átviteli protokollok és címek használatával.

### <a name="customer-network-to-external-user-direct-media"></a>Ügyfél hálózata külső felhasználó felé (közvetlen adathordozó)

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="Egy külső felhasználót használó hívási folyamat.":::

*4. ábra – a külső felhasználó felé irányuló ügyfél hálózata (közvetlen adathordozó)*

A 7. lépésben a 2. folyamat (az ügyfél hálózatról az ügyfél az interneten keresztül) beállítás van kiválasztva.

A távoli mobil felhasználóval (az Azure-on keresztül nem továbbított) közvetlen adathordozó nem kötelező. Más szóval letilthatja az elérési utat, hogy az Azure-beli átviteli továbbítón keresztül kényszerítse ki a média elérési útját.

Ez a médiaadatfolyam-továbbítás kétirányú. A 2. folyamat iránya a távoli mobil felhasználó számára azt jelzi, hogy az egyik oldal egy kapcsolati perspektívából kezdeményezi a kommunikációt.

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>VPN-felhasználó és belső felhasználó (a média továbbítása a Team Transport Relay használatával)

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="Az egyik a VPN-felhasználó felé irányuló hívási folyamat továbbításon keresztül.":::

*5. ábra – VPN-felhasználó belső felhasználó számára (Azure Relay által továbbított média*

A VPN és az ügyfél közötti adatforgalom a 2. folyamatot használja. Az ügyfél hálózata és az Azure közötti jel a 4. folyamatot használja. Azonban a média megkerüli a VPN-t, és a 3. és 4. folyamattal az Azure Media Relay használatával irányítja át.

### <a name="vpn-user-to-internal-user-direct-media"></a>VPN-felhasználó és belső felhasználó (közvetlen adathordozó)

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="Egy, egy-egy hívási folyamat (belső felhasználó) VPN-sel közvetlen adathordozóval":::

*6. ábra – VPN-felhasználó és belső felhasználó (közvetlen adathordozó)*

A VPN és az ügyfél közötti adatforgalom a 2. flow-t használja. Az ügyfél hálózata és az Azure közötti jelek a flow 4-et használják. A média azonban megkerüli a VPN-t, és a 2. flow használatával továbbítja az internetre.

Ez a médiaadatfolyam-továbbítás kétirányú. A 2. folyamat iránya a távoli mobil felhasználó számára azt jelzi, hogy az egyik oldal egy kapcsolati perspektívából kezdeményezi a kommunikációt.

### <a name="vpn-user-to-external-user-direct-media"></a>VPN-felhasználó és külső felhasználó (közvetlen adathordozó)

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="Egy-egy hívási folyamat (külső felhasználó) VPN-sel közvetlen adathordozóval":::

*7. ábra – VPN-felhasználó és külső felhasználó (közvetlen adathordozó)*

A VPN-felhasználó és az ügyfél közötti adatforgalom a 2. és 4. adatfolyamot használja az Azure-ba. Azonban a média megkerüli a VPN-t, és a flow 6 használatával irányítja át.

Ez a médiaadatfolyam-továbbítás kétirányú. A 6. irányú flow iránya a távoli mobil felhasználó számára azt jelzi, hogy az egyik oldal egy kapcsolati perspektívából kezdeményezi a kommunikációt.

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>Használati eset: kommunikációs szolgáltatások ügyfele a PSTN-hez a kommunikációs szolgáltatások törzsén keresztül

A kommunikációs szolgáltatások lehetővé teszik hívások küldését és fogadását a nyilvános kapcsolós telefonos hálózatból (PSTN). Ha a PSTN-törzs a kommunikációs szolgáltatások által biztosított telefonszámok használatával csatlakozik, a használati esethez nem tartoznak speciális kapcsolódási követelmények. Ha saját helyszíni PSTN-törzsét szeretné összekapcsolni az Azure kommunikációs szolgáltatásokkal, használhat SIP felületet (a CY2021-ben érhető el).

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="Egy, a PSTN-résztvevővel egy hívást":::

*8. ábra – a kommunikációs szolgáltatások felhasználója a PSTN-en keresztül az Azure Trunk használatával*

Ebben az esetben az ügyfél és az Azure közötti adatforgalom a 4. flow-t használja.

### <a name="use-case-communication-services-group-calls"></a>Használati eset: kommunikációs szolgáltatások csoportos hívása

Az audio/video/Screen Sharing (VBSS) szolgáltatás az Azure kommunikációs szolgáltatások részét képezi. Olyan nyilvános IP-címmel rendelkezik, amelynek elérhetőnek kell lennie az ügyfél hálózatáról, és elérhetőnek kell lennie egy nomád Felhőbeli ügyféltől. Minden ügyfélnek/végpontnak csatlakoznia kell a szolgáltatáshoz.

A belső ügyfelek az egy-az-egyhez hívásokhoz hasonló módon kapják meg a helyi, a visszaható és a továbbító pályázókat. Az ügyfelek meghívásban fogják elküldeni ezeket a jelölteket a szolgáltatásnak. A szolgáltatás nem használ továbbítót, mert nyilvánosan elérhető IP-címmel rendelkezik, így a helyi IP-címmel válaszol. Az ügyfél és a szolgáltatás az egy-az-egyhez hívásokhoz hasonló módon fogja ellenőriznie a kapcsolatot.

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="OACS-csoport hívása":::

*9. ábra – kommunikációs szolgáltatások csoportos hívása*

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a hívással](../quickstarts/voice-video-calling/getting-started-with-calling.md)

A következő dokumentumok érdekesek lehetnek:

- További információ a [hívások típusairól](../concepts/voice-video-calling/about-call-types.md)
- További információ az [ügyfél-kiszolgáló architektúráról](./client-and-server-architecture.md)