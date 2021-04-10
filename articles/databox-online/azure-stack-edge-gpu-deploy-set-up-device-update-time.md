---
title: Oktatóanyag a Azure Stack Edge Pro-eszköz csatlakoztatásához, konfigurálásához, aktiválásához a Azure Portal GPU-val | Microsoft Docs
description: Az Azure Stack Edge Pro GPU üzembe helyezésére vonatkozó oktatóanyag arra utasítja a fizikai eszköz csatlakoztatását, beállítását és aktiválását.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: ceaa16ee2a54886cde45f37ea90ed617abafffc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546907"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-pro-with-gpu"></a>Oktatóanyag: Azure Stack Edge Pro eszköz beállításainak konfigurálása GPU-val

Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja az eszközhöz kapcsolódó beállításokat a Azure Stack Edge Pro-eszközhöz egy beépített GPU-val. Megadhatja az eszköz nevét, a frissítési kiszolgálót és az időkiszolgálót a helyi webes felületen keresztül.

Az eszközbeállítások körülbelül 5-7 percet is igénybe vehet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Eszközbeállítások megadása
> * Frissítés konfigurálása 
> * Konfigurálás időpontja

## <a name="prerequisites"></a>Előfeltételek

Mielőtt az eszközhöz kapcsolódó beállításokat a GPU-val rendelkező Azure Stack Edge Pro-eszközön konfigurálja, győződjön meg a következőket:

* Fizikai eszköz esetén:

    - A fizikai eszközt a [Azure stack Edge Pro telepítésének](azure-stack-edge-gpu-deploy-install.md)részletesen telepítette.
    - A hálózati és az engedélyezett és konfigurált számítási hálózatot konfigurálta az eszközön, az [oktatóanyag: a hálózat konfigurálása Azure stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)-hoz a GPU-val című részletes útmutatóban.


## <a name="configure-device-settings"></a>Eszközbeállítások megadása

Az eszközhöz kapcsolódó beállítások konfigurálásához kövesse az alábbi lépéseket:

1. Az **eszköz** oldalon hajtsa végre a következő lépéseket:

    1. Adja meg az eszköz rövid nevét. A felhasználóbarát névnek 1 – 13 karakterből kell állnia, és tartalmazhat betűt, számot és kötőjelet.

    2. Adja meg az eszköz **DNS-tartományát** . Ez a tartomány az eszköz fájlkiszolgálóként való beállítására szolgál.

    3. A konfigurált eszközbeállítások ellenőrzéséhez és alkalmazásához válassza az **alkalmaz** lehetőséget.

        ![Helyi webes felhasználói felület "eszköz" 1. oldal](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

        Ha módosította az eszköz nevét és a DNS-tartományt, az eszközön automatikusan létrehozott önaláírt tanúsítványok nem fognak működni. A tanúsítványok konfigurálásakor a következő lehetőségek közül kell választania: 
        
        - Az eszköz tanúsítványainak előállítása és letöltése. 
        - Hozza saját tanúsítványait az eszközhöz, beleértve az aláírási láncot is.
    

        ![Helyi webes felhasználói felület "eszköz" 2. lapja](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-3.png)

    4. Amikor az eszköz neve és a DNS-tartomány megváltozik, létrejön az SMB-végpont.  

    5. A beállítások alkalmazása után válassza a **következő: kiszolgáló frissítése** lehetőséget.

        ![Helyi webes felhasználói felület "eszköz" 3. oldala](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-4.png)

## <a name="configure-update"></a>Frissítés konfigurálása

1. A **frissítés** lapon megadhatja, hogy honnan szeretné letölteni az eszköz frissítéseinek helyét.  

    - A frissítéseket közvetlenül a **Microsoft Update-kiszolgálóról** kérheti le.

        ![Helyi webes felhasználói felület "frissítési kiszolgáló" lapja](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-2.png)

        A frissítéseket a **Windows Server Update Services** (WSUS) szolgáltatásból is telepítheti. Adja meg a WSUS-kiszolgáló elérési útját.
        
        ![Helyi webes felhasználói felület "frissítési kiszolgáló" 2. lapja](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > Ha külön Windows Update kiszolgáló van konfigurálva, és ha a *https* protokollon keresztüli kapcsolódást választja ( *http* helyett), akkor szükség van a frissítési kiszolgálóhoz való kapcsolódáshoz szükséges tanúsítványok aláírására. A tanúsítványok létrehozásáról és feltöltéséről a [tanúsítványok kezelése](azure-stack-edge-gpu-manage-certificates.md)című témakörben olvashat bővebben. 

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

        ![Helyi webes felhasználói felület "Time" lapja](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-2.png)

2. A beállítások alkalmazása után válassza a **Tovább: tanúsítványok** lehetőséget.


## <a name="next-steps"></a>Következő lépések

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Eszközbeállítások megadása
> * Frissítés konfigurálása 
> * Konfigurálás időpontja

Ha meg szeretné tudni, hogyan konfigurálhatja a Azure Stack Edge Pro-eszköz tanúsítványait, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Tanúsítványok konfigurálása](./azure-stack-edge-gpu-deploy-configure-certificates.md)
