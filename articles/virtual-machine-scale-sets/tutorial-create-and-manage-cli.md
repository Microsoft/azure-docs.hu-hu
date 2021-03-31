---
title: 'Oktatóanyag: virtuálisgép-méretezési csoport létrehozása & kezelése – Azure CLI'
description: Megismerheti, hogy az Azure CLI segítségével hogyan hozhat létre virtuálisgép-méretezési csoportokat, valamint néhány általános felügyeleti feladattal ismerkedhet meg, például a példány elindításának és leállításának módjával vagy a méretezési csoport kapacitásának módosításával.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 0f94823b958ae5f95789dd4ef9a62057bdf764a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517461"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Oktatóanyag: Virtuálisgép-méretezési csoport létrehozása és kezelése az Azure CLI használatával
A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A virtuálisgép-méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Virtuálisgép-méretezési csoport létrehozása és kapcsolódás a csoporthoz
> * Virtuálisgép-rendszerképek kiválasztása és használata
> * Virtuálisgép-példányok adott méreteinek áttekintése és használata
> * Méretezési csoport manuális méretezése
> * Méretezési csoportra vonatkozó gyakori felügyeleti feladatok végrehajtása

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.29 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuálisgép-méretezési csoport létrejötte előtt létre kell hozni. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Ebben a példában egy *myResourceGroup* nevű erőforráscsoportot hozunk létre az *eastus* régióban. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Az erőforráscsoport nevének meghatározására a méretezési csoport létrehozásakor vagy módosításakor kerül sor a jelen oktatóanyag keretein belül.


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Hozzon létre egy virtuálisgép-méretezési csoportot az [az vmss create](/cli/azure/vmss) parancs használatával. A következő példa egy *myScaleSet* nevű méretezési csoportot, valamint SSH-kulcsokat hoz létre, amennyiben azok még nem léteznének:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A méretezési csoport erőforrásainak és virtuálisgép-példányainak létrehozása és konfigurálása néhány percet vesz igénybe. A forgalom az egyes virtuális gépek közötti elosztása érdekében a parancs egy terheléselosztót is létrehoz.


## <a name="view-the-vm-instances-in-a-scale-set"></a>A méretezési csoportokban lévő virtuálisgép-példányok megtekintése
A méretezési csoportban futó virtuálisgép-példányok listájának megjelenítéséhez használja az [az vmss list-instances](/cli/azure/vmss) parancsot az alábbi módon:

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Az alábbi példa kimenetében két virtuálisgép-példány látható a méretezési csoportban:

```output
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  c059be0c-37a2-497a-b111-41272641533c
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUP  ec19e7a7-a4cd-4b24-9670-438f4876c1f9
```


A kimenet első oszlopa az *InstanceId* paramétert mutatja. Az egy adott virtuálisgép-példánnyal kapcsolatos további információk megtekintéséhez adja hozzá a `--instance-id` paramétert az [az vmss get-instance-view](/cli/azure/vmss) parancshoz. A következő példa információkat tekint meg az *1*-es számú virtuálisgép-példányról:

```azurecli-interactive
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 1
```


## <a name="list-connection-information"></a>Kapcsolatadatok listázása
A rendszer egy nyilvános IP-címet rendel hozzá a terheléselosztóhoz, amely a forgalmat az egyéni virtuálisgép-példányokhoz irányítja. Alapértelmezés szerint a rendszer a hálózati címfordítási (NAT) szabályokat adja hozzá az Azure-terheléselosztóhoz, amely továbbítja a távoli kapcsolati forgalmat az egyes virtuális gépekre egy adott porton keresztül. A méretezési csoportban lévő virtuálisgép-példányok csatlakoztatásához létesítsen távoli kapcsolatot a hozzárendelt nyilvános IP-címhez és portszámhoz.

A méretezési csoportban lévő virtuálisgép-példányokhoz való csatlakozáshoz használható cím és portok listázásához használja az [az vmss list-instance-connection-info](/cli/azure/vmss) parancsot:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Az alábbi példakimeneten a példány neve, a terheléselosztó nyilvános IP-címe és a portszám látható, amelyeket a NAT-szabályok a forgalom továbbításához használnak:

```output
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH-n keresztül csatlakozzon az első virtuálisgép-példányhoz. Adja meg a nyilvános IP-címét és portszámát a `-p` paraméterrel azt ezt megelőző parancsban látható módon:

```console
ssh azureuser@13.92.224.66 -p 50001
```

A virtuálisgép-példányba történő bejelentkezést követően szükség szerint manuális konfigurációs módosításokat hajthat végre. Egyelőre normál módon zárja be az SSH-munkamenetet:

```console
exit
```


## <a name="understand-vm-instance-images"></a>A virtuálisgép-példányok rendszerképeinek ismertetése
Az oktatóanyag kezdetén létrehozott méretezési csoporthoz a `--image` paraméterben az *UbuntuLTS* érték volt megadva a virtuálisgép-példányokhoz. Az Azure Marketplace számos rendszerképet tartalmaz, amelyek használatával virtuálisgép-példányokat hozhat létre. A leggyakrabban használt rendszerképek listájának megtekintéséhez használja az [az vm image list](/cli/azure/vm/image) parancsot.

```azurecli-interactive
az vm image list --output table
```

Az alábbi példa kimenetében az Azure-beli leggyakoribb virtuálisgép-rendszerképek láthatók. Az *UrnAlias* paraméter segítségével adható meg ezen gyakori rendszerképek egyike a méretezési csoport létrehozása során.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

A teljes lista az `--all` argumentum hozzáadásával tekinthető meg. A rendszerképek listája a `--publisher` (közzétevő) vagy az `–-offer` (ajánlat) alapján is szűrhető. Az alábbi példában a listát az összes rendszerképre szűrjük, amelyek ajánlatában szerepel a *CentOS* kifejezés:

```azurecli-interactive
az vm image list --offer CentOS --all --output table
```

Az alábbi összevont kimenetben néhány elérhető CentOS 7.3 rendszerkép látható:

```output
Offer    Publisher   Sku   Urn                                 Version
-------  ----------  ----  ----------------------------------  -------------
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20161221   7.3.20161221
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170421   7.3.20170421
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170517   7.3.20170517
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170612   7.3.20170612
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170707   7.3.20170707
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170925   7.3.20170925
```

Egy megadott rendszerképet használó méretezési csoport telepítéséhez használja az *Urn* oszlopban lévő értékeket. A rendszerkép megadásakor a rendszerkép verziószáma kicserélhető a *latest* (legújabb) kifejezésre, amely a disztribúció legújabb verzióját választja ki. Az alábbi a példában az `--image` argumentum használatával egy CentOS 7.3-rendszerkép legújabb verzióját adjuk meg.

> [!IMPORTANT]
> Javasoljuk, hogy a *legújabb* rendszerkép-verziót használja. A "legfrissebb" érték megadásával a lemezkép legújabb verziója használható a telepítés ideje alatt. Vegye figyelembe, hogy még ha a legújabbat is használja, a virtuálisgép-lemezkép nem frissül automatikusan az üzembe helyezési idő után, még akkor is, ha az új verzió elérhetővé válik.

Mivel a méretezési csoport erőforrásainak és virtuálisgép-példányainak létrehozása és konfigurálása néhány percet vesz igénybe, nem kell üzembe helyeznie az alábbi méretezési csoportot:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetCentOS \
  --image OpenLogic:CentOS:7.3:latest \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="understand-vm-instance-sizes"></a>A virtuálisgép-példányok méreteinek ismertetése
A virtuálisgép-példány mérete, más néven *SKU*, a virtuálisgép-példány által elérhető számítási erőforrások, azaz a processzor, a grafikus processzor és a memória mennyiségét határozza meg. A méretezési csoportban lévő virtuálisgép-példányokat a várható számítási igényeknek megfelelően kell méretezni.

### <a name="vm-instance-sizes"></a>A virtuálisgép-példányok mérete
Az alábbi táblázat a virtuális gépek gyakori méreteit használati esetek alapján kategorizálja.

| Típus                     | Gyakori méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Általános célú](../virtual-machines/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Kiegyensúlyozott processzor-memória arány. Ideális választás fejlesztéshez/teszteléshez, valamint kis- és közepes méretű alkalmazásokhoz és adatkezelési megoldásokhoz.  |
| [Számításoptimalizált](../virtual-machines/sizes-compute.md)   | Fs, F             | Magas processzor-memória arány a processzor javára. Megfelelő választás a közepes forgalmú alkalmazásokhoz, hálózati berendezésekhez és kötegelt folyamatokhoz.        |
| [Memóriaoptimalizált](../virtual-machines/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Magas memória-mag arány a memória javára. Ideális választás relációs adatbázisokhoz, közepes és nagy gyorsítótárakhoz, memóriában végzett elemzésekhez.                 |
| [Tároptimalizált](../virtual-machines/sizes-storage.md)      | Ls                | Magas lemez-adatátviteli és I/O-műveleti jellemzők. Ideális Big Data-, SQL- és NoSQL-adatbázisok esetén.                                                         |
| [GPU](../virtual-machines/sizes-gpu.md)          | NV, NC            | Specializált virtuális gépek nagy terhelést jelentő grafikus rendereléshez és videószerkesztéshez.       |
| [Nagy teljesítmény](../virtual-machines/sizes-hpc.md) | H, A8-11          | Leghatékonyabb processzorral rendelkező virtuális gépeink, választható nagy átviteli sebességű hálózati adapterekkel (RDMA). 

### <a name="find-available-vm-instance-sizes"></a>Elérhető virtuálisgép-példányméretek keresése
Az adott régióban elérhető virtuálisgép-példány méretek megtekintéséhez használja az [az vm list-sizes](/cli/azure/vm) parancsot.

```azurecli-interactive
az vm list-sizes --location eastus --output table
```

A kimenet a következő sűrített példához hasonló, amelyben az egyes virtuálisgép-méretekhez hozzárendelt erőforrások láthatók:

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 4          3584  Standard_DS1_v2                       1           1047552                    7168
                 8          7168  Standard_DS2_v2                       2           1047552                   14336
[...]
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
[...]
                 4          2048  Standard_F1                           1           1047552                   16384
                 8          4096  Standard_F2                           2           1047552                   32768
[...]
                24         57344  Standard_NV6                          6           1047552                   38912
                48        114688  Standard_NV12                        12           1047552                  696320
```

### <a name="create-a-scale-set-with-a-specific-vm-instance-size"></a>Méretezési csoport létrehozása egy megadott méretű virtuálisgép-példánnyal
Az oktatóanyag elején a méretezési csoport létrehozásakor a *Standard_D1_v2* alapértelmezett virtuálisgép-termékváltozat (SKU) lett megadva a virtuálisgép-példányokhoz. Megadhat egy másik virtuálisgép-példányméretet az [az vm list-sizes](/cli/azure/vm) parancs kimenete alapján. Az alábbi példa a `--vm-sku` paraméterrel a méretezési csoportot a *Standard_F1* virtuálisgép-példányméret megadásával hozza létre. Mivel a méretezési csoport erőforrásainak és virtuálisgép-példányainak létrehozása és konfigurálása néhány percet vesz igénybe, nem kell üzembe helyeznie az alábbi méretezési csoportot:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetF1Sku \
  --image UbuntuLTS \
  --vm-sku Standard_F1 \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="change-the-capacity-of-a-scale-set"></a>Méretezési csoport kapacitásának módosítása
Az oktatóanyag kezdetén létrehozott méretezési csoporthoz alapértelmezés szerint két virtuálisgép-példányt telepített. Az `--instance-count` paraméter [az vmss create](/cli/azure/vmss) parancshoz történő hozzáadásával módosíthatja a méretezési csoporttal létrehozott példányok számát. A meglévő méretezési csoportban lévő virtuálisgép-példányok számának növeléséhez vagy csökkentéséhez manuálisan módosíthatja a kapacitást. A méretezési csoport létrehozza vagy eltávolítja a szükséges számú virtuálisgép-példányt, majd konfigurálja a terheléselosztót a forgalom elosztásához.

A méretezési csoportban lévő virtuálisgép-példányok számának manuális növeléséhez vagy csökkentéséhez használja az [az vmss scale](/cli/azure/vmss) parancsot. Az alábbi példa a méretezési csoportban lévő virtuálisgép-példányok számát *3*-ra állítja:

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 3
```

A méretezési csoport kapacitásának frissítése néhány percet vesz igénybe. A méretezési csoportban jelenleg futó példányok számának megtekintéséhez használja az [az vmss show](/cli/azure/vmss) parancsot, és állítsa be az *sku.capacity* lekérdezést:

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```


## <a name="common-management-tasks"></a>Általános kezelési feladatok
Mostantól létrehozhat egy méretezési csoportot, listát készíthet a kapcsolatadatokról és virtuálisgép-példányokhoz csatlakozhat. Megismerte, hogyan használhatók a különböző operációsrendszer-lemezképek a virtuálisgép-példányokhoz, hogyan választhatók ki a különféle virtuálisgép-méretek és hogyan méretezhető manuálisan a példányok száma. A napi felügyelet részeként szükség lehet a méretezési csoportban lévő virtuálisgép-példányok leállítására, elindítására vagy újraindítására.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>A méretezési csoportokban lévő virtuálisgép-példányok leállítása és felszabadítása
A méretezési csoportban lévő egy vagy több virtuálisgép-példány leállításához használja az [az vmss stop](/cli/azure/vmss) parancsot. Az `--instance-ids` paraméter segítségével megadhat egy vagy több leállítandó virtuálisgép-példányt. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuálisgép-példány le lesz állítva. A következő példa leállítja az *1-es* példányt:

```azurecli-interactive
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

A leállított virtuálisgép-példányok lefoglalva maradnak, és továbbra is számítási költségeket generálnak. Ha szeretné inkább felszabadítani a virtuálisgép-példányokat, és csak tárolási költségeket fizetni, az [az vmss deallocate](/cli/azure/vmss) parancsot használja. A következő példa leállítja és felszabadítja az *1-es* példányt:

```azurecli-interactive
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="start-vm-instances-in-a-scale-set"></a>A méretezési csoportokban lévő virtuálisgép-példányok indítása
A méretezési csoportban lévő egy vagy több virtuálisgép-példány indításához használja az [az vmss start](/cli/azure/vmss) parancsot. Az `--instance-ids` paraméter segítségével megadhat egy vagy több indítandó virtuálisgép-példányt. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuálisgép-példány el lesz indítva. A következő példa elindítja az *1-es* példányt:

```azurecli-interactive
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="restart-vm-instances-in-a-scale-set"></a>A méretezési csoportokban lévő virtuálisgép-példányok újraindítása
A méretezési csoportban lévő egy vagy több virtuálisgép-példány újraindításához használja az [az vmss restart](/cli/azure/vmss) parancsot. Az `--instance-ids` paraméter segítségével megadhat egy vagy több újraindítandó virtuálisgép-példányt. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuálisgép-példány újra lesz indítva. A következő példa újraindítja az *1-es* példányt:

```azurecli-interactive
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az erőforráscsoportok törlésével az összes bennük foglalt erőforrás, azaz a virtuálisgép-példányok, a virtuális hálózat és a lemezek is törölve lesznek. A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag az Azure CLI néhány alapszintű méretezésicsoport-létrehozási és -kezelési műveletét mutatta be:

> [!div class="checklist"]
> * Virtuálisgép-méretezési csoport létrehozása és kapcsolódás a csoporthoz
> * Virtuálisgép-rendszerképek kiválasztása és használata
> * Adott virtuálisgép-méretek áttekintése és használata
> * Méretezési csoport manuális méretezése
> * Méretezési csoportra vonatkozó gyakori felügyeleti feladatok végrehajtása

Folytassa a következő oktatóanyaggal, amely a méretezésicsoport-lemezeket ismerteti.

> [!div class="nextstepaction"]
> [Adatlemezek használata méretezési csoportokkal](tutorial-use-disks-cli.md)
