---
title: Azure Lab Services – egyéni rendszerkép feltöltése a közös képtárba
description: Ismerteti, hogyan tölthetők fel egyéni rendszerképek a megosztott képgyűjteménybe. Az egyetemi informatikai részlegek különösen hasznosnak találják a lemezképek importálását.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 3e2f38b0cab87eab27181ddef79d0c02bd8c9bdb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787163"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>Egyéni rendszerkép feltöltése a Shared Image Gallerybe

A megosztott képkatalógus elérhető a saját egyéni rendszerképeinek importálásához a Azure Lab Services Labs-beli létrehozásához. Az egyetemi informatikai részlegek a következő okok miatt különösen hasznosnak tekintik a lemezképek importálását: 

* Nem kell manuálisan létrehozni képeket a labor sablonjának virtuális gépe használatával.
* A más eszközök, például a SCCM, az Endpoint Manager stb. használatával létrehozott képeket is feltölthet.

Ez a cikk azokat a lépéseket ismerteti, amelyekkel Egyéni rendszerképek helyezhetők el és használhatók Azure Lab Servicesban. 

> [!IMPORTANT]
> Ha fizikai labor-környezetből az az Labs-be helyezi át a lemezképeket, át kell alakítania a appropriatly. Ne egyszerűen használja fel a meglévő rendszerképeket a fizikai laborból. <br/>Részletekért lásd: [Áthelyezés fizikai laborból Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) blogbejegyzésbe.

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>Egyéni rendszerkép létrehozása fizikai labor-környezetből

A következő lépések bemutatják, hogyan importálhat egy olyan egyéni rendszerképet, amely egy fizikai tesztkörnyezet-környezetből indul el. A rendszer Ezután létrehoz egy virtuális merevlemezt a környezetből, és az Azure-ban importálja a megosztott rendszerkép-katalógusba, hogy Azure Lab Serviceson belül is használható legyen.

Számos lehetőség létezik a virtuális merevlemez fizikai tesztkörnyezet környezetből való létrehozásához. A következő lépések bemutatják, hogyan hozhat létre virtuális merevlemezt egy Windows Hyper-V rendszerű virtuális gépről:

1. Kezdje egy olyan Hyper-V virtuális géppel, amely a rendszerképből létrehozott fizikai tesztkörnyezet környezetében található.
    1. A virtuális gépet 1. generációs virtuális géppel kell létrehozni.
    1. A virtuális gépnek rögzített méretű lemeznek kell lennie. Ebben az ablakban is megadhatja a lemez méretét. A lemez mérete nem haladhatja meg a 128 GB-ot.<br/>    
    Azure Lab Services nem támogatja a lemezes mérettel rendelkező rendszerképeket > 128 GB-ot. 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Virtuális merevlemez összekötése":::   
    1. A virtuális gépet a szokásos módon ábrázolhatja.
1. [Kapcsolódjon a virtuális géphez, és készítse elő az Azure-hoz](../virtual-machines/windows/prepare-for-upload-vhd-image.md).
    1. [Windows-konfigurációk beállítása az Azure-hoz](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)
    1. [A virtuális gép kapcsolatának biztosítása érdekében a minimálisan szükséges Windows-szolgáltatásokat ellenőrizze](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)
    1. [A távoli asztal beállításjegyzék-beállításainak frissítése](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)
    1. [A Windows tűzfal szabályainak konfigurálása](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)
    1. Windows-frissítések telepítése
    1. [Telepítse az Azure virtuálisgép-ügynököt és a további konfigurációkat az itt látható módon](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations) 
    
    A fenti lépésekkel egy speciális rendszerkép jön létre. Általánosított rendszerkép létrehozásakor a [Sysprep eszközt](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep)is futtatnia kell. <br/>
        Hozzon létre egy speciális rendszerképet, ha szeretné karbantartani a rendszerképben található szoftverek által igényelt felhasználói könyvtárat (amely tartalmazhat fájlokat, felhasználói fiók adatait stb.).
1. Mivel a **Hyper-V** alapértelmezés szerint létrehoz egy **VHDX** -fájlt, át kell alakítania egy VHD-fájlba.
    1. Navigáljon a **Hyper-V kezelője**  ->  **művelet**  ->  **lemez szerkesztése** elemre.
    1. Itt lehetősége lesz **átalakítani** a lemezt egy VHDX a virtuális merevlemezre
    1. A lemez méretének kibontására tett kísérlet során ügyeljen arra, hogy ne lépje túl a 128 GB-ot.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Művelet kiválasztása":::   
1. Egy felügyelt lemez létrehozásához töltse fel a VHD-t az Azure-ba.
    1. A parancssorból Storage Explorer vagy AzCopy is használhat, a [virtuális merevlemez feltöltése az Azure-ba vagy egy felügyelt lemez másolása másik régióba](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)című témakörben leírtak szerint.        
    Ha a gép alvó állapotba vagy zárakba kerül, előfordulhat, hogy a feltöltési folyamat megszakad, és sikertelen lesz.
    1. Ennek a lépésnek az eredménye, hogy már rendelkezik egy felügyelt lemezzel, amelyet láthat a Azure Portalban. 
        A lemez méretének kiválasztásához használja a Azure Portal "Size\Performance" lapját. Ahogy korábban említettük, a méretnek nem > 128 GB-nak kell lennie.
1. Készítsen pillanatképet a felügyelt lemezről.
    Ezt megteheti a PowerShellből, a Azure Portal használatával vagy a Storage Exploreron belül a [pillanatkép létrehozása a portál vagy a PowerShell használatával](../virtual-machines/windows/snapshot-copy-managed-disk.md)című témakörben leírtak szerint.
1. A megosztott képtárban hozzon létre egy rendszerkép-definíciót és-verziót:
    1. [Rendszerkép-definíció létrehozása](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition)
    1. Azt is meg kell adnia, hogy speciális/általánosított képet hoz-e létre.
1. Hozza létre a labort Azure Lab Services, és válassza ki az egyéni rendszerképet a megosztott rendszerkép-katalógusból.

    Ha az operációs rendszer telepítése után kibontotta a lemezt az eredeti Hyper-V virtuális gépen, akkor a Windows C meghajtóját is ki kell terjeszteni a nem lefoglalt lemezterület használatára. Ehhez jelentkezzen be a sablon virtuális gépre a labor létrehozása után, majd kövesse az [alapszintű Kötet kiterjesztése](/windows-server/storage/disk-management/extend-a-basic-volume)című részhez hasonló lépéseket. Ezt a felhasználói felületen és a PowerShell használatával is elvégezheti.

## <a name="next-steps"></a>Következő lépések

* [Megosztott képgyűjtemény – áttekintés](../virtual-machines/shared-image-galleries.md)
* [A megosztott képgyűjtemény használata](how-to-use-shared-image-gallery.md)