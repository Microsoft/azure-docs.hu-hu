---
title: Azure Relay Hibrid kapcsolatok Protocol-útmutató | Microsoft Docs
description: Ez a cikk azokat az ügyféloldali interakciókat ismerteti, amelyekkel az Hibrid kapcsolatok Relay csatlakozik az ügyfelekhez a figyelő és a küldő szerepköreiben.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 36321f88de173a37c9aa6615c4c0f2b29aec9f20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97976962"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure Relay Hibrid kapcsolatok protokoll

Azure Relay az Azure Service Bus platform egyik kulcsfontosságú képességi pillére. A Relay új _hibrid kapcsolatok_ funkciója a http és a WebSockets szolgáltatáson alapuló biztonságos, nyílt protokollú evolúció. Ez felülírja a korábbi, egyformán megnevezett _BizTalk Services_ funkciót, amely a saját protokoll Foundation-re épül. A Hibrid kapcsolatok az App Services Azure-ba való integrálása továbbra is a következőképpen fog működni.

Hibrid kapcsolatok lehetővé teszi a kétirányú, a kérés-válasz és a bináris adatfolyam-kommunikációt, valamint a két hálózati alkalmazás közötti egyszerű datagram áramlását. Mindkét fél lehet NAT vagy tűzfal mögött.

Ez a cikk azokat az ügyféloldali interakciókat ismerteti, amelyekkel az Hibrid kapcsolatok Relay csatlakozik az ügyfelekhez a figyelő és a küldő szerepköreiben. Azt is leírja, hogy a figyelők hogyan fogadják el az új kapcsolatokat és kéréseket.

## <a name="interaction-model"></a>Interakciós modell

A Hibrid kapcsolatok Relay két felet csatlakoztat egy olyan, az Azure-felhőben található Rendezvous pont megadásával, amelyet a felek felderítenek és csatlakozhatnak a saját hálózata szemszögéből. A Rendezvous pont neve "hibrid kapcsolódás", valamint más dokumentáció, API-k és a Azure Portal is. A cikk további részében a Hibrid kapcsolatok szolgáltatási végpontot "szolgáltatásnak" nevezzük.

A szolgáltatás lehetővé teszi a webes szoftvercsatorna-kapcsolatok és a HTTP (S) kérelmek és válaszok továbbítását.

A interakciós modell a számos más hálózati API-k által létesített nómenklatúrára támaszkodik. Van egy figyelő, amely először a bejövő kapcsolatok kezelésének készültségét jelzi, és később fogadja őket. A másik oldalon az ügyfél a figyelőhöz csatlakozik, és arra vár, hogy a kapcsolat elfogadható legyen a kétirányú kommunikációs útvonal létrehozásához. A "Csatlakozás", a "Listen" és az "Accept" ugyanaz a kifejezés, amelyet a legtöbb szoftvercsatorna API-ban talál.

A továbbított kommunikációs modellek bármelyike egy szolgáltatás-végpont felé irányuló kimenő kapcsolatokat tesz elérhetővé. Így a "figyelő" is "ügyfél" lehet a köznyelven belüli használatban, és más terminológiát is okozhat. A Hibrid kapcsolatokhoz használt pontos terminológia a következő:

A kapcsolatok mindkét oldalán lévő programokat "ügyfélnek" nevezzük, mivel ezek az ügyfelek a szolgáltatásnak. A-ügyfél, amely megvárja a kapcsolatokat, és fogadja a kapcsolatokat, a "figyelő", vagy azt mondják, hogy a "figyelő szerepkör". Az ügyfél, amely egy, a szolgáltatáson keresztüli figyelőhöz kezdeményez egy új kapcsolódást, neve "Sender", vagy a "feladói szerepkör".

### <a name="listener-interactions"></a>Figyelő interakciók

A figyelő öt interakciót tartalmaz a szolgáltatással. a jelen cikk későbbi részében az összes adatátviteli adatot a hivatkozási szakasz ismerteti.

A figyelő, az elfogadás és a kérés üzenetek a szolgáltatástól érkeznek. A megújítási és pingelési műveleteket a figyelő küldik el.

#### <a name="listen-message"></a>Figyelő üzenet

Annak jelzésére, hogy a figyelő készen áll-e a kapcsolatok fogadására, létrehoz egy kimenő WebSocket-kapcsolatot. A kapcsolati kézfogás a továbbítási névtérben konfigurált hibrid kapcsolat nevét, valamint egy olyan biztonsági jogkivonatot tartalmaz, amely az adott név "Listen" (figyelés) jogosultságát adja.

Ha a websockett a szolgáltatás fogadja el, a regisztráció befejeződik, és a létrejött WebSocket a "vezérlési csatorna" állapotban marad az összes későbbi interakció engedélyezéséhez. A szolgáltatás legfeljebb 25 egyidejű figyelőt engedélyez egy hibrid kapcsolatban. Meg kell határozni a AppHooks vonatkozó kvótát.

Hibrid kapcsolatok esetén, ha két vagy több aktív figyelő van, akkor a bejövő kapcsolatok véletlenszerű sorrendben vannak elosztva egymás között. a méltányos terjesztés a lehető legjobb megoldással próbálkozik.

#### <a name="accept-message"></a>Üzenet elfogadása

Amikor egy küldő új kapcsolódást nyit meg a szolgáltatásban, a szolgáltatás kiválasztja és értesíti az egyik aktív figyelőt a hibrid kapcsolatban. Ezt az értesítést a rendszer a nyitott vezérlési csatornán JSON-üzenetként küldi el a figyelőnek. Az üzenet tartalmazza annak a WebSocket-végpontnak az URL-címét, amelyhez a figyelőnek csatlakoznia kell a kapcsolat fogadásához.

Az URL-címet a figyelő közvetlenül a további munka nélkül használhatja.
A kódolt információ csak rövid ideig érvényes, lényegében addig, amíg a küldő hajlandó megvárni a kapcsolódás végpontok közötti megkötését. A maximálisan feltételezett érték 30 másodperc. Az URL-cím csak egy sikeres kapcsolódási kísérlethez használható. Amint létrejön a Rendezvous URL-címmel létesített WebSocket-kapcsolat, a websocketen lévő összes további tevékenység továbbítása a feladótól és a küldőtől történik. Ez a viselkedés a szolgáltatás beavatkozása vagy értelmezése nélkül történik.

### <a name="request-message"></a>Kérelem üzenete

A WebSocket-kapcsolatokon kívül a figyelő HTTP-kérési kereteket is fogadhat a küldőtől, ha ez a funkció explicit módon engedélyezve van a hibrid kapcsolaton.

A HTTP-támogatással Hibrid kapcsolatokhoz csatolt figyelőknek kezelniük kell a `request` kézmozdulatot. Egy figyelő, amely nem kezeli a `request` szolgáltatást, ezért a csatlakozás során ismételt időtúllépési hibákat okoz, és a szolgáltatás a jövőben is blokkolhatja.

A HTTP-keret fejlécének metaadatait a rendszer a JSON formátumba fordítja le, így egyszerűbben kezelhető a figyelő keretrendszer, mivel a HTTP-fejléc elemző kódtárak ritkábbak a JSON-elemzők esetében. A HTTP-metaadatok, amelyek csak a küldő és a továbbító HTTP-átjáró közötti kapcsolatra vonatkoznak, és az engedélyezési adatokat is beleértve, nincs továbbítva. A HTTP-kérések szervei transzparens módon átvihetők bináris WebSocket-keretekként.

A figyelő egyenértékű válasz-kézmozdulattal válaszolhat a HTTP-kérelmekre.

A kérelem/válasz folyamat alapértelmezés szerint a vezérlő csatornát használja, de szükség esetén a "frissítés" egy külön Rendezvous WebSocket-re. A különböző WebSocket-kapcsolatok javítják az egyes ügyféloldali beszélgetések átviteli sebességét, de a figyelőt több, a kezeléshez szükséges kapcsolattal terhelik, ami esetleg nem lenne lehetséges a könnyű ügyfelek számára.

A vezérlési csatornán a kérelem és a válasz törzse legfeljebb 64 kB méretű lehet. A HTTP-fejléc metaadatai összesen 32 kB-ra korlátozódnak. Ha a kérelem vagy a válasz túllépi ezt a küszöbértéket, a figyelőnek az [elfogadás](#accept-message)kezelésére szolgáló kézmozdulattal kell frissítenie egy Rendezvous WebSocket-re.

A kérelmek esetében a szolgáltatás eldönti, hogy a vezérlési csatornán keresztül irányítja-e a kérelmeket. Ez magában foglalja a következőket, de nem korlátozható olyan esetekre, amikor egy kérelem meghaladja a 64 kB-ot (fejlécek és törzs), vagy ha a kérést ["darabolásos" átvitel-kódolással](https://tools.ietf.org/html/rfc7230#section-4.1) küldi el, és a szolgáltatásnak oka van arra, hogy a kérelem túllépje a 64 kB-ot, vagy a kérelem olvasása nem azonnali. Ha a szolgáltatás úgy dönt, hogy a kérést a Rendezvous használatával kézbesíti, csak a Rendezvous-címeket továbbítja a figyelőnek.
Ekkor a figyelőnek létre kell hoznia a Rendezvous WebSocketet, és a szolgáltatás azonnal kézbesíti a teljes kérelmet, beleértve a Rendezvous WebSocket-en keresztül fellépő szervezeteket is. A válasznak a Rendezvous websockett is használnia kell.

A vezérlési csatornán megjelenő kérelmek esetén a figyelő eldönti, hogy válaszol-e a vezérlési csatornán vagy a rendezvouson keresztül. A szolgáltatásnak tartalmaznia kell egy Rendezvous-címeket a vezérlési csatornán keresztül átirányított minden kéréssel. Ez a címe csak az aktuális kérelemből való frissítésre érvényes.

Ha a figyelő úgy dönt, hogy frissíti, az csatlakozik, és azonnal továbbítja a választ a megadott Rendezvous-szoftvercsatornán.

A Rendezvous WebSocket létrejötte után a figyelőnek meg kell őriznie az ügyféltől érkező kérések és válaszok további kezeléséhez. A szolgáltatás fenntartja a WebSocketet mindaddig, amíg a HTTPS szoftvercsatorna-kapcsolat megmarad a küldővel, és az adott feladótól érkező összes további kérelmet átirányítja a karbantartott websocketre. Ha a figyelő úgy dönt, hogy eldobja a Rendezvous WebSocketet a saját oldaláról, akkor a szolgáltatás a küldővel is elveszi a csatlakozást, függetlenül attól, hogy egy későbbi kérelem már folyamatban van-e.

#### <a name="renew-operation"></a>Megújítási művelet

Az a biztonsági jogkivonat, amelyet a figyelő regisztrálásához és a vezérlési csatorna karbantartásához kell használni, a figyelő aktív állapotban lehet. A jogkivonat lejárati ideje nem befolyásolja a folyamatban lévő kapcsolatokat, de ez azt eredményezi, hogy a szolgáltatás a lejárati időpontot követően vagy után nem fogja eldobni a vezérlő csatornát. A "megújítás" művelet egy JSON-üzenet, amelyet a figyelő küldhet a vezérlési csatornához társított jogkivonat cseréjére, hogy a vezérlő csatorna hosszabb ideig is fenntartható legyen.

#### <a name="ping-operation"></a>Pingelési művelet

Ha a vezérlési csatorna hosszú ideig inaktív marad, a közvetítők (például terheléselosztó vagy NAT) elkerülhetnek a TCP-kapcsolatban. A "ping" művelet elkerüli, hogy kis mennyiségű adat küldése a csatornán, amely mindenkit emlékeztet arra a hálózati útvonalra, amelyhez a kapcsolódást szánták, és a figyelő "élő" tesztként is szolgál. Ha a pingelés sikertelen, a vezérlő csatornát használhatatlannak kell tekinteni, és a figyelőnek újra kell csatlakoznia.

### <a name="sender-interaction"></a>Küldői interakció

A küldő két interakciót tartalmaz a szolgáltatással: összekapcsolja a webes szoftvercsatornát, vagy HTTPS-kapcsolaton keresztül küldi a kéréseket. A kérelmeket nem lehet elküldeni a küldő szerepkörből származó webes szoftvercsatornán keresztül.

#### <a name="connect-operation"></a>Csatlakozási művelet

A "Csatlakozás" művelet megnyit egy WebSocketet a szolgáltatásban, amely megadja a hibrid kapcsolat nevét és (opcionálisan, de alapértelmezés szerint kötelező) egy olyan biztonsági jogkivonatot, amely "Send" engedélyt küld a lekérdezési karakterláncban. A szolgáltatás ezután a korábban ismertetett módon együttműködik a figyelővel, és a figyelő létrehoz egy olyan Rendezvous-kapcsolatot, amely ehhez a websockethez csatlakozik. A WebSocket elfogadása után a WebSocket összes további interakciója egy csatlakoztatott figyelővel van ellátva.

#### <a name="request-operation"></a>Kérelem művelete

Azon Hibrid kapcsolatok esetében, amelyeknél a szolgáltatás engedélyezve van, a küldő nagymértékben korlátlan HTTP-kéréseket küldhet a figyelőknek.

A továbbítási hozzáférési token, amely a lekérdezési karakterláncba vagy a kérelem egy HTTP-fejlécében van beágyazva, a továbbító teljes mértékben átlátható a továbbítási címen lévő összes HTTP-műveletre és a továbbítási címek elérési útjának összes utótagra, így a figyelő teljes mértékben átirányítja a végpontok közötti hitelesítést, és még a HTTP-bővítmények, például a [CORS](https://www.w3.org/TR/cors/).

A továbbító végponttal rendelkező küldő engedélyezése alapértelmezés szerint be van kapcsolva, de nem kötelező. A hibrid kapcsolatok tulajdonosa dönthet úgy, hogy engedélyezi a névtelen küldőket. A szolgáltatás az alábbi módon fogja feltartóztatni, megvizsgálni és megtekinteni az engedélyezési adatokat:

1. Ha a lekérdezési karakterlánc egy `sb-hc-token` kifejezést tartalmaz, a kifejezés mindig el lesz távolítva a lekérdezési karakterláncból. A rendszer kiértékeli, hogy be van-e kapcsolva a továbbító engedélyezése.
2. Ha a kérelem fejléce tartalmaz egy `ServiceBusAuthorization` fejlécet, a fejléc kifejezés mindig el lesz távolítva a fejléc-gyűjteményből.
   A rendszer kiértékeli, hogy be van-e kapcsolva a továbbító engedélyezése.
3. Csak akkor, ha a továbbítás engedélyezése be van kapcsolva, és ha a kérések fejléce tartalmaz egy `Authorization` fejlécet, és egyik korábbi kifejezés sem létezik, a fejléc kiértékelése és kimaradása megtörténik. Ellenkező esetben a `Authorization` mindig a-ként lesz átadva.

Ha nincs aktív figyelő, a szolgáltatás egy 502 "hibás átjáró" hibakódot ad vissza. Ha a szolgáltatás nem jelenik meg a kérelem kezelésére, a szolgáltatás 60 másodperc után 504 "átjáró-időtúllépést" ad vissza.

### <a name="interaction-summary"></a>Interakciók összegzése

Ennek az interakciós modellnek az eredménye az, hogy a küldő ügyfél kikerül a kézfogásból egy "tiszta" WebSocket-kapcsolattal, amely egy figyelőhöz csatlakozik, és nem igényel további preambulumot vagy előkészítést. Ez a modell lehetővé teszi, hogy gyakorlatilag bármely meglévő WebSocket-ügyfél implementációja hatékonyan kihasználja a Hibrid kapcsolatok szolgáltatást azáltal, hogy megfelelően kiépített URL-címet biztosít a WebSocket-ügyfél rétegéhez.

A figyelő által az Accept interakción keresztül beszerzett Rendezvous kapcsolati WebSocket is tiszta, és a meglévő WebSocket-kiszolgálók megvalósításához egy kis plusz absztrakció szükséges, amely megkülönbözteti az "elfogadás" műveletet a keretrendszer helyi hálózati figyelői és Hibrid kapcsolatok távoli "elfogadás" műveletekkel.

A HTTP-kérés/válasz modellje a küldőnek egy nagymértékben korlátlan HTTP protokoll felületet biztosít egy opcionális engedélyezési réteggel. A figyelő egy előre elemzett HTTP-kérési fejléc szakaszt kap, amely visszaváltható egy alsóbb rétegbeli HTTP-kérelembe, vagy kezelhető úgy, hogy a bináris keretek HTTP-törzseket végezzenek. A válaszok ugyanazt a formátumot használják. A kérelem és a reagálási törzs kevesebb mint 64 KB-nál kisebb interakciókat lehet kezelni egyetlen webes szoftvercsatornán, amely minden küldő számára meg van osztva. A nagyobb kérelmek és válaszok a Rendezvous modell használatával kezelhetők.

## <a name="protocol-reference"></a>Protokoll-referenciák

Ez a szakasz a korábban leírt protokoll-interakciók részleteit ismerteti.

Az 443-as porton az összes WebSocket-kapcsolat a HTTPS 1,1-es verzióra épül, amelyet általában néhány WebSocket-keretrendszer vagy API eltulajdonít. Az itt ismertetett Leírás az adott keretrendszerre vonatkozó javaslat nélkül marad semleges.

### <a name="listener-protocol"></a>Figyelő protokoll

A figyelő protokoll két kapcsolati kézmozdulatot és három üzenet műveletet tartalmaz.

#### <a name="listener-control-channel-connection"></a>Figyelő-vezérlési csatorna kapcsolatai

Megnyílik a vezérlési csatorna a következő WebSocket-kapcsolat létrehozásával:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

A a `namespace-address` Azure Relay névtér teljes tartományneve, amely a hibrid kapcsolatokat tárolja, jellemzően az űrlapon `{myname}.servicebus.windows.net` .

A lekérdezési karakterlánc paraméterének beállításai a következők.

| Paraméter        | Kötelező | Leírás
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Igen      | A figyelő szerepkörhöz a paraméternek **SB-HC-Action = figyeljnek** kell lennie.
| `{path}`         | Yes      | Az előre konfigurált hibrid kapcsolatok URL-kódolt névtérbeli elérési útja a figyelő regisztrálásához. A kifejezés a rögzített elérésiút-részhez lesz hozzáfűzve `$hc/` .
| `sb-hc-token`    | Yes\*    | A figyelőnek érvényes, URL-kódolású Service Bus közös hozzáférési jogkivonatot kell megadnia a névtérhez vagy hibrid kapcsolathoz, amely a **figyelési** jogosultságot biztosítja.
| `sb-hc-id`       | No       | Ez az ügyfél által megadott opcionális azonosító lehetővé teszi a végpontok közötti diagnosztikai nyomkövetést.

Ha a WebSocket-kapcsolat sikertelen, mert a hibrid kapcsolati útvonal regisztrálása nem történik meg, vagy egy érvénytelen vagy hiányzó jogkivonat vagy valamilyen más hiba történt, a rendszer a hibaüzenetet a normál HTTP 1,1 állapot-visszajelzési modell használatával adja meg. Az állapot leírása olyan nyomkövetési azonosítót tartalmaz, amely az Azure-támogatási munkatársakkal is közölhető:

| Code | Hiba          | Leírás
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nem található      | A hibrid kapcsolatok elérési útja érvénytelen, vagy az alap URL-cím helytelen formátumú.
| 401  | Nem engedélyezett   | A biztonsági jogkivonat hiányzik vagy helytelen formátumú vagy érvénytelen.
| 403  | Forbidden      | A biztonsági jogkivonat nem érvényes ehhez a művelethez ehhez az elérési úthoz.
| 500  | Belső hiba | Hiba történt a szolgáltatásban.

Ha a WebSocket-kapcsolatot szándékosan leállítja a szolgáltatás a kezdeti beállítás után, akkor ennek oka a megfelelő WebSocket protokoll hibakódja, valamint egy leíró hibaüzenet, amely tartalmazza a követési azonosítót is. A szolgáltatás nem állítja le a vezérlési csatornát a hiba feltétele nélkül. Minden tiszta leállítás ügyfél által vezérelt.

| WS állapota | Leírás
| --------- | -------------------------------------------------------------------------------
| 1001      | A hibrid kapcsolatok elérési útja törölve vagy letiltva.
| 1008      | A biztonsági jogkivonat lejárt, ezért a rendszer megsértette az engedélyezési házirendet.
| 1011      | Hiba történt a szolgáltatásban.

#### <a name="accept-handshake"></a>Kézfogás elfogadása

Az "elfogadás" értesítést a szolgáltatás a korábban meghatározott vezérlési csatornán keresztül küldi el a figyelőnek egy WebSocket-szövegmezőben található JSON-üzenetként. Erre az üzenetre nem érkezik válasz.

Az üzenet egy "elfogadás" nevű JSON-objektumot tartalmaz, amely jelenleg a következő tulajdonságokat definiálja:

* **cím** – a WebSocket szolgáltatáshoz való létrehozásához használandó URL-karakterlánc a bejövő kapcsolat fogadásához.
* **ID** – a hálózat egyedi azonosítója. Ha az azonosítót a küldő ügyfél adta meg, akkor a feladó által megadott érték, ellenkező esetben a rendszer által generált érték.
* **connectHeaders** – az összes olyan HTTP-fejléc, amelyet a továbbító végponthoz adott meg a küldő, amely a SEC-WebSocket-Protocol és a SEC-WebSocket-Extensions fejléceket is tartalmazza.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",
        "connectHeaders" : {
            "Host" : "...",
            "Sec-WebSocket-Protocol" : "...",
            "Sec-WebSocket-Extensions" : "..."
        }
     }
}
```

A figyelő a JSON-üzenetben megadott URL-címet használja a küldő szoftvercsatorna fogadására vagy elutasítására szolgáló WebSocket létrehozásához.

##### <a name="accepting-the-socket"></a>A szoftvercsatorna elfogadása

Az elfogadás érdekében a figyelő egy WebSocket-kapcsolatot hoz létre a megadott címen.

Ha az "elfogadás" üzenet `Sec-WebSocket-Protocol` fejlécet tartalmaz, akkor a figyelő csak akkor fogadja el a WebSocketet, ha támogatja ezt a protokollt. Emellett beállítja a fejlécet a WebSocket létrehozásakor.

Ugyanez vonatkozik a `Sec-WebSocket-Extensions` fejlécre is. Ha a keretrendszer támogatja a bővítményt, állítsa be a fejlécet a bővítmény szükséges kézfogásának kiszolgálóoldali válaszára `Sec-WebSocket-Extensions` .

Az URL-címet a-ként kell használni az Accept szoftvercsatorna létrehozásához, de az alábbi paramétereket tartalmazza:

| Paraméter      | Kötelező | Leírás
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Igen      | A szoftvercsatorna elfogadásához a paraméternek a következőnek kell lennie: `sb-hc-action=accept`
| `{path}`       | Yes      | (lásd a következő bekezdést)
| `sb-hc-id`     | No       | Lásd az **azonosító** korábbi leírását.

`{path}` az előre konfigurált hibrid kapcsolatok URL-kódolt névtérbeli elérési útja, amelyen regisztrálni kell a figyelőt. A kifejezés a rögzített elérésiút-részhez lesz hozzáfűzve `$hc/` .

A `path` kifejezés kiterjeszthető egy utótaggal és egy lekérdezési karakterlánc kifejezéssel, amely a regisztrált nevet követi egy elválasztó perjel után.
Ez a paraméter lehetővé teszi, hogy a küldő ügyfél átadja a küldési argumentumokat az elfogadó figyelőnek, ha nem lehetséges a HTTP-fejlécek belefoglalása. A várt érték azt jelzi, hogy a figyelő keretrendszer elemzi a rögzített elérési út részét és a regisztrált nevet az elérési útról, és a maradékot, valószínűleg anélkül, hogy az általa megadott lekérdezési karakterlánc-argumentumok nélkül `sb-` , elérhetővé teszi az alkalmazás számára, hogy eldöntse, hogy fogadja-e a kapcsolódást.

További információ: a következő "küldő protokoll" szakasz.

Ha hiba merül fel, a szolgáltatás a következőképpen válaszolhat:

| Code | Hiba          | Leírás
| ---- | -------------- | -----------------------------------
| 403  | Forbidden      | Az URL-cím érvénytelen.
| 500  | Belső hiba | Hiba történt a szolgáltatásban

 A kapcsolat létrejötte után a kiszolgáló leállítja a WebSocketet, amikor a küldő WebSocket leáll, vagy a következő állapottal:

| WS állapota | Leírás                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | A küldő ügyfél leállítja a kapcsolódást.                                    |
| 1001      | A hibrid kapcsolatok elérési útja törölve vagy letiltva.                        |
| 1008      | A biztonsági jogkivonat lejárt, ezért a rendszer megsértette az engedélyezési házirendet. |
| 1011      | Hiba történt a szolgáltatásban.                                            |

##### <a name="rejecting-the-socket"></a>A szoftvercsatorna elutasítása

 A szoftvercsatorna elutasítása az üzenet megvizsgálása után `accept` hasonló kézfogást igényel, hogy az elutasítás okát közlő állapotkód és az állapot leírása vissza tudjon térni a küldőnek.

 A protokoll kialakításának megválasztása, hogy egy WebSocket-kézfogást használjon (amelyet egy meghatározott hiba állapotának meghatározására terveztek), így a figyelő ügyfél-implementációi továbbra is használhatnak egy WebSocket-ügyfelet, és nem kell külön, operációs rendszer nélküli HTTP-ügyfelet alkalmazni.

 A szoftvercsatorna elutasításához az ügyfél átveszi a címet tartalmazó URI-t az `accept` üzenetből, és két lekérdezési karakterlánc paramétert fűz hozzá, az alábbiak szerint:

| Param                   | Kötelező | Leírás                              |
| ----------------------- | -------- | ---------------------------------------- |
| SB-HC-statusCode        | Yes      | Numerikus HTTP-állapotkód.                |
| SB-HC-statusDescription | Yes      | Az elutasítás emberi olvasási oka. |

A rendszer a létrejövő URI-t használja a WebSocket-kapcsolat létrehozásához.

A megfelelő végrehajtáskor ez a kézfogás szándékosan meghiúsul a 410-es HTTP-hibakód miatt, mert nem lett létrehozva WebSocket. Ha valami probléma merül fel, a következő kódok írják le a hibát:

| Code | Hiba          | Leírás                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Forbidden      | Az URL-cím érvénytelen.                |
| 500  | Belső hiba | Hiba történt a szolgáltatásban. |

#### <a name="request-message"></a>Kérelem üzenete

A `request` szolgáltatás elküldi az üzenetet a figyelőnek a vezérlési csatornán keresztül. A rendszer ugyanezt az üzenetet is elküldi a Rendezvous websocketen a létrehozás után.

A `request` két részből áll: egy fejlécből és egy bináris törzsből álló keretből.
Ha nincs törzs, a rendszer kihagyja a törzs kereteit. A logikai `body` tulajdonság azt jelzi, hogy egy törzs szerepel-e a kérelem üzenetében.

A kérelem törzsével kapcsolatos kérések esetén a struktúra a következőképpen nézhet ki:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

A figyelőnek képesnek kell lennie a kérés törzsének több bináris kereten belüli felosztására (lásd: [WebSocket-töredékek](https://tools.ietf.org/html/rfc6455#section-5.4)).
A kérelem akkor fejeződik be, amikor a FIN jelző készlettel rendelkező bináris keret érkezett.

Törzs nélküli kérelem esetén csak egy szövegkeret szerepel.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

A JSON-tartalma a `request` következő:

* **címe** -URI karakterlánc. Ez a kérelemhez használt Rendezvous-címe. Ha a bejövő kérelem mérete meghaladja a 64 kB-ot, az üzenet hátralévő része üresen marad, és az ügyfélnek az `accept` alább ismertetett műveletnek megfelelő randevú-kézfogást kell kezdeményeznie. A szolgáltatás ezután a kész webes szoftvercsatornán helyezi el a teljes betöltést `request` . Ha a válasz várhatóan meghaladja a 64 kB-ot, a figyelőnek egy randevú-kézfogást is kezdeményeznie kell, majd át kell adnia a választ a létrejött webes szoftvercsatornán.
* **azonosító** – karakterlánc. A kérelem egyedi azonosítója.
* **requestHeaders** – ez az objektum tartalmazza a küldő által a végpontnak megadott összes HTTP-fejlécet, a [fentiekben](#request-operation)ismertetett engedélyezési adatok kivételével, valamint olyan fejléceket, amelyek szigorúan kapcsolódnak az átjáróval létesített kapcsolathoz. Pontosabban, a [RFC7230](https://tools.ietf.org/html/rfc7230)által meghatározott vagy fenntartott fejlécek, kivéve a (z) `Via` és a nem továbbított fejléceket:

  * `Connection` (RFC7230, 6,1. szakasz)
  * `Content-Length`  (RFC7230, 3.3.2. szakasz)
  * `Host`  (RFC7230, 5,4. szakasz)
  * `TE`  (RFC7230, 4,3. szakasz)
  * `Trailer`  (RFC7230, 4,4. szakasz)
  * `Transfer-Encoding`  (RFC7230, 3.3.1. szakasz)
  * `Upgrade` (RFC7230, 6,7. szakasz)
  * `Close`  (RFC7230, 8,1. szakasz)

* **requestTarget** – karakterlánc. Ez a tulajdonság tartalmazza a kérelem  ["kérés célját" (RFC7230, 5,3. szakasz)](https://tools.ietf.org/html/rfc7230#section-5.3) . Magában foglalja a lekérdezési karakterlánc részét, amely az összes `sb-hc-` előtaggal ellátott paramétertől megfosztott.
* **metódus** – karakterlánc. A kérelem módszere [RFC7231, 4. szakasz](https://tools.ietf.org/html/rfc7231#section-4). A `CONNECT` METÓDUS nem használható.
* **Body** – Boolean. Azt jelzi, hogy egy vagy több bináris szövegtörzs követi-e a következőt:.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>Válaszadás a kérelmekre

A fogadónak válaszolnia kell. A kérések megtartása melletti ismételt sikertelen reagálás miatt a figyelő blokkolva lehet.

A válaszokat bármilyen sorrendben elküldheti, de az egyes kérelmeket 60 másodpercen belül kell megválaszolni, vagy a kézbesítés sikertelenként lesz jelezve. A 60 – második határidő akkor számít, ha a `response` szolgáltatás nem fogadta el a keretet. A több bináris kerettel rendelkező folyamatban lévő válasz több mint 60 másodpercig nem lehet üresjáratban, vagy leáll.

Ha a kérést a vezérlési csatornán keresztül fogadja, a választ a kérelem kézhezvételi helyétől vagy egy Rendezvous Channel-csatornán keresztül kell elküldeni a vezérlési csatornán.

A válasz egy "Response" nevű JSON-objektum. A törzs tartalmának kezelésére vonatkozó szabályok pontosan ugyanúgy, mint az `request` üzenet és a `body` tulajdonság alapján.

* **kérelemazonosító** – karakterlánc. Szükséges. A `id` válaszban szereplő üzenet tulajdonságának értéke `request` .
* **statusCode** – szám. Szükséges. egy numerikus HTTP-állapotkód, amely az értesítés eredményét jelzi. A RFC7231 összes állapotkódot [, 6. szakasza](https://tools.ietf.org/html/rfc7231#section-6) engedélyezett, kivéve a [502 "hibás átjáró"](https://tools.ietf.org/html/rfc7231#section-6.6.3) és a [504 "Gateway timeout"](https://tools.ietf.org/html/rfc7231#section-6.6.5)értéket.
* **statusDescription** – karakterlánc. Választható. HTTP-állapot – kód okának kifejezése [RFC7230, 3.1.2. szakasz](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders** – külső http-válaszban beállítani kívánt HTTP-fejlécek.
  Akárcsak a `request` esetében, a RFC7230 definiált fejlécek nem használhatók.
* **Body** – Boolean. Azt jelzi, hogy a bináris törzs kerete (ke) t követi-e.

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Válaszadás a Rendezvous használatával

Az 64 kB-nál nagyobb válaszok esetén a választ egy Rendezvous szoftvercsatornán kell továbbítani. Továbbá, ha a kérelem meghaladja az 64 kB-ot, és az `request` csak a címe mezőt tartalmazza, egy Rendezvous szoftvercsatornát kell létrehozni a beszerzéséhez `request` . Miután létrejött a Rendezvous-szoftvercsatorna, a megfelelő ügyfélre és a megfelelő ügyfélről érkező további kérelmekre adott válaszokat a rendszer a Rendezvous szoftvercsatornán keresztül továbbítja, amíg az megmarad.

A `address` (z) rendszerbeli URL-címet a `request` Rendezvous szoftvercsatorna létrehozásához kell használni, de a következő paramétereket tartalmazza:

| Paraméter      | Kötelező | Leírás
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Igen      | A szoftvercsatorna elfogadásához a paraméternek a következőnek kell lennie: `sb-hc-action=request`

Ha hiba merül fel, a szolgáltatás a következőképpen válaszolhat:

| Code | Hiba           | Leírás
| ---- | --------------- | -----------------------------------
| 400  | Érvénytelen kérelem | Ismeretlen művelet vagy URL-cím érvénytelen.
| 403  | Forbidden       | Az URL-cím lejárt.
| 500  | Belső hiba  | Hiba történt a szolgáltatásban

 A kapcsolat létrejötte után a kiszolgáló leállítja a WebSocketet, ha az ügyfél HTTP-szoftvercsatorna leáll, vagy a következő állapottal rendelkezik:

| WS állapota | Leírás                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | A küldő ügyfél leállítja a kapcsolódást.                                    |
| 1001      | A hibrid kapcsolatok elérési útja törölve vagy letiltva.                        |
| 1008      | A biztonsági jogkivonat lejárt, ezért a rendszer megsértette az engedélyezési házirendet. |
| 1011      | Hiba történt a szolgáltatásban.                                            |


#### <a name="listener-token-renewal"></a>Figyelő token megújítása

Ha a figyelő token hamarosan lejár, lecserélheti azt úgy, hogy szöveges keretbeli üzenetet küld a szolgáltatásnak a megadott vezérlési csatornán keresztül. Az üzenet tartalmaz egy nevű JSON `renewToken` -objektumot, amely jelenleg a következő tulajdonságot határozza meg:

* **token** – érvényes, URL-kódolású Service Bus megosztott hozzáférési jogkivonat a névtérhez vagy hibrid kapcsolathoz, amely a **figyelési** jogosultságot biztosítja.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Ha a jogkivonat ellenőrzése nem sikerül, a hozzáférés megtagadva, és a Cloud Service hibával zárja be a vezérlési csatorna websocketjét. Ellenkező esetben nincs válasz.

| WS állapota | Leírás                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | A biztonsági jogkivonat lejárt, ezért a rendszer megsértette az engedélyezési házirendet. |

### <a name="web-socket-connect-protocol"></a>Web socket csatlakozási protokoll

A küldő protokoll lényegében azonos a figyelő létrehozási módjával.
A cél a végpontok közötti WebSocket maximális átlátszósága. A figyelőhöz való kapcsolódáshoz használt címnek ugyanaz a címe, de a "művelet" különbözik, és a tokennek eltérő engedélyre van szüksége:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sb-hc-token=...
```

A _névtér-címnek_ a Azure Relay névtér teljes tartományneve, amely a hibrid kapcsolatokat tárolja, jellemzően az űrlapon `{myname}.servicebus.windows.net` .

A kérés tetszőleges további HTTP-fejléceket tartalmazhat, beleértve az alkalmazás által definiált is. A rendszer az összes megadott fejlécet átirányítja a figyelőnek, és megtalálható az `connectHeader` **Accept** Control üzenet objektumán.

A lekérdezési karakterlánc paraméterének beállításai a következők:

| Param          | Kötelező? | Leírás
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Igen       | A küldő szerepkör esetében a paraméternek a következőnek kell lennie: `sb-hc-action=connect` .
| `{path}`       | Yes       | (lásd a következő bekezdést)
| `sb-hc-token`  | Yes\*     | A figyelőnek érvényes, URL-kódolású Service Bus közös hozzáférési jogkivonatot kell megadnia a névtérhez vagy a hibrid kapcsolathoz, amely a **küldési** jogosultságot ruházza fel.
| `sb-hc-id`     | No        | Egy opcionális azonosító, amely lehetővé teszi a végpontok közötti diagnosztikai nyomkövetést, és elérhetővé válik a figyelő számára az elfogadási kézfogás során.

 A az `{path}` előre konfigurált hibrid kapcsolatok URL-kódolt névtérbeli elérési útja, amelyen regisztrálni kell a figyelőt. A `path` kifejezés kiterjeszthető egy utótaggal és egy lekérdezési karakterlánc kifejezéssel a további kommunikációhoz. Ha a hibrid kapcsolatok az elérési úton vannak regisztrálva `hyco` , a `path` kifejezést `hyco/suffix?param=value&...` követheti az itt definiált lekérdezési karakterlánc paraméterei is. A teljes kifejezés a következő lehet:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sb-hc-token=...
```

A `path` kifejezés átkerül a figyelőbe az "elfogadás" vezérlő üzenetben található URL-címben.

Ha a WebSocket-kapcsolat sikertelen, mert a hibrid kapcsolat elérési útja nem regisztrálva van, érvénytelen vagy hiányzó jogkivonat vagy valamilyen más hiba történt, a rendszer a hibaüzenetet a normál HTTP 1,1 állapot-visszajelzési modell használatával adja meg. Az állapot leírása olyan nyomkövetési azonosítót tartalmaz, amely az Azure-támogatási munkatársakkal is közölhető:

| Code | Hiba          | Leírás
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nem található      | A hibrid kapcsolatok elérési útja érvénytelen, vagy az alap URL-cím helytelen formátumú.
| 401  | Nem engedélyezett   | A biztonsági jogkivonat hiányzik vagy helytelen formátumú vagy érvénytelen.
| 403  | Forbidden      | A biztonsági jogkivonat nem érvényes ehhez az elérési úthoz és ehhez a művelethez.
| 500  | Belső hiba | Hiba történt a szolgáltatásban.

Ha a WebSocket-kapcsolatot szándékosan leállítja a szolgáltatás a kezdeti beállítás után, akkor ennek oka a megfelelő WebSocket protokoll hibakódja, valamint egy leíró hibaüzenet, amely tartalmazza a követési azonosítót is.

| WS állapota | Leírás
| --------- | ------------------------------------------------------------------------------- 
| 1000      | A figyelő leállítja a szoftvercsatornát.
| 1001      | A hibrid kapcsolatok elérési útja törölve vagy letiltva.
| 1008      | A biztonsági jogkivonat lejárt, ezért a rendszer megsértette az engedélyezési házirendet.
| 1011      | Hiba történt a szolgáltatásban.

### <a name="http-request-protocol"></a>HTTP-kérelem protokollja

A HTTP-kérelem protokollja tetszőleges HTTP-kérelmeket tesz lehetővé, a protokollok frissítései kivételével.
A HTTP-kérelmeket az entitás normál futtatókörnyezeti címe, a hibrid kapcsolatok WebSocket-ügyfelekhez használt $hc Infix nélkül kell megmutatni.

```
https://{namespace-address}/{path}?sb-hc-token=...
```

A _névtér-címnek_ a Azure Relay névtér teljes tartományneve, amely a hibrid kapcsolatokat tárolja, jellemzően az űrlapon `{myname}.servicebus.windows.net` .

A kérés tetszőleges további HTTP-fejléceket tartalmazhat, beleértve az alkalmazás által definiált is. Az összes megadott fejléc, a RFC7230 közvetlenül meghatározottak kivételével (lásd a [kérési üzenetet](#request-message)) a figyelőnek, és a `requestHeader` **kérelem** üzenetének objektumán található.

A lekérdezési karakterlánc paraméterének beállításai a következők:

| Param          | Kötelező? | Leírás
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Igen\*     | A figyelőnek érvényes, URL-kódolású Service Bus közös hozzáférési jogkivonatot kell megadnia a névtérhez vagy a hibrid kapcsolathoz, amely a **küldési** jogosultságot ruházza fel.

A jogkivonat a `ServiceBusAuthorization` vagy a HTTP-fejlécben is elvégezhető `Authorization` . A jogkivonat kihagyható, ha a hibrid kapcsolat úgy van konfigurálva, hogy engedélyezze a névtelen kérelmeket.

Mivel a szolgáltatás hatékonyan működik proxyként, még akkor is, ha nem valódi HTTP-proxyként, egy `Via` fejlécet vagy jegyzeteket tesz a meglévő `Via` fejlécnek, amely megfelel a [RFC7230, a 5.7.1 szakasznak](https://tools.ietf.org/html/rfc7230#section-5.7.1).
A szolgáltatás hozzáadja a továbbítási névtér állomásnevét a következőhöz: `Via` .

| Code | Üzenet  | Leírás                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | A kérést legalább egy figyelő kezeli.  |
| 202  | Elfogadva | A kérést legalább egy figyelő fogadta el. |

Ha hiba lép fel, a szolgáltatás a következőképpen válaszolhat. Azt jelzi, hogy a válasz a szolgáltatásból származik-e, vagy a figyelő a fejléc jelenlétében azonosítható-e `Via` . Ha a fejléc jelen van, a válasz a figyelőtől származik.

| Code | Hiba           | Leírás
| ---- | --------------- |--------- |
| 404  | Nem található       | A hibrid kapcsolatok elérési útja érvénytelen, vagy az alap URL-cím helytelen formátumú.
| 401  | Nem engedélyezett    | A biztonsági jogkivonat hiányzik vagy helytelen formátumú vagy érvénytelen.
| 403  | Forbidden       | A biztonsági jogkivonat nem érvényes ehhez az elérési úthoz és ehhez a művelethez.
| 500  | Belső hiba  | Hiba történt a szolgáltatásban.
| 503  | Hibás átjáró     | A kérést nem lehetett átirányítani a figyelőhöz.
| 504  | Átjáró időtúllépése | A rendszer átirányítja a kérést egy figyelőhöz, de a figyelő nem nyugtázza a beérkezést a szükséges időn belül.

## <a name="next-steps"></a>Következő lépések

* [Relay – gyakori kérdések](relay-faq.md)
* [Névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés a .NET-tel](relay-hybrid-connections-dotnet-get-started.md)
* [Bevezetés a Node használatába](relay-hybrid-connections-node-get-started.md)
