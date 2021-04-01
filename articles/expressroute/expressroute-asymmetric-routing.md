---
title: 'Azure ExpressRoute: aszimmetrikus Útválasztás'
description: Ez a cikk végigvezeti azokon a problémákon, amelyek az aszimmetrikus útválasztással szembesülnek olyan hálózatokban, amelyeken több hivatkozás található a célhelyre.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 0713c52c7f07db93d9ae9084062ef2c3b25a9074
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97560508"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Aszimmetrikus útválasztás több hálózati elérési úttal
Ez a cikk azt ismerteti, hogyan lehet a hálózati forgalom különböző elérési utakat készíteni, ha több útvonal is elérhető a hálózati forrás és a cél között.

Az aszimmetrikus útválasztás megértéséhez két fogalmat kell tudnia. Az első a több hálózati elérési út hatása. A másik az eszközök, például a tűzfal állapotának megőrzése. Az ilyen típusú eszközöket állapot-nyilvántartó eszközöknek nevezzük. Ha ez a két tényező össze van foglalva, létrehozhat egy olyan forgatókönyvet, amelyben a hálózati forgalom eldobásra kerül az állapot-nyilvántartó eszköz által.  A rendszer elveti a forgalmat, mert nem állapította meg, hogy a forgalom magától származik.

## <a name="multiple-network-paths"></a>Több hálózati elérési út
Ha egy vállalati hálózat csak egy internetszolgáltatón keresztül kapcsolódik az internethez, az internetről érkező és az onnan érkező összes forgalom azonos elérési útra utazik. Gyakori, hogy a vállalatok több áramkört vásárolnak, hogy redundáns útvonalakat hozzanak létre a hálózati üzemidő javítása érdekében. Ilyen típusú konfiguráció esetén lehetséges, hogy a forgalom az internetre mutat, és egy másik hivatkozáson keresztül tér vissza. Ezt a forgatókönyvet gyakran nevezzük aszimmetrikus útválasztásnak. Az aszimmetrikus útválasztásban a visszaadott hálózati forgalom az eredeti kimenő folyamattól eltérő elérési utat vesz igénybe.

![Több elérési úttal rendelkező hálózatok](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Bár az aszimmetrikus útválasztás általában akkor fordul elő, amikor az internethez kerül. Akkor is előfordul, ha több elérési út kombinációja lesz bevezetve. Az első példa az internetes elérési út és a saját elérési út, amely ugyanarra a célhelyre mutat. A második példa az, ha több privát elérési úttal is rendelkezik, amelyek ugyanazon a célhelyen fognak megjelenni.

A forrás és a cél közötti útvonalon található minden útválasztó a cél eléréséhez szükséges legjobb útvonalat fogja kiszámítani. Az útválasztó a lehető legjobb elérési utat határozza meg két fő tényező alapján:

* A külső hálózatok közötti útválasztást az általában csak BGP néven emlegetett útválasztási protokoll, a peremátjáró protokoll (Border Gateway Protocol) határozza meg. A BGP átveszi a szomszédok hirdetményeit, és különböző műveleteket futtat rajtuk annak érdekében, hogy megállapítsa a célhoz vezető legjobb utat. A legjobb útvonalat az útválasztó beírja az útvonaltáblába.
* Az útvonalhoz társított alhálózati maszk hossza befolyásolja az útválasztási útvonalakat. Ha egy útválasztó több hirdetményt kap ugyanahhoz az IP-címhez, akkor az útválasztó a hosszabb alhálózati maszk elérési útját fogja kiválasztani, mert az adott útvonalnak minősül.

## <a name="stateful-devices"></a>Állapot-nyilvántartó eszközök
Az útválasztók az útválasztás megállapítása során a csomag IP-fejlécét veszik alapul. Bizonyos eszközök még mélyebben megvizsgálják a csomagot. Ezek az eszközök jellemzően a 4-es rétegű Transmission Control Protocol (TCP) vagy a User Datagram Protocol (UDP), vagy akár a 7. rétegbeli (Application Layer) fejléceket tekintik meg. Ezek az eszközök általában biztonsági vagy sávszélesség-optimalizáló eszközök. 

Gyakori állapot-nyilvántartó eszközök például a tűzfalak. A tűzfal a különböző feltételek alapján engedélyezi vagy elutasítja a csomagok továbbítását a felületén. Ezek a feltételek többek között a protokoll, a TCP/UDP-port és az URL-fejlécek esetében nem korlátozódnak. A csomagok ellenőrzésének ezen szintje nagy mennyiségű feldolgozási terhelést okozhat az eszközön. 

A teljesítmény javítása érdekében a tűzfalak általában csak az adatfolyamok első csomagját vizsgálják meg. Ha lehetővé teszi, hogy a csomag áthaladjon a felületén, megtartja a folyamat adatait az állapot táblájában. Az ehhez a folyamathoz kapcsolódó bármely további csomagot a kezdeti meghatározás alapján engedélyeznek. Egy meglévő folyamat részét képező csomag olyan tűzfalon is megérkezik, amelyből nem származik. Mivel nem rendelkezik a kezdeti folyamattal kapcsolatos előzetes állapotinformációkat, a tűzfal eldobja a csomagot.

## <a name="asymmetric-routing-with-expressroute"></a>Aszimmetrikus útválasztás az ExpressRoute-tal
Ha az Azure ExpressRoute segítségével csatlakozik a Microsofthoz, a hálózata az alábbiak szerint módosul:

* Több kapcsolat jön létre a Microsofthoz. Az egyik kapcsolat a meglévő internetkapcsolat, a másik pedig a ExpressRoute-kapcsolaton keresztül történik. Előfordulhat, hogy a Microsoft felé irányuló egyes forgalom az internetkapcsolaton keresztül történik, de a ExpressRoute-kapcsolaton keresztül tér vissza. Ugyanez akkor is előfordulhat, ha a forgalom áthalad a ExpressRoute, de az Internet elérési útját adja vissza.
* A ExpressRoute áramkör több megadott IP-címet kapott. Így ha a hálózatról érkező forgalom a ExpressRoute-on keresztül kínált szolgáltatásokhoz kapcsolódik a Microsofthoz, az útválasztók mindig az ExpressRoute-kapcsolatokat fogják előnyben részesíteni.

Ha meg szeretné ismerni, hogy a két változás milyen hatással van a hálózatra, tekintsük át az egyes forgatókönyveket. Tegyük fel, hogy van egy áramköre az internethez, és az összes Microsoft-szolgáltatást az interneten keresztül használja. A hálózatról a Microsoft felé irányuló adatforgalom ugyanazon az internetes kapcsolaton halad át, és egy tűzfalon halad át. A tűzfal rögzíti a folyamatot, amikor az első csomagot látja. A rendszer minden, a beszélgetést követő csomagot engedélyezett, mert a folyamat létezik az állapot táblában.

![Aszimmetrikus útválasztás az ExpressRoute-tal](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Ezután létrehoz egy ExpressRoute áramkört a Microsoft által a ExpressRoute-en keresztül kínált szolgáltatások felhasználásához. A Microsoft egyéb szolgáltatásai az interneten keresztül kerülnek felhasználásra. Egy különálló tűzfalat helyez üzembe a peremén, amely ExpressRoute-kapcsolathoz csatlakozik. A Microsoft konkrétabb előtagokat hirdet meg a hálózaton az egyes szolgáltatások ExpressRoute. Az útválasztási infrastruktúra ezért ezeknél az előtagoknál az ExpressRoute elérés utat fogja előnyben részesíteni. 

Ha nem hirdeti meg nyilvános IP-címeit a Microsoftnak a ExpressRoute-en keresztül. A Microsoft az interneten keresztül kommunikál a nyilvános IP-címekkel. A hálózatról a Microsoftnak küldött forgalom a ExpressRoute kapcsolatot használja, de a Microsoft által visszaadott forgalom az Internet elérési útját használja. Ha a peremhálózati tűzfal egy válasz-csomagot lát egy olyan folyamat számára, amely nem ismeri a szolgáltatást, akkor a rendszer eldobja ezeket a csomagokat.

Ha úgy dönt, hogy ugyanazt a hálózati címfordítási (NAT-) készletet hirdeti meg a ExpressRoute és az Internet számára. A privát IP-címeken a hálózatban lévő ügyfelekkel hasonló problémák jelennek meg. Az olyan szolgáltatásokra vonatkozó kérések, mint a Windows Update, az interneten keresztül mennek keresztül, mert a szolgáltatások IP-címei nem jelennek meg a ExpressRoute. A visszaküldött forgalom azonban visszakerül a ExpressRoute-on keresztül. Mivel a Microsoft az internettől és a ExpressRoute azonos alhálózati maszkkal kapott IP-címet, az előnyben részesített útvonal mindig ExpressRoute. Ha egy tűzfal vagy egy másik, a ExpressRoute-kapcsolatban a hálózat peremén lévő, állapot-nyilvántartó eszköz nem rendelkezik előzetes információval a folyamatról, akkor a rendszer eldobja ezeket a csomagokat.

## <a name="asymmetric-routing-solutions"></a>Megoldások az aszimmetrikus útválasztásra
Az aszimmetrikus útválasztás problémájának megoldásához két lehetőség közül választhat. Az első az Útválasztás, a második pedig a forrás-alapú NAT (SNAT) használatával történik.

### <a name="routing"></a>Útválasztás
Győződjön meg arról, hogy a nyilvános IP-címek a megfelelő nagy kiterjedésű hálózati (WAN) kapcsolatokra vannak hirdetve. Ha például az internetet szeretné használni a hitelesítési forgalomhoz és a ExpressRoute a levelek forgalmához. Ne hirdesse a Active Directory összevonási szolgáltatások (AD FS) (AD FS) nyilvános IP-címeit a ExpressRoute-en keresztül. Győződjön meg arról is, hogy nem teszi elérhetővé a helyszíni AD FS-kiszolgálót azokra az IP-címekre, amelyeket az útválasztó fogad a ExpressRoute keresztül. Az ExpressRoute-on keresztül kapott útvonalak konkrétabbak, ezért a rendszer az ExpressRoute-on keresztüli elérési utat részesíti előnyben a Microsoft felé irányuló hitelesítési forgalom esetén. Ha nem veszi figyelembe, hogy az Útválasztás hogyan történik a hálózaton, aszimmetrikus útválasztási problémák merülhetnek fel.

Ha hitelesítésre szeretné használni a ExpressRoute-t, győződjön meg arról, hogy a ExpressRoute-en keresztül, NAT nélkül hirdet AD FS nyilvános IP-címeket. Ha ezt a módszert konfigurálja, a Microsofttól származó forgalom a helyszíni AD FS-kiszolgálóra kerül át a ExpressRoute. A hálózatról a Microsoft felé irányuló visszaküldött forgalom a ExpressRoute-t használja, mivel ez az előnyben részesített útvonal az interneten.

### <a name="source-based-nat"></a>Forrásalapú NAT
Az aszimmetrikus útválasztási probléma megoldásának másik módja a SNAT használata. Választhatja például, hogy ne Hirdessen egy helyszíni Simple Mail Transfer Protocol (SMTP) kiszolgáló nyilvános IP-címét a ExpressRoute-en keresztül. Ehelyett az ilyen típusú kommunikációhoz használni kívánja az internetet. A Microsofttól érkező, a helyszíni SMTP-kiszolgáló felé irányuló kérelem áthalad az interneten. A beérkező kérelmet az SNAT használatával egy belső IP-címre irányítja át. Az SMTP-kiszolgálóról érkező adatforgalom a ExpressRoute helyett a peremhálózati tűzfalon fog megjelenni (amelyet a NAT-hoz használ). Ennek eredményeképpen a visszaküldött forgalom az Internet elérési útját fogja megtenni.

![A forrásalapú NAT hálózati konfigurációja](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Az aszimmetrikus útválasztás észlelése
A Traceroute a legjobb módszer annak vizsgálatához, hogy a forgalom a várt útvonalat járja-e be. Ha várhatóan a helyszíni SMTP-kiszolgálóról a Microsoft felé irányuló forgalom az Internet elérési útját veszi át, a várt traceroute az SMTP-kiszolgálóról Microsoft 365ra. Az eredmény azt ellenőrzi, hogy a forgalom valóban elhagyja-e a hálózatot az internet felé, és nem a ExpressRoute felé.

