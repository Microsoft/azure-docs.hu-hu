---
title: Az alkalmazás tesztelése az Azure-ban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre fájlmegosztást a laborban, és hogyan csatlakoztathatja a helyi gépre és egy virtuális gépre a laborban, majd telepíthet asztali/webalkalmazásokat a fájlmegosztást, és tesztelheti azokat.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: spelluru
ms.openlocfilehash: f8c57b9e1fabbd04a7d9c92484b0f52f074c2577
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "65872405"
---
# <a name="test-your-app-in-azure"></a>Alkalmazás tesztelése az Azure-ban 
Ez a cikk az alkalmazás Azure-beli DevTest Labs használatával történő tesztelésének lépéseit ismerteti. Először állítson be egy fájlmegosztást egy laborban, és csatlakoztassa meghajtóként a helyi fejlesztési gépen, illetve egy virtuális gépet egy laboron belül. Ezt követően a Visual Studio 2019 használatával helyezheti üzembe az alkalmazást a fájlmegosztást, így az alkalmazást a laborban futtathatja a virtuális gépen.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek 
1. [Hozzon létre egy Azure-előfizetést](https://azure.microsoft.com/free/) , ha még nem rendelkezik ilyennel, és jelentkezzen be [Azure Portalba](https://portal.azure.com).
2. A [cikk](devtest-lab-create-lab.md) utasításait követve hozzon létre egy labort Azure DevTest Labs használatával. Rögzítse a labort az irányítópulton, hogy legközelebb megkeresse a következő bejelentkezés alkalmával. A Azure DevTest Labs lehetővé teszi, hogy gyorsan hozzon létre erőforrásokat az Azure-ban a hulladék minimalizálása és a költségek szabályozása érdekében. További információ a DevTest Labs szolgáltatásról: [Áttekintés](devtest-lab-overview.md). 
3. Hozzon létre egy Azure Storage-fiókot a labor erőforráscsoporthoz a [Storage-fiók létrehozása](../storage/common/storage-create-storage-account.md) című cikk utasításait követve. A **Storage-fiók létrehozása** lapon válassza a **meglévő használata** az **erőforráscsoport**számára lehetőséget, és válassza ki a **labor erőforrás-csoportot**. 
4. Hozzon létre egy fájlmegosztást az Azure Storage-ban a [fájlmegosztás létrehozása Azure Files](../storage/files/storage-how-to-create-file-share.md) cikkben található utasításokat követve. 

## <a name="mount-the-file-share-on-your-local-machine"></a>A fájlmegosztás csatlakoztatása a helyi gépen
1. A helyi gépen használja a parancsfájlt az Azure-fájlmegosztás [hitelesítő adatainak](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) megtartásához a Windows szakaszban az [Azure-fájlmegosztás használata Windows](../storage/files/storage-how-to-use-files-windows.md) -cikkben. 
2. Ezután a parancs `net use` használatával csatlakoztassa a fájlmegosztást a gépen. A minta parancs a következő: a parancs futtatása előtt adja meg az Azure Storage nevét és a fájlmegosztás nevét. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Virtuális gép létrehozása a tesztkörnyezetben
1. A **fájlmegosztás** lapon válassza ki az **erőforráscsoportot** a felső navigációs menüben. Megjelenik az **erőforráscsoport** oldal. 
    
    ![Válassza ki az erőforráscsoportot a navigációs menüből](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Az **erőforráscsoport** lapon válassza ki a DevTest Labs szolgáltatásban létrehozott **labort** .

    ![A tesztkörnyezet kiválasztása](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. A labor **DevTest labor** lapján válassza a **+ Hozzáadás** lehetőséget az eszköztáron. 

    ![A labor hozzáadása gomb](media/test-app-in-azure/add-button-in-lab.png)
4. A **kiinduló lap kiválasztása** lapon keressen rá a **smalldisk**kifejezésre, és válassza a **[smalldisk] Windows Server 2016 adatközpont**elemet. 

    ![Kis lemez Windows Server kiválasztása](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. A **virtuális gép** lapon adja meg a **virtuális gép nevét**, **a felhasználónevet**és a **jelszót**, majd válassza a **Létrehozás**lehetőséget.    
    
    ![Virtuális gép létrehozása lap](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>A fájlmegosztás csatlakoztatása a virtuális gépen
1. A virtuális gép sikeres létrehozása után válassza ki a **virtuális gépet** a listából.    

    ![Válassza ki a tesztkörnyezet virtuális gépet](media/test-app-in-azure/select-lab-vm.png)
2. A virtuális géphez való kapcsolódáshoz válassza az eszköztár **Kapcsolódás** elemét. 
3. [Telepítse a Azure PowerShell](/powershell/azure/install-az-ps).
4. Kövesse a fájlmegosztás csatlakoztatása szakasz utasításait. 

## <a name="publish-your-app-from-visual-studio"></a>Az alkalmazás közzététele a Visual studióból
Ebben a szakaszban az alkalmazást a Visual studióból egy Felhőbeli teszt virtuális gépre teszi közzé.

1. Hozzon létre egy asztali/webalkalmazást a Visual Studio 2019 használatával.
2. Hozza létre az alkalmazást a Build lehetőséggel.
3. Az alkalmazás közzétételéhez kattintson a jobb gombbal a projektre a **megoldáskezelő**, és válassza a **Közzététel**lehetőséget. 
4. A **Közzétételi varázslóban**adja meg a fájlmegosztás számára leképezett **meghajtót** .

    **Asztali alkalmazás:**

    ![Asztali alkalmazás](media/test-app-in-azure/desktop-app.png)

    **Webalkalmazás:**

    ![Webalkalmazás](media/test-app-in-azure/web-app.png)

1. A **tovább** gombra kattintva fejezze be a közzétételi munkafolyamatot, és válassza a **Befejezés**gombot. A varázsló lépéseinek befejezése után a Visual Studio létrehozza az alkalmazást, és közzéteszi a fájlmegosztást. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Az alkalmazás tesztelése a teszt virtuális gépen a tesztkörnyezetben

1. Navigáljon a virtuális gép virtuálisgép-lapjára a laborban. 
2. Válassza az **Indítás** lehetőséget az eszköztáron a virtuális gép elindításához, ha leállított állapotban van. A virtuális gép automatikus indítási és automatikus leállítási házirendjeit úgy állíthatja be, hogy elkerülje az egyes időpontok indítását és leállítását. 
3. Kattintson a **Csatlakozás** gombra.

    ![Virtuális gép lapja](media/test-app-in-azure/virtual-machine-page.png)
4. A virtuális gépen nyissa meg a **fájlkezelőt**, és válassza ki **ezt a számítógépet** a fájlmegosztás megkereséséhez.

    ![Megosztás megkeresése a virtuális gépen](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Bármilyen okból, ha nem találja a fájlmegosztást a virtuális gépen vagy a helyi gépen, akkor a `net use` parancs futtatásával újracsatlakoztathatja. A `net use` parancs a **fájlmegosztás** **összekapcsoló** varázslójának a Azure Portalban található.
1. Nyissa meg a fájlmegosztást, és ellenőrizze, hogy megjelenik-e a Visual studióból üzembe helyezett alkalmazás. 

    ![Megosztás megnyitása a virtuális gépen](media/test-app-in-azure/open-file-share.png)

    Mostantól elérheti és tesztelheti az alkalmazást az Azure-ban létrehozott tesztelési virtuális gépen.

## <a name="next-steps"></a>További lépések
A következő cikkekből megtudhatja, hogyan használhatók a virtuális gépek a laborban. 

- [Virtuális gép hozzáadása laborhoz](devtest-lab-add-vm.md)
- [Tesztkörnyezet virtuális gép újraindítása](devtest-lab-restart-vm.md)
- [Laboratóriumi virtuális gép átméretezése](devtest-lab-resize-vm.md)
