---
title: Virtuálisgép-rendszerképek létrehozása a Azure Stack Edge Pro GPU-eszközhöz
description: Ismerteti, hogyan hozhat létre Linux vagy Windows rendszerű virtuálisgép-rendszerképeket az Azure Stack Edge Pro GPU-eszköz használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: ee0587063c0fac67068869c58471ada58354fab7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437672"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-device"></a>Egyéni virtuálisgép-rendszerképek létrehozása az Azure Stack Edge Pro-eszközhöz

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ha virtuális gépeket szeretne üzembe helyezni a Azure Stack Edge Pro-eszközön, létre kell hoznia egy egyéni virtuálisgép-rendszerképet, amelyet a virtuális gépek létrehozásához használhat. Ez a cikk a Linux vagy a Windows rendszerű virtuális gépek egyéni rendszerképeinek létrehozásához szükséges lépéseket ismerteti, amelyekkel virtuális gépeket helyezhet üzembe a Azure Stack Edge Pro-eszközön.

## <a name="vm-image-workflow"></a>VM-rendszerkép munkafolyamata

A munkafolyamathoz létre kell hoznia egy virtuális gépet az Azure-ban, testre kell szabnia a virtuális gépet, általánosítani, majd le kell töltenie az adott virtuális géphez tartozó virtuális MEREVLEMEZt. Ezt az általánosított VHD-t a rendszer feltölti Azure Stack Edge Pro-ba. A rendszer létrehoz egy felügyelt lemezt a virtuális merevlemezről. A rendszer létrehoz egy rendszerképet a felügyelt lemezről. Végül pedig a virtuális gépek az adott rendszerképből jönnek létre.

További információért látogasson el a [virtuális gép üzembe helyezése Azure stack Edge Pro-eszközön Azure PowerShell használatával](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Egyéni Windowsos virtuális gép rendszerképének létrehozása

A következő lépések végrehajtásával hozzon létre egy Windows rendszerű virtuálisgép-rendszerképet.

1. Hozzon létre egy Windows rendszerű virtuális gépet. További információért látogasson el az [oktatóanyag: Windows rendszerű virtuális gépek létrehozása és kezelése a Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Meglévő operációsrendszer-lemez letöltése.

    - Kövesse a [virtuális merevlemez letöltése](../virtual-machines/windows/download-vhd.md)című témakör lépéseit.

    - Az `sysprep` előző eljárásban leírtak helyett használja a következő parancsot.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       A [Sysprep (rendszer-előkészítés) áttekintését](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)is megtekintheti.

Ezzel a VHD-vel most létrehozhatja és üzembe helyezheti a virtuális gépet a Azure Stack Edge Pro-eszközön.

## <a name="create-a-linux-custom-vm-image"></a>Egyéni linuxos virtuális gép rendszerképének létrehozása

Hozzon létre egy linuxos virtuálisgép-rendszerképet a következő lépésekkel.

1. Hozzon létre egy Linux rendszerű virtuális gépet. További információért látogasson el az [oktatóanyag: Linux rendszerű virtuális gépek létrehozása és kezelése az Azure CLI-vel](../virtual-machines/linux/tutorial-manage-vm.md)című témakörben.

1. Szüntesse meg a virtuális gépet. Az Azure virtuálisgép-ügynök használatával törölje a számítógép-specifikus fájlokat és az adatfájlokat. Használja a `waagent` parancsot a `-deprovision+user` forrás linuxos virtuális gépen található paraméterrel. További információ: az [Azure Linux Agent megismerése és használata](../virtual-machines/extensions/agent-linux.md).

    1. Kapcsolódjon Linux rendszerű virtuális géphez egy SSH-ügyféllel.
    2. Az SSH ablakban adja meg a következő parancsot:
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > Csak olyan virtuális gépen futtassa ezt a parancsot, amelyet lemezképként fog rögzíteni. Ez a parancs nem garantálja, hogy a rendszer törli a képet az összes bizalmas adatról, vagy újraelosztásra alkalmas. A `+user` paraméter emellett eltávolítja az utolsó kiosztott felhasználói fiókot is. Ha a felhasználói fiók hitelesítő adatait szeretné megőrizni a virtuális gépen, csak a következőt használja: `-deprovision` .
     
    3. A folytatáshoz adja meg az **y** értéket. A paraméter hozzáadásával `-force` elkerülheti ezt a megerősítő lépést.
    4. A parancs befejezése után a **Kilépés** gombra kattintva zárja be az SSH-ügyfelet.  Ezen a ponton továbbra is fut a virtuális gép.


1. [Meglévő operációsrendszer-lemez letöltése](../virtual-machines/linux/download-vhd.md).

Ezzel a VHD-vel most létrehozhatja és üzembe helyezheti a virtuális gépet a Azure Stack Edge Pro-eszközön. A következő két Azure Marketplace-rendszerkép használatával hozhat létre linuxos egyéni rendszerképeket:

|Elemnév  |Description  |Publisher  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Az Ubuntu Server a világ legnépszerűbb Linux-környezete.|Canonical|
|[Debian 8 "Megbéklyóz"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |A Debian GNU/Linux az egyik legnépszerűbb Linux-disztribúció.     |credativ|

Az Azure Marketplace rendszerképeinek teljes listáját, amelyek működhetnek (nem teszteltek), látogasson el az [Azure marketplace Azure stack hub számára elérhető elemeire](/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910&preserve-view=true).


## <a name="next-steps"></a>Következő lépések

[Virtuális gépek üzembe helyezése Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).