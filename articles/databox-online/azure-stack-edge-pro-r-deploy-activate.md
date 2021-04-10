---
title: Oktatóanyag Azure Stack Edge Pro R-eszköz aktiválásához Azure Portal
description: Az Azure Stack Edge Pro R üzembe helyezésének oktatóanyaga arra utasítja a fizikai eszköz aktiválását.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Pro R device so I can use it to transfer data to Azure.
ms.openlocfilehash: 9ef9cf65b49ab578b08c3df26b98a8382403280a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565282"
---
# <a name="tutorial-activate-azure-stack-edge-pro-r-device"></a>Oktatóanyag: Azure Stack Edge Pro R-eszköz aktiválása

Ez az oktatóanyag azt ismerteti, hogyan aktiválhatja Azure Stack Edge Pro R-eszközét a helyi webes felhasználói felület használatával.

Az aktiválási folyamat körülbelül 5 percet vesz igénybe.  

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * A fizikai eszköz aktiválása

## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge Pro R-eszköz konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* Fizikai eszköz esetén: 
    
    - A fizikai eszközt a [Azure stack Edge Pro R telepítésének](azure-stack-edge-pro-r-deploy-install.md)részletesen telepítette.
    - A hálózati és a számítási hálózati beállításokat a [hálózat konfigurálása, a számítási hálózat és a webproxy](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md) beállítás részletesen konfigurálta.
    - Feltöltötte saját vagy az eszköz tanúsítványait az eszközön, ha módosította az eszköz nevét vagy a DNS-tartományt az **eszköz** lapon. Ha még nem tette meg ezt a lépést, hibaüzenet jelenik meg az eszköz aktiválása közben, és az aktiválás le lesz tiltva. További információkért keresse fel a [tanúsítványok konfigurálása](azure-stack-edge-placeholder.md)című témakört.
    
* Az Azure Stack Edge Pro R-eszköz kezeléséhez létrehozott Azure Stack Edge-szolgáltatásból aktiválhatja az aktiválási kulcsot. További információért látogasson el a [felkészülés a Azure stack Edge Pro R üzembe helyezéséhez](azure-stack-edge-pro-r-deploy-prep.md)című témakörre.


## <a name="activate-the-device"></a>Az eszköz aktiválása

1. Az eszköz helyi webes FELÜLETén válassza az **első lépések** lapot.
2. Az **aktiválási** csempén válassza az **aktiválás** lehetőséget. 

    ![Helyi webes felhasználói felület "Cloud details" 1. oldal](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. Az **aktiválás** ablaktáblán:
    1. Adja meg azt az **aktiválási kulcsot** , amelyet az [Azure stack Edge Pro R aktiválási kulcsának beszerzéséhez](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)kapott.

    1. Engedélyezheti a proaktív naplók gyűjtését, hogy a Microsoft az eszköz állapotának megfelelően Gyűjtse össze a naplókat. Az így összegyűjtött naplók egy Azure Storage-fiókba lesznek feltöltve.
    
    1. Kattintson az **Alkalmaz** gombra.

    ![Helyi webes felhasználói felület "Cloud details" 2. oldal](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)


5. Először az eszköz aktiválva van. Ekkor a rendszer kéri a kulcsfájl letöltését.
    
    ![Helyi webes felhasználói felület "Cloud details" 3. oldal](./media/azure-stack-edge-pro-r-deploy-activate/activate-3.png)
    
    Válassza a **letöltés és folytatás** lehetőséget, majd mentse a *device-serial-no.js* fájlt biztonságos helyen az eszközön kívül. **Ez a kulcsfájl tartalmazza az operációs rendszer lemezének és adatlemezének helyreállítási kulcsait az eszközön**. Ezek a kulcsok szükségesek lehetnek a jövőbeli rendszerek helyreállításának megkönnyítésére.

    Itt látható a *JSON* -fájl tartalma:

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```
        
 
    A következő táblázat a különböző kulcsokat ismerteti:
    
    |Mező  |Leírás  |
    |---------|---------|
    |`Id`    | Ez az eszköz azonosítója.        |
    |`DataVolumeBitLockerExternalKeys`|Ezek az adatlemezek BitLocker-kulcsai, és a helyi adatok helyreállítására szolgálnak az eszközön.|
    |`SystemVolumeBitLockerRecoveryKey`| Ez a rendszerkötet BitLocker-kulcsa. Ez a kulcs segít az eszköz rendszerkonfigurációjának és rendszerállapotának helyreállításában. |
    |`ServiceEncryptionKey`| Ez a kulcs védi az Azure-szolgáltatáson keresztül áramló adatforgalmat. Ez a kulcs biztosítja, hogy az Azure-szolgáltatás sérülése ne veszélyeztesse a tárolt adatokat. |

6. Ugrás az **Áttekintés** oldalra. Az eszköz állapotának **aktiválva** kell lennie.

    ![Helyi webes felhasználói felület – felhő részletei – 4. oldal](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
Az eszköz aktiválása befejeződött. Mostantól megosztásokat is hozzáadhat az eszközhöz.

Ha az aktiválás során problémák merülnek fel, ugorjon az [aktiválási és Azure Key Vault hibák elhárítása](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors)lehetőségre.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * A fizikai eszköz aktiválása

További információ az adatok átviteléről az Azure Stack Edge Pro R-eszközzel:

> [!div class="nextstepaction"]
> [Adatok átvitele Azure Stack Edge Pro R-vel](./azure-stack-edge-gpu-deploy-add-shares.md)