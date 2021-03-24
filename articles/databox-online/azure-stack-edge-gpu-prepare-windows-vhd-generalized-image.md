---
title: Virtuálisgép-rendszerképek létrehozása a Windows virtuális merevlemez általánosított lemezképéről a Azure Stack Edge Pro GPU-eszközhöz
description: Ismerteti, hogyan lehet a rendszerképeket a Windows VHD-ről vagy egy VHDX kezdődően általánosított rendszerképekből elsajátítani. Ezzel az általánosított képpel olyan virtuálisgép-lemezképeket hozhat létre, amelyeket az Azure Stack Edge Pro GPU-eszközön üzembe helyezett virtuális gépekhez használhat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 978099accd57d15c750a27f77b2e220f569a2dd0
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962523"
---
# <a name="use-generalized-image-from-windows-vhd-to-create-a-vm-image-for-your-azure-stack-edge-pro-device"></a>Általános rendszerképek használata a Windows VHD-ről a Azure Stack Edge Pro-eszközhöz készült virtuálisgép-rendszerkép létrehozásához

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ha virtuális gépeket szeretne üzembe helyezni a Azure Stack Edge Pro-eszközön, létre kell hoznia egy egyéni virtuálisgép-rendszerképet, amelyet a virtuális gépek létrehozásához használhat. Ez a cikk egy általánosított rendszerkép létrehozásához szükséges lépéseket ismerteti a Windows rendszerű virtuális merevlemezek vagy VHDX előkészítéséhez. Ez az általánosított rendszerkép a Azure Stack Edge Pro-eszközhöz készült virtuálisgép-rendszerkép létrehozásához használatos. 

## <a name="about-preparing-windows-vhd"></a>Tudnivalók a Windows VHD előkészítéséről

A Windows virtuális merevlemezek vagy VHDX *általánosított* rendszerképek vagy *speciális* rendszerképek létrehozására használhatók. Az alábbi táblázat összefoglalja az *általánosított* és a *speciális* lemezképek közötti fő különbségeket.


|Lemezkép típusa  |Általánosított  |Specializált  |
|---------|---------|---------|
|Cél     |Bármely rendszeren üzembe helyezhető         | Adott rendszerre irányul        |
|Telepítés a rendszerindítás után     | A telepítő szükséges a virtuális gép első indításakor.          | A telepítés nem szükséges. <br> A platform bekapcsolja a virtuális gépet.        |
|Konfiguráció     |Állomásnév, rendszergazda-felhasználó és más virtuálisgép-specifikus beállítások szükségesek.         |Teljesen előre konfigurálva.         |
|Használatban, ha     |Több új virtuális gép létrehozása ugyanabból a rendszerképből.         |Egy adott gép áttelepítése vagy egy virtuális gép visszaállítása a korábbi biztonsági mentésből.         |


Ez a cikk az általánosított lemezképből való üzembe helyezéshez szükséges lépéseket ismerteti. Ha speciális lemezképből szeretne üzembe helyezni, tekintse meg a [speciális Windows VHD használata](azure-stack-edge-placeholder.md) az eszközhöz című témakört.

> [!IMPORTANT]
> Ez az eljárás nem vonatkozik azokra az esetekre, amikor a forrás virtuális merevlemez egyéni konfigurációkkal és beállításokkal van konfigurálva. Előfordulhat például, hogy további műveletek szükségesek az egyéni tűzfalszabályok vagy proxybeállításokat tartalmazó VHD-k általánosítása érdekében. További információ ezekről a további műveletekről: [Windows virtuális merevlemez előkészítése az Azure-ba való feltöltéshez – azure Virtual Machines](../virtual-machines/windows/prepare-for-upload-vhd-image.md).


## <a name="vm-image-workflow"></a>VM-rendszerkép munkafolyamata

A Windows virtuális merevlemez általánosított képként való használatra való előkészítésének magas szintű munkafolyamata a következő lépésekből áll:

1. Alakítsa át a forrás VHD-t vagy a VHDX egy rögzített méretű VHD-re.
1. Hozzon létre egy virtuális gépet a Hyper-V-ben a rögzített VHD használatával.
1. Kapcsolódjon a Hyper-V virtuális géphez.
1. A VHD-t a *Sysprep* segédprogrammal általánosíthatja. 
1. Másolja az általánosított képet a blob Storage-ba.
1. Az általánosított rendszerképek használatával telepíthet virtuális gépeket az eszközön. További információkért lásd: [virtuális gép üzembe helyezése Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) használatával vagy [virtuális gép üzembe helyezése a PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)használatával.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt előkészíti a Windows virtuális merevlemezt a Azure Stack Edge-ben általánosított rendszerképekként való használatra, győződjön meg a következőkről:

- A Windows támogatott verzióját tartalmazó VHD-vagy VHDX rendelkezik. Lásd: a Azure Stack Edge Pro [által támogatott vendég operációs rendszerek]() . 
- A Hyper-V kezelőjével már telepítve van egy Windows-ügyfél. 
- Hozzáférése van egy Azure Blob Storage-fiókhoz, amely a VHD-k előkészítését követően tárolja a virtuális merevlemezt.

## <a name="prepare-a-generalized-windows-image-from-vhd"></a>Általánosított Windows-rendszerkép előkészítése VHD-ről

## <a name="convert-to-a-fixed-vhd"></a>Konvertálás rögzített VHD-re 

Az eszközön rögzített méretű virtuális merevlemezeket kell létrehoznia a virtuálisgép-lemezképek létrehozásához. A forrásként szolgáló Windows VHD-t vagy VHDX-t egy rögzített VHD-re kell konvertálnia. Kövesse az alábbi lépéseket:

1. Nyissa meg a Hyper-V kezelőjét az ügyfél rendszerén. Ugrás a **lemez szerkesztése** gombra.

    ![A Hyper-V kezelőjének megnyitása](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. **Az** alapismeretek lapon válassza a **tovább>** elemet.

1. A **virtuális merevlemez keresése** oldalon keresse meg az átalakítani kívánt forrásként szolgáló Windows VHD vagy VHDX helyét. Válassza a **következő>**.

    ![Virtuális merevlemez helyének megkeresése](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. A **művelet kiválasztása** lapon válassza a **Konvertálás** lehetőséget, majd válassza a **tovább>** elemet.

    ![Művelet lap kiválasztása](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. A **lemez formátumának kiválasztása** lapon válassza a **VHD** formátum lehetőséget, majd válassza a **tovább>** elemet.

   ![Lemez formátumának kiválasztása lap](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)


1. A **lemez típusának kiválasztása** lapon válassza a **rögzített méret** lehetőséget, majd válassza a **tovább>** elemet.

   ![Lemez típusának kiválasztása lap](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)


1. A **lemez konfigurálása** lapon keresse meg a helyet, és adja meg a rögzített méretű VHD-lemez nevét. Válassza a **következő>**.

   ![Lemez konfigurálása lap](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)


1. Tekintse át az összegzést, és válassza a **Befejezés** lehetőséget. A VHD-vagy VHDX-konverzió néhány percet vesz igénybe. Az átalakítás ideje a forrásoldali lemez méretétől függ. 

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
Ezt a rögzített VHD-t a cikkben szereplő összes további lépéshez használhatja.


## <a name="create-a-hyper-v-vm-from-fixed-vhd"></a>Hyper-V virtuális gép létrehozása rögzített VHD-ből

1. A **Hyper-V kezelőjében** a hatókör ablaktáblán kattintson a jobb gombbal a rendszer csomópontjára a helyi menü megnyitásához, majd válassza az **új**  >  **virtuális gép** lehetőséget.

    ![Új virtuális gép kiválasztása a hatókör panelen](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. Az új virtuális gép varázsló alapismeretek **lapján kattintson a** **Tovább gombra**.

1. A **név és hely megadása** lapon adja meg a virtuális gép **nevét** és **helyét** . Kattintson a **Tovább** gombra.

    ![Adja meg a virtuális gép nevét és helyét](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. A **generáció megadása** lapon válassza az **1. generációs** elemet a. vhd-eszköz rendszerképének típusához, majd kattintson a **tovább** gombra.    

    ![Generáció meghatározása](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. Rendelje hozzá a kívánt memóriát és hálózati konfigurációkat.

1. A **virtuális merevlemez összekapcsolása** lapon válassza a **meglévő virtuális merevlemez használata** lehetőséget, adja meg a korábban létrehozott Windows fix VHD helyét, majd kattintson a **tovább** gombra.

    ![Virtuális merevlemez összekapcsolásának lapja](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. Tekintse át az **összegzést** , majd kattintson a **Befejezés** gombra a virtuális gép létrehozásához.

A virtuális gép létrehozása több percet vesz igénybe.
     

## <a name="connect-to-the-hyper-v-vm"></a>Kapcsolódás a Hyper-V rendszerű virtuális géphez

A virtuális gép megjelenik az ügyfél-rendszeren lévő virtuális gépek listáján. 


1. Válassza ki a virtuális gépet, majd kattintson a jobb gombbal, és válassza az **Indítás** lehetőséget. 

    ![Válassza ki a virtuális gépet, és indítsa el](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-2.png)

2. A virtuális gép a **Futtatás** alatt látható megjelenítést jeleníti meg. Válassza ki a virtuális gépet, majd kattintson a jobb gombbal, majd válassza a **kapcsolat** lehetőséget.

    ![Kapcsolódás egy virtuális géphez](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-4.png)

Miután csatlakozott a virtuális géphez, fejezze be a gép telepítése varázslót, majd jelentkezzen be a virtuális gépre.


## <a name="generalize-the-vhd"></a>A VHD általánosítása  

A *Sysprep* segédprogram használatával általánosíthatja a VHD-t. 

1. Nyisson meg egy parancssort a virtuális gépen belül.
1. Futtassa a következő parancsot a virtuális merevlemez általánosítása érdekében. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    Részletekért lásd: a  [Sysprep (rendszer-előkészítés) áttekintése](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).
1.  A parancs befejezése után a virtuális gép le lesz állítva. **Ne indítsa újra a virtuális gépet**.

## <a name="upload-the-vhd-to-azure-blob-storage"></a>A virtuális merevlemez feltöltése az Azure Blob Storage-ba

A VHD-t mostantól egy általánosított rendszerkép létrehozására használhatja Azure Stack Edge-ben. 

1. Töltse fel a VHD-t az Azure Blob Storage-ba. Tekintse meg a [virtuális merevlemez feltöltése Azure Storage Explorer használatával](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md)című témakör részletes utasításait.
1. A feltöltés befejezése után a feltöltött lemezkép használatával virtuálisgép-lemezképeket és virtuális gépeket hozhat létre. 

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->



## <a name="next-steps"></a>Következő lépések

Az üzembe helyezés jellegétől függően az alábbi eljárások közül választhat.

- [Virtuális gép üzembe helyezése általánosított lemezképből Azure Portal használatával](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Virtuális gép üzembe helyezése általánosított lemezképből Azure PowerShell használatával](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
