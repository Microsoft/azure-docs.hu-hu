---
title: Azure-beli üzemi hálózat
description: Ismerje meg az Azure éles hálózatát. A hálózattal létesített kapcsolat létrehozásához tekintse meg a biztonsági hozzáférési módszereket és a védelmi mechanizmusokat.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 724c2ee2d74ca9cab11280e92faac71c2cb2218e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87543809"
---
# <a name="the-azure-production-network"></a>Az Azure éles hálózata
Az Azure éles hálózat felhasználói a saját Azure-alkalmazásaikat és az üzemi hálózatot kezelő belső Azure-támogatási személyzetet is magukban foglalják. Ez a cikk az Azure üzemi hálózattal létesített kapcsolatok létrehozásához szükséges biztonsági hozzáférési módszereket és védelmi mechanizmusokat ismerteti.

## <a name="internet-routing-and-fault-tolerance"></a>Internetes Útválasztás és hibatűrés
A globálisan redundáns belső és külső Azure Domain Name Service (DNS) infrastruktúra, amely több elsődleges és másodlagos DNS-kiszolgáló fürttel is rendelkezik, hibatűrést biztosít. Ugyanakkor a további Azure-alapú hálózati biztonsági vezérlők, például a NetScaler, az elosztott szolgáltatásmegtagadási (DDoS) támadások megelőzése és Azure DNS szolgáltatások integritásának védelme érdekében használatosak.

A Azure DNS-kiszolgálók több adatközpont-létesítményben találhatók. A Azure DNS implementációja tartalmazza a másodlagos és elsődleges DNS-kiszolgálók hierarchiáját az Azure-beli ügyfél-tartománynevek nyilvánosan való feloldásához. A tartománynevek általában egy CloudApp.net-címhez vannak feloldva, amely az ügyfél szolgáltatásához tartozó virtuális IP-címet (VIP) csomagolja. Az Azure-ban egyedi, a bérlői fordítás belső dedikált IP-címéhez (DIP) tartozó VIP-cím a virtuális IP-címekért felelős Microsoft-terheléselosztó.

Az Azure a földrajzilag elosztott Azure-adatközpontokban üzemel az USA-ban, és olyan legkorszerűbb útválasztási platformokra épül, amelyek robusztus, méretezhető építészeti szabványokat implementálnak. A jelentős funkciók közé tartoznak a következők:

- A többprotokollos feliratok váltásának (MPLS)-alapú adatforgalmának megtervezése, amely hatékony kapcsolat-kihasználtságot és a szolgáltatás biztonságos romlását biztosítja, ha leállás van.
- A hálózatok megvalósítása a "szükséges plusz egy" (N + 1) redundancia-architektúrával vagy annál jobb.
- Külsőleg az adatközpontokat dedikált, nagy sávszélességű hálózati áramkörök biztosítják, amelyekkel a több mint 1 200 internetszolgáltatóval redundánsan csatlakoztathatók a tulajdonságok több, mint a több ponttal ellátott internetes szolgáltatónál. Ez a szolgáltatás legfeljebb 2 000 gigabájt/másodperc (GB/s) kapacitást biztosít.

Mivel a Microsoft saját hálózati áramköröket üzemeltet az adatközpontok között, ezek az attribútumok segítik az Azure-ajánlat 99,9 +%-os rendelkezésre állását a hagyományos külső internetszolgáltatók igénye nélkül.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Kapcsolódás a termelési hálózathoz és a kapcsolódó tűzfalakhoz
Az Azure hálózati internetes forgalomra vonatkozó szabályzata az USA-beli legközelebbi regionális adatközpontban található Azure-beli éles hálózatra irányítja a forgalmat. Mivel az Azure éles adatközpontok konzisztens hálózati architektúrát és hardvereket tartanak fenn, a forgalom folyamatának leírását a következőképpen alkalmazza az összes adatközpontra.

Ha az Azure-ra irányuló internetes forgalmat a legközelebbi adatközponthoz irányítják, a kapcsolat létrejön a hozzáférési útválasztók között. Ezek a hozzáférési útválasztók az Azure-csomópontok és az ügyfél által létrehozott virtuális gépek közötti adatforgalom elkülönítésére szolgálnak. A hálózati infrastruktúra eszközei a hozzáférési és a peremhálózati helyeken azok a határi pontok, ahol a bejövő és a kimenő adatforgalomra vonatkozó szűrők vannak alkalmazva. Ezek az útválasztók egy többrétegű hozzáférés-vezérlési lista (ACL) használatával konfigurálhatók a nemkívánatos hálózati forgalom szűréséhez, és szükség esetén a forgalmi díjszabási korlátok alkalmazásához. Az ACL által engedélyezett forgalmat a terheléselosztó irányítja át. A terjesztési útválasztók úgy vannak kialakítva, hogy csak a Microsoft által jóváhagyott IP-címeket engedélyezzék, a hamisítást, valamint az ACL-eket használó TCP-kapcsolatok létesítését.

A külső terheléselosztási eszközök a hozzáférési útválasztók mögött helyezkednek el, hogy hálózati címfordítást (NAT) végezzenek az internetről irányítható IP-címekről az Azure belső IP-címeire. Az eszközök a csomagokat az érvényes üzemi belső IP-címekre és portokra irányítják, és védelmi mechanizmusként működnek a belső éles hálózati címtartomány kihelyezésének korlátozására.

Alapértelmezés szerint a Microsoft az ügyfelek webböngészőjébe továbbított összes forgalom Hypertext Transfer Protocol védelmét (HTTPS) alkalmazza, beleértve a bejelentkezést és az összes forgalmat. A TLS v 1.2 használata lehetővé teszi, hogy egy biztonságos alagúton keresztül átáramlson a forgalom. A hozzáférés-vezérlési listák és az alapszintű útválasztók biztosítják, hogy a forgalom forrása konzisztens legyen a várt értékkel.

Ebben az architektúrában fontos különbséget tenni a hagyományos biztonsági architektúrához képest, hogy nincsenek dedikált hardveres tűzfalak, speciális behatolás-észlelési vagy megelőzési eszközök, illetve más biztonsági berendezések, amelyek általában az Azure éles környezetbe való csatlakozás előtt várhatók. Az ügyfelek általában az Azure-hálózaton lévő hardveres tűzfalakat várnak. Az Azure-ban azonban nincs alkalmazás. Szinte kizárólag azokat a biztonsági funkciókat építették be a szoftverbe, amelyek az Azure-környezetet futtatva robusztus, többrétegű biztonsági mechanizmusokat biztosítanak, beleértve a tűzfalak képességeit is. Emellett a kritikus biztonsági eszközök határának és a kapcsolódó terjeszkedésnek a hatóköre könnyebben kezelhető és leltárba kerül, ahogyan az az előző ábrán is látható, mert az Azure-t futtató szoftver kezeli.

## <a name="core-security-and-firewall-features"></a>Alapvető biztonsági és tűzfal-funkciók
Az Azure számos különböző szinten implementálja a szoftveres biztonsági és tűzfal-funkciókat, hogy kikényszerítse azokat a biztonsági funkciókat, amelyeket általában egy hagyományos környezetben kell elvárni az alapvető biztonsági engedélyezési határ védelme érdekében.

### <a name="azure-security-features"></a>Azure biztonsági funkciók
Az Azure a gazdagép-alapú szoftveres tűzfalakat implementálja az üzemi hálózaton belül. Az Azure-környezet számos alapvető biztonsági és tűzfal-funkciója található. Ezek a biztonsági funkciók az Azure-környezetben található részletes védelmi stratégiát tükrözik. Az Azure-beli ügyféladatokat a következő tűzfalak védik:

**Hypervisor tűzfal (csomagszűrő)**: Ez a tűzfal a hypervisorban van megvalósítva, és a Fabric Controller (FC) ügynök konfigurálja. Ez a tűzfal védi a virtuális gépen belül futó bérlőt a jogosulatlan hozzáféréstől. Alapértelmezés szerint a virtuális gépek létrehozásakor minden forgalom le lesz tiltva, majd az FC-ügynök szabályokat és kivételeket ad hozzá a szűrőben az engedélyezett forgalom engedélyezéséhez.

A szabályok két kategóriája van programozva:

- A **Számítógép konfigurációja vagy az infrastruktúra szabályai**: alapértelmezés szerint minden kommunikáció le van tiltva. Kivételek vannak, amelyek lehetővé teszik a virtuális gépek számára Dynamic Host Configuration Protocol (DHCP) kommunikáció és DNS-információk küldését és fogadását, valamint a "nyilvános" internetes forgalom továbbítását az FC-fürtön és az operációs rendszer aktiválási kiszolgálóján található többi virtuális gépre. Mivel a kimenő célhelyek engedélyezett listája nem tartalmazza az Azure útválasztó alhálózatait és más Microsoft-tulajdonságokat, a szabályok védelmi rétegként működnek.
- **Szerepkör-konfigurációs fájl szabályai**: a bérlők szolgáltatási modellje alapján határozza meg a bejövő ACL-eket. Ha például egy bérlő webes kezelőfelülettel rendelkezik a 80-as porton egy adott virtuális gépen, a 80-es port minden IP-címhez meg van nyitva. Ha a virtuális gépnek van futó feldolgozói szerepköre, a feldolgozói szerepkört csak a virtuális gép nyitja meg ugyanazon a bérlőn belül.

**Natív gazdagép tűzfala**: az Azure Service Fabric és az Azure Storage olyan natív operációs rendszeren fut, amelynek nincs hypervisora, ezért a Windows tűzfal az előző két szabályhoz van konfigurálva.

**Gazdagép tűzfala**: a gazdagép tűzfala védi a Hypervisort futtató gazdagép-partíciót. A szabályok úgy vannak programozva, hogy csak az FC és a Jump mezők használatával beszéljenek a gazdagép partícióján egy adott porton. A többi kivétel a DHCP-válasz és a DNS-válaszok engedélyezése. Az Azure egy számítógép-konfigurációs fájlt használ, amely a gazdagép-partícióhoz tartozó tűzfalszabályok sablonját tartalmazza. A gazda-tűzfal kivételt is tartalmaz, amely lehetővé teszi, hogy a virtuális gépek egy adott protokollon/porton keresztül kommunikáljanak a gazdagép összetevőivel, a hálózati kiszolgálóval és a metaadat-kiszolgálóval

**Vendég tűzfal**: a vendég operációs rendszer Windows tűzfalának része, amely az ügyfelek által az ügyfél virtuális gépeken és a tárolón konfigurálható.

Az Azure-képességekbe beépített további biztonsági funkciók a következők:

- A DIPsból származó IP-címeket hozzárendelő infrastruktúra-összetevők. Az interneten lévő támadók nem tudják kezelni az ilyen címekre irányuló forgalmat, mert nem érik el a Microsoftot. Az Internet Gateway-útválasztók kizárólag belső címekre irányuló csomagokat szűrnek, így nem lépnek be az éles hálózatba. Az egyetlen olyan összetevő, amely a virtuális IP-címekre irányuló forgalmat fogadja el, terheléselosztó.
- Az összes belső csomóponton megvalósított tűzfalak három elsődleges biztonsági architektúrával kapcsolatos szempontot biztosítanak az adott forgatókönyvhöz:

   - A tűzfalak a terheléselosztó mögé kerülnek, és bárhonnan fogadnak csomagokat. Ezek a csomagok külsőleg elérhetővé válnak, és a hagyományos peremhálózati tűzfalon lévő nyitott portoknak is megfelelnek.
   - A tűzfalak csak korlátozott számú címről fogadnak csomagokat. Ez a megfontolás a DDoS-támadások elleni védelmi mélyreható stratégia részét képezi. Ezek a kapcsolatok titkosítási szempontból hitelesítve vannak.
   - A tűzfalak csak a belső csomópontok kiválasztása esetén érhetők el. A csomagokat csak a forrás IP-címeinek enumerált listájából fogadják el, amelyek mindegyike az Azure-hálózatban található. Előfordulhat például, hogy a vállalati hálózat támadása ezekre a címekre irányuló kéréseket tartalmaz, de a támadások le lesznek tiltva, kivéve, ha a csomag címének egyike volt az Azure-hálózaton belüli enumerálási listán.
     - A peremhálózati hozzáférési útválasztó blokkolja a kimenő csomagokat, amelyek az Azure-hálózaton belüli címekre vannak konfigurálva a beállított statikus útvonalak miatt.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni arról, hogy a Microsoft Hogyan védi az Azure-infrastruktúrát, olvassa el a következő témakört:

- [Azure-létesítmények,-telephelyek és fizikai biztonság](physical-security.md)
- [Azure-infrastruktúra rendelkezésre állása](infrastructure-availability.md)
- [Azure Information System-összetevők és-határok](infrastructure-components.md)
- [Azure hálózati architektúra](infrastructure-network.md)
- [Azure SQL Database biztonsági funkciók](infrastructure-sql.md)
- [Azure-beli üzemi műveletek és felügyelet](infrastructure-operations.md)
- [Azure-infrastruktúra figyelése](infrastructure-monitoring.md)
- [Azure-infrastruktúra integritása](infrastructure-integrity.md)
- [Azure Customer-adatvédelem](protection-customer-data.md)
