---
title: Oktatóanyag a Azure Stack Edge Pro R-eszköz idő-, eszköz-és frissítési beállításainak beállításához Azure Portal
description: Az Azure Stack Edge Pro R üzembe helyezésére vonatkozó oktatóanyag arra utasítja a számítógépet, hogy beállítsa az eszköz, a frissítés és az idő beállításait a fizikai eszközhöz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
ms.openlocfilehash: 94abfd704d000b907158b2559a268718046b6661
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059615"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-pro-r"></a>Oktatóanyag: Azure Stack Edge Pro R-eszköz beállításainak konfigurálása

Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja az eszközhöz kapcsolódó beállításokat a Azure Stack Edge Pro R-eszközhöz. Megadhatja az eszköz nevét, a frissítési kiszolgálót és az időkiszolgálót a helyi webes felületen keresztül.

Az eszközbeállítások körülbelül 5-7 percet is igénybe vehet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Eszközbeállítások megadása
> * Frissítés konfigurálása 
> * Konfigurálás időpontja

## <a name="prerequisites"></a>Előfeltételek

Mielőtt az eszközhöz kapcsolódó beállításokat konfigurálja a Azure Stack Edge Pro R-eszközön, győződjön meg a következőket:

* Fizikai eszköz esetén:

    - A fizikai eszközt a [Azure stack Edge Pro R telepítésének](azure-stack-edge-pro-r-deploy-install.md)részletesen telepítette.
    - A hálózati és az engedélyezett és konfigurált számítási hálózatot konfigurálta az eszközön az [oktatóanyag: a hálózat konfigurálása Azure stack Edge Pro R-hez című útmutatóban ](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)részletesen.


## <a name="configure-device-settings"></a>Eszközbeállítások megadása

Az eszközhöz kapcsolódó beállítások konfigurálásához kövesse az alábbi lépéseket:

1. Az **eszköz** oldalon hajtsa végre a következő lépéseket:

    1. Adja meg az eszköz rövid nevét. A felhasználóbarát névnek 1 – 13 karakterből kell állnia, és tartalmazhat betűt, számot és kötőjelet.

    2. Adja meg az eszköz **DNS-tartományát** . Ez a tartomány az eszköz fájlkiszolgálóként való beállítására szolgál.

    3. A konfigurált eszközbeállítások ellenőrzéséhez és alkalmazásához válassza az **alkalmaz** lehetőséget.

        ![Helyi webes felhasználói felület "eszköz" 1. oldal](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/device-2.png)

        Ha módosította az eszköz nevét és a DNS-tartományt, az eszközön automatikusan létrehozott önaláírt tanúsítványok nem fognak működni. A tanúsítványok konfigurálásakor a következő lehetőségek közül kell választania: 
        
        - Az eszköz tanúsítványainak előállítása és letöltése. 
        - Hozza saját tanúsítványait az eszközhöz, beleértve az aláírási láncot is.
    

        ![Helyi webes felhasználói felület "eszköz" 2. lapja](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/device-3.png)

    4. Amikor az eszköz neve és a DNS-tartomány megváltozik, létrejön az SMB-végpont.  

    5. A beállítások alkalmazása után válassza a **következő: kiszolgáló frissítése** lehetőséget.

        ![Helyi webes felhasználói felület "eszköz" 3. oldala](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/device-4.png)

## <a name="configure-update"></a>Frissítés konfigurálása

1. A **frissítés** lapon megadhatja, hogy honnan szeretné letölteni az eszköz frissítéseinek helyét.  

    - A frissítéseket közvetlenül a **Microsoft Update-kiszolgálóról** kérheti le.

        ![Helyi webes felhasználói felület "frissítési kiszolgáló" lapja](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/update-2.png)

        A frissítéseket a **Windows Server Update Services** (WSUS) szolgáltatásból is telepítheti. Adja meg a WSUS-kiszolgáló elérési útját.
        
        ![Helyi webes felhasználói felület "frissítési kiszolgáló" 2. lapja](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > Ha külön Windows Update kiszolgáló van konfigurálva, és ha a *https* protokollon keresztüli kapcsolódást választja ( *http* helyett), akkor szükség van a frissítési kiszolgálóhoz való kapcsolódáshoz szükséges tanúsítványok aláírására. A tanúsítványok létrehozásáról és feltöltéséről a [tanúsítványok kezelése](azure-stack-edge-gpu-manage-certificates.md)című témakörben olvashat bővebben.         
        > Ha leválasztott módban szeretne dolgozni, például az Azure Stack Edge-eszközök többplatformos adatközpontba való csatlakoztatására, engedélyezze a WSUS szolgáltatást. Az aktiválás során az eszköz frissítéseket keres, és ha a kiszolgáló nincs beállítva, az aktiválás sikertelen lesz. 


2. Kattintson az **Alkalmaz** gombra.
3. A frissítési kiszolgáló konfigurálása után válassza a **következő: idő** lehetőséget.
    

## <a name="configure-time"></a>Konfigurálás időpontja

Az alábbi lépéseket követve konfigurálhatja az eszközön az időbeállításokat. 

> [!IMPORTANT]
> Bár az időbeállítások nem kötelezőek, javasoljuk, hogy egy elsődleges NTP-t és egy másodlagos NTP-kiszolgálót állítson be az eszköz helyi hálózatán. Ha a helyi kiszolgáló nem érhető el, a nyilvános NTP-kiszolgálókat is konfigurálhatja.

Az NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt ahhoz, hogy hitelesíteni lehessen a felhőalapú szolgáltatóktól.

1. Az **idő** lapon kiválaszthatja az eszköz időzónáját, valamint az elsődleges és másodlagos NTP-kiszolgálókat.  
    
    1. Az **időzóna** legördülő listában válassza ki azt az időzónát, amely megfelel az eszköz üzembe helyezésének földrajzi helyének.
        Az eszköz alapértelmezett időzónája a PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.

    2. Az **Elsődleges NTP-kiszolgáló** mezőbe írja be az eszköz elsődleges kiszolgálóját, vagy fogadja el a Time.Windows.com alapértelmezett értékét.  
        Győződjön meg arról, hogy a hálózat lehetővé teszi, hogy az NTP-forgalom áthaladjon az adatközpontból az internetre.

    3. Szükség esetén a **másodlagos NTP-kiszolgáló** mezőbe írja be az eszköz másodlagos kiszolgálóját.

    4. A beállított idő beállításainak ellenőrzéséhez és alkalmazásához válassza az **alkalmaz** lehetőséget.

        ![Helyi webes felhasználói felület "Time" lapja](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/time-2.png)

2. A beállítások alkalmazása után válassza a **Tovább: tanúsítványok** lehetőséget.


## <a name="next-steps"></a>Következő lépések

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Eszközbeállítások megadása
> * Frissítés konfigurálása 
> * Konfigurálás időpontja

Az Azure Stack Edge Pro R-eszköz tanúsítványának konfigurálásáról a következő témakörben tájékozódhat:

> [!div class="nextstepaction"]
> [Tanúsítványok konfigurálása](./azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)
