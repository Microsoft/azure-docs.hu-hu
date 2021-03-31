---
title: 'Azure ExpressRoute: társítás beállítása: klasszikus'
description: A cikk az ExpressRoute-kapcsolatcsoportok privát, nyilvános és Microsoft társviszony-létesítéses létrehozásának és kiépítésének lépéseit ismerteti. A cikk azt is bemutatja, hogyan ellenőrizheti a kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti őket.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.openlocfilehash: a4a3bad1e868fa0e75611630ffb5db5ba13126b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89395553"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Peering létrehozása és módosítása ExpressRoute-áramkörhöz (klasszikus)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Videó – privát peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó – nyilvános peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 

Ez a cikk végigvezeti egy ExpressRoute-áramkör társítási/útválasztási konfigurációjának a PowerShell és a klasszikus üzemi modell használatával történő létrehozásának és kezelésének lépésein. Az alábbi lépések azt is bemutatják, hogyan ellenőrizheti az ExpressRoute-kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti és szüntetheti meg őket. Egy ExpressRoute áramkörhöz beállíthat egy, kettő vagy mindhárom (Azure Private, Azure Public és Microsoft) társítást. A társviszony-létesítéseket tetszőleges sorrendben konfigurálhatja. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. 

Ezek az utasítások csak a 2. rétegbeli kapcsolati szolgáltatásokat kínáló szolgáltatóktól létrehozott áramkörökre vonatkoznak. Ha olyan szolgáltatót használ, amely felügyelt 3. rétegbeli szolgáltatásokat kínál (jellemzően IPVPN, például MPLS), akkor a kapcsolati szolgáltató konfigurálja és kezeli az útválasztást.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Az útmutatást követve [hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-classic.md) , és a folytatás előtt engedélyezze az áramkört a kapcsolat szolgáltatója számára. Az ExpressRoute-kapcsolatcsoportnak kiosztott és engedélyezett állapotban kell lennie, hogy az alább ismertetett parancsmagokat futtatni lehessen.

### <a name="download-the-latest-powershell-cmdlets"></a>A legújabb PowerShell-parancsmagok letöltése

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="azure-private-peering"></a>Azure-beli privát társhálózat-létesítés

Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Azure privát társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat. 

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. **Hozzon létre egy ExpressRoute áramkört.**

   Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-classic.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure privát társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat.
2. **Ellenőrizze a ExpressRoute áramkört, és győződjön meg róla, hogy kiépítve van.**
   
   Ellenőrizze, hogy az ExpressRoute áramkör kiépítve és engedélyezve van-e.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Eredményük a következő:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Győződjön meg arról, hogy az áramkör kiépítve és engedélyezve van. Ha nem, akkor működjön együtt a kapcsolat szolgáltatójával, hogy az áramkört a szükséges állapotba és állapotba kapja.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoport számára.**

   Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:
   
   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ez nem képezheti semmilyen, virtuális hálózatok számára lefoglalt címtér részét.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ez nem képezheti semmilyen, virtuális hálózatok számára lefoglalt címtér részét.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg arról, hogy az áramkörben lévő más társak nem ugyanazt a VLAN-azonosítót használják.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számokat is. Ellenőrizze, hogy nem a 65515-et használja-e.
   * Egy MD5-kivonat, ha használni kívánja. Nem **kötelező**.
     
   Az alábbi példát követve konfigurálhatja az Azure-beli privát kapcsolatot az áramkörhöz:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   Ha MD5-kivonatot szeretne használni, az alábbi példát követve konfigurálhatja a saját áramkörét:

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > Győződjön meg róla, hogy az AS-számot a társ-és a ügyfél ASN-ként adja meg.
   > 

### <a name="to-view-azure-private-peering-details"></a>Azure privát társviszony-létesítés részleteinek megtekintése

A konfigurációs adatokat a következő parancsmaggal tekintheti meg:

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

Eredményük a következő:

```
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 100
```

### <a name="to-update-azure-private-peering-configuration"></a>Azure privát társviszony-létesítés konfigurációjának frissítése

A konfiguráció bármelyik részét frissítheti az alábbi parancsmag használatával. A következő példában az áramkör VLAN-azonosítója 100 és 500 között frissül.

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>Azure privát társviszony-létesítés törlése

A társviszony-létesítés konfigurációját a következő parancsmag futtatásával törölheti. A parancsmag futtatása előtt győződjön meg arról, hogy az összes virtuális hálózat le van csatolva a ExpressRoute áramkörről.

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Azure nyilvános társviszony-létesítés

Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Azure nyilvános társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat.

> [!NOTE]
> Az Azure nyilvános társítása elavult az új áramköröknél.
>

### <a name="to-create-azure-public-peering"></a>Azure nyilvános társviszony-létesítés létrehozása

1. **ExpressRoute-kapcsolatcsoport létrehozása**

   Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-classic.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure nyilvános társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat.
2. **Ellenőrizze, hogy a ExpressRoute-áramkör kiépítve van-e**

   Először ellenőrizze, hogy az ExpressRoute-kapcsolatcsoport Kiosztott és Engedélyezett állapotban van-e.

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Eredményük a következő:

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Ellenőrizze, hogy az áramkör kiosztottként és Engedélyezveként jelenik-e meg. Ha nem, akkor működjön együtt a kapcsolat szolgáltatójával, hogy az áramkört a szükséges állapotba és állapotba kapja.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **Az Azure-beli nyilvános kapcsolatok konfigurálása az áramkörhöz**
   
   A folytatás előtt győződjön meg arról, hogy rendelkezik a következő információkkal:
   
   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg arról, hogy az áramkörben lévő más társak nem ugyanazt a VLAN-azonosítót használják.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Egy MD5-kivonat, ha használni kívánja. Nem **kötelező**.

   > [!IMPORTANT]
   > Győződjön meg róla, hogy az AS-számot a társítási ASN-ként adja meg, nem pedig a Customer ASN-t.
   >  
     
   Az alábbi példát követve konfigurálhatja az Azure nyilvános társ-összevonást az áramkörhöz:

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   Ha MD5-kivonatot szeretne használni, használja az alábbi példát az áramkör konfigurálásához:
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>Azure nyilvános társviszony-létesítés részleteinek megtekintése

A konfigurációs adatok megtekintéséhez használja a következő parancsmagot:

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

Eredményük a következő:

```powershell
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 131.107.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 131.107.0.4/30
State                          : Enabled
VlanId                         : 200
```

### <a name="to-update-azure-public-peering-configuration"></a>Azure nyilvános társviszony-létesítés konfigurációjának frissítése

A konfiguráció bármelyik részét frissítheti az alábbi parancsmag használatával. Ebben a példában az áramkör VLAN-azonosítója 200 és 600 között frissül.

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

Ellenőrizze, hogy az áramkör kiosztottként és Engedélyezveként jelenik-e meg. 
### <a name="to-delete-azure-public-peering"></a>Azure nyilvános társviszony-létesítés törlése

A következő parancsmag futtatásával távolíthatja el a társ-konfigurációt:

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Microsoftos társhálózat-létesítés

Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat. 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

1. **ExpressRoute-kapcsolatcsoport létrehozása**
  
   Kövesse az utasításokat az [ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-classic.md) létrehozásához, és kérje meg kapcsolatszolgáltatóját, hogy ossza ki azt. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, igényelheti tőle, hogy engedélyezze Önnek az Azure privát társviszony-létesítést. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Ha azonban a kapcsolatszolgáltató felügyeli az útválasztást Ön helyett, a kör létrehozása után kövesse az alábbi utasításokat.
2. **Ellenőrizze, hogy a ExpressRoute-áramkör kiépítve van-e**

   Ellenőrizze, hogy az áramkör kiosztottként és Engedélyezveként jelenik-e meg. 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   Eredményük a következő:
   
   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   Ellenőrizze, hogy az áramkör kiosztottként és Engedélyezveként jelenik-e meg. Ha nem, akkor működjön együtt a kapcsolat szolgáltatójával, hogy az áramkört a szükséges állapotba és állapotba kapja.

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **A Microsoft társközi beállítása az áramkörhöz**
   
    Mielőtt folytatná, ellenőrizze az alábbi információk meglétét.
   
   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg arról, hogy az áramkörben lévő más társak nem ugyanazt a VLAN-azonosítót használják.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha előtagokat szeretne elküldeni, vesszővel tagolt listát is küldhet. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
   * Ügyfél ASN: Ha olyan előtagokat hirdet meg, amelyek nem a társviszony-létesítési AS-számra vannak regisztrálva, megadhatja az AS-számot, amelyre regisztrálva vannak. Nem **kötelező**.
   * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
   * Egy MD5-kivonat, ha használni kívánja. **Választható.**
     
   A következő parancsmag futtatásával konfigurálja a Microsoft-társítást az áramkörhöz:
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>Microsoft társviszony-létesítés részleteinek megtekintése

A konfigurációs adatokat a következő parancsmaggal tekintheti meg:

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
Eredményük a következő:

```powershell
AdvertisedPublicPrefixes       : 123.0.0.0/30
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 2245
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : ARIN
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 300
```

### <a name="to-update-microsoft-peering-configuration"></a>Microsoft társviszony-létesítés konfigurációjának frissítése

A konfiguráció bármely részét a következő parancsmaggal frissítheti:

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>Microsoft társviszony-létesítés törlése

A következő parancsmag futtatásával távolíthatja el a társ-konfigurációt:

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>Következő lépések

Ezután [csatoljon egy VNet egy ExpressRoute-áramkörhöz](expressroute-howto-linkvnet-classic.md).

* A munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute-munkafolyamatok](expressroute-workflows.md).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
