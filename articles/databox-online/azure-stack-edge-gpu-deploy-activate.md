---
title: Oktatóanyag Azure Stack Edge Pro-eszköz aktiválásához Azure Portal GPU-val | Microsoft Docs
description: Az Azure Stack Edge Pro GPU üzembe helyezésére vonatkozó oktatóanyag arra utasítja a fizikai eszköz aktiválását.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: alkohli
ms.openlocfilehash: 8e88fb2f6f2fc9ad50911bfda2245cd95ae33236
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058748"
---
# <a name="tutorial-activate-azure-stack-edge-pro-with-gpu"></a>Oktatóanyag: Azure Stack Edge Pro aktiválása GPU-val

Ez az oktatóanyag azt ismerteti, hogyan aktiválhatja Azure Stack Edge Pro-eszközét egy beépített GPU-val a helyi webes felhasználói felület használatával.

Az aktiválási folyamat körülbelül 5 percet vesz igénybe.

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * A fizikai eszköz aktiválása

## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge Pro-eszköz GPU-val való konfigurálása előtt győződjön meg az alábbiakról:

* Fizikai eszköz esetén: 
    
    - A fizikai eszközt a [Azure stack Edge Pro telepítésének](azure-stack-edge-gpu-deploy-install.md)részletesen telepítette.
    - A hálózati és a számítási hálózati beállításokat a [hálózat konfigurálása, a számítási hálózat és a webproxy](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md) beállítás részletesen konfigurálta.
    - Feltöltötte saját vagy az eszköz tanúsítványait az eszközön, ha módosította az eszköz nevét vagy a DNS-tartományt az **eszköz** lapon. Ha még nem tette meg ezt a lépést, hibaüzenet jelenik meg az eszköz aktiválása közben, és az aktiválás le lesz tiltva. További információkért keresse fel a [tanúsítványok konfigurálása](azure-stack-edge-gpu-deploy-configure-certificates.md)című témakört.
    
* Az Azure Stack Edge Pro-eszköz kezeléséhez létrehozott Azure Stack Edge-szolgáltatásból aktiválhatja az aktiválási kulcsot. További információért látogasson el a [felkészülés a Azure stack Edge Pro üzembe helyezéséhez](azure-stack-edge-gpu-deploy-prep.md)című témakörre.


## <a name="activate-the-device"></a>Az eszköz aktiválása

1. Az eszköz helyi webes FELÜLETén válassza az **első lépések** lapot.
2. Az **aktiválási** csempén válassza az **aktiválás** lehetőséget. 

    ![Helyi webes felhasználói felület "felhő részletei" oldal](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. **Az aktiválás ablaktáblán** adja meg a [Azure stack Edge Pro aktiválási kulcsának beszerzése](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)című rész **aktiválási kulcsát** .

4. Kattintson az **Alkalmaz** gombra.

    ![Helyi webes felhasználói felület "Cloud details" 2. oldal](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Először az eszköz aktiválva van. Ekkor a rendszer kéri a kulcsfájl letöltését.
    
    ![Helyi webes felhasználói felület "Cloud details" 3. oldal](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
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

Ha szeretné megismerni, hogyan vihetők át adatok az Azure Stack Edge Pro-eszközével, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Adatok átvitele Azure Stack Edge Pro-val](./azure-stack-edge-gpu-deploy-add-shares.md)