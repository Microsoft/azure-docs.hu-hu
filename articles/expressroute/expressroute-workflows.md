---
title: 'Azure ExpressRoute: áramkör-konfigurációs munkafolyamat'
description: Ezen a lapon látható a ExpressRoute-áramkörök és-társítások konfigurálásának munkafolyamata
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: duau
ms.custom: contperf-fy21q1
ms.openlocfilehash: 24ad325cae2ee71ad49ee8ee055a83ceb8fa7ef2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721735"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>Az ExpressRoute kapcsolatcsoport-kiépítési munkafolyamatai és a kapcsolatcsoportok állapotai

Ez a cikk részletesen ismerteti a szolgáltatás kiépítési és útválasztási konfigurációs munkafolyamatait. Az alábbi fejezetek a ExpressRoute áramkör végpontok közötti kiépítésének feladatait ismertetik.

## <a name="workflow-steps"></a>Munkafolyamat lépései

### <a name="1-prerequisites"></a>1. előfeltételek

Győződjön meg arról, hogy az előfeltételek teljesülnek. A teljes listát lásd: [Előfeltételek és ellenőrzőlista](expressroute-prerequisites.md).

* Egy Azure-előfizetés lett létrehozva.
* A fizikai kapcsolat létrejött a ExpressRoute partnerrel, vagy a ExpressRoute Direct használatával lett konfigurálva. Tekintse át a [helyek és partnerek](expressroute-locations-providers.md#partners) témakört, ahol megtekintheti a ExpressRoute partnereket és ExpressRoute a közvetlen kapcsolatot a különböző helyek között.

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. a kapcsolat megrendelése vagy a ExpressRoute közvetlen konfigurálása

Rendeljen kapcsolatot a szolgáltatótól, vagy konfigurálja a közvetlen ExpressRoute.

#### <a name="expressroute-partner-model"></a>ExpressRoute partneri modell

Rendeljen kapcsolatot a szolgáltatótól. Ez a folyamat változó. A kapcsolat megrendelésével kapcsolatos további információkért forduljon a kapcsolati szolgáltatóhoz.

* Válassza ki a ExpressRoute-partnert
* Válassza ki az egyenrangú helyet
* Válassza ki a sávszélességet
* Számlázási modell kiválasztása
* Válassza a standard vagy a prémium bővítmény lehetőséget.

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct modell

* Az elérhető ExpressRoute közvetlen kapacitásának megtekintése a különböző helyekre.
* A portok lefoglalásához hozza létre a ExpressRoute Direct-erőforrást az Azure-előfizetésében.
* Kérje le és fogadja el az engedélyezési engedélyt, és rendelje meg a fizikai kapcsolatok listáját a partneri hely szolgáltatójától.
* Engedélyezheti a rendszergazdai állapotot, és megtekintheti a fényszinteket és a fizikai hivatkozásokat [Azure monitor](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics)használatával.

### <a name="3-create-an-expressroute-circuit"></a>3. ExpressRoute áramkör létrehozása

#### <a name="expressroute-partner-model"></a>ExpressRoute partneri modell

Ellenőrizze, hogy a ExpressRoute-partner készen áll-e a kapcsolat létesítésére. A ExpressRoute-áramkör számlázása a szolgáltatási kulcs kiállításának pillanatától számítva történik. Az áramkör létrehozásához kövesse az [ExpressRoute-áramkör létrehozása](expressroute-howto-circuit-portal-resource-manager.md) című témakör utasításait.

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct modell

Győződjön meg arról, hogy a fizikai kapcsolat és a rendszergazdai állapot mindkét hivatkozáson engedélyezve van. Útmutatásért tekintse meg a [ExpressRoute Direct konfigurálását ismertető témakört](how-to-expressroute-direct-portal.md) . A ExpressRoute-áramkör számlázása a szolgáltatási kulcs kiállításának pillanatától számítva történik. Az áramkör létrehozásához kövesse az [ExpressRoute-áramkör létrehozása](expressroute-howto-circuit-portal-resource-manager.md) című témakör utasításait.

### <a name="4-service-provider-provisions-connectivity"></a>4. a szolgáltató belefoglalja a kapcsolatot

Ez a szakasz csak a ExpressRoute partneri kapcsolat modelljére vonatkozik:

* Adja meg a szolgáltatás kulcsát (s-Key) a kapcsolat szolgáltatójának.
* Adja meg a kapcsolati szolgáltató (például VPN-azonosító) számára szükséges további információkat.
* Ha a szolgáltató kezeli az útválasztási konfigurációt, adja meg a szükséges adatokat.

A ExpressRoute áramkör kiépítési állapotát a PowerShell, a Azure Portal vagy a parancssori felület használatával ellenőrizheti, hogy az áramkör üzembe helyezése sikeresen megtörtént-e.

### <a name="5-configure-routing-domains"></a>5. útválasztási tartományok konfigurálása

Útválasztási tartományok konfigurálása. Ha a kapcsolat szolgáltatója a 3. réteg konfigurációját kezeli, akkor a rendszer az áramkör útválasztási beállításait konfigurálja. Ha a kapcsolati szolgáltató csak a 2. rétegbeli szolgáltatásokat kínálja, vagy ha közvetlen ExpressRoute használ, akkor az útválasztási [követelmények](expressroute-routing.md) és az [útválasztási konfiguráció](expressroute-howto-routing-classic.md) cikkeiben ismertetett irányelvek szerint kell konfigurálnia az útválasztást.

#### <a name="for-azure-private-peering"></a>Azure-beli privát partnerek számára

Az Azure Virtual Networkben üzembe helyezett virtuális gépekhez és felhőalapú szolgáltatásokhoz való kapcsolódás engedélyezése a privát társak számára.

* IPv4-alhálózatok:
    * Az 1. elérési út (/30) társítási alhálózata
    * Társítási alhálózat a 2. elérési úthoz (/30)
* IPv6-alhálózatok (nem kötelező):
    * Társítási alhálózat az 1. elérési úthoz (/126)
    * Társítási alhálózat a 2. elérési úthoz (/126)
* A VLAN-azonosító a társításhoz
* ASN társításhoz
* ExpressRoute ASN = 12076
* MD5-kivonat (nem kötelező)

#### <a name="for-microsoft-peering"></a>Microsoft-partnerek számára

A Microsoft online szolgáltatások, például a Microsoft 365 elérésének engedélyezése. Emellett az összes Azure Pásti-szolgáltatás a Microsoft-partnereken keresztül is elérhető. Gondoskodnia kell arról, hogy a Microsofthoz való csatlakozáshoz külön proxyt vagy Edge-t használjon, mint amelyet az internethez használ. Ha a ExpressRoute és az internet között ugyanazt a szegélyt használja, az aszimmetrikus útválasztást okoz, és a hálózati kapcsolat kimaradást okoz.

* IPv4-alhálózatok:
    * Az 1. elérési út (/30) társítási alhálózatának nyilvános IP-nek kell lennie
    * A 2. elérési út (/30) társítási alhálózata nyilvános IP-címmel kell rendelkeznie
* IPv6-alhálózatok (nem kötelező):
    * Az 1. elérési út (/126) társítási alhálózatának nyilvános IP-címmel kell rendelkeznie
    * Társítási alhálózat a 2. útvonalhoz (/126) – nyilvános IP-cím kell, hogy legyen.
* A VLAN-azonosító a társításhoz
* ASN társításhoz
* Meghirdetett előtagok – nyilvános IP-előtagnak kell lennie
* Ügyfél ASN-je (nem kötelező, ha különbözik az ASN-től)
* RIR/IRR IP-és ASN-ellenőrzéshez
* ExpressRoute ASN = 12076
* MD5-kivonat (nem kötelező)

### <a name="6-start-using-the-expressroute-circuit"></a>6. a ExpressRoute áramkör használatának megkezdése

* Az Azure-beli virtuális hálózatokat összekapcsolhatja a ExpressRoute áramkörével, hogy lehetővé váljon a helyszíni és az Azure-beli virtuális hálózat közötti kapcsolat. Útmutatásért tekintse meg a VNet csatlakoztatása [egy áramköri](expressroute-howto-linkvnet-arm.md) cikkhez című cikket. Ezek a virtuális hálózatok lehetnek ugyanabban az Azure-előfizetésben, mint a ExpressRoute áramkör, vagy egy másik előfizetésben is lehetnek.
* Kapcsolódjon az Azure-szolgáltatásokhoz és a Microsoft Cloud Serviceshez a Microsoft-partneri kapcsolaton keresztül.

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>ExpressRoute-partneri áramkör kiépítési állapota

A következő szakasz az ExpressRoute partneri kapcsolati modell különböző ExpressRoute-áramköri állapotait ismerteti.
Minden ExpressRoute-partner áramkör két állapottal rendelkezik:

* A **ServiceProviderProvisioningState** a kapcsolati szolgáltató oldalán lévő állapotot jelöli. Ez lehet *NotProvisioned*, *kiépítés* vagy *üzembe* helyezés. A ExpressRoute áramkörnek kiépített állapotban kell lennie a társítás konfigurálásához. **Ez az állapot csak a ExpressRoute partneri körökre vonatkozik, és nem jelenik meg egy ExpressRoute Direct áramkör tulajdonságaiban**.

* Az **állapot** a Microsoft kiépítési állapotát jelöli. Ez a tulajdonság úgy van beállítva, hogy az ExpressRoute-kör létrehozásakor engedélyezve legyen

### <a name="possible-states-of-an-expressroute-circuit"></a>Egy ExpressRoute áramkör lehetséges állapotai

Ez a szakasz a ExpressRoute partner csatlakozási modellje alatt létrehozott ExpressRoute-áramkör lehetséges állapotait ismerteti.

**Létrehozáskor**

Az ExpressRoute áramkör a következő állapotokat fogja jelenteni az erőforrások létrehozásakor.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**Ha a kapcsolati szolgáltató az áramkör kiépítési folyamatában van**

A ExpressRoute áramkör a következő állapotokat fogja jelenteni, miközben a kapcsolati szolgáltató dolgozik az áramkör kiépítésekor.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**Ha a kapcsolati szolgáltató befejezte a létesítési folyamatot**

A ExpressRoute áramkör a következő állapotokat fogja jelenteni, amint a kapcsolati szolgáltató sikeresen kiépítte az áramkört.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**Ha a kapcsolat szolgáltatója kiépíti az áramkört**

Ha a ExpressRoute áramkört el kell helyezni, az áramkör a következő állapotokat fogja jelenteni, amint a szolgáltató befejezte a megszüntetési folyamatot.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

Igény szerint újra engedélyezheti, vagy PowerShell-parancsmagok futtatásával törölheti az áramkört.  

> [!IMPORTANT]
> Az áramkör nem törölhető, ha a ServiceProviderProvisioningState kiépítés vagy üzembe helyezés történik. A kapcsolat szolgáltatójának le kell vonnia az áramkört, mielőtt törölni lehetne. A Microsoft továbbra is számlázza az áramkört, amíg az Azure-ban nem törli az ExpressRoute Circuit-erőforrást.
> 

## <a name="routing-session-configuration-state"></a>Útválasztási munkamenet konfigurációjának állapota

A BGP-kiépítési állapot azt jelenti, hogy a BGP-munkamenet engedélyezve van-e a Microsoft Edge-ben. Az állapotot engedélyezni kell a privát vagy a Microsoft-partnerek használatához.

Fontos, hogy a BGP-munkamenet állapota különösen a Microsoft-társak számára legyen ellenőrizhető. A BGP kiépítési állapotán kívül egy másik, *meghirdetett nyilvános előtagok* nevű állam is van. A meghirdetett nyilvános előtagok állapotának *konfigurált* állapotban kell lennie, mind a BGP-munkamenethez, mind az Útválasztás teljes körű működéséhez. 

Ha a meghirdetett nyilvános előtag állapota *érvényesítés szükséges* állapotra van állítva, a BGP-munkamenet nincs engedélyezve, mivel a meghirdetett előtagok nem feleltek meg az as-számnak az útválasztási kibocsátásiegység-forgalmi jegyzékekben.

> [!IMPORTANT]
> Ha a meghirdetett nyilvános előtagok *kézi ellenőrzési* állapotban vannak, meg kell nyitnia egy támogatási jegyet a [Microsoft támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) szolgálatával, és bizonyítania kell, hogy Ön rendelkezik a meghirdetett IP-címekkel a társított autonóm rendszerszámmal együtt.
> 
> 

## <a name="next-steps"></a>Következő lépések

* Az ExpressRoute-kapcsolat konfigurálása.
  
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-arm.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)
