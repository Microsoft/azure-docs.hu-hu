---
title: Oktatóanyag – Linux rendszerű virtuális gépek létrehozása és kezelése az Azure CLI-vel
description: Ez az oktatóanyag ismerteti, hogyan használja az Azure CLI-t Linux rendszerű Azure-beli virtuális gépek létrehozásához és kezeléséhez
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 77213fe83a56a33f4d10658f85465349949be792
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555601"
---
# <a name="tutorial-create-and-manage-linux-vms-with-the-azure-cli"></a>Oktatóanyag: Linux rendszerű virtuális gépek létrehozása és kezelése az Azure CLI használatával

Az Azure-beli virtuális gépek egy teljes mértékben konfigurálható és rugalmas számítási környezetet nyújtanak. Az oktatóanyag az Azure-beli virtuális gépek üzembe helyezésének alapvető elemeit ismerteti, például a virtuális gépek méretének és rendszerképeinek kiválasztását és a virtuális gépek telepítését. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Virtuális gép létrehozása és csatlakozás a virtuális géphez
> * Virtuálisgép-rendszerképek kiválasztása és használata
> * Adott virtuálisgép-méretek áttekintése és használata
> * Virtuális gép átméretezése
> * Virtuális gép állapotának áttekintése és értelmezése

Ez az oktatóanyag a CLI-t használja a [Azure Cloud Shellon](../../cloud-shell/overview.md)belül, amely folyamatosan frissül a legújabb verzióra. A Cloud Shell megnyitásához válassza a **kipróbálás** lehetőséget a kód bármely blokkjának elejéről.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. 

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni. Ebben a példában egy *myResourceGroupVM* nevű erőforráscsoportot hozunk létre az *eastus* régióban. 

```azurecli-interactive
az group create --name myResourceGroupVM --location eastus
```

Az erőforráscsoport meghatározására a virtuális gép létrehozásakor vagy módosításakor kerül sor, ahogy ez az oktatóanyagból is kiderül.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. 

Virtuális gép létrehozásakor több lehetőség is rendelkezésre áll, például az operációsrendszer-lemezkép, a lemezméretezés vagy a rendszergazdai hitelesítő adatok. Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre, mely Ubuntu Server rendszert futtat. Ezen a virtuális gépen létrehozunk egy *azureuser* nevű felhasználói fiókot, illetve SSH-kulcsokat generálunk, ha még nem léteznek a kulcsok alapértelmezett helyén (*~/.ssh*):

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása eltarthat néhány percig. A virtuális gép létrehozása után az Azure CLI a virtuális géppel kapcsolatos adatokat jelenít meg. Jegyezze fel a `publicIpAddress` címet, amelynek segítségével a virtuális gép elérhető. 

```output
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Kapcsolódás egy virtuális géphez

Most már SSH-n keresztül kapcsolódhat a virtuális géphez az Azure Cloud Shellben vagy a helyi számítógépről. Cserélje le a példában lévő IP-címet az előző lépésben feljegyzett `publicIpAddress` címre.

```bash
ssh azureuser@52.174.34.95
```

Miután bejelentkezett a virtuális gépre, telepíthet és konfigurálhat alkalmazásokat. Miután végzett, a szokásos módon zárhatja be az SSH-munkamenetet:

```bash
exit
```

## <a name="understand-vm-images"></a>Virtuálisgép-rendszerképek ismertetése

Az Azure Marketplace-en számos rendszerkép található, amelyekkel új virtuális gépeket lehet létrehozni. Az előző lépések során a virtuális gépet egy Ubuntu-rendszerkép használatával hoztuk létre. Ebben a lépésben az Azure CLI segítségével egy CentOS-rendszerképet keresünk a piactéren, amelynek használatával aztán üzembe helyezünk egy második virtuális gépet. 

A leggyakrabban használt rendszerképek listájának megtekintéséhez használja az [az vm image list](/cli/azure/vm/image) parancsot.

```azurecli-interactive 
az vm image list --output table
```

A parancs kimenete a legnépszerűbb Azure-beli virtuálisgép-rendszerképeket adja vissza.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

A teljes lista az `--all` argumentum hozzáadásával jeleníthető meg. A rendszerképek listája a `--publisher` (közzétevő) vagy az `–-offer` (ajánlat) alapján is szűrhető. Ebben a példában a listát az összes rendszerképre szűrjük, amelyek ajánlata megfelel a *CentOS*-nek. 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

Részleges kimenet:

```output
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

A virtuális gép egy adott rendszerkép használatával való üzembe helyezéséhez jegyezze fel az *Urn* oszlopban lévő értéket, amely a közzétevőből, az ajánlatból, az SKU-ból és opcionálisan a rendszerkép [azonosításához](cli-ps-findimage.md#terminology) használható verziószámból áll. A rendszerkép megadásakor a rendszerkép verziószáma kicserélhető a „latest” (legújabb) kifejezésre, amely a disztribúció legújabb verzióját választja ki. Ebben a példában az `--image` argumentum használatával egy CentOS 6.5-rendszerkép legújabb verzióját adjuk meg.  

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Virtuális gépek méreteinek ismertetése

A virtuális gép mérete a virtuális gép által elérhető számítási erőforrások, azaz a processzor, a grafikus processzor és a memória mennyiségét határozza meg. A virtuális gépeket a várható számítási igényeknek megfelelően kell méretezni. Ha a számítási feladatok mennyisége nő, a meglévő virtuális gépet át is lehet méretezni.

### <a name="vm-sizes"></a>A virtuális gépek mérete

Az alábbi táblázat a méreteket használati esetek alapján kategorizálja.  

| Típus                     | Gyakori méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Általános célú](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Kiegyensúlyozott processzor-memória arány. Ideális választás fejlesztéshez/teszteléshez, valamint kis- és közepes méretű alkalmazásokhoz és adatkezelési megoldásokhoz.  |
| [Számításoptimalizált](../sizes-compute.md)   | Fsv2          | Magas processzor-memória arány a processzor javára. Megfelelő választás a közepes forgalmú alkalmazásokhoz, hálózati berendezésekhez és kötegelt folyamatokhoz.        |
| [Memóriaoptimalizált](../sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | Magas memória-mag arány a memória javára. Ideális választás relációs adatbázisokhoz, közepes és nagy gyorsítótárakhoz, memóriában végzett elemzésekhez.                 |
| [Tároptimalizált](../sizes-storage.md)      | Lsv2, ls              | Magas lemez-adatátviteli és I/O-műveleti jellemzők. Ideális Big Data-, SQL- és NoSQL-adatbázisok esetén.                                                         |
| [GPU](../sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | Specializált virtuális gépek nagy terhelést jelentő grafikus rendereléshez és videószerkesztéshez.       |
| [Nagy teljesítmény](../sizes-hpc.md) | H        | Leghatékonyabb processzorral rendelkező virtuális gépeink, választható nagy átviteli sebességű hálózati adapterekkel (RDMA). |


### <a name="find-available-vm-sizes"></a>Elérhető virtuálisgép-méretek keresése

Egy adott régióban elérhető virtuálisgép-méretek megtekintéséhez használja az [az vm list-sizes](/cli/azure/vm) parancsot. 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

Részleges kimenet:

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Meghatározott méretű virtuális gép létrehozása

Az előző virtuálisgép-létrehozási példában a méretet nem adtuk meg, így az alapértelmezett méret lett alkalmazva. A virtuális gép létrehozásakor a méret az [az vm create](/cli/azure/vm) paranccsal és a `--size` argumentummal adható meg. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>Virtuális gép átméretezése

A virtuális gépek az üzembe helyezésüket követően a rendelkezésre álló erőforrás-kiosztás növelése vagy csökkentése érdekében átméretezhetők. A virtuális gép aktuális méretét az [az vm show](/cli/azure/vm) paranccsal tekintheti meg:

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

A virtuális gép átméretezése előtt ellenőrizze, hogy a kívánt méret az aktuális Azure-fürtön elérhető-e. Az [az vm list-vm-resize-options](/cli/azure/vm) parancs a méretek listáját adja vissza. 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```

Ha a kívánt méret elérhető, a virtuális gépet bekapcsolt állapotban is át lehet méretezni, de a művelet során újraindul. Az átméretezést az [az vm resize]( /cli/azure/vm) paranccsal hajthatja végre.

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Ha a kívánt méret nem érhető el az aktuális fürtön, a virtuális gépet fel kell szabadítani az átméretezés végrehajtása előtt. Az [az vm deallocate]( /cli/azure/vm) paranccsal állíthatja le és szabadíthatja fel a virtuális gépet. Fontos megjegyezni, hogy a virtuális gép visszakapcsolásakor az ideiglenes lemezen lévő adatok törlődhetnek. A nyilvános IP-cím is megváltozik, ha nem statikus IP-címet használ. 

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

Az átméretezés a felszabadítást követően hajtható végre. 

```azurecli-interactive
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

Az átméretezést követően a virtuális gép elindítható.

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Virtuális gépek energiaállapotai

Számos energiaállapot van, amelyek közül az Azure-beli virtuális gépek felvesznek egyet. Ez az állapot a virtuális gép aktuális állapotát tükrözi a hipervizor szempontjából. 

### <a name="power-states"></a>Energiaállapotok

| Energiaállapot | Leírás
|----|----|
| Indítás | Azt jelzi, hogy a virtuális gép indítása folyamatban van. |
| Futó | Azt jelzi, hogy a virtuális gép fut. |
| Leállítás | Azt jelzi, hogy a virtuális gép leáll. | 
| Leállítva | Azt jelzi, hogy a virtuális gép leállt. A leállított virtuális gépekért továbbra is díjat kell fizetni.  |
| Felszabadítás | Azt jelzi, hogy a virtuális gép felszabadítása folyamatban van. |
| Felszabadítva | Azt jelzi, hogy a virtuális gép el lett távolítva a hipervizorról, a vezérlősíkon azonban továbbra is elérhető. A felszabadított állapotban lévő virtuális gépekért nem kell díjat fizetni. |
| - | Azt jelzi, hogy a virtuális gép energiaállapota ismeretlen. |

### <a name="find-the-power-state"></a>Energiaellátás állapotának megállapítása

Egy adott virtuális gép állapotát az [az vm get-instance-view](/cli/azure/vm) paranccsal kérheti le. Ügyeljen arra, hogy egy érvényes nevet adjon meg a virtuális géphez és az erőforráscsoporthoz. 

```azurecli-interactive
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Kimenet:

```output
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

Az előfizetésben lévő összes virtuális gép energiaellátási állapotának lekéréséhez használja [Virtual Machines az összes olyan API-](/rest/api/compute/virtualmachines/listall) t, amelynek **statusOnly** a paraméter értéke *true (igaz*).

## <a name="management-tasks"></a>Felügyeleti feladatok

A virtuális gépek életciklusa során szükség lehet felügyeleti feladatok futtatására, például a virtuális gép indítására, leállítására vagy törlésére. Emellett előfordulhat, hogy szkripteket is szeretne létrehozni az ismétlődő vagy összetett feladatok automatizálására. Az Azure CLI használatával számos gyakori felügyeleti feladat futtatható a parancssorból vagy szkriptek segítségével. 

### <a name="get-ip-address"></a>IP-cím lekérése

A parancs a virtuális gép privát és nyilvános IP-címeit adja vissza.  

```azurecli-interactive
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Virtuális gép leállítása

```azurecli-interactive
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Virtuális gép indítása

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Erőforráscsoport törlése

Az erőforráscsoportok törlésével az összes bennük foglalt erőforrás is törölve lesz, azaz a virtuális gépek, a virtuális hálózat és a lemezek. A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a virtuális gépek létrehozásának és kezelésének alapvető műveleteivel ismerkedett meg, például:

> [!div class="checklist"]
> * Virtuális gép létrehozása és csatlakozás a virtuális géphez
> * Virtuálisgép-rendszerképek kiválasztása és használata
> * Adott virtuálisgép-méretek áttekintése és használata
> * Virtuális gép átméretezése
> * Virtuális gép állapotának áttekintése és értelmezése

Folytassa a következő oktatóanyaggal, amely a virtuálisgép-lemezeket ismerteti.  

> [!div class="nextstepaction"]
> [VM-lemezek létrehozása és kezelése](./tutorial-manage-disks.md)