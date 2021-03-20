---
title: VHD-fájl feltöltése Azure DevTest Labsre Storage Explorer használatával
description: VHD-fájl feltöltése a labor Storage-fiókjába Microsoft Azure Storage Explorer használatával
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d4e421932ebba5d4f389000c12bcf44ac2f37599
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91282527"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>VHD-fájl feltöltése a labor Storage-fiókjába Microsoft Azure Storage Explorer használatával

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs a VHD-fájlok használhatók a virtuális gépek kiépítéséhez használt egyéni rendszerképek létrehozására. Ez a cikk bemutatja, hogyan tölthet fel egy VHD-fájlt a labor Storage-fiókjába a [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) használatával. A VHD-fájl feltöltése után a [következő lépések szakasz](#next-steps) azokat a cikkeket sorolja fel, amelyek bemutatják, hogyan hozhat létre egyéni rendszerképet a feltöltött VHD-fájlból. További információ az Azure-beli lemezekről és virtuális merevlemezekről: [Bevezetés a Managed Disks](../virtual-machines/managed-disks-overview.md) szolgáltatásba

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

A következő lépések végigvezetik a VHD-fájlok DevTest Labs-be történő feltöltésének lépésein [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)használatával.

1. [Töltse le és telepítse a Microsoft Azure Storage Explorer legújabb verzióját](https://www.storageexplorer.com).

1. Szerezze be a labor Storage-fiókjának nevét a Azure Portal használatával:

    1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Válassza a **minden szolgáltatás** lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
    
    1. A laborok listájából válassza ki a kívánt labort.  
    
    1. A labor paneljén válassza a **Konfigurálás** lehetőséget. 
    
    1. A labor- **konfiguráció** panelen válassza az **Egyéni lemezképek (VHD-k)** lehetőséget.
    
    1. Az **Egyéni lemezképek** panelen válassza a **+ Hozzáadás** lehetőséget. 
    
    1. Az **Egyéni rendszerkép** panelen válassza a **VHD** elemet.
    
    1. A **VHD** panelen válassza **a virtuális merevlemez feltöltése a PowerShell használatával** lehetőséget.
    
        ![VHD feltöltése a PowerShell használatával][0]
    
    1. A **rendszerkép feltöltése a PowerShell panel használatával** megjeleníti az **Add-AzureVhd** parancsmag hívását. Az első paraméter (*cél*) a tesztkörnyezet tárolási fiókjának nevét tartalmazza a következő formátumban:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Jegyezze fel a Storage-fiók nevét, mert a későbbi lépésekben használatban van.
    
1. Kapcsolódás Azure-előfizetési fiókhoz Storage Explorer használatával.

    > [!TIP] 
    > 
    > A Storage Explorer több kapcsolatbeállításokat is támogat. Ez a szakasz az Azure-előfizetéshez társított Storage-fiókhoz való csatlakozást mutatja be. Ha meg szeretné tekinteni a Storage Explorer által támogatott egyéb kapcsolódási lehetőségeket, tekintse meg a következő cikket: [Bevezetés a Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)használatába.
 
    1. Nyissa meg a Storage Explorert.
    
    1. A Storage Explorer területen válassza az **Azure-fiók beállításai** lehetőséget. 
    
        ![Azure-fiók beállításai][1]
    
    1. A bal oldali ablaktábla megjeleníti azokat a Microsoft-fiókokat, amelyeken bejelentkezett. Egy másik fiókhoz csatlakozáshoz válassza a **Fiók hozzáadása** lehetőséget, és a párbeszédpanelek útmutatásait követve jelentkezzen be egy olyan Microsoft-fiókkal, amely legalább egy aktív Azure-előfizetéssel társítva van.
    
        ![Fiók hozzáadása][2]
    
    1. Amint sikeresen bejelentkezett egy Microsoft-fiókkal, a bal oldali ablaktáblán megjelenik a fiókhoz társított összes Azure-előfizetés. Válassza ki azt az Azure-előfizetést amellyel dolgozni szeretne, majd válassza az **Alkalmaz** lehetőséget. (Az **összes előfizetés** kiválasztásával kiválaszthatja az összes felsorolt Azure-előfizetést, vagy sem.)
    
        ![Azure-előfizetések kiválasztása][3]
    
    1. A bal oldali ablaktábla megjeleníti a kiválasztott Azure-előfizetésekhez társított összes tárfiókot.
    
        ![Kiválasztott Azure-előfizetések][4]

1. Keresse meg a labor Storage-fiókját:

    1. A Storage Explorer bal oldali ablaktáblán keresse meg és bontsa ki a labort birtokló Azure-előfizetés csomópontját.
    
    1. Az előfizetés csomópontja alatt bontsa ki a **Storage-fiókok** elemet.

    1. Bontsa ki a labor Storage-fiók csomópontját a **blob-tárolók**, a **fájlmegosztás**, a **várólisták** és a **táblák** csomópontjainak megjelenítéséhez.
    
    1. Bontsa ki a **blob-tárolók** csomópontot.
    
    1. Válassza a blob-tároló feltöltése lehetőséget a tartalom megjelenítéséhez a jobb oldali ablaktáblán.
        
        ![Könyvtár feltöltése][5]

1. Töltse fel a VHD-fájlt a Storage Explorer használatával:

    1. A Storage Explorer jobb oldali ablaktáblában meg kell jelennie a Blobok listájának a labor Storage-fiókjának **feltöltési** blob-tárolójában. A blob-szerkesztő eszköztárán válassza a **feltöltés** lehetőséget. 
        
        ![Képernyőfelvétel: a Storage Explorer jobb oldali ablaktábla, ahol a "feltöltés" gomb van kiválasztva.][6]
    
    1. A **feltöltés** legördülő menüben válassza a **fájlok feltöltése..**. lehetőséget.
    
    1. A **fájlok feltöltése** párbeszédpanelen válassza a három pontot.
        
        ![A "fájlok feltöltése" párbeszédpanelt megjelenítő képernyőkép a kiválasztott három ponttal.][8]  

    1. A **feltölteni kívánt fájlok kiválasztása** párbeszédpanelen keresse meg a kívánt VHD-fájlt, jelölje ki, majd kattintson a **Megnyitás** gombra.
    
    1. Amikor visszatér a **fájlok feltöltése** párbeszédpanelre, módosítsa a **blob típusát** az **oldal blobra**.
    
    1. Válassza a **Feltöltés** lehetőséget.

        ![Fájl kiválasztása][9]  
    
    1. A Storage Explorer **tevékenység naplója** ablaktáblán a letöltési állapot látható (a feltöltés megszakítására szolgáló hivatkozásokkal együtt). A VHD-fájlok feltöltésének folyamata a VHD-fájl méretétől és a kapcsolatok sebességétől függően hosszadalmas lehet. 

        ![Feltöltési fájl állapota][10]  

## <a name="next-steps"></a>Következő lépések

- [Egyéni rendszerkép létrehozása Azure DevTest Labs egy VHD-fájlból a Azure Portal használatával](devtest-lab-create-template.md)
- [Egyéni rendszerkép létrehozása Azure DevTest Labs VHD-fájlból a PowerShell használatával](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
