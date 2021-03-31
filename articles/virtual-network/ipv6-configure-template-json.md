---
title: IPv6-alapú kettős stack-alkalmazás üzembe helyezése alapszintű Load Balancer az Azure Virtual Networkben – Resource Manager-sablon
titlesuffix: Azure Virtual Network
description: Ez a cikk bemutatja, hogyan helyezhet üzembe egy IPv6-alapú kettős verem alkalmazást az Azure Virtual Networkben Azure Resource Manager virtuálisgép-sablonok használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 548b416ed0f21df83dafdb1c2d7015c625c36e4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95244293"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template"></a>IPv6-alapú Dual stack-alkalmazás üzembe helyezése alapszintű Load Balancer az Azure-ban – sablon

Ez a cikk a Azure Resource Manager virtuálisgép-sablon részét képező IPv6-konfigurációs feladatok listáját tartalmazza. Az ebben a cikkben ismertetett sablon használatával helyezzen üzembe egy olyan alapszintű Load Balancer, amely egy IPv4-és IPv6-alhálózattal rendelkező kettős Veremes virtuális hálózatot tartalmaz, egy alapszintű Load Balancer kettős (IPv4 + IPv6) előtér-konfigurációkkal, valamint két IP-konfigurációval, hálózati biztonsági csoporttal és nyilvános IP-címmel rendelkező virtuális gépekkel.

A kettős verem (IPV4 + IPv6) alkalmazás standard Load Balancer használatával történő üzembe helyezését lásd: [IPv6 kettős verem alkalmazás üzembe helyezése standard Load Balancer sablonnal](ipv6-configure-standard-load-balancer-template-json.md).

## <a name="required-configurations"></a>Szükséges konfigurációk

Keresse meg a sablonhoz tartozó szakaszt, és ellenőrizze, hogy hol történnek.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>A virtuális hálózat IPv6-addressSpace

A sablon szakasza, melyet hozzá szeretne adni:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6-alhálózat az IPv6 virtuális hálózat addressSpace

A sablon szakasza, melyet hozzá szeretne adni:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>A hálózati adapter IPv6-konfigurációja

A sablon szakasza, melyet hozzá szeretne adni:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6 hálózati biztonsági csoport (NSG) szabályai

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "fd00:db8:deca:deed::/64",
              "destinationAddressPrefix": "fd00:db8:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Feltételes konfiguráció

Ha hálózati virtuális készüléket használ, adja hozzá az IPv6-útvonalakat az útválasztási táblázatban. Ellenkező esetben ez a konfiguráció nem kötelező.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "fd00:db8:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "fd00:db8:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Választható konfiguráció

### <a name="ipv6-internet-access-for-the-virtual-network"></a>IPv6-alapú internet-hozzáférés a virtuális hálózathoz

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6 nyilvános IP-címek

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6-előtér Load Balancerhoz

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>IPv6-alapú háttér-címkészlet Load Balancer

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6 Load Balancer-szabályok a bejövő és a kimenő portok hozzárendeléséhez

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Minta virtuálisgép-sablon JSON
Ha Azure Resource Manager sablonnal szeretne üzembe helyezni egy IPv6-alapú kettős verembeli alkalmazást, amely alapszintű Load Balancer az Azure Virtual Network szolgáltatásban, tekintse meg a [minta sablont.](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/)

## <a name="next-steps"></a>Következő lépések

A [nyilvános IP-címekre](https://azure.microsoft.com/pricing/details/ip-addresses/), a [hálózati sávszélességre](https://azure.microsoft.com/pricing/details/bandwidth/)vagy a [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/)díjszabására vonatkozó részletekért tekintse meg a következőt:.
