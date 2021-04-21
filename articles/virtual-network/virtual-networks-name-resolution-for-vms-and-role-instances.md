---
title: Azure virtuális hálózatokon található erőforrások névfeloldása
titlesuffix: Azure Virtual Network
description: Névfeloldási forgatókönyvek Azure IaaS-hez, hibrid megoldásokhoz, különböző felhőszolgáltatások között, Active Directory dns-kiszolgáló használata.
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
ms.openlocfilehash: 80a0b4634a2e84181271b515d2f6f63271cce7f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784964"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Azure virtuális hálózatokon található erőforrások névfeloldása

Attól függően, hogy hogyan használja az Azure-t IaaS-, PaaS- és hibrid megoldások üzembe helyezésére, előfordulhat, hogy engedélyeznie kell, hogy a virtuális hálózatban üzembe helyezett virtuális gépek és egyéb erőforrások kommunikáljanak egymással. Bár az IP-címek használatával is engedélyezheti a kommunikációt, sokkal egyszerűbb olyan neveket használni, amelyek könnyen meg lehet őket öröklni, és nem változnak. 

Ha a virtuális hálózatokban üzembe helyezett erőforrásoknak belső IP-címekre kell feloldanunk a tartományneveket, három módszer közül egyet használhatnak:

* [Azure DNS zónák létrehozása](../dns/private-dns-overview.md)
* [Az Azure által biztosított névfeloldás](#azure-provided-name-resolution)
* [Saját DNS-kiszolgálót](#name-resolution-that-uses-your-own-dns-server) használó névfeloldás (amely lekérdezéseket továbbíthat az Azure által biztosított DNS-kiszolgálóknak)

A használt névfeloldási típus attól függ, hogy az erőforrásoknak hogyan kell kommunikálniuk egymással. Az alábbi táblázat forgatókönyveket és a hozzájuk tartozó névfeloldási megoldásokat mutatja be:

> [!NOTE]
> Azure DNS a privát zónák használata az előnyben részesített megoldás, és rugalmasságot biztosít a DNS-zónák és -rekordok kezelése terén. További információkat az [Azure DNS privát tartományokhoz való használatát](../dns/private-dns-overview.md) ismertető cikkben olvashat.

> [!NOTE]
> Ha az Azure által biztosított DNS-t használja, a rendszer automatikusan alkalmazza a megfelelő DNS-utótagot a virtuális gépekre. Minden más beállításhoz teljes tartományneveket (FQDN) kell használnia, vagy manuálisan kell alkalmaznia a megfelelő DNS-utótagot a virtuális gépekre.

| **Forgatókönyv** | **Megoldás** | **DNS-utótag** |
| --- | --- | --- |
| Azonos virtuális hálózaton található virtuális gépek vagy Azure Cloud Services felhőszolgáltatásban található szerepkörpéldányok közötti névfeloldás. | [Azure DNS zónák vagy](../dns/private-dns-overview.md) [az Azure által biztosított névfeloldás](#azure-provided-name-resolution) |Állomásnév vagy teljes tartománynév |
| Névfeloldás a különböző virtuális hálózatokban lévő virtuális gépek vagy a különböző felhőszolgáltatások szerepkörpéldányai között. |[Azure DNS vagy](../dns/private-dns-overview.md) ügyfél által felügyelt DNS-kiszolgálók, amelyek lekérdezéseket továbbítnak a virtuális hálózatok között az Azure-beli feloldás érdekében (DNS-proxy). Lásd: [Névfeloldás saját DNS-kiszolgáló használatával.](#name-resolution-that-uses-your-own-dns-server) |Csak FQDN |
| Névfeloldás egy Azure App Service (webalkalmazás, függvény vagy robot) használatával, amely virtuális hálózati integrációt használ az ugyanazon a virtuális hálózaton található szerepkörpéldányokkal vagy virtuális gépekkel. |Az ügyfél által felügyelt DNS-kiszolgálók lekérdezéseket továbbítnak a virtuális hálózatok között az Azure-beli feloldás érdekében (DNS-proxy). Lásd: [Névfeloldás saját DNS-kiszolgáló használatával.](#name-resolution-that-uses-your-own-dns-server) |Csak FQDN |
| Névfeloldás App Service Web Apps azonos virtuális hálózaton lévő virtuális gépekre. |Az ügyfél által felügyelt DNS-kiszolgálók lekérdezéseket továbbítnak a virtuális hálózatok között az Azure-beli feloldás érdekében (DNS-proxy). Lásd: [Névfeloldás saját DNS-kiszolgáló használatával.](#name-resolution-that-uses-your-own-dns-server) |Csak FQDN |
| Névfeloldás App Service Web Apps egy virtuális hálózaton lévő virtuális gépekről egy másik virtuális hálózatban lévő virtuális gépekre. |Az ügyfél által felügyelt DNS-kiszolgálók lekérdezéseket továbbítnak a virtuális hálózatok között az Azure-beli feloldás érdekében (DNS-proxy). Lásd: [Névfeloldás saját DNS-kiszolgáló használatával.](#name-resolution-that-uses-your-own-dns-server) |Csak FQDN |
| Helyszíni számítógép- és szolgáltatásnevek feloldása az Azure-beli virtuális gépekről vagy szerepkörpéldányokról. |Ügyfél által felügyelt DNS-kiszolgálók (helyszíni tartományvezérlő, helyi írásható tartományvezérlő vagy zónaátvitellel szinkronizált másodlagos DNS-másodlagos kiszolgáló). Lásd: [Névfeloldás saját DNS-kiszolgáló használatával.](#name-resolution-that-uses-your-own-dns-server) |Csak FQDN |
| Azure-beli gazdagépnevek feloldása a helyszíni számítógépekről. |A lekérdezéseket a megfelelő virtuális hálózaton található, ügyfél által felügyelt DNS-proxykiszolgálóra továbbítja, a proxykiszolgáló feloldás érdekében a lekérdezéseket az Azure-ba továbbítja. Lásd: [Névfeloldás saját DNS-kiszolgáló használatával.](#name-resolution-that-uses-your-own-dns-server) |Csak FQDN |
| Fordított DNS belső IP-címhez. |[Azure DNS vagy](../dns/private-dns-overview.md) Az Azure által biztosított névfeloldás vagy névfeloldás saját [DNS-kiszolgáló](#azure-provided-name-resolution) [használatával.](#name-resolution-that-uses-your-own-dns-server) |Nem alkalmazható |
| Névfeloldás különböző felhőszolgáltatásokban, nem virtuális hálózaton található virtuális gépek vagy szerepkörpéldányok között. |Nem alkalmazható. A különböző felhőszolgáltatásokban lévő virtuális gépek és szerepkörpéldányok közötti kapcsolat nem támogatott a virtuális hálózaton kívül. |Nem alkalmazható|

## <a name="azure-provided-name-resolution"></a>Az Azure által biztosított névfeloldás

Az Azure által biztosított névfeloldás csak alapszintű mérvadó DNS-képességeket biztosít. Ha ezt a beállítást használja, a DNS-zónaneveket és -rekordokat az Azure automatikusan kezeli, és nem fogja tudni szabályozni a DNS-zónaneveket vagy a DNS-rekordok életciklusát. Ha teljes körű DNS-megoldásra van szüksége [](../dns/private-dns-overview.md) a virtuális hálózatokhoz, magánzónákat vagy Azure DNS DNS-kiszolgálókat kell [használnia.](#name-resolution-that-uses-your-own-dns-server)

A nyilvános DNS-nevek feloldásán keresztül az Azure belső névfeloldási megoldást biztosít az azonos virtuális hálózaton vagy felhőszolgáltatásban található virtuális gépek és szerepkörpéldányok számára. A felhőszolgáltatásban a virtuális gépek és a példányok ugyanazt a DNS-utótagot osztják meg, így csak az állomásnév elegendő. A klasszikus üzembe helyezési modellel üzembe helyezett virtuális hálózatokban azonban a különböző felhőszolgáltatások különböző DNS-utótagokkal rendelkezik. Ebben az esetben szüksége lesz a teljes tartománynévre a különböző felhőszolgáltatások közötti nevek feloldásához. Az Azure Resource Manager-telepítési modellel üzembe helyezett virtuális hálózatokban a DNS-utótag konzisztens a virtuális hálózatban lévő összes virtuális gépen, így nincs szükség teljes tartománynévre. A DNS-nevek virtuális gépekhez és hálózati adapterekhez is hozzárendelhetőek. Bár az Azure által biztosított névfeloldás nem igényel semmilyen konfigurációt, nem ez a megfelelő választás az összes üzembe helyezési forgatókönyvhöz, az előző táblázatban részletezett módon.

> [!NOTE]
> Felhőszolgáltatások webes és feldolgozói szerepköreinek használata esetén a szerepkörpéldányok belső IP-címeit is elérheti az Azure Service Management REST API. További információkért tekintse meg a [Service Management REST API referenciáját.](/previous-versions/azure/ee460799(v=azure.100)) A cím a szerepkörnéven és a példányszámon alapul. 
>

### <a name="features"></a>Funkciók

Az Azure által biztosított névfeloldás a következő funkciókat tartalmazza:
* Könnyű használat. Nem igényel konfigurálást.
* Magas rendelkezésre állás. Nem kell saját DNS-kiszolgálókból fürtöt létrehoznia és kezelnie.
* A szolgáltatást a saját DNS-kiszolgálóival együtt használva feloldhatja a helyszíni és az Azure-beli állomásneveket is.
* Az egy felhőszolgáltatáson belüli virtuális gépek és szerepkörpéldányok között névfeloldás is használható, FQDN nélkül.
* Az üzembe helyezési modellt Azure Resource Manager virtuális hálózatokon lévő virtuális gépek között névfeloldás használható, FQDN nélkül. A klasszikus üzembe helyezési modellben a virtuális hálózatoknak teljes tartománynevet (FQDN) kell megkövetelni, amikor különböző felhőszolgáltatásokban feloldja a neveket. 
* Az automatikusan létrehozott nevek használata helyett használhatja az üzemelő példányokat legjobban leíró állomásneveket.

### <a name="considerations"></a>Megfontolandó szempontok

Az Azure által biztosított névfeloldás használata esetén megfontolt pontok:
* Az Azure által létrehozott DNS-utótag nem módosítható.
* A DNS-keresés hatóköre egy virtuális hálózatra terjed ki. Az egyik virtuális hálózathoz létrehozott DNS-nevek nem oldhatóak fel más virtuális hálózatokból.
* Saját rekordjait nem regisztrálhatja manuálisan.
* A WINS és a NetBIOS nem támogatott. A virtuális gépeket nem láthatja a Windows Explorer.
* Az állomásneveknek DNS-kompatibilisnek kell lenniük. A nevek csak 0–9, a-z és -, és nem kezdődhet "-" végződővel.
* A DNS-lekérdezések forgalma minden virtuális gépnél le van szabályozás alatt. A szabályozás nem lehet hatással a legtöbb alkalmazásra. Ha kérések szabályozása figyelhető meg, győződjön meg arról, hogy az ügyféloldali gyorsítótárazás engedélyezve van. További információ: [DNS-ügyfél konfigurálása.](#dns-client-configuration)
* A klasszikus üzembe helyezési modellben minden virtuális hálózathoz csak az első 180 felhőszolgáltatás virtuális gépei vannak regisztrálva. Ez a korlát nem vonatkozik a virtuális hálózatokra a Azure Resource Manager.
* A Azure DNS IP-cím 168.63.129.16. Ez egy statikus IP-cím, és nem fog megváltozni.

### <a name="reverse-dns-considerations"></a>Fordított DNS-re vonatkozó szempontok
A fordított DNS minden ARM-alapú virtuális hálózatban támogatott. Fordított DNS-lekérdezéseket (PTR-lekérdezéseket) is ki lehet használni a virtuális gépek IP-címeinek a virtuális gépek teljes tartománynevére való leképezése érdekében.
* A virtuális gépek IP-címeinek PTR-lekérdezései \[ vmname \] .internal.cloudapp.net
* A .internal.cloudapp.net.internal.cloudapp.net FQDN-eket a rendszer a virtuális géphez rendelt \[ \] IP-címre oldja fel.
* Ha a virtuális hálózat egy privát Azure DNS [virtuális](../dns/private-dns-overview.md) hálózatként kapcsolódik egy virtuális hálózathoz, a fordított DNS-lekérdezések két rekordot fognak visszaadni. Az egyik rekord a következő lesz: \[ vmname \] .[ privatednszonename], a másik pedig \[ vmname \] .internal.cloudapp.net
* A fordított DNS-keresés hatóköre egy adott virtuális hálózatra terjed ki, még akkor is, ha más virtuális hálózatokhoz van társviszonyban. A virtuális társhálózatban található virtuális gépek IP-címeinek fordított DNS-lekérdezései (PTR-lekérdezések) NXDOMAIN-t fognak visszaadni.
* Ha ki szeretné kapcsolni a fordított [DNS-függvényt](../dns/private-dns-overview.md) egy virtuális hálózatban, ehhez hozzon létre egy névkeresési zónát Azure DNS privát zónák használatával, és kapcsolja ezt a zónát a virtuális hálózathoz. Ha például a virtuális hálózat IP-címterülete 10.20.0.0/16, akkor létrehozhat egy üres privát DNS-zónát 20.10.in-addr.arpa, és csatolhatja a virtuális hálózathoz. Amikor a zónát a virtuális hálózathoz kapcsolja, tiltsa le az automatikus regisztrációt a hivatkozáson. Ez a zóna felülírja a virtuális hálózat alapértelmezett névkeresési zónáit, és mivel ez a zóna üres, NXDOMAIN értéket fog kapni a fordított DNS-lekérdezésekhez. A privát [DNS-zónák](../dns/private-dns-getstarted-portal.md) virtuális hálózathoz való csatolásának részleteiért tekintse meg a rövid útmutatót.

> [!NOTE]
> Ha azt szeretné, hogy a névfeloldásos DNS-keresés több virtuális hálózaton is átfedődjon, létrehozhat egy névkeresési zónát (in-addr.arpa) [Azure DNS](../dns/private-dns-overview.md) privát zónákhoz, és több virtuális hálózathoz is hozzá lehet adni. A virtuális gépek fordított DNS-rekordjait azonban manuálisan kell kezelnie.
>


## <a name="dns-client-configuration"></a>DNS-ügyfél konfigurálása

Ez a szakasz az ügyféloldali gyorsítótárazást és az ügyféloldali újraalkalmazásokat tartalmazza.

### <a name="client-side-caching"></a>Ügyféloldali gyorsítótárazás

Nem kell minden DNS-lekérdezést a hálózaton keresztül elküldeni. Az ügyféloldali gyorsítótárazás segít csökkenteni a késést, és javítja a hálózati hibákkal szembeni rugalmasságot azáltal, hogy feloldja az ismétlődő DNS-lekérdezéseket a helyi gyorsítótárból. A DNS-rekordok tartalmaznak egy TTL-mechanizmust, amely lehetővé teszi a gyorsítótár számára, hogy a rekordot a lehető legnagyobb ideig tárolja a rekordok frissessége befolyásolása nélkül. Így az ügyféloldali gyorsítótárazás a legtöbb helyzetben megfelelő.

Az alapértelmezett Windows DNS-ügyfél beépített DNS-gyorsítótárral rendelkezik. Egyes Linux-disztribúciók alapértelmezés szerint nem tartalmazzák a gyorsítótárazást. Ha azt találja, hogy még nincs helyi gyorsítótár, adjon hozzá egy DNS-gyorsítótárat minden Linux rendszerű virtuális géphez.

Számos különböző DNS-gyorsítótárazó csomag érhető el (például dnsmasq). A dnsmasq telepítése a leggyakoribb disztribúciókra a következő:

* **Ubuntu (a resolvconf-et használja):**
  * Telepítse a dnsmasq csomagot a `sudo apt-get install dnsmasq` következővel: .
* **SUSE (netconf-et használ):**
  * Telepítse a dnsmasq csomagot a `sudo zypper install dnsmasq` következővel: .
  * Engedélyezze a dnsmasq szolgáltatást a `systemctl enable dnsmasq.service` következővel: . 
  * Indítsa el a dnsmasq szolgáltatást a `systemctl start dnsmasq.service` következővel: . 
  * **Szerkessze az /etc/sysconfig/network/config** fájlt, és módosítsa *a NETCONFIG_DNS_FORWARDER="" paramétert* *dnsmasq névre.*
  * Frissítse az resolv.conf fájlban a `netconfig update` következőt: , hogy a gyorsítótárat helyi DNS-feloldóként állítsa be.
* **CentOS (a NetworkManagert használja):**
  * Telepítse a dnsmasq csomagot a `sudo yum install dnsmasq` következővel: .
  * Engedélyezze a dnsmasq szolgáltatást a `systemctl enable dnsmasq.service` következővel: .
  * Indítsa el a dnsmasq szolgáltatást a `systemctl start dnsmasq.service` következővel: .
  * Adja *hozzá a 127.0.0.1;* előtagú tartománynév-kiszolgálókat az **/etc/dhclient-eth0.conf fájlhoz.**
  * Indítsa újra a hálózati szolgáltatást a használatával, hogy a gyorsítótárat helyi `service network restart` DNS-feloldóként állítsa be.

> [!NOTE]
> A dnsmasq csomag csak egy a Linuxhoz elérhető számos DNS-gyorsítótár közül. Használat előtt ellenőrizze, hogy megfelel-e az adott igényeknek, és ellenőrizze, hogy nincs-e más gyorsítótár telepítve.

    
### <a name="client-side-retries"></a>Ügyféloldali újraalkalmazások

A DNS elsődlegesen UDP-protokoll. Mivel az UDP protokoll nem garantálja az üzenetek kézbesítését, az újrapróbálkozási logikát magában a DNS-protokollban kezeli a rendszer. Minden DNS-ügyfél (operációs rendszer) eltérő újrapróbálkozási logikát mutathat a létrehozó igényeitől függően:

* A Windows operációs rendszerek egy másodperc után újrapróbálkoznak, majd ismét két másodperc, négy másodperc és újabb négy másodperc elteltével. 
* Az alapértelmezett Linux-telepítő öt másodperc után újra fog újraküldni. Javasoljuk, hogy az újrapróbálkozási specifikációkat 1 másodperces időközönként ötre módosítsa.

Ellenőrizze a Linux rendszerű virtuális gépek aktuális beállításait a `cat /etc/resolv.conf` következővel: . Nézze meg *a beállítássort,* például:

```bash
options timeout:1 attempts:5
```

A resolv.conf fájl általában automatikusan jön létre, és nem szerkeszthető. A beállítássor hozzáadásának konkrét *lépései* eloszlásonként eltérőek lehetnek:

* **Ubuntu** (a resolvconf-et használja):
  1. Adja hozzá *a beállítássort* az **/etc/resolvconf/resolv.conf.d/tail fájlhoz.**
  2. Futtassa `resolvconf -u` az et a frissítéshez.
* **SUSE** (netconf-et használ):
  1. Adja *hozzá a timeout:1 attempts:5* paramétert **a NETCONFIG_DNS_RESOLVER_OPTIONS="" paraméterhez** az **/etc/sysconfig/network/config fájlban.**
  2. Futtassa `netconfig update` az et a frissítéshez.
* **CentOS** (NetworkManagert használ):
  1. Adja *hozzá az echo "options timeout:1 attempts:5"* et az **/etc/NetworkManager/dispatcher.d/11-dhclient parancshoz.**
  2. Frissítse a et `service network restart` a sel.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Saját DNS-kiszolgálót használó névfeloldás

Ez a szakasz a virtuális gépeket, a szerepkörpéldányokat és a webalkalmazásokat tartalmazza.

### <a name="vms-and-role-instances"></a>Virtuális gépek és szerepkörpéldányok

Előfordulhat, hogy a névfeloldási igények túlmutatnak az Azure által biztosított funkciókon. Előfordulhat például, hogy Microsoft-tartományokat kell használnia, Windows Server Active Directory feloldani a DNS-neveket a virtuális hálózatok között. Ezen forgatókönyvek lefedése érdekében az Azure lehetővé teszi saját DNS-kiszolgálók használatát.

A virtuális hálózaton belüli DNS-kiszolgálók DNS-lekérdezéseket küldhetnek az Azure rekurzív feloldóinak. Ez lehetővé teszi az adott virtuális hálózaton belüli gazdagépnevek feloldását. Például egy Azure-ban futó tartományvezérlő (DC) válaszolhat a tartománya DNS-lekérdezésére, és továbbíthatja az összes többi lekérdezést az Azure-nak. A lekérdezések továbbítása lehetővé teszi, hogy a virtuális gépek a helyszíni erőforrásokat (a tartományvezérlőn keresztül) és az Azure által biztosított állomásneveket is látják (a továbbítón keresztül). Az Azure rekurzív feloldóihoz a 168.63.129.16 virtuális IP-cím biztosítja a hozzáférést.

A DNS-továbbítás a virtuális hálózatok közötti DNS-feloldáson is lehetővé teszi, és lehetővé teszi a helyszíni gépek számára az Azure által biztosított állomásnevek feloldását. A virtuális gép gazdagépnevének feloldásához a DNS-kiszolgáló virtuális gépnek ugyanabban a virtuális hálózatban kell lennie, és úgy kell konfigurálni, hogy az állomásnév-lekérdezéseket az Azure-ba továbbítja. Mivel a DNS-utótag minden virtuális hálózatban eltérő, feltételes továbbítási szabályokkal DNS-lekérdezéseket küldhet a megfelelő virtuális hálózatra feloldás érdekében. Az alábbi képen két virtuális hálózat és egy helyszíni hálózat látható, amely DNS-feloldáson keresztül működik a virtuális hálózatok között ezzel a módszerrel. Dns-továbbító például az [Azure gyorsindítási](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) sablonok katalógusában és a [GitHubon érhető el.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder)

> [!NOTE]
> A szerepkörpéldányok az azonos virtuális hálózaton belüli virtuális gépek névfeloldási műveletét hajtják végre. Ezt a virtuális gép gazdagépnevét és DNS-utótagját internal.cloudapp.net FQDN **használatával** teszi meg. Ebben az esetben azonban a névfeloldás csak akkor sikeres, ha a szerepkörpéldány virtuálisgép-neve meg van határozva a [szerepkörsémában (.cscfg fájl).](/previous-versions/azure/reference/jj156212(v=azure.100))
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Azokat a szerepkörpéldányokat, amelyek egy másik virtuális hálózatban lévő virtuális gépek névfeloldását (FQDN-t az **internal.cloudapp.net-utótag** használatával) kell végrehajtaniuk, az ebben a szakaszban leírt metódussal kell elvégezniük (egyéni DNS-kiszolgálók továbbítása a két virtuális hálózat között).
>

![Virtuális hálózatok közötti DNS diagramja](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Az Azure által biztosított névfeloldás használata esetén az Azure Dynamic Host Configuration Protocol (DHCP) belső DNS-utótagot (**.internal.cloudapp.net**) biztosít minden virtuális gépnek. Ez az utótag lehetővé teszi a gazdagépnév-feloldás beállítását, mivel a gazdagépnév-rekordok a internal.cloudapp.net **vannak.** Ha saját névfeloldási megoldást használ, ez az utótag nem lesz megadva a virtuális gépek számára, mert zavar más DNS-architektúrákat (például tartományhoz csatlakozott forgatókönyveket). Ehelyett az Azure egy nem működőhelyőrzőt (reddog.microsoft.com ).

Szükség esetén a Belső DNS-utótag a PowerShell vagy az API használatával határozható meg:

* Az Azure Resource Manager-alapú üzembe helyezési modellekben lévő virtuális hálózatok esetében az utótag a [hálózati adapteren (REST API)](/rest/api/virtualnetwork/networkinterfaces), a [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) PowerShell-parancsmagon és az [az network nic show](/cli/azure/network/nic#az_network_nic_show) Azure CLI-parancson keresztül érhető el.
* A klasszikus üzembe helyezési modellekben az utótag a [Get Deployment API](/previous-versions/azure/reference/ee460804(v=azure.100)) hívással vagy a [Get-AzureVM -Debug](/powershell/module/servicemanagement/azure.service/get-azurevm) parancsmaggal érhető el.

Ha a lekérdezések Az Azure-ba való továbbítása nem megfelelő az Ön igényeinek, saját DNS-megoldást kell biztosítania. A DNS-megoldásnak a következőt kell:

* Adja meg például a megfelelő állomásnévfeloldási adatokat a [DDNS-en](virtual-networks-name-resolution-ddns.md)keresztül. Ha DDNS-t használ, előfordulhat, hogy le kell tiltania a DNS-rekordok gyorsítótárazását. Az Azure DHCP-bérletek hosszúak, és a gyorsítótárazás idő előtt eltávolíthatja a DNS-rekordokat. 
* Adjon meg megfelelő rekurzív feloldási megoldást a külső tartománynevek feloldásának lehetővé teszi.
* Elérhetőnek kell lennie (TCP és UDP az 53-as porton) a kiszolgáló ügyfelekről, és képesnek kell lennie az internet elérésére.
* Az internetről való hozzáférés ellen kell védelmet biztosítani a külső ügynökök által jelentett fenyegetések mérséklése érdekében.

> [!NOTE]
> A legjobb teljesítmény érdekében, ha Azure-beli virtuális gépeket használ DNS-kiszolgálóként, az IPv6-ot le kell tiltani.

### <a name="web-apps"></a>Webalkalmazások
Tegyük fel, hogy az ugyanazon a virtuális hálózaton található virtuális gépekhez App Service virtuális hálózathoz kapcsolt webalkalmazásból kell névfeloldási műveletet végeznie. A lekérdezéseket az Azure-ba továbbító DNS-továbbítóval (168.63.129.16 virtuális IP-cím) egy egyéni DNS-kiszolgáló beállítása mellett hajtsa végre a következő lépéseket:
1. Ha még nem tette meg, engedélyezze a virtuális hálózati integrációt a webalkalmazás számára az Alkalmazás integrálása [virtuális hálózattalcímen leírtak szerint.](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
2. A Azure Portal a webalkalmazást üzemeltető App Service hálózat szinkronizálása lehetőséget  a **Hálózat,** Virtual Network **integráció alatt.**

    ![Képernyőkép a virtuális hálózat névfeloldásról](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Ha egy virtuális hálózathoz csatolt App Service használatával létrehozott webalkalmazásból kell névfeloldási műveletet végeznie egy másik virtuális hálózatban lévő virtuális gépekhez, egyéni DNS-kiszolgálókat kell használnia mindkét virtuális hálózaton az alábbiak szerint:

* Állítson be egy DNS-kiszolgálót a cél virtuális hálózaton egy olyan virtuális gépen, amely a lekérdezéseket az Azure rekurzív feloldójának is továbbítja (virtuális IP-cím: 168.63.129.16). Dns-továbbító például az [Azure gyorsindítási](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) sablonok katalógusában és a [GitHubon érhető el.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder) 
* Állítson be egy DNS-továbbító a forrás virtuális hálózaton egy virtuális gépen. A DNS-továbbító konfigurálható úgy, hogy a lekérdezéseket a cél virtuális hálózat DNS-kiszolgálójának továbbadja.
* Konfigurálja a forrás DNS-kiszolgálót a forrás virtuális hálózat beállításaiban.
* Engedélyezze a virtuális hálózati integrációt a webalkalmazás számára a forrás virtuális hálózathoz való kapcsolódáshoz az Alkalmazás integrálása virtuális [hálózattal útmutatását követve.](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* A Azure Portal a webalkalmazást üzemeltető App Service hálózat szinkronizálása lehetőséget  a **Hálózat,** Virtual Network **integráció alatt.**

## <a name="specify-dns-servers"></a>DNS-kiszolgálók megadása
Ha saját DNS-kiszolgálókat használ, az Azure lehetővé teszi több DNS-kiszolgáló megadását virtuális hálózatonként. Hálózati adapterenként (az Azure Resource Manager esetében) vagy felhőszolgáltatásonként (a klasszikus üzemi modell esetében) is megadhat több DNS-kiszolgálót. A hálózati adapterhez vagy felhőszolgáltatáshoz megadott DNS-kiszolgálók elsőbbséget élveznek a virtuális hálózathoz megadott DNS-kiszolgálókkal.

> [!NOTE]
> A hálózati kapcsolat tulajdonságai, például a DNS-kiszolgáló IP-címei, nem szerkeszthetők közvetlenül a virtuális gépeken belül. Ennek az az oka, hogy a virtuális hálózati adapter cseréjekor előfordulhat, hogy a szolgáltatás szervize során törlődnek. Ez a Windows és Linux rendszerű virtuális gépekre is vonatkozik.

Az üzembe helyezési modell Azure Resource Manager dns-kiszolgálókat is megadhat egy virtuális hálózathoz és egy hálózati adapterhez. Részletekért [lásd: Virtuális](manage-virtual-network.md) hálózat kezelése és [Hálózati adapter kezelése.](virtual-network-network-interface.md)

> [!NOTE]
> Ha egyéni DNS-kiszolgálót választ a virtuális hálózathoz, meg kell adnia legalább egy DNS-kiszolgáló IP-címét; ellenkező esetben a virtuális hálózat figyelmen kívül hagyja a konfigurációt, és ehelyett az Azure által biztosított DNS-t használja.

A klasszikus üzembe helyezési modell használata esetén a virtuális hálózat DNS-kiszolgálói a Azure Portal hálózati konfigurációs fájlban [adhatók meg.](/previous-versions/azure/reference/jj157100(v=azure.100)) Felhőszolgáltatások esetén a DNS-kiszolgálókat [](/previous-versions/azure/reference/ee758710(v=azure.100)) a szolgáltatáskonfigurációs fájlon keresztül vagy a PowerShell használatával adhatja meg a [New-AzureVM parancs használatával.](/powershell/module/servicemanagement/azure.service/new-azurevm)

> [!NOTE]
> Ha módosítja egy már telepített virtuális hálózat vagy virtuális gép DNS-beállításait, az új DNS-beállítások végrehajtásához DHCP-címbérlet megújítását kell végrehajtania a virtuális hálózat összes érintett virtuális gépére. A Windows operációs rendszert futtató virtuális gépek esetén ezt közvetlenül a virtuális gépbe begépelve `ipconfig /renew` használhatja. A lépések az operációs rendszertől függően változnak. Tekintse meg az operációs rendszer típusának megfelelő dokumentációt.

## <a name="next-steps"></a>Következő lépések

Azure Resource Manager modell:

* [Virtuális hálózat kezelése](manage-virtual-network.md)
* [Hálózati adapter kezelése](virtual-network-network-interface.md)

Klasszikus üzembe helyezési modell:

* [Azure-szolgáltatás konfigurációs sémája](/previous-versions/azure/reference/ee758710(v=azure.100))
* [Virtual Network konfigurációs séma](/previous-versions/azure/reference/jj157100(v=azure.100))
* [Hálózati Virtual Network konfigurálása hálózati konfigurációs fájl használatával](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file)