---
title: 'Azure ExpressRoute: a ExpressRoute közvetlen konfigurálása: parancssori felület'
description: Ismerje meg, hogyan konfigurálhatja az Azure ExpressRoute Direct szolgáltatást az Azure CLI használatával közvetlenül a Microsoft globális hálózatához való kapcsolódáshoz.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/28/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0bdf2c4dda3e272ae04681f886f6e4da31dcebd8
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569839"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli"></a>ExpressRoute közvetlen konfigurálása az Azure CLI használatával

A közvetlen ExpressRoute lehetővé teszi a Microsoft globális hálózatának közvetlen kapcsolódását a világ különböző pontjain található, stratégiai módon terjesztett helyekről. További információ: [About ExpressRoute Direct-kapcsolat](expressroute-erdirect-about.md).

## <a name="create-the-resource"></a><a name="resources"></a>Az erőforrás létrehozása

1. Jelentkezzen be az Azure-ba, és válassza ki a ExpressRoute tartalmazó előfizetést. A ExpressRoute Direct erőforrásnak és a ExpressRoute-áramköröknek ugyanahhoz az előfizetéshez kell tartoznia. Az Azure CLI-ben futtassa a következő parancsokat:

   ```azurecli
   az login
   ```

   Keresse meg a fiók előfizetéseit: 

   ```azurecli
   az account list 
   ```

   Válassza ki azt az előfizetést, amelyhez ExpressRoute-áramkört szeretne létrehozni:

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

2. Regisztrálja újra az előfizetést a Microsoft. Network-be a expressrouteportslocation és a expressrouteport API-k eléréséhez

   ```azurecli
   az provider register --namespace Microsoft.Network
   ```
3. Az összes olyan hely listázása, ahol a ExpressRoute Direct támogatott:
    
   ```azurecli
   az network express-route port location list
   ```

   **Példa kimenetre**
  
   ```output
   [
   {
    "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
    "location": null,
    "name": "Equinix-Ashburn-DC2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA3",
    "location": null,
    "name": "Equinix-Dallas-DA3",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "111 8th Avenue, New York, NY 10011",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-New-York-NY5",
    "location": null,
    "name": "Equinix-New-York-NY5",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "11 Great Oaks Blvd, SV1, San Jose, CA 95119",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-SV1",
    "location": null,
    "name": "Equinix-San-Jose-SV1",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "2001 Sixth Ave., Suite 350, SE2, Seattle, WA 98121",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Seattle-SE2",
    "location": null,
    "name": "Equinix-Seattle-SE2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ]
   ```
4. Annak megállapítása, hogy az előző lépésben felsorolt helyszínek egyike elérhető-e a sávszélességgel:

   ```azurecli
   az network express-route port location show -l "Equinix-Ashburn-DC2"
   ```

   **Példa kimenetre**

   ```output
   {
   "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
   "availableBandwidths": [
    {
      "offerName": "100 Gbps",
      "valueInGbps": 100
    }
   ],
   "contact": "support@equinix.com",
   "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
   "location": null,
   "name": "Equinix-Ashburn-DC2",
   "provisioningState": "Succeeded",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ```
5. Hozzon létre egy ExpressRoute Direct-erőforrást, amely az előző lépésekben kiválasztott hely alapján van.

   A ExpressRoute Direct a QinQ és a Dot1Q beágyazást is támogatja. Ha a QinQ lehetőséget választja, minden ExpressRoute-áramkör dinamikusan hozzá van rendelve egy S-címkéhez, és egyedi a ExpressRoute Direct erőforrásban. Az áramkörben található minden C-címkének egyedinek kell lennie az áramkörön, de nem a ExpressRoute közvetlen erőforráson keresztül.  

   Ha a Dot1Q Encapsulation (beágyazás) lehetőséget választja, a C-tag egyediségét kell kezelnie a teljes ExpressRoute közvetlen erőforrásban.  

   > [!IMPORTANT]
   > A ExpressRoute Direct csak egy beágyazási típus lehet. A beágyazási típus nem módosítható a ExpressRoute közvetlen erőforrás létrehozása után.
   > 
 
   ```azurecli
   az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
   ```

   > [!NOTE]
   > A **beágyazás** attribútumot is beállíthatja a **Dot1Q**. 
   >

   **Példa kimenetre**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "02ee21fe-4223-4942-a6bc-8d81daabc94f",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }  
   ```

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>Engedélyezési engedély (LOA) előállítása

Adja meg a legutóbb létrehozott ExpressRoute közvetlen erőforrás nevét, az erőforráscsoport nevét és az ügyfél nevét, hogy az LOA-t és (opcionálisan) adja meg a dokumentum tárolására szolgáló fájl helyét. Ha a fájl elérési útja nem hivatkozik rá, a rendszer letölti a dokumentumot az aktuális könyvtárba.

```azurecli
az network express-route port generate-loa -n Contoso-Direct -g Contoso-Direct-rg --customer-name Contoso --destination C:\Users\SampleUser\Downloads\LOA.pdf
```

## <a name="change-adminstate-for-links"></a><a name="state"></a>Hivatkozások AdminState módosítása

Ezt a folyamatot az 1. rétegbeli tesztek elvégzéséhez használhatja. Győződjön meg arról, hogy minden egyes kapcsolatok megfelelően vannak kialakítva az elsődleges és a másodlagos portok mindegyik útválasztóján.

1. Állítsa be a hivatkozásokat az **engedélyezve**értékre. Ismételje meg ezt a lépést az egyes hivatkozások **engedélyezésre**való beállításához.

   A hivatkozások [0] az elsődleges port és a hivatkozások [1] a másodlagos port.

   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
   ```
   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
   ```
   **Példa kimenetre**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "<resourceGUID>",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }
   ```

   Használja ugyanezt az eljárást a portok leállításához a használatával `AdminState = "Disabled"` .

## <a name="create-a-circuit"></a><a name="circuit"></a>Kapcsolatcsoport létrehozása

Alapértelmezés szerint a ExpressRoute Direct-erőforrást tartalmazó előfizetésben 10 áramkört hozhat létre. Microsoft ügyfélszolgálata növelheti az alapértelmezett korlátot. Ön felelős a kiépített és a felhasznált sávszélesség nyomon követéséhez. A kiépített sávszélesség a ExpressRoute Direct-erőforrás összes áramköre sávszélességének összege. A felhasznált sávszélesség a mögöttes fizikai felületek fizikai használata.

Az itt vázolt forgatókönyvek támogatásához további áramköri sávszélességeket is használhat a közvetlen ExpressRoute. A sávszélesség 40 GB/s és 100 Gbps.

A **SkuTier** lehet helyi, standard vagy prémium.

A **SkuFamily** csak MeteredData lehet. A ExpressRoute Direct nem támogatja a korlátlan használatát.

Hozzon létre egy áramkört a ExpressRoute Direct erőforráson:

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  A többi sávszélesség 5 GB/s, 10 GB/s és 40 GB/s.

  **Példa kimenetre**

  ```output
  {
  "allowClassicOperations": false,
  "allowGlobalReach": false,
  "authorizations": [],
  "bandwidthInGbps": 100.0,
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"<etagnumber>\"",
  "expressRoutePort": {
    "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
    "resourceGroup": "Contoso-Direct-rg"
  },
  "gatewayManagerEtag": "",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRouteCircuits/ERDirect-ckt-cli",
  "location": "westus",
  "name": "ERDirect-ckt-cli",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "serviceKey": "<serviceKey>",
  "serviceProviderNotes": null,
  "serviceProviderProperties": null,
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "MeteredData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "stag": null,
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits"
  }  
  ```

## <a name="next-steps"></a>További lépések

A közvetlen ExpressRoute kapcsolatos további információkért tekintse meg az [áttekintést](expressroute-erdirect-about.md).
