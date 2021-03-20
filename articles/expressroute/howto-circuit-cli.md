---
title: 'Rövid útmutató: ExpressRoute-kör létrehozása és módosítása: Azure CLI'
description: Ez a rövid útmutató bemutatja, hogyan lehet létrehozni, kiépíteni, ellenőrizni, frissíteni, törölni és kiépíteni egy ExpressRoute-áramkört az Azure CLI használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: a1d50c3f8f94fbfd7dbcb9b25e051b7f2951c518
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91969075"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-cli"></a>Rövid útmutató: ExpressRoute-kör létrehozása és módosítása az Azure CLI használatával

Ez a rövid útmutató azt ismerteti, hogyan hozható létre egy Azure ExpressRoute-áramkör a parancssori felületen (CLI). A cikk azt is bemutatja, hogyan ellenőrizhető az áramkör állapota, frissítése vagy törlése és megszüntetése.

## <a name="prerequisites"></a>Előfeltételek

* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és a [munkafolyamatokat](expressroute-workflows.md) .
* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Telepítse a CLI-parancsok legújabb verzióját (2,0 vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI használatába](/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute-kör létrehozása és kiépítése

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését

A konfiguráció megkezdéséhez jelentkezzen be az Azure-fiókjába. Ha a "kipróbálás" Cloud Shell használja, automatikusan bejelentkezett. Az alábbi példák segítséget nyújtanak a kapcsolódáshoz:

```azurecli-interactive
az login
```

Keresse meg a fiókot az előfizetésekben.

```azurecli-interactive
az account list
```

Válassza ki azt az előfizetést, amelyhez ExpressRoute-áramkört kíván létrehozni.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>A támogatott szolgáltatók, helyszínek és sávszélességek listájának beolvasása

A ExpressRoute kör létrehozása előtt szüksége lesz a támogatott kapcsolati szolgáltatók, helyszínek és sávszélesség-beállítások listájára. A CLI-parancs `az network express-route list-service-providers` visszaadja ezt az információt, amelyet később a következő lépésekben fog használni:

```azurecli-interactive
az network express-route list-service-providers
```

A válasz a következő példához hasonló:

```output
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Ellenőrizze a választ, hogy megjelenik-e a kapcsolat szolgáltatója. Jegyezze fel a következő információkat, amelyekre szüksége lesz az áramkör létrehozásakor:

* Name
* PeeringLocations
* BandwidthsOffered

Most már készen áll egy ExpressRoute-áramkör létrehozására.

### <a name="create-an-expressroute-circuit"></a>ExpressRoute-kapcsolatcsoport létrehozása

> [!IMPORTANT]
> A ExpressRoute-áramkör számlázása a szolgáltatási kulcs kiállításának pillanatától számítva történik. Akkor hajtsa végre ezt a műveletet, amikor a kapcsolati szolgáltató készen áll az áramkör kiépítésére.
>
>

Ha még nem rendelkezik erőforráscsoporthoz, létre kell hoznia egyet a ExpressRoute áramkör létrehozása előtt. Hozzon létre egy erőforráscsoportot a következő parancs futtatásával:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

Az alábbi példa bemutatja, hogyan hozhat létre egy 200 Mbps ExpressRoute áramkört a Equinix a Szilícium-völgyben. Ha más szolgáltatót használ, és a különböző beállításokat használja, cserélje le ezeket az információkat a kérelem elkészítésekor.

Győződjön meg arról, hogy a megfelelő SKU-szintet és SKU-családot adta meg:

* Az SKU-szint meghatározza, hogy a ExpressRoute áramkör [helyi](expressroute-faqs.md#expressroute-local), standard vagy [prémium](expressroute-faqs.md#expressroute-premium)szintű-e. Megadhat helyi, * standard vagy *prémium* *szintűeket* is. A SKU nem módosítható a *standard/prémium* szintről a *helyi* értékre.
* Az SKU termékcsalád meghatározza a számlázási típust. Megadhatja a *MeteredData* a forgalmi díjas csomaghoz és a *UnlimitedData* korlátlan adatcsomag esetén. A számlázási típust a *MeteredData* és a *UnlimitedData* között is módosíthatja, de a típus nem módosítható a *UnlimitedData* értékről a *MeteredData*-re. Egy *helyi* áramkör csak *UnlimitedData* .


A ExpressRoute-áramkör számlázása a szolgáltatási kulcs kiállításának pillanatától számítva történik. A következő példa egy új szolgáltatási kulcsra vonatkozó kérelem:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

A válasz tartalmazza a szolgáltatás kulcsát.

### <a name="list-all-expressroute-circuits"></a>Az összes ExpressRoute-áramkör listázása

Az összes létrehozott ExpressRoute-áramkör listájának lekéréséhez futtassa a `az network express-route list` parancsot. Ezt az információt bármikor lekérheti a parancs használatával. Az összes áramkör listázásához a hívást paraméterek nélkül végezze el.

```azurecli-interactive
az network express-route list
```

A szolgáltatás kulcsa megjelenik a válasz *serviceKey* mezőjében.

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Az összes paraméter részletes leírását a parancs "-h" paraméter használatával történő futtatásával érheti el.

```azurecli-interactive
az network express-route list -h
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>A szolgáltatás kulcsának elküldése a kapcsolati szolgáltatónak a kiépítés céljából

A "ServiceProviderProvisioningState" információt nyújt a szolgáltatói oldalon üzembe helyezés aktuális állapotáról. Az állapot a Microsoft oldalon található állapotot biztosítja. További információt a [munkafolyamatok című cikkben](expressroute-workflows.md#expressroute-circuit-provisioning-states)talál.

Új ExpressRoute-kör létrehozásakor az áramkör a következő állapotban van:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Az áramkör a következő állapotra vált, amikor a kapcsolati szolgáltató jelenleg engedélyezi az Ön számára:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

A ExpressRoute áramkör használatához a következő állapotban kell lennie:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Az áramköri kulcs állapotának és állapotának rendszeres időközönkénti keresése

Az állapot és a szolgáltatási kulcs állapotának ellenőrzése után megtudhatja, hogy a szolgáltató kiosztotta-e az áramkört. Az áramkör konfigurálása után a *ServiceProviderProvisioningState* *kiépítve* jelenik meg, ahogy az az alábbi példában is látható:

```azurecli-interactive
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

A válasz a következő példához hasonló:

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="create-your-routing-configuration"></a>Az útválasztási konfiguráció létrehozása

Részletes útmutatásért lásd az [ExpressRoute-áramköri útválasztási konfiguráció](howto-routing-cli.md) című cikket az áramköri társítások létrehozásához és módosításához.

> [!IMPORTANT]
> Ezek az utasítások csak a 2. rétegbeli kapcsolati szolgáltatásokat nyújtó szolgáltatók által létrehozott áramkörökre vonatkoznak. Ha olyan szolgáltatót használ, amely felügyelt 3. rétegbeli szolgáltatásokat (jellemzően IP VPN, például MPLS) kínál, a kapcsolati szolgáltatója konfigurálja és kezeli az útválasztást.
>
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal

Ezután csatoljon egy virtuális hálózatot a ExpressRoute-áramkörhöz. Használja a [virtuális hálózatok összekapcsolása a ExpressRoute áramkörökkel](howto-linkvnet-cli.md) című cikket.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>ExpressRoute áramkör módosítása

Egy ExpressRoute-kör bizonyos tulajdonságait módosíthatja a kapcsolat befolyásolása nélkül. A következő módosításokat hajthatja végre leállás nélkül:

* Engedélyezheti vagy letilthatja a ExpressRoute-áramkörhöz tartozó ExpressRoute Premium-bővítményt. Az SKU *szabványos/prémiumról* *helyire* történő módosítása nem támogatott.
* Növelheti a ExpressRoute-áramkör sávszélességét, ha rendelkezésre áll a porton elérhető kapacitás. Az áramkör sávszélességének visszaminősítése azonban nem támogatott.
* A mérési tervet megváltoztathatja a mért adatoktól a korlátlan számú adatokig. A mérési tervnek a korlátlan számú adatokról a mért adatokra való módosítása azonban nem támogatott.
* Engedélyezheti és letilthatja a *klasszikus műveletek engedélyezését*.

A korlátozásokkal és korlátozásokkal kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Az ExpressRoute Premium bővítmény engedélyezése

A következő parancs használatával engedélyezheti a ExpressRoute prémium bővítményt a meglévő áramkörhöz:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Az áramkör most már engedélyezve van a ExpressRoute Premium kiegészítő funkciói. Ha sikeresen futtatta a parancsot, megkezdjük a prémium szintű kiegészítő funkció számlázását.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Az ExpressRoute Premium bővítmény letiltása

> [!IMPORTANT]
> Ez a művelet sikertelen lehet, ha olyan erőforrásokat használ, amelyek nagyobbak a standard szintű áramkör számára engedélyezettnél.
>
>

Az ExpressRoute Premium bővítmény letiltása előtt Ismerje meg a következő feltételeket:

* A prémiumról a standard szintre való visszalépés előtt meg kell győződnie arról, hogy az áramkörhöz kapcsolódó virtuális hálózatok száma kevesebb, mint 10. Ha nem, a frissítési kérelem meghiúsul, és a prémium díjszabás szerint számoljuk el.
* A többi geopolitikai régióban lévő virtuális hálózatokat először le kell kapcsolni. Ha nem távolítja el a hivatkozást, a frissítési kérelem sikertelen lesz, és a prémium díjszabásban továbbra is számlázunk.
* Az útválasztási táblázatnak kisebbnek kell lennie, mint 4 000 útvonal a privát társak számára. Ha az útválasztási táblázat mérete nagyobb, mint 4 000 útvonal, a BGP-munkamenet eldobásra kerül. A BGP-munkamenetet nem lehet újra engedélyezni, amíg a meghirdetett előtagok száma 4 000.

A következő példa használatával letilthatja a ExpressRoute Premium bővítményt a meglévő áramkörhöz:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>A ExpressRoute-áramkör sávszélességének frissítése

A szolgáltató által támogatott sávszélesség-beállításokért olvassa el a [ExpressRoute – gyakori kérdések](expressroute-faqs.md)című részt. A meglévő áramköri méretnél nagyobb méretet is kiválaszthat.

> [!IMPORTANT]
> Ha nem áll rendelkezésre elegendő kapacitás a meglévő porton, lehet, hogy újra létre kell hoznia a ExpressRoute áramkört. Az áramkör nem frissíthető, ha az adott helyen nem érhető el további kapacitás.
>
> Egy ExpressRoute áramkör sávszélessége nem csökkenthető megszakítás nélkül. A lefokozási sávszélességhez meg kell szüntetnie a ExpressRoute áramkört, majd újra kell telepítenie egy új ExpressRoute áramkört.
>

Miután eldöntötte, hogy milyen méretűre van szüksége, használja az alábbi parancsot az áramkör átméretezéséhez:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Az áramkör a Microsoft oldalán lesz frissítve. Ezt követően kapcsolatba kell lépnie a kapcsolat szolgáltatójával, hogy frissítse a konfigurációkat a saját oldalán a változásnak megfelelően. Az értesítés elvégzése után megkezdjük a frissített sávszélesség-beállítás számlázását.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Az SKU áthelyezése a mért értékről korlátlanra

A ExpressRoute áramkör SKU-jának módosításához a következő példa használható:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>A klasszikus és a Resource Manager-környezetekhez való hozzáférés szabályozása

Tekintse át a [ExpressRoute-áramkörök áthelyezése a Klasszikusból a Resource Manager](expressroute-howto-move-arm.md)-alapú üzemi modellbe című témakör utasításait.

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute áramkör kiépítése

ExpressRoute áramkör megszüntetéséhez és törléséhez ellenőrizze, hogy a következő feltételek teljesülnek-e:

* Az összes virtuális hálózatot le kell kapcsolni a ExpressRoute áramkörből. Ha a művelet meghiúsul, ellenőrizze, hogy a virtuális hálózatok az áramkörhöz vannak-e kapcsolva.
* Ha a ExpressRoute Circuit Service Provider kiépítési állapota kiépítés vagy **kiépítve** **van,** akkor a szolgáltatóval kell együttműködni, hogy kiépítse az áramkört a saját oldalán. Továbbra is fenntartjuk az erőforrásokat és számlázunk, amíg a szolgáltató befejezi az áramkör kiépítését, és értesítést küld nekünk.
* Ha a szolgáltató kivette az áramkört, ami azt jelenti, hogy a szolgáltató kiépítési állapota **nem kiépítve** értékre van állítva, akkor törölheti az áramkört. Az áramkör számlázása ezután leáll.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A ExpressRoute-áramkört a következő parancs futtatásával törölheti:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Miután létrehozta az áramkört, és kiépíti a szolgáltatóval, folytassa a következő lépéssel a társítás konfigurálásához:

> [!div class="nextstepaction"]
> [Az ExpressRoute-áramkör útválasztásának létrehozása és módosítása](howto-routing-cli.md)
