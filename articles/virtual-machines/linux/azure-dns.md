---
title: DNS-névfeloldási beállítások Linux rendszerű virtuális gépekhez
description: A Linux rendszerű virtuális gépek névfeloldási forgatókönyvei az Azure IaaS, beleértve a megadott DNS-szolgáltatásokat, a hibrid külső DNS-t és a saját DNS-kiszolgálóját.
author: RicksterCDN
ms.service: virtual-machines
ms.subservice: networking
ms.topic: conceptual
ms.date: 10/19/2016
ms.author: rclaus
ms.collection: linux
ms.openlocfilehash: e689e934f11e3cc2a621f25525e507a72a7044b8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556791"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>DNS-névfeloldási lehetőségek Linux rendszerű virtuális gépekhez az Azure-ban
Az Azure alapértelmezés szerint DNS-névfeloldást biztosít az egyetlen virtuális hálózatban lévő összes virtuális géphez. Saját DNS-névfeloldási megoldást is alkalmazhat saját DNS-szolgáltatásainak konfigurálásával az Azure-gazdagépeken futó virtuális gépeken. A következő forgatókönyvek segíthetnek kiválasztani az adott helyzetnek megfelelőt.

* [Az Azure által biztosított névfeloldás](#name-resolution-that-azure-provides)
* [Névfeloldás saját DNS-kiszolgáló használatával](#name-resolution-using-your-own-dns-server)

A használt névfeloldás típusa attól függ, hogy a virtuális gépek és a szerepkör-példányok hogyan kell kommunikálni egymással.

Az alábbi táblázat a forgatókönyveket és a hozzájuk tartozó névfeloldási megoldásokat szemlélteti:

| **Forgatókönyv** | **Megoldás** | **Utótag** |
| --- | --- | --- |
| Az azonos virtuális hálózatban lévő szerepkör-példányok vagy virtuális gépek közötti névfeloldás |Az Azure által biztosított névfeloldás |állomásnév vagy teljes tartománynév (FQDN) |
| Névfeloldás a különböző virtuális hálózatokban lévő szerepkör-példányok vagy virtuális gépek között |Ügyfél által felügyelt DNS-kiszolgálók, amelyek a virtuális hálózatok közötti lekérdezéseket továbbítják az Azure (DNS-proxy) általi feloldáshoz. Lásd: [névfeloldás a saját DNS-kiszolgáló használatával](#name-resolution-using-your-own-dns-server). |Csak FQDN |
| Helyszíni számítógépek és szolgáltatások neveinek feloldása szerepkör-példányokból vagy virtuális gépekből az Azure-ban |Ügyfél által felügyelt DNS-kiszolgálók (például helyszíni tartományvezérlő, helyi írásvédett tartományvezérlő vagy a zónaletöltés használatával szinkronizált másodlagos DNS-kiszolgáló). Lásd: [névfeloldás a saját DNS-kiszolgáló használatával](#name-resolution-using-your-own-dns-server). |Csak FQDN |
| Azure-beli gazdagépek feloldása helyszíni számítógépekről |Továbbítja a lekérdezéseket egy ügyfél által felügyelt DNS-proxykiszolgálóhoz a megfelelő virtuális hálózaton. A proxykiszolgáló lekérdezéseket továbbít az Azure-nak a feloldáshoz. Lásd: [névfeloldás a saját DNS-kiszolgáló használatával](#name-resolution-using-your-own-dns-server). |Csak FQDN |
| Fordított DNS belső IP-címekhez |[Névfeloldás saját DNS-kiszolgáló használatával](#name-resolution-using-your-own-dns-server) |n.a. |

## <a name="name-resolution-that-azure-provides"></a>Az Azure által biztosított névfeloldás
A nyilvános DNS-nevek feloldásával együtt az Azure belső névfeloldást biztosít a virtuális gépek és az azonos virtuális hálózatban lévő szerepkör-példányok számára. A Azure Resource Manageron alapuló virtuális hálózatok esetében a DNS-utótag konzisztens a virtuális hálózaton belül; a teljes tartománynév nem szükséges. A DNS-neveket mindkét hálózati adapterhez (NIC) és virtuális géphez is hozzá lehet rendelni. Bár az Azure által biztosított névfeloldás nem igényel konfigurálást, az összes telepítési forgatókönyv esetében nem megfelelő választás az előző táblázatban látható módon.

### <a name="features-and-considerations"></a>Funkciók és szempontok
**Szolgáltatások**

* Az Azure által biztosított névfeloldás használatához nincs szükség konfigurációra.
* Az Azure által biztosított névfeloldási szolgáltatás nagyon elérhető. A saját DNS-kiszolgálók fürtjének létrehozása és kezelése nem szükséges.
* Az Azure által biztosított névfeloldási szolgáltatás a saját DNS-kiszolgálókkal együtt a helyszíni és az Azure-beli állomásnevek feloldására is használható.
* A névfeloldás a virtuális hálózatokban lévő virtuális gépek között, a teljes tartománynevet nem kell megadnia.
* Az automatikusan létrehozott nevek helyett az üzemelő példányokat legjobban leíró állomásnévket is használhat.

**Szempontok**

* Az Azure által létrehozott DNS-utótag nem módosítható.
* Saját rekordjait nem lehet manuálisan regisztrálni.
* A WINS és a NetBIOS nem támogatott.
* Az állomásnévnek DNS-kompatibilisnek kell lennie.
    A névnek csak 0-9, a-z és '-' értékkel kell rendelkeznie, és nem kezdődhet vagy végződhet "-" értékkel. Lásd: RFC 3696, 2. szakasz.
* A DNS-lekérdezési forgalom szabályozása minden egyes virtuális gép esetében szabályozható. A szabályozás nem befolyásolja a legtöbb alkalmazást.  Ha a kérelmek szabályozása megfigyelhető, győződjön meg arról, hogy az ügyféloldali gyorsítótárazás engedélyezve van.  További információ: [Az Azure által biztosított névfeloldás legtöbbje](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Az Azure által biztosított névfeloldás legtöbbje
**Ügyféloldali gyorsítótárazás:**

Bizonyos DNS-lekérdezések küldése nem a hálózaton keresztül történik. Az ügyféloldali gyorsítótárazás segítségével csökkentheti a késést, és javíthatja a hálózat következetlenségét azáltal, hogy feloldja az ismétlődő DNS-lekérdezéseket egy helyi gyorsítótárból. A DNS-rekordok élettartama (TTL), amely lehetővé teszi a gyorsítótár számára, hogy a lehető leghosszabb ideig tárolja a rekordot a rekord frissességének befolyásolása nélkül. Ennek eredményeképpen az ügyféloldali gyorsítótárazás a legtöbb esetben megfelelő.

Egyes Linux-disztribúciók alapértelmezés szerint nem tartalmazzák a gyorsítótárazást. Azt javasoljuk, hogy adjon hozzá egy gyorsítótárat az egyes linuxos virtuális gépekhez, miután megtörtént, hogy még nem létezik helyi gyorsítótár.

Számos különböző DNS-gyorsítótárazási csomag (például DNSMasq) érhető el. A DNSMasq telepítésének lépései a leggyakoribb disztribúciókban:

**Ubuntu (resolvconf-t használ)**
  * Telepítse a DNSMasq csomagot ("sudo apt-get install DNSMasq").

**SUSE (netconf használ)**:
1. Telepítse a DNSMasq csomagot ("sudo Zypper install DNSMasq").
2. Engedélyezze a DNSMasq szolgáltatást ("systemctl engedélyezése DNSMasq. Service").
3. Indítsa el a DNSMasq szolgáltatást ("systemctl Start DNSMasq. Service").
4. Szerkessze a "/etc/sysconfig/network/config" kifejezést, és módosítsa NETCONFIG_DNS_FORWARDER = "" kifejezést "DNSMasq" értékre.
5. Frissítse a resolv. conf fájlt ("netconfig Update") a gyorsítótár helyi DNS-feloldóként való beállításához.

**CentOS by Rogue Wave Software (korábban OpenLogic; hálózatkezelő használ)**
1. Telepítse a DNSMasq csomagot ("sudo yum install DNSMasq").
2. Engedélyezze a DNSMasq szolgáltatást ("systemctl engedélyezése DNSMasq. Service").
3. Indítsa el a DNSMasq szolgáltatást ("systemctl Start DNSMasq. Service").
4. Adja hozzá a "előtag tartomány neve-kiszolgálók 127.0.0.1;" kifejezést a "/etc/dhclient-eth0.conf" értékhez.
5. A hálózati szolgáltatás ("a szolgáltatás hálózati újraindítása") újraindítása a gyorsítótár helyi DNS-feloldóként való beállításához

> [!NOTE]
> : A "DNSMasq" csomag csak a Linux rendszerhez elérhető számos DNS-gyorsítótár egyike. Használat előtt tekintse át az igényeinek megfelelő megfelelőségét, valamint azt, hogy nincs-e telepítve más gyorsítótár.
>
>

**Ügyféloldali újrapróbálkozások**

A DNS elsődlegesen UDP protokoll. Mivel az UDP protokoll nem garantálja az üzenetek kézbesítését, maga a DNS-protokoll kezeli az újrapróbálkozási logikát. Minden DNS-ügyfél (operációs rendszer) különböző újrapróbálkozási logikát tud kimutatni a létrehozó beállításaitól függően:

* A Windows operációs rendszer újrapróbálkozik egy másodperc után, majd ismét egy másik kettő, négy és egy másik négy másodperc múlva.
* Az alapértelmezett Linux-telepítő öt másodperc elteltével próbálkozik újra.  Ezt úgy kell megváltoztatnia, hogy ötször próbálkozzon újra egy másodperces időközökkel.  

Ha szeretné megtekinteni a Linux rendszerű virtuális gépek aktuális beállításait, a "Cat/etc/resolv.conf", és megtekintheti a "beállítások" sort, például:

```config-conf
options timeout:1 attempts:5
```

A resolv. conf fájl automatikusan jön létre, és nem szerkeszthető. A "beállítások" sort hozzáadó konkrét lépések eloszlás szerint változnak:

**Ubuntu** (resolvconf-t használ)
1. Adja hozzá a Options sort a következőhöz: "/etc/resolvconf/resolv.conf.d/Head".
2. A frissítéshez futtassa a "resolvconf-u" parancsot.

**SUSE** (netconf használ)
1. Adja hozzá a "timeout: 1 kísérlet: 5" értéket a NETCONFIG_DNS_RESOLVER_OPTIONS = "" paraméterhez a következőben: "/etc/sysconfig/network/config".
2. A frissítéshez futtassa a "netconfig Update" parancsot.

**CentOS by Rogue Wave Software (korábban OpenLogic) (a** hálózatkezelő-t használja)
1. Adja hozzá a "RES_OPTIONS =" időtúllépés: 1 kísérlet: 5 "" értéket a "/etc/sysconfig/network" értékhez.
2. A frissítéshez futtassa a "Service Network restart" parancsot.

## <a name="name-resolution-using-your-own-dns-server"></a>Névfeloldás saját DNS-kiszolgáló használatával
A névfeloldási igények az Azure által biztosított funkciókon túlmutatnak. Előfordulhat például, hogy a virtuális hálózatok között DNS-feloldásra van szükség. Ennek a forgatókönyvnek a biztosításához használhatja a saját DNS-kiszolgálóit.  

A virtuális hálózaton belüli DNS-kiszolgálók továbbítják a DNS-lekérdezéseket az Azure rekurzív feloldóinak az azonos virtuális hálózatban lévő állomásnevek feloldásához. Az Azure-ban futó DNS-kiszolgáló például válaszolhat a saját DNS-zónákra vonatkozó DNS-lekérdezésekre, és az összes többi lekérdezést továbbítja az Azure-nak. Ez a funkció lehetővé teszi a virtuális gépek számára, hogy megtekintsék a zóna fájljaiban és az Azure által biztosított (a továbbítón keresztüli) gazdagépekben található bejegyzéseket. Az Azure rekurzív feloldóinak hozzáférése a virtuális IP-168.63.129.16 keresztül történik.

A DNS-továbbítás a virtuális hálózatok közötti DNS-feloldást is lehetővé teszi, és lehetővé teszi a helyszíni gépek számára az Azure által biztosított állomásnevek feloldását. A virtuális gép állomásneve megoldásához a DNS-kiszolgáló virtuális gépnek ugyanabban a virtuális hálózaton kell lennie, és úgy kell konfigurálni, hogy az állomásnév-lekérdezéseket továbbítsa az Azure-nak. Mivel a DNS-utótag különbözik az egyes virtuális hálózatokban, a feltételes továbbítási szabályok segítségével a megfelelő virtuális hálózatra küldhet DNS-lekérdezéseket a megoldáshoz. Az alábbi képen két virtuális hálózat és egy helyszíni hálózat jelenik meg, amely a virtuális hálózatok közötti DNS-feloldást végzi a következő módszer használatával:

![DNS-feloldás virtuális hálózatok között](./media/azure-dns/inter-vnet-dns.png)

Ha az Azure által biztosított névfeloldást használ, a belső DNS-utótagot a rendszer DHCP használatával adja meg az egyes virtuális gépek számára. Ha saját névfeloldási megoldást használ, ezt az utótagot a rendszer nem adja meg a virtuális gépek számára, mert az utótag más DNS-architektúrákkal is ütközik. Ha a gépeket teljes tartománynévvel vagy a virtuális gépek utótagjának konfigurálásával szeretné megtekinteni, a PowerShell vagy az API használatával határozhatja meg az utótagot:

* Azure Resource Manager által felügyelt virtuális hálózatok esetében az utótag a [hálózati kártya](/rest/api/virtualnetwork/networkinterfaces) erőforrásán keresztül érhető el. A parancs futtatásával is `azure network public-ip show <resource group> <pip name>` megjelenítheti a nyilvános IP-cím részleteit, beleértve a hálózati adapter teljes tartománynevét is.

Ha az Azure-ba irányuló lekérdezések továbbítása nem felel meg az igényeinek, meg kell adnia a saját DNS-megoldását.  A DNS-megoldásnak a következőket kell tennie:

* Adja meg a megfelelő állomásnév-feloldást, például a [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md)-n keresztül. Ha a DDNS-t használja, előfordulhat, hogy le kell tiltania a DNS-rekordok kitakarítását. Az Azure DHCP-bérletei nagyon hosszúak, a kitakarítások pedig a DNS-rekordok idő előtt eltávolíthatók.
* Adjon meg megfelelő rekurzív megoldást a külső tartománynevek feloldásának engedélyezéséhez.
* Elérhetőnek kell lennie (TCP és UDP a 53-as porton) az általa kiszolgált ügyfelektől, és elérhetővé kell tenni az internetet.
* Védelmet biztosít az internetről a külső ügynökök által jelentett fenyegetések enyhítése érdekében.

> [!NOTE]
> A legjobb teljesítmény érdekében, ha Azure DNS-kiszolgálókon lévő virtuális gépeket használ, tiltsa le az IPv6 protokollt, és rendeljen hozzá egy [példány-szintű nyilvános IP-címet](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) minden DNS-kiszolgáló virtuális géphez.  
>
>
