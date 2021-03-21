---
title: TCP/IP teljesítmény-hangolás Azure-beli virtuális gépekhez | Microsoft Docs
description: Ismerje meg az Azure-beli virtuális gépekkel kapcsolatos különböző gyakori TCP/IP-teljesítmény-hangolási technikákat és azok kapcsolatát.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: 10fae563f80a4d77e3f4bb1d5ca809a4130b050a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558848"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP teljesítmény-hangolás Azure-beli virtuális gépekhez

Ez a cikk a TCP/IP-teljesítmény hangolási módszereit és az Azure-on futó virtuális gépekhez való használat során megfontolandó szempontokat ismerteti. Alapszintű áttekintést nyújt a technikákról, és bemutatja, hogyan hangolható be.

## <a name="common-tcpip-tuning-techniques"></a>Gyakori TCP/IP-hangolási technikák

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, töredezettség és nagyméretű küldés kiszervezése

#### <a name="mtu"></a>MTU

A maximális átviteli egység (MTU) a bájtban megadott legnagyobb méretű keret (csomag), amely a hálózati adapteren keresztül küldhető el. Az MTU egy konfigurálható beállítás. Az Azure-beli virtuális gépeken használt alapértelmezett MTU és a legtöbb hálózati eszköz alapértelmezett beállítása 1 500 bájt.

#### <a name="fragmentation"></a>Töredezettség

A töredezettség akkor fordul elő, ha egy olyan csomagot kap, amely meghaladja a hálózati adapterek MTU-értékét. A TCP/IP-verem megtöri a csomagot kisebb darabokra (töredékekre), amelyek megfelelnek a csatoló MTU-ának. A töredezettség az IP-rétegen történik, és független a mögöttes protokolltól (például TCP). Ha 2 000 bájtos csomagot küld a rendszer a 1 500-es MTU-értékkel rendelkező hálózati adapteren, a csomag 1 1 500 bájtos csomagra és 1 500 bájtos csomagra lesz bontva.

A forrás és a cél közötti elérési úton lévő hálózati eszközök el tudják dobni a csomagokat, amelyek túllépik az MTU-t, vagy kisebb darabokra darabolják a csomagot.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>A nem töredék bit egy IP-csomagban

A nem töredék (DF) bit az IP protokoll fejlécének egyik jelzője. A DF bit azt jelzi, hogy a küldő és a fogadó közötti útvonalon lévő hálózati eszközök nem darabolják fel a csomagot. Ez a bit számos okból állítható be. (Lásd a jelen cikk "Path MTU-felderítés" című szakaszát egy példához.) Ha egy hálózati eszköz olyan csomagot kap, amely nem tartalmaz bit-készletet, és a csomag mérete meghaladja az eszköz Interface MTU-értékét, akkor a standard működés az eszköz eldobása. Az eszköz az ICMP-töredezettség szükséges üzenetet küld vissza a csomag eredeti forrására.

#### <a name="performance-implications-of-fragmentation"></a>A töredezettség teljesítményének következményei

A töredezettség negatív teljesítménybeli következményekkel járhat. A teljesítményre gyakorolt hatás egyik fő oka a csomagok töredezettségének és újraösszeállításának a CPU/memória hatása. Ha egy hálózati eszköznek fel kell osztania egy csomagot, a töredezettség elvégzéséhez le kell foglalnia a CPU-/memória-erőforrásokat.

Ugyanaz a dolog történik a csomag újraösszeállításakor. A hálózati eszköznek az összes töredéket tárolnia kell, amíg meg nem érkeznek, így az eredeti csomagba újra összeállíthatók. Ez a töredezettségi és újraösszeállítási folyamat késést is okozhat.

A töredezettség más lehetséges negatív teljesítménybeli következménye, hogy a töredezett csomagok megérkeznek a sorrendbe. Ha a csomagok nem sorrendben érkeznek, a hálózati eszközök bizonyos típusai elhelyezhetik őket. Ebben az esetben a teljes csomagot újra kell küldeni.

A töredékeket a biztonsági eszközök, például a hálózati tűzfalak vagy a hálózati eszközök fogadási pufferei kimerülése esetén általában eldobják. Ha egy hálózati eszköz fogadási pufferei ki vannak merítve, a hálózati eszköz egy töredezett csomag újraösszeállítását kísérli meg, de nem rendelkezik a csomag tárolására és visszavételére szolgáló erőforrásokkal.

A töredezettséget negatív műveletnek tekintheti, de a töredezettség támogatása akkor szükséges, ha a különböző hálózatokat az interneten keresztül csatlakoztatja.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Az MTU módosításának előnyei és következményei

Általánosságban elmondható, hogy hatékonyabb hálózatot hozhat létre az MTU növelésével. Minden továbbított csomag fejléc-információval rendelkezik, amely az eredeti csomagba kerül. Ha a töredezettség több csomagot hoz létre, annál nagyobb a fejléc feje, és ez a hálózat kevésbé hatékony lesz.

Íme egy példa. Az Ethernet-fejléc mérete 14 bájt, valamint egy 4 bájtos keret-ellenőrzési sorozat a keret konzisztenciájának biztosítása érdekében. Ha a rendszer 1 2 000 bájtos csomagot kap, a hálózatban 18 bájt Ethernet-terhelést ad hozzá. Ha a csomag egy 1 500 bájtos csomagba és egy 500 bájtos csomagba van feldarabolva, minden csomag 18 bájtos Ethernet-fejlécet tartalmaz, összesen 36 bájt.

Ne feledje, hogy az MTU növelése nem feltétlenül hoz létre hatékonyabb hálózatot. Ha egy alkalmazás csak a 500 bájtos csomagokat küldi el, ugyanaz a fejléc jelenik meg, függetlenül attól, hogy az MTU értéke 1 500 bájt vagy 9 000 bájt. A hálózat csak akkor válik hatékonyabbá, ha az MTU által érintett nagyobb méretű csomagokat használ.

#### <a name="azure-and-vm-mtu"></a>Azure és VM MTU

Az Azure-beli virtuális gépek alapértelmezett MTU-értéke 1 500 bájt. Az Azure Virtual Network stack a csomagok 1 400 bájtnál való darabolását kísérli meg.

Vegye figyelembe, hogy az Virtual Network verem nem hatékony, mert a csomagokat 1 400 bájtra darabolja, annak ellenére, hogy a virtuális gépek 1 500-os MTU-értékkel rendelkeznek. A hálózati csomagok nagy hányada jóval kisebb, mint 1 400 vagy 1 500 bájt.

#### <a name="azure-and-fragmentation"></a>Az Azure és a töredezettség

Virtual Network a verem úgy van beállítva, hogy elveszítse az eredeti töredezett sorrendben nem megjelenő töredezett csomagokat. Ezeket a csomagokat a rendszer a FragmentSmack-ben közzétett, 2018-ben bejelentett hálózati biztonsági sebezhetőség miatt eldobja.

A FragmentSmack olyan hiba, amellyel a Linux kernel a töredezett IPv4-és IPv6-csomagok újraépítését kezelte. A távoli támadó ezt a hibát felhasználva költséges töredék-újraösszeállítási műveleteket indíthat el, ami növelheti a PROCESSZORt és szolgáltatásmegtagadást eredményezhet a célszámítógépen.

#### <a name="tune-the-mtu"></a>Az MTU hangolása

Az Azure-beli virtuális gépek MTU-értékét bármely más operációs rendszeren is konfigurálhatja. Azonban érdemes megfontolnia az Azure-ban megjelenő töredezettséget, amely az MTU konfigurálásakor fordul elő.

Nem javasoljuk ügyfeleinknek a virtuális gépek MTU növelését. Ebben a témakörben megtudhatja, hogyan valósítja meg az Azure az MTU-t, és hogyan hajtja végre a töredezettséget.

> [!IMPORTANT]
>Az MTU növelése nem ismert a teljesítmény javítása érdekében, és negatív hatással lehet az alkalmazás teljesítményére.
>
>

#### <a name="large-send-offload"></a>Nagyméretű küldés kiszervezése

A nagyméretű küldés kiszervezése (LSO) javíthatja a hálózati teljesítményt azáltal, hogy kiszervezi a csomagok szegmentálását az Ethernet-adapterre. Ha a LSO engedélyezve van, a TCP/IP-verem egy nagyméretű TCP-csomagot hoz létre, majd a továbbítás előtt elküldi az Ethernet-adapternek a szegmentáláshoz. A LSO előnye, hogy felszabadítja a PROCESSZORt a csomagok szegmentálásához, amely megfelel az MTU-nek, és a feldolgozást a hardveren végzett Ethernet-felületre végzi. Ha többet szeretne megtudni a LSO előnyeiről, tekintse meg a [nagyméretű küldési kiürítés támogatása](/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)című témakört.

Ha a LSO engedélyezve van, előfordulhat, hogy az Azure-ügyfelek nagy méretű kereteket látnak a csomagok rögzítésekor. Ezek a nagyméretű keretek azt eredményezik, hogy egyes ügyfelek meggondolják a töredezettséget, vagy nagy MTU-t használnak, ha nem. A LSO segítségével az Ethernet-adapter nagyobb méretű, maximális mennyiségű (MSS) szegmenst tud reklámozni a TCP/IP-verem számára egy nagyobb TCP-csomag létrehozásához. Ezt a teljes nem szegmentált keretet ezután továbbítja az Ethernet-adapternek, és láthatóvá válik a virtuális gépen végrehajtott csomagok rögzítése során. A csomag azonban az Ethernet-adapter által az Ethernet-adapter MTU-értéke szerint számos kisebb képkockára lesz bontva.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP MSS ablak skálázás és PMTUD

#### <a name="tcp-maximum-segment-size"></a>Maximális TCP-szegmens mérete

A maximális TCP-szegmens mérete (MSS) olyan beállítás, amely korlátozza a TCP-szegmensek méretét, így elkerülhető a TCP-csomagok töredezettsége. Az operációs rendszerek általában ezt a képletet használják a MSS beállítására:

`MSS = MTU - (IP header size + TCP header size)`

Az IP-fejléc és a TCP-fejléc 20 bájt, vagy 40 bájt összesen. Így az 1 500-es MTU-kapcsolattal rendelkező interfészek 1 460-as MSS-vel rendelkeznek. A MSS azonban konfigurálható.

Ez a beállítás a TCP háromutas kézfogásban van elfogadva, ha egy TCP-munkamenet a forrás és a cél között van beállítva. Mindkét fél egy MSS-értéket küld, a kettő közül a kettőt pedig a TCP-kapcsolatban.

Ne feledje, hogy a forrás és a cél MTU nem az egyetlen olyan tényező, amely meghatározza a MSS értékét. Az adatközvetítő hálózati eszközök, például a VPN-átjárók, beleértve az Azure VPN Gateway is, a forrástól és a célhelytől függetlenül módosíthatják az MTU-t, így biztosítva az optimális hálózati teljesítményt.

#### <a name="path-mtu-discovery"></a>Elérési út MTU-felderítése

A rendszer egyezteti a MSS-t, de előfordulhat, hogy nem jelzi a ténylegesen használható MSS-t. Ennek az az oka, hogy a forrás és a cél közötti útvonalon lévő más hálózati eszközöknél a forrás és a cél alacsonyabb MTU-értékkel rendelkezhet. Ebben az esetben az az eszköz, amelynek MTU-értéke kisebb, mint a csomag, el fogja dobni a csomagot. Az eszköz visszaküldi a szükséges ICMP-töredezettséget (3. kód) üzenet, amely az MTU-t tartalmazza. Ez az ICMP-üzenet lehetővé teszi, hogy a forrás gazdagépe megfelelően csökkentse a Path MTU-t. A folyamatot nevezzük Path MTU-felderítésnek (PMTUD).

A PMTUD folyamat nem hatékony, és hatással van a hálózati teljesítményre. Ha a csomagok küldése meghaladja a hálózati elérési út MTU-értékét, a csomagokat újra el kell küldeni egy alacsonyabb MSS-vel. Ha a küldő nem kapja meg az ICMP-töredezettségre vonatkozó szükséges üzenetet, valószínűleg azért, mert az elérési úton lévő hálózati tűzfal miatt (általában *PMTUD-Blackhole* nevezik), a küldőnek nem tudom, hogy csökkentenie kell a MSS-t, és folyamatosan újra kell küldenie a csomagot. Ezért nem javasoljuk az Azure virtuális gép MTU-értékének növelését.

#### <a name="vpn-and-mtu"></a>VPN és MTU

Ha olyan virtuális gépeket használ, amelyek beágyazást végeznek (például IPsec VPN-eket), a csomagok méretével és az MTU-val kapcsolatos további szempontokat is figyelembe kell venni A VPN-EK további fejléceket adhatnak hozzá a csomagokhoz, ami növeli a csomagok méretét, és kisebb MSS-t igényel.

Az Azure esetében javasoljuk, hogy állítsa be a TCP MSS 1 350 bájt és a bújtatási interfész MTU értékét 1 400-re. További információ: [VPN-eszközök és IPSec/IKE-paraméterek lap](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Késés, oda-és visszaút ideje, valamint a TCP-ablak skálázása

#### <a name="latency-and-round-trip-time"></a>Késés és oda-és visszaút ideje

A hálózati késést a száloptikai hálózatban fellépő fény sebessége szabályozza. A TCP hálózati átviteli sebességét is hatékonyan szabályozza a két hálózati eszköz közötti oda-és visszaúti idő (RTT).

| Útvonal | Távolság | Egyirányú idő | RTT |
| ----- | -------- | ------------ | --- |
|New York – San Francisco|4 148 km|21 MS|42 MS|
|New York – London|5 585 km|28 MS|56 MS|
|New York-i Sydney|15 993 km|80 MS|160 MS|

Ez a táblázat a két helyszín közötti egyenes távolságot mutatja. A hálózatokban a távolság általában hosszabb, mint az egyenes vonal távolsága. Itt egy egyszerű képlettel számíthatja ki a minimális RTT, amelyet a fény sebessége szabályoz:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

A propagálás sebességét 200-re használhatja. Ez a távolság (kilométerben), amely a fény 1 ezredmásodpercen át halad.

Vegyük példaként a New York-i San Francisco-ot. Az egyenes vonal hossza 4 148 km. Ha ezt az értéket az egyenletbe csatlakoztatja, a következőkhöz juthat:

`Minimum RTT = 2 * (4,148 / 200)`

Az egyenlet kimenete ezredmásodpercben van.

Ha a legjobb hálózati teljesítményt szeretné kipróbálni, a logikai lehetőség a legrövidebb távolságú célhelyek kiválasztása. A virtuális hálózatot úgy is meg kell terveznie, hogy optimalizálja a forgalom elérési útját, és csökkentse a késést. További információt a jelen cikk "hálózati kialakítással kapcsolatos szempontok" című szakaszában talál.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Késés és a kerekítés időkorlátja a TCP protokollon

Az oda-és visszautazási idő közvetlen hatással van a maximális TCP-átviteli sebességre. A TCP protokollban az *ablakméret* a TCP-kapcsolaton keresztül elküldhető forgalom maximális mennyisége, mielőtt a küldőnek nyugtát kell kapnia a fogadótól. Ha a TCP MSS 1 460 értékre van beállítva, és a TCP-ablakméret értéke 65 535, a küldő 45-csomagokat küldhet, mielőtt nyugtát kapjon a fogadótól. Ha a küldő nem kap nyugtát, akkor a rendszer visszaküldi az adatküldési kéréseket. A képlet a következő:

`TCP window size / TCP MSS = packets sent`

Ebben a példában a 65 535/1 460 a 45-ra van kerekítve.

Ez a "visszaigazolás visszaigazolása" állapotra, amely az adatok megbízható kézbesítésének biztosítására szolgál, mi okozza a RTT a TCP átviteli sebességét. Minél hosszabb a küldő vár nyugtára, annál hosszabb ideig kell várnia a további adatok küldése előtt.

Az alábbi képlettel egy TCP-kapcsolatok maximális átviteli sebességét számítjuk ki:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Ez a táblázat az egyetlen TCP-kapcsolatok maximális megabájt/másodperces átviteli sebességét jeleníti meg. (Az olvashatóság érdekében a mértékegységhez megabájtot kell használni.)

| TCP-ablak mérete (bájt) | RTT késés (MS) | Maximális megabájt/másodperc átviteli sebesség | Maximális megabit/másodperc átviteli sebesség |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65 535|1|65,54|524,29|
|65 535|30|2,18|17,48|
|65 535|60|1.09|8,74|
|65 535|90|.73|5,83|
|65 535|120|.55|4,37|

Ha a csomagok elvesznek, akkor a TCP-kapcsolat maximális átviteli sebessége csökken, amíg a küldő újraküldi a már elküldött adatokat.

#### <a name="tcp-window-scaling"></a>TCP-ablak skálázása

A TCP-ablak skálázása olyan technika, amely dinamikusan növeli a TCP-ablakméret méretét, így több adatátvitel is engedélyezhető, mielőtt nyugtázás szükséges. Az előző példában a rendszer a 45 csomagokat küldi el, mielőtt nyugtázás lenne szükséges. Ha növeli a nyugták megkezdése előtt elküldhető csomagok számát, akkor csökkenti a küldőnek a nyugtára várakozási idejének számát, ami növeli a maximális TCP-átviteli sebességet.

Ez a táblázat a következő kapcsolatokat szemlélteti:

| TCP-ablak mérete (bájt) | RTT késés (MS) | Maximális megabájt/másodperc átviteli sebesség | Maximális megabit/másodperc átviteli sebesség |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65 535|30|2,18|17,48|
|131 070|30|4,37|34,95|
|262 140|30|8,74|69,91|
|524 280|30|17,48|139,81|

A TCP-ablakméret TCP-fejlécének értéke azonban csak 2 bájt hosszúságú, ami azt jelenti, hogy a fogadási ablak maximális értéke 65 535. A maximális ablakméret növeléséhez egy TCP-ablak skálázási tényezője lett bevezetve.

A skálázási tényező egy operációs rendszeren konfigurálható beállítás is. A TCP-ablakméret méretének kiszámításához használja a következő képletet:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Itt látható a 3. ablak méretezési tényezője és a 65 535-os ablakméret:

`65,535 \* (2^3) = 262,140 bytes`

A 14-ös méretezési tényező a TCP-ablakméret 14 (a maximálisan engedélyezett eltolás) értékét eredményezi. A TCP-ablak mérete 1 073 725 440 bájt (8,5 Gigabit) lesz.

#### <a name="support-for-tcp-window-scaling"></a>A TCP-ablak skálázásának támogatása

A Windows különböző skálázási tényezőket állíthat be a különböző típusú kapcsolatok esetében. (A kapcsolatok osztályai közé tartozik az adatközpont, az internet stb.) A `Get-NetTCPConnection` PowerShell-parancs használatával megtekintheti az ablak skálázási kapcsolattípus:

```powershell
Get-NetTCPConnection
```

A `Get-NetTCPSetting` PowerShell-parancs használatával megtekintheti az egyes osztályok értékeit:

```powershell
Get-NetTCPSetting
```

A kezdeti TCP-ablakméret és a TCP-méretezési tényező a Windowsban a PowerShell-parancs használatával állítható be `Set-NetTCPSetting` . További információ:  [set-NetTCPSetting](/powershell/module/nettcpip/set-nettcpsetting).

```powershell
Set-NetTCPSetting
```

Ezek a következő érvényes TCP-beállítások `AutoTuningLevel` :

| AutoTuningLevel | Skálázási tényező | Skálázási szorzó | Képlet –<br/>Maximális ablakméret kiszámítása |
| --------------- | -------------- | ------------------ | -------------------------------------------- |
|Disabled (Letiltva)|Nincsenek|Nincsenek|Ablak mérete|
|Korlátozott hozzáférésű|4|2 ^ 4|Ablak mérete * (2 ^ 4)|
|Szigorúan korlátozott|2|2 ^ 2|Ablak mérete * (2 ^ 2)|
|Normál|8|2 ^ 8|Ablak mérete * (2 ^ 8)|
|Kísérleti|14|2 ^ 14|Ablak mérete * (2 ^ 14)|

Ezek a beállítások a legvalószínűbb hatással vannak a TCP-teljesítményre, de ne feledje, hogy az Azure-on kívül számos más tényező is befolyásolhatja a TCP-teljesítményt.

#### <a name="increase-mtu-size"></a>MTU méretének növeléséhez

Mivel a nagyobb MTU-érték nagyobb MSS-t jelent, lehet, hogy az MTU növelése növelheti a TCP-teljesítményt. Valószínűleg nem. A csomagok méretének előnyeit és hátrányait csak a TCP-forgalomon túl lehet. A korábban leírtaknak megfelelően a TCP-teljesítményt befolyásoló legfontosabb tényezők a TCP-ablakok mérete, a csomagok elvesztése és a RTT.

> [!IMPORTANT]
> Nem javasoljuk, hogy az Azure-ügyfelek megváltoztassák az alapértelmezett MTU-értéket a virtuális gépeken.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Gyorsított Hálózatkezelés és fogadó oldali skálázás

#### <a name="accelerated-networking"></a>Gyorsított hálózatkezelés

A virtuálisgép-hálózat funkciói a vendég virtuális gépen és a hypervisoron/gazdagépen is hagyományosan nagy CPU-Igényűek. Minden olyan csomagot, amely a gazdagépen keresztül halad át, a gazdagép CPU-ját dolgozza fel, beleértve az összes virtuális hálózat beágyazását és leállítását. Tehát minél több forgalmat halad át a gazdagépen, annál nagyobb a CPU-terhelés. Ha pedig a gazdagép CPU-je más műveletekkel van elfoglalva, az hatással lesz a hálózati teljesítményre és a késésre is. Az Azure gyorsított hálózatkezeléssel kezeli ezt a problémát.

A gyorsított hálózatkezelés konzisztens ultralow hálózati késést biztosít az Azure-ban és az olyan technológiákon keresztül, mint az SR-IOV. A gyorsított hálózatkezelés nagy mértékben áthelyezi az Azure szoftver által meghatározott hálózatkezelési veremet a processzorokból és a FPGA-alapú SmartNICs. Ez a módosítás lehetővé teszi a végfelhasználói alkalmazások számára a számítási ciklusok visszaigénylését, ami kevesebb terhelést eredményez a virtuális gépen, csökkentve a vibrálás és az inkonzisztencia késleltetését. Más szóval a teljesítmény több determinisztikus is lehet.

A gyorsított hálózatkezelés javítja a teljesítményt azáltal, hogy lehetővé teszi a vendég virtuális gép számára, hogy megkerüljék a gazdagépet, és közvetlenül egy gazdagép SmartNIC hozza létre a DataPath. A gyorsított hálózatkezelés előnyei a következők:

- **Kisebb késés/nagyobb csomagok másodpercenként (PPS)**: a virtuális kapcsolónak a DataPath való eltávolítása kiküszöböli a gazdagépen a szabályzatok feldolgozásához töltött időt, és növeli a virtuális gépen feldolgozható csomagok számát.

- **Csökkentett Jitter**: a virtuális kapcsolók feldolgozása az alkalmazandó házirend mennyiségétől és a feldolgozást végző CPU munkaterheléstől függ. A szabályzat kényszerítésének a hardverre való kiszervezése eltávolítja a változékonyságot a csomagok közvetlenül a virtuális géphez való továbbításával, a gazdagépek közötti kommunikáció és a szoftveres megszakítások és a környezeti kapcsolók eltávolításával.

- **Csökkentett CPU-kihasználtság**: a gazdagépen lévő virtuális kapcsoló megkerülése kevesebb CPU-kihasználtságot eredményez a hálózati forgalom feldolgozásakor.

A gyorsított hálózatkezelés használatához explicit módon engedélyeznie kell azt az egyes alkalmazható virtuális gépeken. Útmutatásért lásd: [Linux rendszerű virtuális gép létrehozása gyorsított hálózatkezeléssel](./create-vm-accelerated-networking-cli.md) .

#### <a name="receive-side-scaling"></a>Fogadó oldali skálázás

A fogadó oldali skálázás (RSS) egy olyan hálózati illesztőprogram-technológia, amely hatékonyabban osztja el a hálózati adatforgalmat azáltal, hogy több processzoron több CPU-ra terjeszti a fogadási feldolgozást többprocesszoros rendszerekben. Egyszerű feltételek mellett az RSS lehetővé teszi, hogy a rendszer több fogadott forgalmat feldolgozzon, mivel az összes rendelkezésre álló CPU-t csak egy helyett használja. Az RSS további technikai megvitatására lásd: [Bevezetés a fogadási oldali skálázásba](/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

A legjobb teljesítmény érdekében, ha a gyorsított hálózatkezelés engedélyezve van egy virtuális gépen, engedélyeznie kell az RSS-t. Az RSS a gyorsított hálózatkezelést nem használó virtuális gépeken is biztosít előnyöket. Tekintse át, hogyan állapíthatja meg, hogy az RSS engedélyezve van-e, és hogyan engedélyezheti azt: az Azure-beli [virtuális gépek hálózati átviteli sebességének optimalizálása](./virtual-network-optimize-network-bandwidth.md).

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TCP TIME_WAIT és TIME_WAIT merénylet

A TCP TIME_WAIT egy másik gyakori beállítás, amely hatással van a hálózatra és az alkalmazások teljesítményére. A sok szoftvercsatorna megnyitásakor és bezárásakor (például ügyfélként vagy kiszolgálóként (forrás IP-cím: forrásoldali port + cél IP-cím: célport) a TCP normál működése során egy adott szoftvercsatorna hosszú ideig TIME_WAIT állapotban is végződhet. A TIME_WAIT állapot azt jelenti, hogy a további adatmennyiséget egy szoftvercsatornán kell továbbítani, mielőtt bezárja. Így a TCP/IP-stackek általában megakadályozzák a szoftvercsatornák újrafelhasználását az ügyfél TCP SYN-csomagjának csendes eldobásával.

Az az időtartam, ameddig a szoftvercsatorna TIME_WAIT konfigurálható. 30 másodperc és 240 másodperc között lehet. A szoftvercsatornák véges erőforrás, és az adott időpontban használható szoftvercsatornák száma konfigurálható. (A rendelkezésre álló szoftvercsatornák száma általában körülbelül 30 000.) Ha a rendelkezésre álló szoftvercsatornák használatban vannak, vagy ha az ügyfelek és a kiszolgálók nem egyeznek TIME_WAIT beállításokkal, és egy virtuális gép egy TIME_WAIT állapotban próbálja használni a szoftvercsatornát, akkor az új kapcsolatok sikertelenek lesznek, mivel a TCP SYN csomagok csendesen el vannak dobva.

A kimenő szoftvercsatornák porttartomány értéke általában az operációs rendszer TCP/IP-veremében konfigurálható. Ugyanez igaz a TCP TIME_WAIT beállítások és a szoftvercsatorna újrafelhasználására is. A számok módosítása javíthatja a méretezhetőséget. Azonban a helyzettől függően ezek a változások együttműködési problémákat okozhatnak. Ha megváltoztatja ezeket az értékeket, körültekintően kell megjelennie.

Ezt a skálázási korlátozást a TIME_WAIT merénylettel kezelheti. TIME_WAIT merénylet lehetővé teszi, hogy a szoftvercsatorna bizonyos helyzetekben újra felhasználható legyen, például ha az új kapcsolat IP-csomagjaiban szereplő sorozatszám meghaladja az előző kapcsolat utolsó csomagjának sorszámát. Ebben az esetben az operációs rendszer lehetővé teszi az új kapcsolódás létrejöttét (az új SYN/ACK-t fogadja el), és kényszeríti az előző, TIME_WAIT állapotban lévő kapcsolódás bezárását. Ez a képesség az Azure-beli Windows rendszerű virtuális gépeken támogatott. Ha további információt szeretne a más virtuális gépek támogatásáról, tekintse meg az operációs rendszer gyártójával foglalkozó témakört.

A TCP TIME_WAIT beállításainak és a forrásport tartományának konfigurálásáról további információt a [hálózati teljesítmény javítása érdekében módosítható beállítások](/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance)című témakörben talál.

## <a name="virtual-network-factors-that-can-affect-performance"></a>A teljesítményt befolyásoló virtuális hálózati tényezők

### <a name="vm-maximum-outbound-throughput"></a>Virtuális gép maximális kimenő átviteli sebessége

Az Azure számos virtuálisgép-méretet és-típust biztosít, amelyek mindegyike különböző teljesítmény-képességekkel rendelkezik. Ezen képességek egyike a hálózati átviteli sebesség (vagy a sávszélesség), amelyet a megabit/másodpercben (Mbps) mérnek. Mivel a virtuális gépek megosztott hardveren futnak, a hálózati kapacitást a virtuális gépek között egyenlően kell megosztani ugyanazon hardver használatával. A nagyobb méretű virtuális gépek nagyobb sávszélességet foglalnak le, mint a kisebb virtuális gépek.

Az egyes virtuális gépek számára lefoglalt hálózati sávszélesség mérése a virtuális gépről érkező kimenő forgalomra vonatkozik. A virtuális gépet elhagyó összes hálózati forgalom beleszámít a lefoglalt korlát felé, a célhelytől függetlenül. Ha például egy virtuális gépen 1 000 MB/s korlát van, akkor ez a korlát attól függetlenül érvényes, hogy a kimenő forgalom egy másik, ugyanazon a virtuális hálózatban lévő virtuális gépre, vagy az Azure-on kívülre van-e szánva.

A bejövő forgalom nem mérhető vagy nem korlátozódik közvetlenül. Más tényezők, például a processzor és a tárterület korlátai is lehetnek, amelyek befolyásolhatják a virtuális gép a bejövő adatok feldolgozásának képességét.

A gyorsított hálózatkezelés úgy lett kialakítva, hogy javítsa a hálózati teljesítményt, beleértve a késést, az átviteli sebességet és a CPU-kihasználtságot. A gyorsított hálózatkezelés javíthatja a virtuális gépek átviteli sebességét, de csak a virtuális gép lefoglalt sávszélességére képes.

Az Azure-beli virtuális gépekhez legalább egy hálózati adapter csatlakozik. Lehet, hogy több van. A virtuális gép számára lefoglalt sávszélesség az összes kimenő forgalom összege a számítógéphez csatlakoztatott összes hálózati adapteren. Más szóval a sávszélesség virtuális gépenként van lefoglalva, függetlenül attól, hogy hány hálózati adapter van csatlakoztatva a számítógéphez.

A várt kimenő átviteli sebesség és az egyes virtuálisgép-méretek által támogatott hálózati adapterek száma az Azure-beli [Windows rendszerű virtuális gépek méretében](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)van részletezve. Ha meg szeretné tekinteni a maximális átviteli sebességet, válasszon egy típust, például az **általános célú** elemet, majd keresse meg az eredményül kapott oldalon található méretezési sorozat szakaszát (például "Dv2-sorozat"). Minden adatsorozathoz van egy táblázat, amely az utolsó oszlopban tartalmaz hálózati specifikációkat, amelyek a "Max NIC/várt hálózati sávszélesség (Mbps)" címmel rendelkeznek.

Az átviteli sebesség korlátja a virtuális gépre vonatkozik. Az átviteli sebességet a következő tényezők nem érintik:

- **Hálózati adapterek száma**: a sávszélesség korlátja a virtuális gépről érkező összes kimenő forgalom összegére vonatkozik.

- **Gyorsított hálózatkezelés**: bár ez a funkció hasznos lehet a közzétett korlát eléréséhez, nem változtatja meg a korlátot.

- **Forgalmi cél**: minden célhely a kimenő korlát felé mutat.

- **Protokoll**: az összes protokollon keresztüli kimenő forgalom a korlát irányába számít.

További információ: [virtuális gép hálózati sávszélessége](./virtual-machine-network-throughput.md).

### <a name="internet-performance-considerations"></a>Internetes teljesítménnyel kapcsolatos megfontolások

A cikkben leírtak szerint az interneten és az Azure-on kívüli tényezők hatással lehetnek a hálózati teljesítményre. Íme néhány tényező:

- **Késés**: a két cél közötti oda-és visszaúti idő a közbenső hálózatokkal kapcsolatos problémákra is hatással lehet, olyan forgalom esetén, amely nem a "legrövidebb" távolsági útvonalat, és az optimális kikapcsolási útvonalakat használja.

- **Csomagok elvesztése**: a csomagok elvesztése a hálózati torlódás, a fizikai elérési út problémái és a hálózati eszközök teljesítményének oka lehet.

- **MTU-méret/töredezettség**: az útvonal töredezettsége az adatbevitel késése, illetve a nem a rendeléstől érkező csomagok esetében lehet, ami befolyásolhatja a csomagok kézbesítését.

A traceroute jó eszköz a hálózati teljesítmény jellemzőinek (például a csomagok elvesztésének és késésének) mérésére a forrás-eszköz és a célként megadott eszköz közötti hálózati útvonalon.

### <a name="network-design-considerations"></a>A hálózat kialakításával kapcsolatos szempontok

A cikkben korábban tárgyalt megfontolások mellett a virtuális hálózat topológiája hatással lehet a hálózat teljesítményére. Például egy olyan sugaras kialakítás, amely globálisan backhauls az egyközpontos virtuális hálózatra, hálózati késést fog bevezetni, ami hatással lesz az általános hálózati teljesítményre.

A hálózati forgalom által áthaladó hálózati eszközök száma is hatással lehet a teljes késésre. Például egy küllős és küllős kialakításban, ha a forgalom egy küllős hálózati virtuális készüléken és egy hub virtuális készüléken halad át az internetre való továbbítás előtt, a hálózati virtuális berendezések késést okozhatnak.

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure-régiók, virtuális hálózatok és késés

Az Azure-régiók több adatközpontból állnak, amelyek egy általános földrajzi területen belül találhatók. Előfordulhat, hogy ezek az adatközpontok nem fizikailag egymás mellett vannak. Bizonyos esetekben 10 kilométernél kevesebbet választanak. A virtuális hálózat logikai átfedésben van az Azure fizikai adatközpont-hálózat felett. A virtuális hálózat nem jelent semmilyen konkrét hálózati topológiát az adatközponton belül.

Az azonos virtuális hálózatban és alhálózatban lévő két virtuális gép lehet például különböző állványok, sorok vagy akár adatközpontokban is. Ezek elválaszthatók a száloptikai kábel vagy a Fibre Optic-kábel által elválasztott lábakkal. Ez a változat változó késést (néhány ezredmásodpercet) jelenthet a különböző virtuális gépek között.

A virtuális gépek földrajzi elhelyezkedése és a két virtuális gép közötti lehetséges késések a rendelkezésre állási csoportok és a Availability Zones konfigurációjában is befolyásolhatják. Az adatközpontok közötti távolság azonban régiónként jellemző, és elsősorban az adatközpont topológiája befolyásolja a régióban.

### <a name="source-nat-port-exhaustion"></a>Forrás NAT-portjának kimerülése

Az Azure-beli üzemelő példányok az Azure-on kívüli végpontokkal is kommunikálhatnak a nyilvános interneten és/vagy a nyilvános IP-térben. Ha egy példány kimenő kapcsolatokat kezdeményez, az Azure dinamikusan leképezi a magánhálózati IP-címet egy nyilvános IP-címhez. Miután az Azure létrehozta ezt a leképezést, a kimenő forgalomból származó folyamat visszaadott forgalma elérheti azt a magánhálózati IP-címet is, ahol a folyamat származik.

Minden kimenő kapcsolatok esetében a Azure Load Balancernak meg kell őriznie ezt a leképezést egy bizonyos ideig. Az Azure több-bérlős jellegéből adódóan ez a leképezés minden virtuális gép kimenő forgalmához erőforrás-igényes lehet. Így az Azure-Virtual Network konfigurációján alapuló korlátok vannak beállítva. Azt is megteheti, hogy egy Azure-beli virtuális gép csak bizonyos számú kimenő kapcsolatot tud kiszolgálni egy adott időpontban. Ha eléri ezeket a korlátokat, a virtuális gép nem tud több kimenő kapcsolatot létesíteni.

Ez a viselkedés azonban konfigurálható. A SNAT és a SNAT-portok kimerülésével kapcsolatos további információkért tekintse meg [ezt a cikket](../load-balancer/load-balancer-outbound-connections.md).

## <a name="measure-network-performance-on-azure"></a>Hálózati teljesítmény mérése az Azure-ban

A cikk teljesítménybeli maximális száma a két virtuális gép közötti hálózati késéssel/oda-vissza (RTT) kapcsolatos. Ez a szakasz néhány javaslatot tartalmaz a késés/RTT tesztelésére és a TCP-teljesítmény és a virtuálisgép-hálózat teljesítményének tesztelésére. Az ebben a szakaszban ismertetett módszerekkel beállíthatja és tesztelheti a korábban tárgyalt TCP/IP-és hálózati értékeket. A késés, az MTU, a MSS és az ablak mérete értékeit a korábban megadott számításokhoz csatlakoztathatja, és összehasonlíthatja az elméleti maximális értékeket a tesztelés során megfigyelt tényleges értékekkel.

### <a name="measure-round-trip-time-and-packet-loss"></a>A kerekítési idő és a csomagok elvesztésének mérése

A TCP-teljesítmény nagy mértékben támaszkodik a RTT és a csomagok elvesztésére. A Windows és a Linux rendszerben elérhető PING segédprogram biztosítja a legegyszerűbb módszert a RTT és a csomagok elvesztésének mérésére. A PING kimenete a forrás és a cél minimális/maximális/átlagos késését mutatja. Emellett a csomagok elvesztését is megjeleníti. A PING alapértelmezés szerint az ICMP protokollt használja. A PsPing a TCP-RTT tesztelésére is használható. További információ: [PsPing](/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>TCP-kapcsolatok tényleges átviteli sebességének mérése

A NTttcp egy olyan eszköz, amellyel tesztelheti a Linux vagy Windows rendszerű virtuális gépek TCP-teljesítményét. Módosíthatja a különböző TCP-beállításokat, majd tesztelheti az előnyöket a NTttcp használatával. További információkért tekintse meg a következő forrásokat:

- [Sávszélesség/átviteli sebesség tesztelése (NTttcp)](./virtual-network-bandwidth-testing.md)

- [NTttcp segédprogram](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Virtuális gép tényleges sávszélességének mérése

A iPerf nevű eszköz használatával tesztelheti a különböző virtuálisgép-típusok teljesítményét, a gyorsított hálózatkezelést stb. a iPerf Linux és Windows rendszereken is elérhető. a iPerf a TCP-t vagy az UDP-t használhatja a teljes hálózati teljesítmény teszteléséhez. a iPerf TCP-átviteli sebességének tesztelését a cikkben tárgyalt tényezők befolyásolják (például késés és RTT). Így az UDP jobb eredményeket eredményezhet, ha csak a maximális átviteli sebességet szeretné tesztelni.

További információért lásd a következő cikkeket:

- [Expressroute hálózati teljesítményének hibaelhárítása](../expressroute/expressroute-troubleshooting-network-performance.md)

- [VPN teljesítményének érvényesítése virtuális hálózaton](../vpn-gateway/vpn-gateway-validate-throughput-to-vnet.md)

### <a name="detect-inefficient-tcp-behaviors"></a>Nem hatékony TCP-viselkedések észlelése

A csomagok rögzítése során az Azure-ügyfeleknek TCP-jelzőket (SACK, DUP ACK, újraküldést és gyors újraküldést) tartalmazó TCP-csomagokat láthatnak, amelyek hálózati teljesítménnyel kapcsolatos problémákat jelezhetnek. Ezek a csomagok kifejezetten jelzik a csomagok elvesztését eredményező hálózati hatékonyságot. A csomagok elvesztését azonban nem feltétlenül az Azure teljesítményproblémák okozzák. Teljesítményproblémák merülhetnek fel az alkalmazással kapcsolatos problémák, az operációs rendszer problémái vagy az Azure platformmal közvetlenül nem kapcsolódó egyéb problémák miatt.

Azt is vegye figyelembe, hogy egyes újraküldési és ismétlődő nyugták a hálózatban normálisak. A TCP-protokollok megbízhatónak lettek kialakítva. A csomagok rögzítésében a TCP-csomagok bizonyítása nem feltétlenül jelent rendszerszintű hálózati problémát, kivéve, ha túlzottak.

Ezek a csomagok azonban arra utalnak, hogy a TCP-átviteli sebesség nem tudja elérni a maximális teljesítményt, a cikk más részeiben tárgyalt okok miatt.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az Azure-beli virtuális gépek TCP/IP-teljesítményének finomhangolását, érdemes elolvasnia a [virtuális hálózatok megtervezésével](./virtual-network-vnet-plan-design-arm.md) kapcsolatos egyéb szempontokat, vagy többet is [megtudhat a virtuális hálózatok csatlakoztatásáról és konfigurálásáról](./index.yml).