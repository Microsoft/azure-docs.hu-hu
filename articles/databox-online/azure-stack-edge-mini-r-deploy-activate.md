---
title: Oktatóanyag a Azure Stack Edge mini R-eszköz titkosításához és aktiválásához Azure Portalban | Microsoft Docs
description: Az Azure Stack Edge mini R üzembe helyezésére vonatkozó oktatóanyag arra utasítja a fizikai eszköz titkosítását és aktiválását.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: 483bf27b68e50c751c8dea1bf9375a95ed2a051f
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558805"
---
# <a name="tutorial-activate-azure-stack-edge-mini-r"></a>Oktatóanyag: Azure Stack Edge mini R aktiválása

Ez az oktatóanyag azt ismerteti, hogyan aktiválhatja Azure Stack Edge mini R-eszközét a helyi webes felhasználói felület használatával.

Az aktiválási folyamat körülbelül 15 percet vesz igénybe.

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * A fizikai eszköz aktiválása

## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge mini R-eszköz konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* Fizikai eszköz esetén: 
    
    - Telepítette a fizikai eszközt a [Azure stack Edge mini R telepítésének](azure-stack-edge-mini-r-deploy-install.md)részletesen.
    - A hálózati és a számítási hálózati beállításokat a [hálózat konfigurálása, a számítási hálózat és a webproxy](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md) beállítás részletesen konfigurálta.
    - A saját tanúsítványait feltöltötte az eszközön, ha az eszköz nevét vagy a DNS-tartományt módosította az **eszköz** lapon. Ezek a lépések részletesen a [tanúsítványok, a VPN és a titkosítatlan titkosítás konfigurálása](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)című témakörben találhatók. Ha még nem tette meg ezt a lépést, az aktiválás le lesz tiltva.
    - Konfigurálta az eszközön a titkosítást. Ha még nem tette meg ezt a lépést, hibaüzenet jelenik meg az eszköz aktiválása közben, és az aktiválás le lesz tiltva. További információ [: a tanúsítványok, a VPN és a titkosítás konfigurálása](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md).
    
* Az Azure Stack Edge mini R-eszköz kezeléséhez létrehozott Azure Stack Edge-szolgáltatásból aktiválhatja az aktiválási kulcsot. További információért látogasson el a [felkészülés a Azure stack Edge mini R üzembe helyezéséhez](azure-stack-edge-mini-r-deploy-prep.md)című témakörre.


## <a name="activate-the-device"></a>Az eszköz aktiválása

1. Az eszköz helyi webes FELÜLETén válassza az **első lépések** lapot.
2. Az **aktiválási** csempén válassza az **aktiválás** lehetőséget. 

    ![Helyi webes felhasználói felület "Cloud details" 1. oldal](./media/azure-stack-edge-mini-r-deploy-activate/activation-1.png)
    
3. Az **aktiválás** ablaktáblán:
    1. Adja meg azt az **aktiválási kulcsot** , amelyet az [Azure stack Edge Pro R aktiválási kulcsának beszerzéséhez](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)kapott.

    1. Engedélyezheti a proaktív naplók gyűjtését, hogy a Microsoft az eszköz állapotának megfelelően Gyűjtse össze a naplókat. Az így összegyűjtött naplók egy Azure Storage-fiókba lesznek feltöltve.
    
    1. Kattintson az **Alkalmaz** gombra.

    ![Helyi webes felhasználói felület "Cloud details" 2. oldal](./media/azure-stack-edge-mini-r-deploy-activate/activation-2.png)


5. Először az eszköz aktiválva van. Ekkor a rendszer kéri a kulcsfájl letöltését.
    
    ![Helyi webes felhasználói felület "Cloud details" 3. oldal](./media/azure-stack-edge-mini-r-deploy-activate/activation-3.png)
    
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
      "SEDEncryptionExternalKey": "xZM/vC7GxjqHZ3VMo7xSs/wH9rRsF/PNM+mOsZ+GaL0=",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```        
 
    A következő táblázat a különböző kulcsokat ismerteti:
    
    |Mező  |Leírás  |
    |---------|---------|
    |`Id`    | Ez az eszköz azonosítója.        |
    |`DataVolumeBitLockerExternalKeys`|Ezek az adatlemezek BitLocker-kulcsai, és a helyi adatok helyreállítására szolgálnak az eszközön.|
    |`SystemVolumeBitLockerRecoveryKey`| Ez a rendszerkötet BitLocker-kulcsa. Ez a kulcs segít az eszköz rendszerkonfigurációjának és rendszerállapotának helyreállításában. |
    |`SEDEncryptionExternalKey`| Ez a felhasználó által megadott vagy rendszer által generált kulcs a beépített titkosítással rendelkező öntitkosított adatmeghajtók védelme érdekében használatos. |
    |`ServiceEncryptionKey`| Ez a kulcs védi az Azure-szolgáltatáson keresztül áramló adatforgalmat. Ez a kulcs biztosítja, hogy az Azure-szolgáltatás sérülése ne veszélyeztesse a tárolt adatokat. |

6. Ugrás az **Áttekintés** oldalra. Az eszköz állapotának **aktiválva** kell lennie.

    ![Helyi webes felhasználói felület – felhő részletei – 4. oldal](./media/azure-stack-edge-mini-r-deploy-activate/activation-4.png)
 
Az eszköz aktiválása befejeződött. Mostantól megosztásokat is hozzáadhat az eszközhöz.

Ha az aktiválás során problémák merülnek fel, ugorjon az [aktiválási és Azure Key Vault hibák elhárítása](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors)lehetőségre.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * A fizikai eszköz aktiválása

Ha szeretné megismerni, hogyan vihetők át adatok az Azure Stack Edge mini R-eszközzel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Adatok átvitele Azure Stack Edge mini R-vel](./azure-stack-edge-gpu-deploy-add-shares.md)