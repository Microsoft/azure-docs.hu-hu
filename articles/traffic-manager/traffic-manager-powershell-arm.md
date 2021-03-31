---
title: Traffic Manager kezelése a PowerShell használatával az Azure-ban
description: Ez a képzési terv a Traffic Manager Azure PowerShell használatának első lépéseiben nyújt segítséget.
services: traffic-manager
documentationcenter: na
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 21076fe23301c189d9987f78706cc81691ce7a4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89400534"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Traffic Manager kezelése a PowerShell használatával

Azure Resource Manager az Azure-szolgáltatások előnyben részesített kezelőfelülete. Az Azure Traffic Manager profilokat Azure Resource Manager-alapú API-kkal és eszközökkel lehet felügyelni.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Erőforrásmodell

Az Azure Traffic Manager Traffic Manager profil nevű beállításcsoport használatával van konfigurálva. Ez a profil a DNS-beállításokat, a forgalmi útválasztási beállításokat, a végpont-figyelési beállításokat és azon szolgáltatási végpontok listáját tartalmazza, amelyeknek a forgalmát át kell irányítani.

Minden Traffic Manager-profilt egy "TrafficManagerProfiles" típusú erőforrás képvisel. A REST API szinten az egyes profilok URI-ja a következő:

`https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}`

## <a name="setting-up-azure-powershell"></a>Azure PowerShell beállítása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ezek az utasítások a Microsoft Azure PowerShell. A következő cikk a Azure PowerShell telepítésének és konfigurálásának módját ismerteti.

* [How to install and configure Azure PowerShell (Az Azure PowerShell telepítése és konfigurálása)](/powershell/azure/)

A cikkben szereplő példák azt feltételezik, hogy van egy meglévő erőforráscsoport. Hozzon létre egy erőforráscsoportot a következő parancs használatával:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager megköveteli, hogy minden erőforráscsoport rendelkezzen egy hellyel. A rendszer ezt a helyet használja alapértelmezettként az adott erőforráscsoporthoz létrehozott erőforrásokhoz. Mivel azonban a Traffic Manager profil erőforrásai globálisak, nem regionálisak, az erőforráscsoport helyének megválasztása nem befolyásolja az Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Traffic Manager-profil létrehozásához használja a következő `New-AzTrafficManagerProfile` parancsmagot:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

A következő táblázat ismerteti a paramétereket:

| Paraméter | Leírás |
| --- | --- |
| Név |Az Traffic Manager-profil erőforrásának neve. Az azonos erőforráscsoporthoz tartozó profiloknak egyedi névvel kell rendelkezniük. Ez a név nem azonos a DNS-lekérdezésekhez használt DNS-névvel. |
| ResourceGroupName |A profil erőforrását tartalmazó erőforráscsoport neve. |
| TrafficRoutingMethod |Megadja a forgalom-útválasztási módszert, amellyel meghatározható, hogy melyik végpontot adja vissza a rendszer a DNS-lekérdezés válaszában. A lehetséges értékek a következők: "Performance", "súlyozott" vagy "priority". |
| RelativeDnsName |Megadja a Traffic Manager profil által megadott DNS-név állomásnév részét. Ez az érték az Azure Traffic Manager által használt DNS-tartománynévvel együtt alkotja a profil teljes tartománynevét (FQDN). A "contoso" értékének beállítása például "contoso.trafficmanager.net" lesz. |
| TTL |Megadja a DNS élettartamát (TTL) másodpercben. Ez a TTL tájékoztatja a helyi DNS-feloldókat és a DNS-ügyfeleket arról, hogy mennyi ideig kell gyorsítótárazni a DNS-válaszokat ehhez a Traffic Manager profilhoz. |
| MonitorProtocol |Meghatározza a végpont állapotának figyeléséhez használandó protokollt. A lehetséges értékek a következők: "HTTP" és "HTTPS". |
| MonitorPort |Meghatározza a végpont állapotának figyeléséhez használt TCP-portot. |
| MonitorPath |Megadja a végponti állapot mintavételéhez használt végponti tartománynévhez viszonyított elérési utat. |

A parancsmag létrehoz egy Traffic Manager-profilt az Azure-ban, és egy megfelelő profil-objektumot ad vissza a PowerShell-nek. Ezen a ponton a profil nem tartalmaz végpontokat. A végpontok Traffic Manager profilhoz való hozzáadásával kapcsolatos további információkért lásd: Traffic Manager-végpontok hozzáadása.

## <a name="get-a-traffic-manager-profile"></a>Traffic Manager profil beolvasása

Meglévő Traffic Manager profil objektum beolvasásához használja a következő `Get-AzTrafficManagerProfle` parancsmagot:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Ez a parancsmag egy Traffic Manager profil objektumot ad vissza.

## <a name="update-a-traffic-manager-profile"></a>Traffic Manager-profil frissítése

Traffic Manager profilok módosítása 3 lépésből álló folyamatot követ:

1. Kérje le a profilt a használatával `Get-AzTrafficManagerProfile` , vagy használja a által visszaadott profilt `New-AzTrafficManagerProfile` .
2. Módosítsa a profilt. Hozzáadhat és eltávolíthat végpontokat, illetve módosíthatja a végpontok vagy a profilok paramétereit. Ezek a módosítások off-line műveletekkel rendelkeznek. Csak a profilt jelölő, a memóriában lévő helyi objektumot módosítja.
3. Véglegesítse a módosításokat a `Set-AzTrafficManagerProfile` parancsmag használatával.

Az összes profil tulajdonságai módosíthatók a profil RelativeDnsName kivételével. A RelativeDnsName módosításához új névvel kell törölnie a profilt és egy új profilt.

Az alábbi példa bemutatja, hogyan módosíthatja a profil TTL-értékét:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

A Traffic Manager végpontok három típusa létezik:

1. Az **Azure-végpontok** az Azure-ban üzemeltetett szolgáltatások
2. A **külső végpontok** az Azure-on kívül üzemeltetett szolgáltatások
3. A **beágyazott végpontok** Traffic Manager profilok beágyazott hierarchiáinak létrehozására használhatók. A beágyazott végpontok lehetővé teszik a speciális forgalom – az összetett alkalmazások útválasztási konfigurációit.

Mindhárom esetben a végpontok kétféleképpen vehetők fel:

1. A korábban leírt 3 lépésből álló folyamat használatával. Ennek a módszernek az az előnye, hogy egyetlen frissítésben több végpont-módosítás is elvégezhető.
2. Az New-AzTrafficManagerEndpoint parancsmag használata. Ez a parancsmag egy végpontot hoz létre egy meglévő Traffic Manager-profilhoz egyetlen művelettel.

## <a name="adding-azure-endpoints"></a>Azure-végpontok hozzáadása

Azure-végpontok referenciái az Azure-ban üzemeltetett szolgáltatásokhoz. Az Azure-végpontok két típusa támogatott:

1. Azure App Service
2. Azure PublicIpAddress-erőforrások (amelyek egy terheléselosztó vagy egy virtuális gép hálózati adapteréhez csatlakoztathatók). A PublicIpAddress hozzá kell rendelni a Traffic Managerhoz használandó DNS-nevet.

Minden esetben:

* A szolgáltatás a vagy a "Targetresourceid azonosítója" paraméterének használatával van megadva `Add-AzTrafficManagerEndpointConfig` `New-AzTrafficManagerEndpoint` .
* A Targetresourceid azonosítója a "Target" és a "EndpointLocation" jelölést is feltételezi.
* A "Weight" megadása nem kötelező. A súlyok használata csak akkor történik meg, ha a profil úgy van konfigurálva, hogy a "súlyozott" forgalom-útválasztási módszert használja. Ellenkező esetben a rendszer figyelmen kívül hagyja őket. Ha meg van adva, az értéknek 1 és 1000 közötti számnak kell lennie. Az alapértelmezett érték: "1".
* A "priority" megadása nem kötelező. A prioritásokat csak akkor használja a rendszer, ha a profil a "priority" forgalom – útválasztási módszer használatára van konfigurálva. Ellenkező esetben a rendszer figyelmen kívül hagyja őket. Az érvényes értékek 1 és 1000 közöttiek, a magasabb prioritást jelző alacsonyabb értékekkel. Ha egy végponthoz meg van adva, azt az összes végponthoz meg kell adni. Ha nincs megadva, a rendszer az "1" kezdetű alapértelmezett értékeket alkalmazza a végpontok listájának sorrendjében.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>1. példa: App Service-végpontok hozzáadása a használatával `Add-AzTrafficManagerEndpointConfig`

Ebben a példában egy Traffic Manager profilt hozunk létre, és két App Service végpontot adunk hozzá a `Add-AzTrafficManagerEndpointConfig` parancsmag használatával.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>2. példa: publicIpAddress-végpont hozzáadása a használatával `New-AzTrafficManagerEndpoint`

Ebben a példában egy nyilvános IP-cím erőforrás kerül a Traffic Manager-profilba. A nyilvános IP-címnek konfigurálnia kell egy DNS-nevet, és a virtuális gép hálózati adapteréhez vagy egy terheléselosztóhoz is köthető.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Külső végpont hozzáadása

A Traffic Manager külső végpontokat használ az Azure-on kívül üzemeltetett szolgáltatásokhoz való közvetlen forgalomra. Az Azure-végpontokhoz hasonlóan a külső végpontok is hozzáadhatók `Add-AzTrafficManagerEndpointConfig` , amelyeket a követ `Set-AzTrafficManagerProfile` , vagy `New-AzTrafficManagerEndpoint` .

Külső végpontok megadásakor:

* Meg kell adni a végpont tartománynevét a "Target" paraméter használatával.
* Ha a "Performance" forgalom – útválasztási módszer van használatban, a "EndpointLocation" megadása kötelező. Ellenkező esetben nem kötelező. Az értéknek [érvényes Azure-régió nevének](https://azure.microsoft.com/regions/)kell lennie.
* A "Weight" és a "priority" nem kötelező.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>1. példa: külső végpontok hozzáadása a és a használatával `Add-AzTrafficManagerEndpointConfig``Set-AzTrafficManagerProfile`

Ebben a példában egy Traffic Manager profilt hozunk létre, két külső végpontot adunk hozzá, és véglegesítjük a módosításokat.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>2. példa: külső végpontok hozzáadása a használatával `New-AzTrafficManagerEndpoint`

Ebben a példában egy külső végpontot adunk hozzá egy meglévő profilhoz. A profil a profil és az erőforráscsoport neve alapján van megadva.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>"Beágyazott" végpontok hozzáadása

Mindegyik Traffic Manager profil egyetlen forgalom-útválasztási módszert határoz meg. Vannak azonban olyan forgatókönyvek, amelyekkel összetettebb forgalmi útválasztás szükséges, mint az egyetlen Traffic Manager profil által biztosított útválasztás. Traffic Manager-profilok beágyazásával több forgalom-útválasztási módszer előnyeit is összekapcsolhatja. A beágyazott profilok lehetővé teszik az alapértelmezett Traffic Manager viselkedés felülbírálását a nagyobb és összetettebb alkalmazások központi telepítésének támogatásához. Részletesebb példák: [beágyazott Traffic Manager profilok](traffic-manager-nested-profiles.md).

A beágyazott végpontok a szülő profilban konfigurálhatók, egy adott végpont ("NestedEndpoints") használatával. Beágyazott végpontok megadásakor:

* Meg kell adni a végpontot a "Targetresourceid azonosítója" paraméter használatával.
* Ha a "Performance" forgalom – útválasztási módszer van használatban, a "EndpointLocation" megadása kötelező. Ellenkező esetben nem kötelező. Az értéknek [érvényes Azure-régió nevének](https://azure.microsoft.com/regions/)kell lennie.
* Az Azure-végpontok esetében a "Weight" és a "priority" nem kötelező.
* A "MinChildEndpoints" paraméter megadása nem kötelező. Az alapértelmezett érték: "1". Ha a rendelkezésre álló végpontok száma a küszöbérték alá esik, a szülő profil a "csökkentett teljesítményű" osztályt veszi figyelembe, és a fölérendelt profil többi végpontján átirányítja a forgalmat.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>1. példa: beágyazott végpontok hozzáadása a és a használatával `Add-AzTrafficManagerEndpointConfig``Set-AzTrafficManagerProfile`

Ebben a példában új Traffic Manager gyermek-és szülő-profilt hozunk létre, adja hozzá a gyermeket beágyazott végpontként a szülőhöz, és véglegesítse a módosításokat.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

Ebben a példában a rövidség kedvéért nem adunk hozzá más végpontokat a gyermek-vagy szülő profilokhoz.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>2. példa: beágyazott végpontok hozzáadása a használatával `New-AzTrafficManagerEndpoint`

Ebben a példában egy meglévő alárendelt profilt adunk hozzá beágyazott végpontként egy meglévő szülő profilhoz. A profil a profil és az erőforráscsoport neve alapján van megadva.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Végpontok hozzáadása egy másik előfizetésből

A Traffic Manager különböző előfizetések végpontokkal is működhetnek. Az előfizetésre kell váltania a hozzáadni kívánt végponttal, hogy lekérje a szükséges bemenetet Traffic Manager. Ezután a Traffic Manager profillal kell váltania az előfizetésekhez, és hozzá kell adnia a végpontot. Az alábbi példa bemutatja, hogyan teheti ezt meg egy nyilvános IP-címmel.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Traffic Manager végpont frissítése

Egy meglévő Traffic Manager végpontot kétféleképpen frissíthet:

1. Szerezze be a Traffic Manager profilt a használatával `Get-AzTrafficManagerProfile` , frissítse a végpont tulajdonságait a profilon belül, és véglegesítse a módosításokat a használatával `Set-AzTrafficManagerProfile` . Ennek a módszernek az az előnye, hogy egynél több végpontot tud frissíteni egyetlen műveletben.
2. Szerezze be a Traffic Manager végpontot a használatával `Get-AzTrafficManagerEndpoint` , frissítse a végpont tulajdonságait, és véglegesítse a módosításokat a használatával `Set-AzTrafficManagerEndpoint` . Ez a módszer egyszerűbb, mivel nem igényli az indexelést a profilban található végpontok tömbbe.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>1. példa: végpontok frissítése a és a használatával `Get-AzTrafficManagerProfile``Set-AzTrafficManagerProfile`

Ebben a példában a prioritást két végpontra módosítjuk egy meglévő profilon belül.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>2. példa: végpont frissítése a `Get-AzTrafficManagerEndpoint` és a használatával `Set-AzTrafficManagerEndpoint`

Ebben a példában egyetlen végpont súlyát módosítjuk egy meglévő profilban.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Végpontok és profilok engedélyezése és letiltása

Traffic Manager lehetővé teszi az egyes végpontok engedélyezését és letiltását, valamint lehetővé teszi a teljes profilok engedélyezését és letiltását.
Ezek a módosítások a végpont vagy a profil erőforrásainak beolvasásával/frissítésével vagy beállításával hajthatók végre. Ezeknek a gyakori műveleteknek a egyszerűsítése érdekében a dedikált parancsmagokon keresztül is támogatottak.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>1. példa: Traffic Manager profil engedélyezése és letiltása

Traffic Manager profil engedélyezéséhez használja a következőt: `Enable-AzTrafficManagerProfile` . A profil megadható egy profil objektum használatával. A profil objektum a folyamaton keresztül vagy a "-TrafficManagerProfile" paraméter használatával adható át. Ebben a példában a profilt a profil és az erőforráscsoport neve alapján adjuk meg.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Traffic Manager profil letiltása:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

A Disable-AzTrafficManagerProfile parancsmag megerősítést kér. Ezt a kérést a "-Force" paraméter használatával lehet letiltani.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>2. példa: Traffic Manager végpont engedélyezése és letiltása

Traffic Manager végpont engedélyezéséhez használja a következőt: `Enable-AzTrafficManagerEndpoint` . A végpontot kétféleképpen lehet megadni

1. A folyamaton keresztül átadott TrafficManagerEndpoint objektum vagy a "-TrafficManagerEndpoint" paraméter használata
2. A végpont neve, a végpont típusa, a profil neve és az erőforráscsoport neve:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Hasonlóképpen, ha le szeretne tiltani egy Traffic Manager végpontot:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

A `Disable-AzTrafficManagerProfile` `Disable-AzTrafficManagerEndpoint` rendszerhez hasonlóan a parancsmag megerősítést kér. Ezt a kérést a "-Force" paraméter használatával lehet letiltani.

## <a name="delete-a-traffic-manager-endpoint"></a>Traffic Manager végpont törlése

Az egyes végpontok eltávolításához használja a következő `Remove-AzTrafficManagerEndpoint` parancsmagot:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Ez a parancsmag megerősítést kér. Ezt a kérést a "-Force" paraméter használatával lehet letiltani.

## <a name="delete-a-traffic-manager-profile"></a>Traffic Manager-profil törlése

Traffic Manager-profil törléséhez használja a `Remove-AzTrafficManagerProfile` parancsmagot a profil és az erőforráscsoport nevének megadásával:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Ez a parancsmag megerősítést kér. Ezt a kérést a "-Force" paraméter használatával lehet letiltani.

A törlendő profil is megadható egy profil objektummal:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Ez a folyamat is lehet vezetékes:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Következő lépések

[Traffic Manager figyelés](traffic-manager-monitoring.md)

[A Traffic Manager teljesítményével kapcsolatos megfontolások](traffic-manager-performance-considerations.md)
