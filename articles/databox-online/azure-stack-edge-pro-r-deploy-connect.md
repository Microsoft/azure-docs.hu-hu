---
title: Oktatóanyag a Azure Stack Edge Pro R-eszköz csatlakoztatásához, konfigurálásához, aktiválásához Azure Portalban | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat a Azure Stack Edge Pro R-eszközhöz a helyi webes felhasználói felület használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 1ded68783ec45c649ab4aa41996cb0113a7fa42d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059921"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-r"></a>Oktatóanyag: Kapcsolódás Azure Stack Edge Pro R-hez

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a Azure Stack Edge Pro R-eszközhöz a helyi webes KEZELŐFELÜLET használatával.

A csatlakozási folyamat körülbelül 5 percig tart.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Kapcsolódás fizikai eszközhöz


## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge Pro R-eszköz konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* A fizikai eszközt a [Azure stack Edge Pro R telepítésének](azure-stack-edge-pro-r-deploy-install.md)részletesen telepítette.


## <a name="connect-to-the-local-web-ui-setup"></a>Kapcsolódás a helyi webes felhasználói felület telepítéséhez

1. Állítsa be a számítógépén az Ethernet-adaptert az Azure Stack Edge Pro R-eszközhöz való csatlakozásra a 192.168.100.5 statikus IP-címmel és a 255.255.255.0 alhálózattal.

2. Csatlakoztassa a számítógépet az eszközön a PORT 1-hez. Ha a számítógépet közvetlenül (kapcsoló nélkül) csatlakoztatja az eszközhöz, használjon fordított bekötésű kábelt vagy USB Ethernet-adaptert. A következő ábra alapján azonosíthatja eszközén a PORT 1-et.

    ![Egy bekábelezett eszköz hátlapja](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)


3. Nyissa meg egy böngészőablakot, és lépjen az eszköz helyi webes felhasználói felületére a `https://192.168.100.10` címen.  
    Ez a művelet eltarthat pár percig, miután bekapcsolta az eszközt.

    Megjelenik egy hiba vagy egy figyelmeztetés, amely azt jelzi, hogy probléma van a webhely biztonsági tanúsítványával. 
   
    ![A webhely biztonsági tanúsítványának hibaüzenete](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-1.png)


4. Válassza **a folytatás ehhez a weboldalhoz** lehetőséget.  
    Ezek a lépések eltérők lehetnek, attól függően, hogy milyen böngészőt használ.

5. Jelentkezzen be az eszköz webes felhasználói felületére. Az alapértelmezett jelszó a *jelszó1*. 
   
    ![Azure Stack Edge-eszköz bejelentkezési lapja](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-3.png)

6. A parancssorban módosítsa az eszköz rendszergazdai jelszavát.  
    Az új jelszó 8–16 karakterből állhat. A következő karakterek közül háromfélét tartalmaznia kell: kisbetűk, nagybetűk, számok és speciális karakterek.

Most már **megtekintheti** az eszköz áttekintés lapját. A következő lépés az eszköz hálózati beállításainak konfigurálása.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Kapcsolódás fizikai eszközhöz


A Azure Stack Edge Pro R-eszköz hálózati beállításainak konfigurálásáról további információt a következő témakörben talál:

> [!div class="nextstepaction"]
> [Hálózat konfigurálása](./azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)
