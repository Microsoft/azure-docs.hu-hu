---
title: Windows rendszerű virtuális gépekkel kapcsolatos problémák elhárítása az Azure-ban | Microsoft Docs
description: A Windows rendszerű virtuális gépekkel kapcsolatos problémák hibaelhárítása a Azure Resource Manager telepítési modellben.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 98a16e0a60ddf149e8f0e1a092051f3e98ea8225
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627062"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Windows rendszerű virtuális gépek Azure-beli üzembe helyezése során előforduló problémák elhárítása

A virtuális gépek üzembe helyezésével kapcsolatos problémák elhárításához az Azure-ban tekintse át a leggyakoribb hibák és megoldások [legfontosabb problémáit](#top-issues) .

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget.

## <a name="top-issues"></a>Leggyakoribb problémák
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>A fürt nem támogatja a kért VM-méretet
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Próbálja megismételni a kérést kisebb virtuálisgép-méret használatával.
- Ha a kért virtuális gép mérete nem módosítható:
    - Állítsa le az összes virtuális gépet a rendelkezésre állási csoportból. Kattintson **Az erőforráscsoportok >** az erőforráscsoport > **erőforrások** > a rendelkezésre állási csoport > **Virtual Machines** a virtuális gép > **leállítása** elemre.
    - Az összes virtuális gép leállítása után a kívánt méretben hozza létre a virtuális gépet.
    - Először indítsa el az új virtuális gépet, majd válassza ki a leállított virtuális gépeket, és kattintson az Indítás gombra.


## <a name="the-cluster-does-not-have-free-resources"></a>A fürt nem rendelkezik szabad erőforrásokkal
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Később próbálja megismételni a kérést.
- Ha az új virtuális gép egy másik rendelkezésre állási csoport része lehet
    - Hozzon létre egy virtuális gépet egy másik rendelkezésre állási készletben (ugyanabban a régióban).
    - Adja hozzá az új virtuális gépet ugyanahhoz a virtuális hálózathoz.

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Hogyan használhatok és helyezhetek üzembe Windows-rendszerképeket az Azure-ban?

Az Azure-ban a Windows 7, Windows 8 vagy Windows 10 operációs rendszert használhatja fejlesztési és tesztelési forgatókönyvekhez, ha rendelkezik megfelelő Visual Studio-előfizetéssel (korábban MSDN). Ez a [cikk](../windows/client-images.md) a Windows-ügyfél Azure-ban való futtatásának és az Azure Gallery-lemezképek használatának támogathatósági követelményeit ismerteti.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Hogyan helyezhetek üzembe egy virtuális gépet a Hybrid use Benefit (HUB) használatával?

A Windows rendszerű virtuális gépek több különböző módon telepíthetők a Azure Hybrid Use Benefit használatával.

Nagyvállalati Szerződés-előfizetés esetén:

• A virtuális gépeket a Azure Hybrid Use Benefit-vel előre konfigurált, adott Piactéri rendszerképekből telepítse.

Nagyvállalati szerződés:

• Töltsön fel egy egyéni virtuális gépet, és telepítsen egy Resource Manager-sablon vagy Azure PowerShell használatával.

További információkat találhat az alábbi forrásokban:

 - [Azure Hybrid Use Benefit áttekintése](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Letölthető GYIK](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Azure Hybrid use Benefit a Windows Server és a Windows ügyfél számára](../windows/hybrid-use-benefit-licensing.md).

 - [Hogyan használhatom a Hybrid use benefitet az Azure-ban](/archive/blogs/azureedu/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>A havi kreditek Hogyan aktiválása a Visual Studio Enterprise (BizSpark) szolgáltatáshoz

A havi kreditek aktiválásához tekintse meg ezt a [cikket](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Hogyan adhatok hozzá Enterprise Dev/Testt a Nagyvállalati Szerződés (EA) szolgáltatáshoz, hogy hozzáférjenek az ablakos ügyfelek rendszerképeihez?

Az előfizetések Enterprise Dev/Test ajánlat alapján történő létrehozásának lehetősége azon fiók tulajdonosai számára korlátozódik, akik engedélyt kaptak erre a vállalati rendszergazda. A fiók tulajdonosa előfizetéseket hoz létre az Azure-fiók portálon keresztül, majd az aktív Visual Studio-előfizetőket közös rendszergazdaként kell hozzáadnia. A fejlesztéshez és teszteléshez szükséges erőforrások kezeléséhez és használatához. Bővebb információ: [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Hiányoznak az illesztőprogramok a Windows N sorozatú virtuális géphez

A Windows-alapú virtuális gépek illesztőprogramjainak telepítéséhez szükséges utasítások [itt](../sizes-gpu.md#supported-operating-systems-and-drivers)találhatók.

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nem találom az N sorozatú virtuális gépen található GPU-példányt

Az Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához az üzembe helyezést követően minden virtuális gépen telepítenie kell a grafikus illesztőprogramokat. Az illesztőprogram beállítási információi [itt](../sizes-gpu.md#supported-operating-systems-and-drivers)érhetők el.

## <a name="are-n-series-vms-available-in-my-region"></a>Az N sorozatú virtuális gépek elérhetők a saját régióban?

A rendelkezésre álló termékek elérhetőségét a [régiók táblázata alapján](https://azure.microsoft.com/regions/services), a díjszabást pedig [itt](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)tekintheti meg.

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Milyen ügyféloldali rendszerképeket használhatok és telepíthetek az Azure-ban, és Hogyan szerezhetem be őket?

Az Azure-ban a Windows 7, a Windows 8 vagy a Windows 10 operációs rendszert használhatja fejlesztési és tesztelési forgatókönyvekhez, ha rendelkezik a megfelelő Visual Studio (korábbi MSDN) előfizetéssel. 

- A Windows 10-es lemezképek az Azure Galleryben érhetők el a [jogosult fejlesztési és tesztelési ajánlatokon](../windows/client-images.md)belül. 
- A Visual Studio-előfizetők bármilyen típusú ajánlaton belül a 64 bites Windows 7, Windows 8 vagy Windows 10 rendszerű rendszerképek [megfelelő előkészítését és létrehozását](../windows/prepare-for-upload-vhd-image.md) is lehetővé teszi, majd [feltöltheti őket az Azure](../windows/upload-generalized-managed.md)-ba. A használat az aktív Visual Studio-előfizetők által a fejlesztéshez és teszteléshez is korlátozott.

Ez a [cikk](../windows/client-images.md) a Windows-ügyfél Azure-ban való futtatásának és az Azure Gallery-lemezképek használatának támogathatósági követelményeit ismerteti.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nem látom a virtuális gép átméretezése során használni kívánt virtuálisgép-méretet.

Ha egy virtuális gép fut, a rendszer egy fizikai kiszolgálóra telepíti. Az Azure-régiókban található fizikai kiszolgálók általános fizikai hardverek csoportjaiba vannak csoportosítva. A virtuális gép különböző hardveres fürtökre való áthelyezését igénylő virtuális gépek átméretezése eltérő lehet attól függően, hogy melyik üzemi modellt használták a virtuális gép üzembe helyezéséhez.

- A klasszikus üzemi modellben üzembe helyezett virtuális gépeket el kell távolítani és újra kell telepíteni, hogy a virtuális gépeket egy másik méretű családban lévő méretre módosítsa.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- A Resource Manager-alapú üzemi modellben üzembe helyezett virtuális gépeket a rendelkezésre állási csoportba tartozó virtuális gépek méretének módosítása előtt le kell állítania a rendelkezésre állási csoport összes virtuális gépén.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>A felsorolt virtuálisgép-méret nem támogatott a rendelkezésre állási csoport telepítésekor.

Válassza ki a rendelkezésre állási csoport fürtjét támogató méretet. Azt javasoljuk, hogy egy rendelkezésre állási csoport létrehozásakor válassza ki a szükséges legnagyobb méretű virtuálisgép-méretet, és hogy az első üzembe helyezése legyen a rendelkezésre állási csoportnak.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Felvehetek meglévő klasszikus virtuális gépet egy rendelkezésre állási csoportba?

Igen. Meglévő klasszikus virtuális gépet hozzáadhat egy új vagy meglévő rendelkezésre állási csoporthoz. További információ: [meglévő virtuális gép hozzáadása egy rendelkezésre állási csoporthoz](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Következő lépések
Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is.

Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget.
