---
title: VNet-KOPPINTÁS létrehozása, módosítása vagy törlése – Azure CLI
description: Megtudhatja, hogyan hozhat létre, módosíthat vagy törölhet virtuális hálózatokat az Azure CLI használatával.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e3a56e4a6eb1fb6eb633021178ef78f8ac7287d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014788"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>A virtuális hálózatok használata KOPPINTÁSsal az Azure CLI használatával

Az Azure Virtual Network (terminál-hozzáférési pont) funkció lehetővé teszi a virtuális gép hálózati forgalmának folyamatos továbbítását egy hálózati csomag gyűjtője vagy analitikai eszköze számára. A gyűjtő vagy az elemzési eszközt egy [hálózati virtuális berendezési](https://azure.microsoft.com/solutions/network-appliances/) partner kapja meg. A virtuális hálózati KOPPINTÁSsal való együttműködéshez ellenőrzött partneri megoldások listáját a [partneri megoldások](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions)című témakörben találja. 

## <a name="create-a-virtual-network-tap-resource"></a>Virtuális hálózati KOPPINTÁS erőforrásának létrehozása

Az [Előfeltételek](virtual-network-tap-overview.md#prerequisites) beolvasása a virtuális hálózati koppintó erőforrás létrehozása előtt. Az alábbi parancsokat futtathatja a [Azure Cloud Shell](https://shell.azure.com/bash), vagy futtathatja az Azure parancssori felületét (CLI) a számítógépről. A Azure Cloud Shell egy ingyenes interaktív rendszerhéj, amely nem igényli az Azure CLI telepítését a számítógépre. Be kell jelentkeznie az Azure-ba egy olyan fiókkal, amely rendelkezik a megfelelő [engedélyekkel](virtual-network-tap-overview.md#permissions). Ehhez a cikkhez az Azure CLI 2.0.46 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). A virtuális hálózat KOPPINTÁS jelenleg bővítményként érhető el. A bővítmény telepítéséhez futtatnia kell a parancsot `az extension add -n virtual-network-tap` . Ha helyileg futtatja az Azure CLI-t, akkor azt is futtatnia kell, `az login` hogy létre kell hoznia egy, az Azure-hoz való kapcsolódást.

1. Kérje le az előfizetés AZONOSÍTÓját egy olyan változóra, amelyet egy későbbi lépésben használ:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Állítsa be azt az előfizetés-azonosítót, amelyet a virtuális hálózati KOPPINTó erőforrás létrehozásához fog használni.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Regisztrálja újra azt az előfizetés-azonosítót, amelyet a virtuális hálózati KOPPINTó erőforrás létrehozásához fog használni. Ha a TAP-erőforrás létrehozásakor regisztrációs hibaüzenetet kap, futtassa a következő parancsot:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Ha a virtuális hálózat célhelye a gyűjtő vagy elemzési eszköz hálózati virtuális berendezésének hálózati adaptere, akkor

   - Kérje le a hálózati virtuális berendezés hálózati adapterének IP-konfigurációját egy későbbi lépésben használt változóra. Az azonosító a végpont, amely összesíti a TAP-forgalmat. A következő példa lekéri a *ipconfig1* IP-konfiguráció azonosítóját egy *myNetworkInterface* nevű hálózati adapterhez egy *myResourceGroup* nevű erőforráscsoport:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Hozzon létre egy virtuális hálózatot a westcentralus Azure-régióban az IP-konfiguráció AZONOSÍTÓjának használatával célként és egy opcionális port tulajdonságként. A port meghatározza a hálózati adapter IP-konfigurációjának célport beállítását, ahol a TAP-forgalom fogadása történik:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Ha a virtuális hálózat célhelye egy belső Azure Load Balancer:
  
   - Kérje le az Azure belső terheléselosztó előtér-IP-konfigurációját egy későbbi lépésben használt változóra. Az azonosító a végpont, amely összesíti a TAP-forgalmat. A következő példa lekéri a *myInternalLoadBalancer* nevű terheléselosztó *frontendipconfig1* előtér-IP-konfigurációjának azonosítóját egy *myResourceGroup* nevű erőforráscsoporthoz:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - A virtuális hálózat létrehozásához KOPPINTson a előtér-IP-konfiguráció AZONOSÍTÓját használja célként és egy opcionális port tulajdonságként. A port megadja a célként megadott portot az előtér-IP-konfigurációban, ahol a TAP-forgalom fogadása történik:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. A virtuális hálózat létrehozásának megerősítése:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>KOPPINTÁS konfigurációjának hozzáadása hálózati adapterhez

1. Egy meglévő virtuális hálózati KOPPINTó erőforrás AZONOSÍTÓjának beolvasása. A következő példa egy *myResourceGroup* nevű erőforráscsoport egy *myTap* nevű virtuális hálózatát kéri le:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Hozzon létre egy KOPPINTÁS konfigurációt a figyelt virtuális gép hálózati adapterén. A következő példa létrehoz egy *myNetworkInterface* nevű hálózati adapterhez tartozó koppintó konfigurációt:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. A TAP-konfiguráció létrehozásának megerősítése:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>A KOPPINTÁS konfigurációjának törlése egy hálózati adapteren

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Virtuális hálózati csapok listázása egy előfizetésben

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Virtuális hálózati KOPPINTÁS törlése egy erőforráscsoporthoz

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
