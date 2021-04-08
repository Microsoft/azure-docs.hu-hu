---
title: Azure virtuális hálózatokon található erőforrások névfeloldása
titlesuffix: Azure Virtual Network
description: Névfeloldási forgatókönyvek az Azure IaaS, a hibrid megoldások, a különböző felhőalapú szolgáltatások, a Active Directory és a saját DNS-kiszolgáló használata között.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.custom: fasttrack-edit
ms.openlocfilehash: bbaf2fb99f1268a752fab4322078b0566a054d30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222853"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Azure virtuális hálózatokon található erőforrások névfeloldása

Attól függően, hogyan használja az Azure-t a IaaS, a Pásti és a hibrid megoldások üzemeltetésére, előfordulhat, hogy engedélyeznie kell a virtuális gépeket és a virtuális hálózatokban üzembe helyezett egyéb erőforrásokat az egymással való kommunikációhoz. Bár az IP-címek használatával is engedélyezheti a kommunikációt, sokkal egyszerűbb a könnyen megjegyezhető és nem módosítható nevek használata. 

Ha a virtuális hálózatokban üzembe helyezett erőforrásoknak a tartományneveket belső IP-címekre kell feloldaniuk, a következő három módszer egyikét használhatja:

* [Privát zónák Azure DNS](../dns/private-dns-overview.md)
* [Azure által biztosított névfeloldás](#azure-provided-name-resolution)
* [A saját DNS-kiszolgálót használó](#name-resolution-that-uses-your-own-dns-server) névfeloldás (amely a lekérdezéseket az Azure által biztosított DNS-kiszolgálókra továbbítja)

A használt névfeloldási típus attól függ, hogy az erőforrásoknak hogyan kell kommunikálniuk egymással. Az alábbi táblázat a forgatókönyveket és a hozzájuk tartozó névfeloldási megoldásokat szemlélteti:

> [!NOTE]
> Azure DNS privát zónák az előnyben részesített megoldás, és rugalmasságot biztosít a DNS-zónák és-rekordok kezeléséhez. További információkat az [Azure DNS privát tartományokhoz való használatát](../dns/private-dns-overview.md) ismertető cikkben olvashat.

> [!NOTE]
> Ha az Azure által megadott DNS-t használja, akkor a rendszer automatikusan alkalmazza a megfelelő DNS-utótagot a virtuális gépekre. Az összes többi beállításhoz teljes tartománynevet (FQDN) kell használnia, vagy manuálisan kell alkalmaznia a megfelelő DNS-utótagot a virtuális gépekre.

| **Forgatókönyv** | **Megoldás** | **DNS-utótag** |
| --- | --- | --- |
| Az azonos virtuális hálózatban található virtuális gépek és az Azure Cloud Services szerepkör példányai közötti névfeloldás ugyanazon a felhőalapú szolgáltatásban. | [Azure DNS privát zónák](../dns/private-dns-overview.md) vagy az [Azure által biztosított](#azure-provided-name-resolution) névfeloldás |Állomásnév vagy FQDN |
| Névfeloldás a különböző virtuális hálózatokban lévő virtuális gépek és a különböző felhőalapú szolgáltatások szerepkör-példányai között. |[Azure DNS privát zónák](../dns/private-dns-overview.md) vagy az ügyfél által felügyelt DNS-kiszolgálók a virtuális hálózatok közötti lekérdezéseket továbbítják az Azure (DNS-proxy) általi feloldáshoz. Lásd: [névfeloldás a saját DNS-kiszolgáló használatával](#name-resolution-that-uses-your-own-dns-server). |Csak FQDN |
| Névfeloldás egy Azure App Serviceból (webalkalmazás, függvény vagy bot) virtuális hálózati integráció használatával egy adott virtuális hálózatban lévő szerepkör-példányokhoz vagy virtuális gépekhez. |Az ügyfél által felügyelt DNS-kiszolgálók lekérdezést továbbítanak a virtuális hálózatok között az Azure (DNS-proxy) általi feloldáshoz. Lásd: [névfeloldás a saját DNS-kiszolgáló használatával](#name-resolution-that-uses-your-own-dns-server). |Csak FQDN |
| A App Service Web Apps névfeloldása ugyanazon a virtuális hálózaton lévő virtuális gépekre. |Az ügyfél által felügyelt DNS-kiszolgálók lekérdezést továbbítanak a virtuális hálózatok között az Azure (DNS-proxy) általi feloldáshoz. Lásd: [névfeloldás a saját DNS-kiszolgáló használatával](#name-resolution-that-uses-your-own-dns-server). |Csak FQDN |
| Az egyik virtuális hálózatban található App Service Web Apps névfeloldása egy másik virtuális hálózatban lévő virtuális gépekre. |Az ügyfél által felügyelt DNS-kiszolgálók lekérdezést továbbítanak a virtuális hálózatok között az Azure (DNS-proxy) általi feloldáshoz. Lásd: [névfeloldás a saját DNS-kiszolgáló használatával](#name-resolution-that-uses-your-own-dns-server). |Csak FQDN |
| A helyszíni számítógépek és szolgáltatások neveinek feloldása az Azure-beli virtuális gépek vagy szerepkör-példányok között. |Ügyfél által felügyelt DNS-kiszolgálók (helyszíni tartományvezérlő, helyi írásvédett tartományvezérlő vagy a zónaletöltés használatával szinkronizált másodlagos DNS-kiszolgáló). Lásd: [névfeloldás a saját DNS-kiszolgáló használatával](#name-resolution-that-uses-your-own-dns-server). |Csak FQDN |
| Az Azure-beli állomásnevek feloldása a helyszíni számítógépekről. |Továbbítsa a lekérdezéseket egy ügyfél által felügyelt DNS-proxykiszolgálóhoz a megfelelő virtuális hálózatban, a proxykiszolgáló lekérdezéseket továbbít az Azure-nak a feloldáshoz. Lásd: [névfeloldás a saját DNS-kiszolgáló használatával](#name-resolution-that-uses-your-own-dns-server). |Csak FQDN |
| Fordított DNS a belső IP-címekhez. |[Saját DNS-kiszolgálóval](#name-resolution-that-uses-your-own-dns-server) [Azure DNS saját zónák](../dns/private-dns-overview.md) vagy az [Azure által biztosított](#azure-provided-name-resolution) névfeloldás vagy névfeloldás. |Nem alkalmazható |
| A különböző felhőalapú szolgáltatásokban található virtuális gépek vagy szerepkör-példányok közötti névfeloldás a virtuális hálózatban nem. |Nem alkalmazható. A virtuális hálózatokon kívül nem támogatott a virtuális gépek és a szerepkör-példányok közötti kapcsolat a különböző felhőalapú szolgáltatásokban. |Nem alkalmazható|

## <a name="azure-provided-name-resolution"></a>Azure által biztosított névfeloldás

Az Azure által biztosított névfeloldás csak alapvető mérvadó DNS-képességeket biztosít. Ha ezt a beállítást használja, a DNS-zónák neveit és rekordjait automatikusan az Azure fogja felügyelni, és nem fogja tudni vezérelni a DNS-zónák nevét vagy a DNS-rekordok életciklusát. Ha a virtuális hálózatok teljes mértékben Kiemelt DNS-megoldásra van szüksége, akkor [Azure DNS privát zónákat](../dns/private-dns-overview.md) vagy [ügyfél által felügyelt DNS-kiszolgálókat](#name-resolution-that-uses-your-own-dns-server)kell használnia.

A nyilvános DNS-nevek feloldásával együtt az Azure belső névfeloldást biztosít a virtuális gépek és a szerepkör-példányok számára, amelyek ugyanazon a virtuális hálózaton vagy a felhőalapú szolgáltatáson belül találhatók. A felhőalapú szolgáltatásban lévő virtuális gépek és példányok ugyanazzal a DNS-utótaggal rendelkeznek, így az állomásnév önmagában is elegendő. A klasszikus üzemi modell használatával üzembe helyezett virtuális hálózatok esetében azonban a különböző felhőalapú szolgáltatások eltérő DNS-utótagokkal rendelkeznek. Ebben az esetben a teljes tartománynevet kell használnia a különböző felhőalapú szolgáltatások közötti nevek feloldásához. A Azure Resource Manager üzemi modellel üzembe helyezett virtuális hálózatok esetében a DNS-utótag konzisztens a virtuális hálózaton belüli összes virtuális gépen, így a teljes tartománynév nem szükséges. A DNS-neveket a virtuális gépekhez és a hálózati adapterekhez is hozzá lehet rendelni. Bár az Azure által biztosított névfeloldás nem igényel konfigurálást, az összes központi telepítési forgatókönyv esetében nem megfelelő választás az előző táblázatban részletezett módon.

> [!NOTE]
> A Cloud Services webes és feldolgozói szerepköreinek használatakor a szerepkör-példányok belső IP-címeit is elérheti az Azure Service Management REST API használatával. További információt a [Service Management REST API dokumentációjában](/previous-versions/azure/ee460799(v=azure.100))talál. A címe a szerepkör neve és a példány száma alapján történik. 
>

### <a name="features"></a>Funkciók

Az Azure által biztosított névfeloldás a következő funkciókat tartalmazza:
* Egyszerű használat. Nem igényel konfigurálást.
* Magas rendelkezésre állás. A saját DNS-kiszolgálók fürtjének létrehozása és kezelése nem szükséges.
* A szolgáltatást a saját DNS-kiszolgálóival együtt használhatja a helyszíni és az Azure-beli állomásnevek feloldásához.
* A névfeloldást a virtuális gépek és a szerepkör-példányok között lehet használni ugyanazon a felhőalapú szolgáltatáson belül anélkül, hogy teljes tartománynevet kellene használnia.
* Névfeloldást használhat a virtuális hálózatokban lévő virtuális hálózatok között, amelyek az Azure Resource Manager üzemi modellt használják, anélkül, hogy teljes tartománynevet kellene használnia. A klasszikus üzemi modellben lévő virtuális hálózatoknak teljes tartománynevet kell megkövetelniük a különböző felhőalapú szolgáltatásokban található nevek feloldásához. 
* Az automatikusan létrehozott nevek helyett használhatja az üzemelő példányokat legjobban leíró állomásneveket.

### <a name="considerations"></a>Megfontolandó szempontok

Az Azure által biztosított névfeloldás használatakor megfontolandó szempontok:
* Az Azure által létrehozott DNS-utótag nem módosítható.
* A DNS-címkeresés egy virtuális hálózatra terjed ki. Az egyik virtuális hálózathoz létrehozott DNS-neveket nem lehet feloldani más virtuális hálózatokból.
* Saját rekordjait nem lehet manuálisan regisztrálni.
* A WINS és a NetBIOS nem támogatott. A virtuális gépek nem láthatók a Windows Intézőben.
* A gazdagépek nevének DNS-kompatibilisnek kell lennie. A névnek csak 0-9, a-z és "-" értékkel kell rendelkeznie, és nem kezdődhet vagy végződhet "-" értékkel.
* A DNS-lekérdezési forgalom minden virtuális géphez le van szabályozva. A szabályozás nem befolyásolja a legtöbb alkalmazást. Ha a kérelmek szabályozása megfigyelhető, győződjön meg arról, hogy az ügyféloldali gyorsítótárazás engedélyezve van. További információ: [DNS-ügyfél konfigurációja](#dns-client-configuration).
* A klasszikus üzemi modellben csak az első 180 Cloud Services-beli virtuális gépek vannak regisztrálva minden egyes virtuális hálózathoz. Ez a korlát nem vonatkozik a Azure Resource Manager lévő virtuális hálózatokra.
* A Azure DNS IP-cím 168.63.129.16. Ez egy statikus IP-cím, és nem fog változni.

### <a name="reverse-dns-considerations"></a>Fordított DNS-megfontolások
A fordított DNS minden ARM-alapú virtuális hálózatban támogatott. Fordított DNS-lekérdezéseket adhat ki (PTR-lekérdezések) a virtuális gépek IP-címeinek a virtuális gépek teljes tartománynevére való leképezéséhez.
* A virtuális gépek IP-címeire vonatkozó összes PTR-lekérdezés a vmname űrlap teljes tartománynevét fogja visszaadni \[ \] . internal.cloudapp.net
* A címkeresés a vmname űrlap teljes tartománynevén \[ \] történik. a Internal.cloudapp.net a virtuális géphez HOZZÁRENDELT IP-címekre lesz feloldva.
* Ha a virtuális hálózat egy [Azure DNS magánhálózati zónához](../dns/private-dns-overview.md) van csatolva regisztrációs virtuális hálózatként, akkor a fordított DNS-lekérdezések két rekordot adnak vissza. Az egyik rekord a vmname alak lesz \[ \] . [ privatednszonename], a másik pedig a \[ vmname \] . internal.cloudapp.net formátumú.
* A fordított DNS-keresés hatóköre egy adott virtuális hálózatra terjed ki, még akkor is, ha más virtuális hálózatokhoz van kiállítva. A fordított DNS-lekérdezések (PTR-lekérdezések) a társ virtuális hálózatokban található virtuális gépek IP-címeihez a NXDOMAIN lesznek visszaadva.
* Ha ki szeretné kapcsolni a fordított DNS-függvényt egy virtuális hálózatban, akkor ehhez hozzon létre egy névkeresési zónát [Azure DNS privát zónák](../dns/private-dns-overview.md) használatával, és kapcsolja a zónát a virtuális hálózathoz. Ha például a virtuális hálózat IP-címe 10.20.0.0/16, akkor létrehozhat egy üres magánhálózati DNS-zónát a 20.10.in-addr. arpa paranccsal, és összekapcsolhatja azt a virtuális hálózattal. A zóna virtuális hálózathoz való összekapcsolásakor le kell tiltania az automatikus regisztrációt a hivatkozáson. Ez a zóna felülbírálja a virtuális hálózat alapértelmezett névkeresési zónáit, és mivel ez a zóna üres, a fordított DNS-lekérdezések NXDOMAIN fogja kapni. A saját DNS-zónák létrehozásával és a virtuális hálózattal való összekapcsolásával kapcsolatos további tudnivalókért tekintse meg a rövid [útmutató útmutatóját](../dns/private-dns-getstarted-portal.md) .

> [!NOTE]
> Ha azt szeretné, hogy a fordított DNS-címkeresés átnyúljon a virtuális hálózaton keresztül, létrehozhat egy névkeresési zónát (in-addr. arpa) [Azure DNS privát zónákat](../dns/private-dns-overview.md) , és összekapcsolhatja azt több virtuális hálózattal. A virtuális gépek fordított DNS-rekordjait azonban manuálisan kell kezelnie.
>


## <a name="dns-client-configuration"></a>DNS-ügyfél konfigurációja

Ez a szakasz az ügyféloldali gyorsítótárazást és az ügyféloldali újrapróbálkozásokat ismerteti.

### <a name="client-side-caching"></a>Ügyféloldali gyorsítótárazás

Nem kell minden DNS-lekérdezést elküldeni a hálózaton keresztül. Az ügyféloldali gyorsítótárazás segítségével csökkentheti a késést, és javíthatja a hálózati visszavertség rugalmasságát, ha az ismétlődő DNS-lekérdezéseket oldja fel egy helyi gyorsítótárból. A DNS-rekordok élettartam (TTL) mechanizmust tartalmaznak, amely lehetővé teszi, hogy a gyorsítótár a lehető leghosszabb ideig tárolja a rekordot a rekord frissességének befolyásolása nélkül. Így az ügyféloldali gyorsítótárazás a legtöbb esetben megfelelő.

Az alapértelmezett Windows DNS-ügyfél beépített DNS-gyorsítótárral rendelkezik. Egyes Linux-disztribúciók alapértelmezés szerint nem tartalmazzák a gyorsítótárazást. Ha úgy találja, hogy már nincs helyi gyorsítótár, adjon hozzá egy DNS-gyorsítótárat az egyes linuxos virtuális gépekhez.

Számos különböző DNS-gyorsítótárazási csomag elérhető (például DNSMasq). A következőképpen telepítheti a DNSMasq a leggyakoribb disztribúciók esetében:

* **Ubuntu (resolvconf-t használ)**:
  * Telepítse a DNSMasq csomagot a-val `sudo apt-get install dnsmasq` .
* **SUSE (netconf használ)**:
  * Telepítse a DNSMasq csomagot a-val `sudo zypper install dnsmasq` .
  * Engedélyezze a DNSMasq szolgáltatást a szolgáltatással `systemctl enable dnsmasq.service` . 
  * Indítsa el a DNSMasq szolgáltatást a szolgáltatással `systemctl start dnsmasq.service` . 
  * Szerkessze a **/etc/sysconfig/network/config**, és módosítsa a *NETCONFIG_DNS_FORWARDER = ""* *DNSMasq*.
  * Frissítse a resolv. conf fájlt a `netconfig update` használatával a gyorsítótár helyi DNS-feloldóként való beállításához.
* **CentOS (hálózatkezelő használ)**:
  * Telepítse a DNSMasq csomagot a-val `sudo yum install dnsmasq` .
  * Engedélyezze a DNSMasq szolgáltatást a szolgáltatással `systemctl enable dnsmasq.service` .
  * Indítsa el a DNSMasq szolgáltatást a szolgáltatással `systemctl start dnsmasq.service` .
  * Adja *hozzá* a **/etc/dhclient-eth0.conf**.
  * Indítsa újra a hálózati szolgáltatást a `service network restart` használatával, hogy a gyorsítótárat a helyi DNS-feloldóként állítsa be.

> [!NOTE]
> A DNSMasq-csomag csak a Linuxon elérhető számos DNS-gyorsítótár egyike. A használata előtt győződjön meg arról, hogy megfelel az igényeinek, és győződjön meg arról, hogy nincs telepítve más gyorsítótár.

    
### <a name="client-side-retries"></a>Ügyféloldali újrapróbálkozások

A DNS elsődlegesen UDP protokoll. Mivel az UDP protokoll nem garantálja az üzenetek kézbesítését, az újrapróbálkozási logikát maga a DNS protokoll kezeli. Minden DNS-ügyfél (operációs rendszer) különböző újrapróbálkozási logikát tud kimutatni a létrehozó beállításaitól függően:

* A Windows operációs rendszer újrapróbálkozik egy másodperc elteltével, majd ismét egy másik két másodperc, négy másodperc és egy másik négy másodperc után. 
* Az alapértelmezett Linux-telepítő öt másodperc elteltével próbálkozik újra. Javasoljuk, hogy az újrapróbálkozási specifikációkat ötször, egy másodperces időközönként módosítsa.

A Linux rendszerű virtuális gépek aktuális beállításainak megtekintése a következővel: `cat /etc/resolv.conf` . Tekintse meg a *Beállítások* sort, például:

```bash
options timeout:1 attempts:5
```

Az resolv. conf fájl általában automatikusan jön létre, és nem szerkeszthető. Az *Options (beállítások* ) sor hozzáadásának konkrét lépései a terjesztés szerint változnak:

* **Ubuntu** (resolvconf-t használ):
  1. Adja hozzá a *Options* sort a következőhöz: **/etc/resolvconf/resolv.conf.d/tail**.
  2. `resolvconf -u`A frissítéshez futtassa a parancsot.
* **SUSE** (netconf használ):
  1. *Időkorlát hozzáadása: 1 kísérlet: 5* a **NETCONFIG_DNS_RESOLVER_OPTIONS = ""** paraméterhez a **/etc/sysconfig/network/config**.
  2. `netconfig update`A frissítéshez futtassa a parancsot.
* **CentOS** (hálózatkezelő használ):
  1. Adja hozzá az *echo "Options timeout: 1 kísérlet: 5"* **/etc/NetworkManager/Dispatcher.d/11-dhclient**.
  2. Frissítés a szolgáltatással `service network restart` .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Saját DNS-kiszolgálót használó névfeloldás

Ez a szakasz a virtuális gépeket, a szerepkör-példányokat és a webalkalmazásokat ismerteti.

### <a name="vms-and-role-instances"></a>Virtuális gépek és szerepkör-példányok

A névfeloldási igények az Azure által biztosított funkciókon túl is megjelenhetnek. Előfordulhat például, hogy a Microsoft Windows Server Active Directory tartományokat kell használnia a virtuális hálózatok közötti DNS-nevek feloldásához. Ezen forgatókönyvek fedezetéül az Azure lehetővé teszi saját DNS-kiszolgálók használatát.

A virtuális hálózaton belüli DNS-kiszolgálók továbbítják a DNS-lekérdezéseket az Azure rekurzív feloldóinak. Ez lehetővé teszi az adott virtuális hálózaton belüli állomásnevek feloldását. Az Azure-ban futó tartományvezérlők például a tartományokra vonatkozó DNS-lekérdezésekre tudnak válaszolni, és az összes többi lekérdezést az Azure-ba továbbítják. A továbbítási lekérdezések lehetővé teszik a virtuális gépek számára a helyszíni erőforrások (a TARTOMÁNYVEZÉRLŐn keresztül) és az Azure által biztosított állomásnevek (a továbbítón keresztüli) megtekintését. Az Azure-ban lévő rekurzív feloldók elérését a virtuális IP-168.63.129.16 keresztül biztosítjuk.

A DNS-továbbítás lehetővé teszi a DNS-feloldást a virtuális hálózatok között, és lehetővé teszi a helyszíni gépek számára az Azure által biztosított állomásnevek feloldását. A virtuális gép állomásneve feloldásához a DNS-kiszolgáló virtuális gépnek ugyanabban a virtuális hálózatban kell lennie, és konfigurálni kell az állomásnév-lekérdezések Azure-ba való továbbítását. Mivel a DNS-utótag különbözik az egyes virtuális hálózatokban, a feltételes továbbítási szabályok segítségével a megfelelő virtuális hálózatra küldhet DNS-lekérdezéseket a megoldáshoz. A következő kép két virtuális hálózatot és egy helyszíni hálózatot mutat be, amely a virtuális hálózatok közötti DNS-feloldást hajtja végre ezzel a módszerrel. Egy példa a DNS-továbbítóra az [Azure Gyorsindítás sablonok](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) katalógusában és a [githubban](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> A szerepkör-példányok az ugyanazon a virtuális hálózaton belüli virtuális gépek névfeloldását is elvégezhetik. Ezt a teljes tartománynevet használja, amely a virtuális gép állomásnevét és **Internal.cloudapp.net** DNS-utótagját tartalmazza. Ebben az esetben azonban a névfeloldás csak akkor sikeres, ha a szerepkör-példány rendelkezik a [szerepkör-sémában (. cscfg fájlban)](/previous-versions/azure/reference/jj156212(v=azure.100))definiált virtuálisgép-névvel.
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Azok a szerepkörök, amelyeknek el kell végezniük a virtuális gépek névfeloldását egy másik virtuális hálózatban (a **Internal.cloudapp.net** utótag használatával) az ebben a szakaszban ismertetett módszer használatával (a két virtuális hálózat között továbbított egyéni DNS-kiszolgálók).
>

![A virtuális hálózatok közötti DNS diagramja](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Ha az Azure által biztosított névfeloldást használja, az Azure Dynamic Host Configuration Protocol (DHCP) egy belső DNS-utótagot (**. internal.cloudapp.net**) biztosít minden egyes virtuális géphez. Ez az utótag lehetővé teszi az állomásnév-feloldást, mert az állomásnév rekordok a **Internal.cloudapp.net** zónában vannak. Ha saját névfeloldási megoldást használ, az utótagot a rendszer nem adja meg a virtuális gépek számára, mert az más DNS-architektúrákkal (például a tartományhoz csatlakoztatott forgatókönyvekkel) ütközik. Az Azure Ehelyett nem működő helyőrzőt (*reddog.microsoft.com*) biztosít.

Ha szükséges, a belső DNS-utótagot a PowerShell vagy az API használatával határozhatja meg:

* Azure Resource Manager üzembe helyezési modellben található virtuális hálózatok esetében az utótag a [hálózati adapteren REST API](/rest/api/virtualnetwork/networkinterfaces), a [Get-AzNetworkInterface PowerShell-](/powershell/module/az.network/get-aznetworkinterface) parancsmag és az az [Network NIC show](/cli/azure/network/nic#az-network-nic-show) Azure CLI parancs használatával érhető el.
* A klasszikus üzembe helyezési modellekben az utótag az [üzembe helyezési API](/previous-versions/azure/reference/ee460804(v=azure.100)) -hívás vagy a [Get-AzureVM-debug](/powershell/module/servicemanagement/azure.service/get-azurevm) parancsmag használatával érhető el.

Ha az Azure-ba irányuló lekérdezések továbbítása nem felel meg az igényeinek, meg kell adnia a saját DNS-megoldását. A DNS-megoldásnak a következőket kell tennie:

* Adja meg a megfelelő állomásnév-feloldást a [DDNS](virtual-networks-name-resolution-ddns.md)-n keresztül, például:. Ha a DDNS-t használja, előfordulhat, hogy le kell tiltania a DNS-rekordok kitakarítását. Az Azure DHCP-bérletek hosszúak, és a kitakarításkor a DNS-rekordok idő előtt eltávolíthatók. 
* Adjon meg megfelelő rekurzív megoldást a külső tartománynevek feloldásának engedélyezéséhez.
* Elérhetőnek kell lennie (TCP és UDP a 53-as porton) az általa üzemeltetett ügyfelektől, és képesnek kell lennie az internet elérésére.
* A külső ügynökök által jelentett fenyegetések enyhítése érdekében biztosítani kell az internetről való hozzáférést.

> [!NOTE]
> A legjobb teljesítmény érdekében, ha Azure-beli virtuális gépeket használ DNS-kiszolgálóként, az IPv6-ot le kell tiltani.

### <a name="web-apps"></a>Webalkalmazások
Tegyük fel, hogy névfeloldást kell végeznie a webalkalmazásból, amelyet a virtuális hálózathoz kapcsolódó App Service használatával kell létrehozni az azonos virtuális hálózatban lévő virtuális gépekhez. Olyan egyéni DNS-kiszolgáló beállításán kívül, amely DNS-továbbítóval továbbítja a lekérdezéseket az Azure-nak (virtuális IP-168.63.129.16), hajtsa végre a következő lépéseket:
1. Engedélyezze a virtuális hálózatok integrálását a webalkalmazáshoz, ha azt már nem tette meg, az [alkalmazás integrálása virtuális hálózattal](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakörben leírtak szerint.
2. A Azure Portal a webalkalmazást futtató App Service tervnél válassza a **hálózat szinkronizálása a hálózatban** , **Virtual Network** **az** integráció lehetőséget.

    ![Képernyőfelvétel a virtuális hálózat névfeloldásáról](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Ha olyan névfeloldást kell elvégeznie a webalkalmazásból, amely a App Service használatával készült, egy virtuális hálózathoz kapcsolt virtuális hálózatban lévő virtuális gépekhez, a következőképpen kell egyéni DNS-kiszolgálókat használnia mindkét virtuális hálózaton:

* Állítson be egy DNS-kiszolgálót a célként megadott virtuális hálózatban egy olyan virtuális gépen, amely az Azure rekurzív feloldójának lekérdezéseit is továbbítja (virtuális IP-168.63.129.16). Egy példa a DNS-továbbítóra az [Azure Gyorsindítás sablonok](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) katalógusában és a [githubban](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Hozzon létre egy DNS-továbbítót a forrás virtuális hálózaton egy virtuális gépen. Konfigurálja a DNS-továbbítót úgy, hogy továbbítsa a lekérdezéseket a célként megadott virtuális hálózatban lévő DNS-kiszolgálónak.
* Konfigurálja a forrás DNS-kiszolgálót a forrásként szolgáló virtuális hálózat beállításaiban.
* Engedélyezze a virtuális hálózatok integrálását a webalkalmazáshoz a forrás virtuális hálózatra való hivatkozáshoz, kövesse az [alkalmazás integrálása virtuális hálózattal](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakör utasításait.
* A Azure Portal a webalkalmazást futtató App Service tervnél válassza a **hálózat szinkronizálása a hálózatban** , **Virtual Network** **az** integráció lehetőséget.

## <a name="specify-dns-servers"></a>DNS-kiszolgálók meghatározása
Ha saját DNS-kiszolgálókat használ, az Azure lehetővé teszi, hogy virtuális hálózatonként több DNS-kiszolgálót is megadhat. Hálózati adapterenként (az Azure Resource Manager esetében) vagy felhőszolgáltatásonként (a klasszikus üzemi modell esetében) is megadhat több DNS-kiszolgálót. A hálózati adapterhez vagy a felhőalapú szolgáltatáshoz megadott DNS-kiszolgálók elsőbbséget élveznek a virtuális hálózathoz megadott DNS-kiszolgálókkal szemben.

> [!NOTE]
> A hálózati kapcsolatok tulajdonságait, például a DNS-kiszolgáló IP-címeit nem szabad közvetlenül a virtuális gépeken belül szerkeszteni. Ennek az az oka, hogy a szolgáltatás meggyógyítása során előfordulhat, hogy a virtuális hálózati adapter helyére kerül. Ez a Windows és a Linux rendszerű virtuális gépekre is vonatkozik.

A Azure Resource Manager üzemi modell használatakor megadhatja a virtuális hálózathoz és a hálózati adapterhez tartozó DNS-kiszolgálókat. Részletekért lásd: [virtuális hálózat kezelése](manage-virtual-network.md) és [hálózati adapterek kezelése](virtual-network-network-interface.md).

> [!NOTE]
> Ha egyéni DNS-kiszolgálót választ a virtuális hálózatához, meg kell adnia legalább egy DNS-kiszolgáló IP-címét; Ellenkező esetben a virtuális hálózat figyelmen kívül hagyja a konfigurációt, és az Azure által biztosított DNS-t használja helyette.

A klasszikus üzemi modell használatakor megadhatja a virtuális hálózat DNS-kiszolgálóit a Azure Portal vagy a [hálózati konfigurációs fájlban](/previous-versions/azure/reference/jj157100(v=azure.100)). A Cloud Services esetében a DNS-kiszolgálókat a [szolgáltatás konfigurációs fájljával](/previous-versions/azure/reference/ee758710(v=azure.100)) vagy a PowerShell használatával adhatja meg a [New-AzureVM](/powershell/module/servicemanagement/azure.service/new-azurevm).

> [!NOTE]
> Ha módosítja egy olyan virtuális hálózat vagy virtuális gép DNS-beállításait, amely már telepítve van, az új DNS-beállítások érvénybe léptetéséhez a DHCP-bérlet megújítását kell végrehajtania a virtuális hálózatban lévő összes érintett virtuális gépen. A Windows operációs rendszert futtató virtuális gépek esetén `ipconfig /renew` közvetlenül a virtuális gépen írhat be. A lépések az operációs rendszertől függően változnak. Tekintse meg az operációs rendszer típusának megfelelő dokumentációt.

## <a name="next-steps"></a>Következő lépések

Azure Resource Manager telepítési modell:

* [Virtuális hálózat kezelése](manage-virtual-network.md)
* [Hálózati adapter kezelése](virtual-network-network-interface.md)

Klasszikus üzembe helyezési modell:

* [Azure-szolgáltatás konfigurációs sémája](/previous-versions/azure/reference/ee758710(v=azure.100))
* [Virtual Network konfigurációs séma](/previous-versions/azure/reference/jj157100(v=azure.100))
* [Virtual Network konfigurálása hálózati konfigurációs fájl használatával](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file)