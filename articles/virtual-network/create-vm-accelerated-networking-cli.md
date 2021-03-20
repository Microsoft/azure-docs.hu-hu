---
title: Gyorsított hálózatkezelést használó Azure-beli virtuális gép létrehozása az Azure CLI-vel
description: Megtudhatja, hogyan hozhat létre Linux rendszerű virtuális gépet, ha engedélyezve van a gyorsított hálózatkezelés.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 643a52c9be04fb325b8e1d088faeb68e473aa673
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919952"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Gyorsított hálózatkezeléssel rendelkező, Linux rendszerű virtuális gép létrehozása az Azure CLI-vel

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy linuxos virtuális gépet (VM) a gyorsított hálózatkezeléssel. A gyorsított hálózatkezeléssel rendelkező Windows rendszerű virtuális gépek létrehozásával kapcsolatban lásd: [Windows rendszerű virtuális gép létrehozása gyorsított hálózatkezeléssel](create-vm-accelerated-networking-powershell.md). A gyorsított hálózatkezelés lehetővé teszi az egyszintű I/O-virtualizálás (SR-IOV) használatát egy virtuális gépre, nagy mértékben javítja hálózati teljesítményét. Ez a nagy teljesítményű elérési út megkerüli a gazdagépet a DataPath, csökkenti a késést, a vibrálás és a CPU-kihasználtságot, és a legszigorúbb hálózati számítási feladatokhoz használja a támogatott virtuálisgép-típusoknál. Az alábbi képen a két virtuális gép közötti kommunikáció gyorsított hálózatkezeléssel és anélkül látható:

![Összehasonlítás](./media/create-vm-accelerated-networking/accelerated-networking.png)

A gyorsított hálózatkezelés nélkül a virtuális GÉPEN belüli és kívüli összes hálózati forgalomnak át kell haladnia a gazdagépen és a virtuális kapcsolón. A virtuális kapcsoló biztosítja az összes házirend-kényszerítést, például a hálózati biztonsági csoportokat, a hozzáférés-vezérlési listát, az elkülönítést és az egyéb, a hálózati forgalomhoz tartozó virtualizált szolgáltatásokat. Ha többet szeretne megtudni a virtuális kapcsolókról, olvassa el a [Hyper-V hálózati virtualizálás és a virtuális kapcsoló](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134230(v=ws.11)) című cikket.

A gyorsított hálózatkezeléssel a hálózati forgalom a virtuális gép hálózati adapterén (NIC) érkezik, majd továbbítva lesz a virtuális géphez. A virtuális kapcsoló által érintett összes hálózati házirend már ki van kiszervezve és alkalmazva a hardveren. A szabályzat a hardveren való alkalmazása lehetővé teszi, hogy a hálózati adapter közvetlenül a virtuális gépre továbbítsa a hálózati forgalmat, megkerülve a gazdagépet és a virtuális kapcsolót, miközben a gazdagépen alkalmazott összes házirendet megőrzi.

A gyorsított hálózatkezelés előnyei csak azokra a virtuális gépekre érvényesek, amelyeken engedélyezve van a szolgáltatás. A legjobb eredmények elérése érdekében ideális, ha ezt a funkciót legalább két, ugyanahhoz az Azure Virtual Network (VNet) hálózathoz csatlakozó virtuális gépre engedélyezi. A virtuális hálózatok keresztüli kommunikáció vagy a helyszíni csatlakozás esetén ez a funkció minimális hatással van a teljes késésre.

## <a name="benefits"></a>Előnyök
* **Kisebb késés/nagyobb csomagok másodpercenként (PPS):** Ha eltávolítja a virtuális kapcsolót a DataPath, azzal eltávolítja a gazdagépen a házirendek feldolgozására fordított időt, és növeli a virtuális gépen belül feldolgozható csomagok számát.
* **Csökkentett Jitter:** A virtuális kapcsolók feldolgozása az alkalmazandó házirend mennyiségétől és a feldolgozást végző CPU munkaterheléstől függ. A szabályzat kényszerítésének a hardverre való kiszervezése eltávolítja a változékonyságot a csomagok közvetlenül a virtuális géphez való továbbításával, a gazdagép és a virtuális gépek közötti kommunikáció, valamint az összes szoftveres megszakítás és környezeti kapcsoló eltávolításával.
* **Csökkent CPU-kihasználtság:** A gazdagépen lévő virtuális kapcsoló megkerülése kevesebb CPU-kihasználtságot eredményez a hálózati forgalom feldolgozásakor.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
Az Azure-katalógusból az alábbi disztribúciók támogatottak: 
* **Ubuntu 14,04 Linux rendszeren – Azure kernel**
* **Ubuntu 16,04 vagy újabb** 
* **SLES12 SP3 vagy újabb verzió** 
* **RHEL 7,4 vagy újabb**
* **CentOS 7,4 vagy újabb**
* **CoreOS Linux**
* **Debian "stretch" a backports rendszermaggal, Debian "Buster" vagy újabb verzióval**
* **Oracle Linux 7,4-es és újabb verziók a Red hat-kompatibilis Kernelrel (RHCK)**
* **Oracle Linux 7,5 és újabb verzió a UEK 5-ös verziójával**
* **FreeBSD 10,4, 11,1 & 12,0 vagy újabb**

## <a name="limitations-and-constraints"></a>Korlátozások és megkötések

### <a name="supported-vm-instances"></a>Támogatott VM-példányok
A gyorsított hálózatkezelést a legtöbb általános célú és a számítási optimalizált példány mérete támogatja 2 vagy több vCPU. A feleznie támogató példányokon a gyorsított hálózatkezelést a 4 vagy több vCPU rendelkező virtuálisgép-példányok támogatják. 

A gyorsított hálózatkezelés támogatását az egyes [virtuálisgép-méretek](../virtual-machines/sizes.md) dokumentációjában találja. 

### <a name="custom-images"></a>Egyéni rendszerképek
Ha egyéni rendszerképet használ, és a rendszerkép támogatja a gyorsított hálózatkezelést, győződjön meg arról, hogy a szükséges illesztőprogramok működnek a Mellanox ConnectX-3 és a ConnectX-4 LX hálózati adapterek használatával az Azure-ban.

### <a name="regions"></a>Régiók
Az összes nyilvános Azure-régióban, valamint Azure Government felhőkben is elérhető.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Gyorsított hálózatkezelés engedélyezése egy futó virtuális gépen
A gyorsított hálózatkezelést nem támogató, támogatott virtuálisgép-méret csak akkor engedélyezhető, ha a funkció le van állítva és fel van foglalva.  
### <a name="deployment-through-azure-resource-manager"></a>Üzembe helyezés Azure Resource Manageron keresztül
A virtuális gépek (klasszikus) a gyorsított hálózatkezeléssel nem helyezhetők üzembe.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Linux rendszerű virtuális gép létrehozása az Azure gyorsított hálózatkezeléssel
## <a name="portal-creation"></a>Portál létrehozása
Bár ez a cikk a gyorsított hálózatkezelést használó virtuális gépek Azure CLI használatával történő létrehozását ismerteti, a [Azure Portal használatával létrehozhat egy gyorsított hálózatkezeléssel rendelkező virtuális gépet](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)is. Amikor virtuális gépet hoz létre a portálon, a **virtuális gép létrehozása** panelen válassza a **hálózatkezelés** lapot.  Ezen a lapon van lehetőség a **gyorsított hálózatkezelésre**.  Ha a [támogatott operációs rendszert](#supported-operating-systems) és virtuálisgép- [méretet](#supported-vm-instances)választotta, akkor ez a beállítás automatikusan betöltődik a "be" értékre.  Ha nem, akkor feltölti a felgyorsított hálózatkezelés "off" beállítását, és megadja a felhasználónak, hogy miért nem engedélyezhető.   

* *Megjegyzés:* Csak a támogatott operációs rendszerek engedélyezhetők a portálon keresztül.  Ha egyéni rendszerképet használ, és a rendszerkép támogatja a gyorsított hálózatkezelést, akkor a parancssori felület vagy a PowerShell használatával hozza létre a virtuális gépet. 

A virtuális gép létrehozása után megerősítheti a gyorsított hálózatkezelést, ha azt [szeretné, hogy a gyorsított hálózatkezelés engedélyezve legyen](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>CLI-létrehozás
### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Telepítse a legújabb [Azure CLI](/cli/azure/install-azure-cli) -t, és jelentkezzen be egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával. Az alábbi példákban cserélje le a példában szereplő paraméterek nevét a saját értékeire. Példa a paraméterek neveire: *myResourceGroup*, *myNic* és *myVm*.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *CentralUS* helyen:

```azurecli
az group create --name myResourceGroup --location centralus
```

Válasszon egy támogatott Linux-régiót a [Linux-gyorsított hálózatkezelésben](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. Az alábbi példa egy *myVnet* nevű virtuális hálózatot hoz létre egy alhálózattal:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hozzon létre egy hálózati biztonsági csoportot az [az Network NSG Create](/cli/azure/network/nsg)paranccsal. A következő példa a *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot hozza létre:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

A hálózati biztonsági csoport több alapértelmezett szabályt tartalmaz, amelyek közül az egyik letiltja az internetről érkező összes bejövő hozzáférést. Nyisson meg egy portot, hogy engedélyezze az SSH-hozzáférést a virtuális géphez az [az Network NSG Rule Create](/cli/azure/network/nsg/rule)paranccsal:

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Hálózati adapter létrehozása gyorsított hálózatkezeléssel

Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal. Nincs szükség nyilvános IP-cím megadására, ha nem tervezi a virtuális gép internetről való elérését, de a cikk lépéseinek elvégzéséhez szükség van rá.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Hozzon létre egy hálózati adaptert az [az Network NIC Create](/cli/azure/network/nic) paranccsal, és engedélyezze a gyorsított hálózatkezelést. A következő példa létrehoz egy *myNic* nevű hálózati adaptert a *MyVnet* virtuális hálózat *mySubnet* alhálózatán, és hozzárendeli a *myNetworkSecurityGroup* hálózati biztonsági csoportot a hálózati adapterhez:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>Virtuális gép létrehozása és a hálózati adapter csatlakoztatása
A virtuális gép létrehozásakor határozza meg a létrehozott hálózati adaptert `--nics` . Válassza ki a [Linux-gyorsított hálózatkezelésben](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview)felsorolt méretet és eloszlást. 

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Az alábbi példa egy *myVM* nevű virtuális gépet hoz létre a UbuntuLTS-lemezképpel és egy olyan mérettel, amely támogatja a gyorsított hálózatkezelést (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

A virtuális gépek méretének és jellemzőinek listáját lásd: Linux rendszerű [virtuális gépek méretei](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

A virtuális gép létrehozása után a rendszer a következő példában szereplő kimenethez hasonló kimenetet ad vissza. Jegyezze fel a **publicIpAddress** értékét. Ez a címe a virtuális gép a következő lépésekben való elérésére szolgál.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Annak ellenőrzése, hogy engedélyezve van-e a gyorsított hálózat

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Cserélje le a értéket a `<your-public-ip-address>` létrehozott virtuális géphez hozzárendelt nyilvános IP-címhez, és cserélje le az *azureuser* -t, ha `--admin-username` a virtuális gép létrehozásakor más értéket használt.

```bash
ssh azureuser@<your-public-ip-address>
```

A bash rendszerhéjból írja be `uname -r` és erősítse meg, hogy a kernel verziója a következő verziók egyike, vagy nagyobb:

* **Ubuntu 16,04**: 4.11.0 – 1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Győződjön meg arról, hogy a Mellanox VF eszköz elérhető a virtuális géphez a `lspci` paranccsal. A visszaadott kimenet a következő kimenethez hasonló:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

A (z) parancs futtatásával keressen tevékenységeket a VF (Virtual Function) szolgáltatásban `ethtool -S eth0 | grep vf_` . Ha a következő mintához hasonló kimenetet kap, a gyorsított hálózatkezelés engedélyezve és működik.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
A gyorsított hálózatkezelés mostantól engedélyezve van a virtuális gépen.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>A virtuális függvény dinamikus kötésének és visszavonásának kezelése 
Az alkalmazásoknak a virtuális gépen elérhető szintetikus hálózati adapteren kell futniuk. Ha az alkalmazás közvetlenül a VF hálózati adapteren fut, nem kapja meg a virtuális géphez tartozó **összes** csomagot, mivel egyes csomagok megjelennek a szintetikus felületen.
Ha a szintetikus hálózati adapteren keresztül futtat egy alkalmazást, az garantálja, hogy az alkalmazás megkapja az **összes** olyan csomagot, amely erre a célra szolgál. Azt is ellenőrzi, hogy az alkalmazás folyamatosan fut-e, még akkor is, ha a VF-t visszavonják a gazdagép kiszolgálása során. A szintetikus hálózati ADAPTERhez kötődő alkalmazások **kötelező** követelményt jelentenek az összes alkalmazás számára, amely kihasználja a **gyorsított hálózatkezelést**.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Gyorsított hálózatkezelés engedélyezése meglévő virtuális gépeken
Ha felgyorsított hálózatkezelés nélküli virtuális gépet hozott létre, akkor lehetséges, hogy egy meglévő virtuális gépen engedélyezte ezt a funkciót.  A virtuális gépnek támogatnia kell a gyorsított hálózatkezelést az alábbi, a fent ismertetett előfeltételek teljesítésével:

* A virtuális gépnek a gyorsított hálózatkezeléshez támogatott méretnek kell lennie
* A virtuális gépnek támogatott Azure Gallery-rendszerképnek (és Linux kernel-verziónak) kell lennie
* A rendelkezésre állási csoportokban vagy VMSS lévő virtuális gépeket le kell állítani/fel kell osztani a gyorsított hálózatkezelés bármely hálózati adapteren való engedélyezése előtt.

### <a name="individual-vms--vms-in-an-availability-set"></a>A rendelkezésre állási csoportba tartozó egyes virtuális gépek & virtuális gépek
Először állítsa le/szabadítsa fel a virtuális gépet, vagy ha rendelkezésre állási csoport, a készletben lévő összes virtuális gép:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Fontos megjegyezni, hogy ha a virtuális gépet különállóan hozták létre, rendelkezésre állási csoport nélkül, a gyorsított hálózatkezelés engedélyezéséhez csak le kell állítania/fel kell szabadítania az egyes virtuális gépeket.  Ha a virtuális gép rendelkezésre állási csoporttal lett létrehozva, a rendelkezésre állási csoportba tartozó összes virtuális gépet le kell állítani/fel kell szüntetni, mielőtt engedélyezné a gyorsított hálózatkezelést bármelyik hálózati adapteren. 

Ha leállt, engedélyezze a gyorsított hálózatkezelést a virtuális gép hálózati adapterén:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Indítsa újra a virtuális gépet, vagy ha rendelkezésre állási csoport, a készletben lévő összes virtuális gép, és ellenőrizze, hogy engedélyezve van-e a gyorsított hálózat: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
A VMSS némileg eltérő, de ugyanazt a munkafolyamatot követi.  Először állítsa le a virtuális gépeket:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

A virtuális gépek leállítása után frissítse a gyorsított hálózatkezelési tulajdonságot a hálózati adapter alatt:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Vegye figyelembe, hogy a VMSS olyan virtuálisgép-frissítésekkel rendelkezik, amelyek három különböző beállítással, automatikus, működés közbeni és manuális beállításokkal alkalmazzák a frissítéseket.  Ebben az útmutatóban a szabályzat automatikusra van beállítva, hogy a VMSS az újraindítást követően azonnal vegye fel a módosításokat.  Ha automatikusra szeretné állítani, hogy a módosítások azonnal fellegyenek, válassza a következőt: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Végezetül indítsa újra a VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Ha újraindul, várjon, amíg a frissítés befejeződik, de ha elkészült, a VF a virtuális gépen fog megjelenni.  (Ellenőrizze, hogy támogatott operációs rendszert és virtuálisgép-méretet használ-e.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Meglévő virtuális gépek átméretezése gyorsított hálózatkezeléssel

A gyorsított hálózatkezelést használó virtuális gépeket csak a gyorsított hálózatkezelést támogató virtuális gépekre lehet átméretezni.  

A gyorsított hálózatkezelést használó virtuális gépek nem méretezhetők át olyan virtuálisgép-példányra, amely nem támogatja a gyorsított hálózatkezelést az átméretezési művelettel.  Ehelyett a következő virtuális gépek egyikének átméretezéséhez: 

* Állítsa le/szabadítsa fel a virtuális gépet, vagy ha rendelkezésre állási csoport/VMSS, állítsa le/szabadítsa fel a set/VMSS összes virtuális gépét.
* A gyorsított hálózatkezelést le kell tiltani a virtuális gép hálózati adapterén, vagy ha rendelkezésre állási csoport/VMSS, a set/VMSS összes virtuális gépe.
* Ha a gyorsított hálózatkezelés le van tiltva, a virtuális gép/rendelkezésre állási csoport/VMSS olyan új méretre helyezhető át, amely nem támogatja a gyorsított hálózatkezelést és az újraindítást.