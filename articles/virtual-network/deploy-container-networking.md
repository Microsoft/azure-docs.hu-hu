---
title: Azure Virtual Network tárolóalapú hálózatkezelésének üzembe helyezése | Microsoft Docs
description: Ismerje meg, hogyan helyezheti üzembe az Azure Virtual Network Container Network Interface (CNI) beépülő modult a Kubernetes-fürtökhöz.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: b95b3cfdf8fea6e31015d945566803569b4ba064
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222921"
---
# <a name="deploy-the-azure-virtual-network-container-network-interface-plug-in"></a>Az Azure Virtual Network tárolóalapú hálózati adaptere beépülő moduljának üzembe helyezése

Az Azure Virtual Network tárolóalapú hálózati adapterének beépülő modulja egy Azure-beli virtuális gépen települ, és virtuális hálózati képességeket biztosít a Kubernetes-podok és a Docker-tárolók számára. A beépülő modullal kapcsolatos további információért tekintse meg az [Azure Virtual Network-képességek használatának engedélyezése a tárolók számára](container-networking-overview.md) című részt. A beépülő modult továbbá az Azure Kubernetes Service (AKS) szolgáltatással is lehet használni a [Speciális hálózatkezelés](../aks/configure-azure-cni.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lehetőség kiválasztásával, amely automatikusan egy virtuális hálózatba helyezi az AKS-tárolókat.

## <a name="deploy-plug-in-for-acs-engine-kubernetes-cluster"></a>ACS-motorral üzembe helyezett Kubernetes-fürt beépülő moduljának üzembe helyezése

Az ACS-motor üzembe helyez egy Kubernetes-fürtöt az Azure Resource Manager-sablon használatával A fürtkonfiguráció egy JSON-fájlban van meghatározva, amelyet a sablon létrehozásakor a rendszer továbbít az eszköznek. A támogatott fürtbeállítások teljes listájával és a beállítások leírásával kapcsolatos további részletekért tekintse meg a [Microsoft Azure Container Service Engine – Fürtdefiníció](https://github.com/Azure/acs-engine/blob/master/docs/clusterdefinition.md) című részt. Ez a beépülő modul az ACS-motor használatával létrehozott fürtök alapértelmezett hálózatkezelési beépülő modulja. A következő hálózatkonfigurációs beállítások fontosak a beépül modul konfigurálásakor:

  | Beállítás                              | Leírás                                                                                                           |
  |--------------------------------------|------------------------------------------------------------------------------------------------------                 |
  | firstConsecutiveStaticIP             | A fő csomópontnak kiosztott IP-cím. Ez egy kötelező beállítás.                                     |
  | clusterSubnet a kubernetesConfig alatt | Annak a virtuális hálózati alhálózatnak a CIDR-je, amelyben a fürt üzembe lett helyezve, és amelyből az IP-címek ki lettek osztva a podok számára.   |
  | vnetSubnetId a masterProfile alatt     | Meghatározza annak az alhálózatnak az Azure Resource Manager-beli erőforrás-azonosítóját, amelyben a fürt üzembe lesz helyezve                    |
  | vnetCidr                             | Azon virtuális hálózat CIDR-je, amelyben a fürt üzembe lesz helyezve                                                             |
  | max-Pods a kubeletConfig alatt         | Az összes ügynök-virtuálisgépen található podok maximális száma A beépülő modul esetében az alapértelmezett érték 30. Legfeljebb 250-et adhat meg  |

### <a name="example-configuration"></a>Konfigurációs példa

A következő json-példa egy, a következő tulajdonságokkal rendelkező fürtre vonatkozik:
-   1 fő csomópont és 2 ügynökcsomópont 
-   Egy fő és egy ügynökcsomópontot is tartalmazó, *KubeClusterSubnet* (10.0.0.0/20) nevű alhálózatban van üzembe helyezve.

```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
        "clusterSubnet": "10.0.0.0/20" --> Subnet allocated for the cluster
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "ACSKubeMaster",
      "vmSize": "Standard_A2",
      "vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<Vnet Name>/subnets/KubeClusterSubnet",
      "firstConsecutiveStaticIP": "10.0.1.50", --> IP address allocated to the Master node
"vnetCidr": "10.0.0.0/16" --> Virtual network address space
    },
    "agentPoolProfiles": [
      {
        "name": "k8sagentpoo1",
        "count": 2,
        "vmSize": "Standard_A2_v2",
"vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<VNet Name>/subnets/KubeClusterSubnet",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
    "linuxProfile": {
      "adminUsername": "KubeServerAdmin",
      "ssh": {
        "publicKeys": [
          {…}
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "dd438987-aa12-4754-b47d-375811889714",
      "secret": "azure123"
    }
  }
}
```

## <a name="deploy-plug-in-for-a-kubernetes-cluster"></a>Kubernetes-fürt beépülő moduljának üzembe helyezése

A beépülő modul egy Kubernetes-fürt összes Azure-beli virtuális gépén való telepítéséhez hajtsa végre a következő lépéseket:

1. [Töltse le és telepítse a beépülő modult](#download-and-install-the-plug-in).
2. Előre foglaljon le egy virtuális hálózati IP-címkészletet minden egyes olyan virtuális gépen, amelyről IP-címeket fog a podokhoz rendelni. Minden Azure-beli virtuális gép rendelkezik egy elsődleges virtuális magánhálózati IP-címmel az összes hálózati adapterhez. A podok IP-címkészletében szereplő címek másodlagos címként (*ipconfig*) vannak megadva a virtuális gép hálózati adapterén, a következő lehetőségek egyikének használatával:

   - **CLI**: [Több IP-cím hozzárendelése az Azure CLI használatával](virtual-network-multiple-ip-addresses-cli.md)
   - **PowerShell**: [Több IP-cím hozzárendelése a PowerShell használatával](virtual-network-multiple-ip-addresses-powershell.md)
   - **Portal**: [Több IP-cím hozzárendelése az Azure Portal használatával](virtual-network-multiple-ip-addresses-portal.md)
   - **Azure Resource Manager-sablon**: [Több IP-cím hozzárendelése sablonok használatával](./template-samples.md)

   Győződjön meg arról, hogy megfelelő számú IP-címet ad hozzá azon podok számára, amelyeket el szeretne indítani a virtuális gépen.

3. A fürtje számára a hálózatkezelést biztosító beépülő modult úgy választhatja ki, hogy a fürt létrehozásakor átadja a Kubeletnek a `–network-plugin=cni` parancssori kapcsolót. A Kubernetes alapértelmezés szerint azokban a címtárakban keresi a beépülő modult és a konfigurációs fájlt, ahová eredetileg telepítették őket.
4. Ha szeretné, hogy podjai elérjék az internetet, adja hozzá a következő *iptables* szabályt Linux rendszerű virtuális gépén az internetforgalom forráshálózati címfordításához. A következő példában a megadott IP-címtartomány a következő: 10.0.0.0/8.

   ```bash
   iptables -t nat -A POSTROUTING -m iprange ! --dst-range 168.63.129.16 -m
   addrtype ! --dst-type local ! -d 10.0.0.0/8 -j MASQUERADE
   ```

   A szabályok elvégzik azon forgalom hálózati címfordítását, amely nem a megadott IP-címtartományok felé tart. A rendszer azt feltételezi, hogy az előző tartományokon kívül eső teljes forgalom internetforgalom. Tetszés szerint megadhatja a virtuális gép virtuális hálózatának, a virtuális társhálózatok és a helyszíni hálózatok IP-címtartományát is.

   A Windows rendszerű virtuális gépek automatikusan elvégzik annak a forgalomnak a forráshálózati címfordítását, amelynek a célja kívül esik azon az alhálózaton, amelyhez a virtuális gép tartozik. Egyéni IP-címtartományokat nem lehet megadni.

Az előző lépések végrehajtását követően a Kubernetes ügynök-virtuálisgépeken megjelenő podokhoz a rendszer automatikusan magánhálózati IP-címeket rendel a virtuális hálózatról.

## <a name="deploy-plug-in-for-docker-containers"></a>Beépülő modulok üzembe helyezése Docker-tárolókhoz

1. [Töltse le és telepítse a beépülő modult](#download-and-install-the-plug-in).
2. Docker-tárolók létrehozása a következő parancs használatával:

   ```
   ./docker-run.sh \<container-name\> \<container-namespace\> \<image\>
   ```

A tárolók automatikusan megkezdik az IP-címek fogadását a lefoglalt készletből. A Docker-tárolókba irányuló forgalom terheléselosztásához a tárolókat egy szoftveres terheléselosztó mögé kell helyezni, valamint konfigurálni kell egy terheléselosztói mintavételt is, ugyanúgy, mintha egy virtuális gép szabályzatait és mintavételeit hozná létre.

### <a name="cni-network-configuration-file"></a>A CNI hálózati konfigurációs fájlja

A CNI hálózati konfigurációs fájlja JSON formátumban van megadva. Alapértelmezés szerint a `/etc/cni/net.d` (Linux rendszeren) és a `c:\cni\netconf` (Windows rendszeren) helyen érhetők el. A fájl meghatározza a beépülő modul konfigurációját, amely a Windows és Linux rendszerek esetében eltérő. A következő json egy Linux konfigurációs mintafájl, amelyet néhány kulcsfontosságú beállítás magyarázata követ. Nem kell módosítania a fájlt:

```json
{
       "cniVersion":"0.3.0",
       "name":"azure",
       "plugins":[
          {
             "type":"azure-vnet",
             "mode":"bridge",
             "bridge":"azure0",
             "ipam":{
                "type":"azure-vnet-ipam"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "portMappings":true
             },
             "snat":true
          }
       ]
}
```

#### <a name="settings-explanation"></a>Beállítások magyarázata

- **cniVersion** (CNI verziója): Az Azure Virtual Network CNI beépülő moduljai a [CNI spec](https://github.com/containernetworking/cni/blob/master/SPEC.md) 0.3.0-ás és 0.3.1-es verzióit támogatják.
- **name** (név): A hálózat neve. Ehhez a tulajdonsághoz bármilyen egyedi érték megadható.
- **type** (típus): A hálózati beépülő modul neve. Állítsa az *azure-vnet* értékre.
- **mode** (mód): Működési mód. A mező nem kötelező. A „híd” az egyetlen támogatott mód. További információért tekintse meg a [működési módokról](https://github.com/Azure/azure-container-networking/blob/master/docs/network.md) szóló részt.
- **bridge** (híd): Azon híd neve, amellyel a tárolók és a virtuális hálózatok össze lesznek kötve. A mező nem kötelező. Ha nincs megadva, a beépülő modul automatikusan választ egy egyedi nevet a fő felületindex alapján.
- **ipam type** (IPAM típusa): Az IPAM beépülő modul neve. Mindig az *azure-vnet-ipam* értékre van állítva.

## <a name="download-and-install-the-plug-in"></a>A beépülő modul letöltése és telepítése

Töltse le a beépülő modult a [GitHubról](https://github.com/Azure/azure-container-networking/releases). Töltse le az Ön által használt platform legfrissebb verzióját:

- **Linux**: [Azure-vnet-CNI-Linux-amd64- \<version no.\> . tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-linux-amd64-v1.0.12-rc3.tgz)
- **Windows**: [Azure-vnet-CNI-Windows-amd64- \<version no.\> . zip](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-windows-amd64-v1.0.12-rc3.zip)

Másolja a [Linux](https://github.com/Azure/azure-container-networking/blob/master/scripts/install-cni-plugin.sh) vagy [Windows](https://github.com/Azure/azure-container-networking/blob/master/scripts/Install-CniPlugin.ps1) rendszerhez tartozó telepítési szkriptet a számítógépére. Mentse a szkriptet egy `scripts` könyvtárba a számítógépén. Linux esetén az `install-cni-plugin.sh`, Windows esetén pedig az `install-cni-plugin.ps1` nevet adja a fájlnak. A beépülő modul telepítéséhez futtassa a platformjának megfelelő szkriptet, és adja meg az Ön által használt beépülő modul verzióját. Például megadhatja a *v1.0.12-rc3* verziót:

   ```bash
   \$scripts/install-cni-plugin.sh [version]
   ```

   ```powershell
   scripts\\ install-cni-plugin.ps1 [version]
   ```

A szkript Linux esetén a `/opt/cni/bin` mappába, Windows esetén pedig a `c:\cni\bin` mappába telepíti a beépülő modult. A telepített beépülő modulhoz egy egyszerű hálózati konfigurációs fájl is tartozik, amely a telepítés után lép működésbe. Nem szükséges frissíteni. A fájlban található beállításokkal kapcsolatos további információért tekintse meg a [CNI hálózati konfigurációs fájlja](#cni-network-configuration-file) című szakaszt.