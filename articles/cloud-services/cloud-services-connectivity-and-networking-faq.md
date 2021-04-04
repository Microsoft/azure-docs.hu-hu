---
title: Kapcsolatokkal és hálózatkezeléssel kapcsolatos problémák
description: Ez a cikk a Microsoft Azure Cloud Services kapcsolatával és hálózatkezelésével kapcsolatos gyakori kérdéseket sorolja fel.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c7b83c615e4ac19e10b5c4f6cc1a102206b1a39a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742420"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Az Azure Cloud Services (klasszikus) kapcsolódási és hálózatkezelési problémái: gyakori kérdések (GYIK)

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Ez a cikk az [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)kapcsolati és hálózatkezelési problémáival kapcsolatos gyakori kérdéseket tartalmazza. A mérettel kapcsolatos információkért tekintse meg a virtuálisgép- [méret Cloud Servicesét ismertető oldalt](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Nem tudok IP-címet fenntartani egy több-VIP felhőalapú szolgáltatásban.
Először ellenőrizze, hogy be van-e kapcsolva az IP-címet lefoglaló virtuálisgép-példány. Másodszor, győződjön meg arról, hogy fenntartott IP-címeket használ mind az előkészítési, mind a éles üzembe helyezéshez. A telepítés frissítésekor *ne* módosítsa a beállításokat.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Hogyan használja Távoli asztal, ha van NSG?
Vegyen fel szabályokat a NSG, amelyek engedélyezik a forgalmat a **3389** -es és a **20000**-es porton. Távoli asztal a **3389**-es portot használja. A Cloud Service-példányok terheléselosztás alatt állnak, így nem lehet közvetlenül szabályozni, hogy melyik példányt kívánja csatlakozni. A *RemoteForwarder* és a *remoteaccess* ügynök felügyeli az RDP protokoll (RDP) forgalmat, és lehetővé teszi az ügyfél számára, hogy RDP-cookie-t küldjön, és megadhat egy egyéni példányt a kapcsolódáshoz. A *RemoteForwarder* és a *remoteaccess* -ügynöknek nyitva kell lennie az **20000** -as porthoz, ami blokkolható lehet, ha van NSG.

## <a name="can-i-ping-a-cloud-service"></a>Tudok pingelni egy felhőalapú szolgáltatást?

Nem, nem a normál "ping"/ICMP protokoll használatával. Az ICMP protokoll használata nem engedélyezett az Azure Load balancerben.

A kapcsolat teszteléséhez javasoljuk, hogy végezzen ping portot. Míg a Ping.exe ICMP protokollt használ, más eszközöket (például PSPing, nmap és Telnet) is használhat egy adott TCP-porthoz való csatlakozás teszteléséhez.

További információ: [Az Azure-beli virtuális gépek kapcsolatának tesztelése az ICMP helyett a portokat használó pingek használata](/archive/blogs/mast/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Hogyan az ismeretlen IP-címekről érkező több ezer találat fogadását, amelyek rosszindulatú támadást jelezhetnek a felhőalapú szolgáltatásban?
Az Azure egy többrétegű hálózati biztonságot valósít meg a platform szolgáltatásainak az elosztott szolgáltatásmegtagadási (DDoS) támadások elleni védelme érdekében. Az Azure DDoS védelmi rendszer az Azure folyamatos figyelési folyamatának része, amely folyamatosan javul a penetrációs tesztelésen keresztül. Ez a DDoS védelmi rendszer úgy lett kialakítva, hogy ne csak a külső támadásoktól, hanem más Azure-bérlőtől is ellenálljanak. További információ: [Azure Network Security](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Létrehozhat egy indítási feladatot is, amely szelektív módon blokkol bizonyos IP-címeket. További információ: [egy adott IP-cím blokkolása](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Amikor megpróbálok RDP-t létesíteni a Cloud Service-példánnyal, a következő üzenet jelenik meg: "a felhasználói fiók lejárt."
Előfordulhat, hogy a "Ez a felhasználói fiók lejárt" hibaüzenet jelenik meg, ha megkerüli az RDP-beállításokban konfigurált lejárati dátumot. A következő lépésekkel módosíthatja a lejárati dátumot a portálról:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), lépjen a Cloud Service-be, és válassza a **Távoli asztal** lapot.

2. Válassza ki a **termelési** vagy **előkészítési** üzembe helyezési tárolóhelyet.

3. Módosítsa a **lejárat** dátumát, majd mentse a konfigurációt.

Most már képesnek kell lennie a gép RDP-jére.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Miért nem egyenlően osztja el a forgalmat az Azure Load Balancer?
További információ a belső terheléselosztó működéséről: [Azure Load Balancer új terjesztési mód](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

A használt terjesztési algoritmus egy 5 rekordos (forrás IP-cím, forrásport, cél IP-cím, célport és protokolltípus) kivonat, amely az elérhető kiszolgálók forgalmát térképezi fel. Csak a szállítási munkameneten belül nyújt stickiet. Az azonos TCP-vagy UDP-munkamenetben lévő csomagok ugyanarra az adatközponti IP-(DIP-) példányra vannak irányítva, amely a terheléselosztási végpont mögött található. Amikor az ügyfél bezárja és újra megnyitja a kapcsolatot, vagy egy új munkamenetet indít el ugyanabból a forrás IP-címről, a forrásport megváltozik, és a forgalom egy másik DIP-végpontra lép.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Hogyan lehet átirányítani a bejövő forgalmat a Cloud Service alapértelmezett URL-címére egy egyéni URL-címre?

Az IIS URL-Újraírási moduljának használatával átirányíthatja a felhőalapú szolgáltatás (például. cloudapp.net) alapértelmezett URL-címére irányuló forgalmat \* egy egyéni névre vagy URL-címre. Mivel az URL-cím újraírása modul alapértelmezés szerint engedélyezve van a webes szerepkörökben, és a szabályai konfigurálva vannak az alkalmazás web.configjában, az újraindítások/rendszerképektől függetlenül mindig elérhető a virtuális gépen. További információt a következő témakörben talál:

- [Újraírási szabályok létrehozása az URL-átírási modulhoz](/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Alapértelmezett hivatkozás eltávolítása](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Hogyan lehet letiltani vagy letiltani a bejövő forgalmat a felhőalapú szolgáltatás alapértelmezett URL-címére?

Megakadályozhatja a bejövő forgalmat a felhőalapú szolgáltatás alapértelmezett URL-címére/nevére (például \* . cloudapp.net). Állítsa a gazdagép fejlécét egy egyéni DNS-névre (például a www \. MyCloudService.com) a hely kötési konfigurációja területen a Cloud Service Definition (*. csdef) fájlban, a jelzett módon:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

Mivel ez a állomásfejléc-kötés a csdef-fájlban van kikényszerítve, a szolgáltatás csak a "www.MyCloudService.com" egyéni névvel érhető el. A "*. cloudapp.net" tartományba irányuló összes bejövő kérelem mindig meghiúsul. Ha egyéni SLB-mintavételt vagy belső terheléselosztó szolgáltatást használ a szolgáltatásban, akkor a szolgáltatás alapértelmezett URL-címének/nevének blokkolása zavarhatja a szondázás viselkedését.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Hogyan lehet meggyőződni arról, hogy egy felhőalapú szolgáltatás nyilvános IP-címe soha nem változik?

Annak biztosítása érdekében, hogy a felhőalapú szolgáltatás (más néven VIP) nyilvános IP-címe ne módosuljon, hogy az egyes ügyfelek csak néhány konkrét ügyfelet fogadnak el, javasoljuk, hogy a hozzá tartozó fenntartott IP-címet. Ellenkező esetben az Azure által biztosított virtuális IP-cím le van foglalva az előfizetésből, ha törli az üzemelő példányt. A sikeres VIP-swap művelethez egyedi, fenntartott IP-címekre van szükség a termelési és az előkészítési tárolóhelyek esetében. Enélkül a swap művelet sikertelen lesz. Az IP-cím lefoglalásához és a felhőalapú szolgáltatáshoz való hozzárendeléséhez tekintse meg a következő cikkeket:

- [Meglévő felhőalapú szolgáltatás IP-címének lefoglalása](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
- [Fenntartott IP-cím hozzárendelése egy felhőalapú szolgáltatáshoz szolgáltatás-konfigurációs fájl használatával](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Ha több példánya van a szerepkörökhöz, a RIP és a felhőalapú szolgáltatás társítása nem okozhat állásidőt. Azt is megteheti, hogy hozzáadja az Azure-adatközpont IP-tartományát az engedélyezési listához. Az összes Azure IP-tartomány megtalálható a [Microsoft letöltőközpontból](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Ez a fájl tartalmazza az Azure-adatközpontokban használt IP-címtartományt (beleértve a számítási, az SQL-és a tárolási tartományokat). A rendszer hetente közzétesz egy frissített fájlt, amely a jelenleg telepített tartományokat és az IP-címtartományok közelgő változásait tükrözi. A fájlban megjelenő új tartományok legalább egy hétig nem használhatók az adatközpontokban. Töltse le hetente az új. xml fájlt, és végezze el a szükséges módosításokat a webhelyen, hogy megfelelően azonosítsa az Azure-ban futó szolgáltatásokat. Az Azure ExpressRoute felhasználói láthatják, hogy ez a fájl az Azure Space BGP-hirdetményének minden hónap első hetében való frissítésére szolgál.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Hogyan használhatom Azure Resource Manager virtuális hálózatokat a Cloud Services szolgáltatással?

A Cloud Services nem helyezhető Azure Resource Manager virtuális hálózatba. A Resource Manager-alapú virtuális hálózatok és a klasszikus üzembe helyezési virtuális hálózatok egymással is csatlakoztathatók. További információ: [Virtual Network peering](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Hogyan szerezhetem be az Cloud Services által használt nyilvános IP-címek listáját?

A következő PS-parancsfájl használatával lekérheti az előfizetéshez tartozó Cloud Services nyilvános IP-címeinek listáját

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```