---
title: Azure Stack Edge Pro számítási hálózatának kezelése modulok eléréséhez | Microsoft Docs
description: Ismerteti, hogyan lehet kiterjeszteni a számítási hálózatot a Azure Stack Edge Pro-ban a modulok külső IP-n keresztüli eléréséhez.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 19c92deb58ac51aa882e7123b9a90aa3eae627d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90894117"
---
# <a name="enable-compute-network-on-your-azure-stack-edge-pro"></a>Számítási hálózat engedélyezése a Azure Stack Edge Pro-ban

Ez a cikk azt ismerteti, hogyan érheti el a Azure Stack Edge Pro-ban futó modulok az eszközön engedélyezett számítási hálózatot.

A hálózat konfigurálásához hajtsa végre a következő lépéseket:

- Hálózati adapter engedélyezése a Azure Stack Edge Pro-eszközön a számítási feladatokhoz
- Modul hozzáadása a számítási hálózat eléréséhez az Azure Stack Edge Pro-ban
- Ellenőrizze, hogy a modul hozzáférhet-e az engedélyezett hálózati adapterhez

Ebben az oktatóanyagban egy webkiszolgáló-alkalmazás modult fog használni a forgatókönyv bemutatásához.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következőkre lesz szüksége:

- Egy Azure Stack Edge Pro-eszköz, amelyen az eszköz telepítése befejeződött.
- Elvégezte a **számítási lépés konfigurálását** az [oktatóanyagban: az adatok átalakítása Azure stack Edge Pro-val](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute) az eszközön. Az eszköznek rendelkeznie kell egy társított IoT Hub erőforrással, egy IoT eszközzel és egy IoT Edge eszközzel.

## <a name="enable-network-interface-for-compute"></a>Hálózati adapter engedélyezése a számítási feladatokhoz

Ha külső hálózaton keresztül szeretné elérni az eszközön futó modulokat, IP-címet kell rendelnie az eszköz egyik hálózati adapteréhez. Ezeket a számítási beállításokat a helyi webes felhasználói felületről kezelheti.

A számítási beállítások konfigurálásához hajtsa végre a következő lépéseket a helyi webes felületen.

1. A helyi webes KEZELŐFELÜLETen lépjen a **konfiguráció > számítási beállítások** elemre.  

2. **Engedélyezze** az eszközön futtatni kívánt számítási modulhoz való kapcsolódáshoz használni kívánt hálózati adaptert.

    - Ha statikus IP-címeket használ, adjon meg egy IP-címet a hálózati adapterhez.
    - Ha DHCP-t használ, a rendszer automatikusan hozzárendeli az IP-címeket. Ez a példa DHCP-t használ.

    ![Számítási beállítások engedélyezése 1](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. A beállítások alkalmazásához kattintson az **alkalmaz** gombra. Jegyezze fel a hálózati adapterhez hozzárendelt IP-címet, ha DHCP-t használ.

    ![Számítási beállítások engedélyezése](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Webkiszolgáló-alkalmazás hozzáadása modul

A következő lépésekkel adhat hozzá egy webkiszolgáló-modult az Azure Stack Edge Pro-eszközhöz.

1. Lépjen az Azure Stack Edge Pro-eszközhöz társított IoT Hub erőforráshoz, majd válassza ki **IoT Edge eszközt**.
2. Válassza ki az Azure Stack Edge Pro-eszközhöz társított IoT Edge eszközt. Az **eszköz részletei** lapon válassza a **modulok beállítása** lehetőséget. A **modulok hozzáadása** lapon válassza a **+ Hozzáadás** lehetőséget, majd válassza ki **IoT Edge modult**.
3. A **IoT Edge egyéni modulok** panelen:

    1. Adja meg a telepíteni kívánt webkiszolgáló-modul **nevét** .
    2. Adjon meg egy **rendszerkép-URI** -t a modul rendszerképéhez. A megadott névvel és címkékkel egyező modul beolvasása. Ebben az esetben `nginx:stable` egy stabil Nginx-rendszerképet fog lekérni a nyilvános [Docker-tárházból](https://hub.docker.com/_/nginx/).
    3. Illessze be a következő mintakód a **tároló létrehozási beállításai** között:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Ez a konfiguráció lehetővé teszi a modul elérését a számítási hálózati IP-cím használatával *http* protokollon keresztül a 8080-as TCP-porton (az alapértelmezett webkiszolgáló-port a 80-es).

        ![A port adatainak meghatározása IoT Edge egyéni modul panelen](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. Kattintson a **Mentés** gombra.

## <a name="verify-module-access"></a>Modul-hozzáférés ellenőrzése

1. Ellenőrizze, hogy a modul telepítése sikeres volt-e, és fut-e. Az **eszköz adatai** lap **modulok** lapján a modul futásidejű állapotának **futnia** kell.  
2. Kapcsolódjon a webkiszolgáló-alkalmazás modulhoz. Nyisson meg egy böngészőablakot, és írja be a következőt:

    `http://<compute-network-IP-address>:8080`

    Látnia kell, hogy a webkiszolgáló alkalmazás fut.

    ![A modulhoz való kapcsolódás ellenőrzése a megadott porton keresztül](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [kezelheti a felhasználókat az Azure Portalon keresztül](azure-stack-edge-manage-users.md).
