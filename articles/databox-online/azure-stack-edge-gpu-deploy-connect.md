---
title: Oktatóanyag a Azure Portal GPU-val való kapcsolódáshoz, konfiguráláshoz, aktiváláshoz Azure Stack Edge Pro-eszközhöz | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat a Azure Stack Edge-eszközhöz egy beépített GPU-val a helyi webes felhasználói felület használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: 0046bc6376b0a03f75aff5461b99e3f450dc0a28
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062947"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-with-gpu"></a>Oktatóanyag: Kapcsolódás Azure Stack Edge Pro-hoz GPU használatával

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a Azure Stack Edge Pro-eszközhöz egy beépített GPU-val a helyi webes felhasználói felület használatával.

A csatlakozási folyamat körülbelül 5 percig tart.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Kapcsolódás fizikai eszközhöz


## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge Pro-eszköz GPU-val való konfigurálása előtt győződjön meg az alábbiakról:

* A fizikai eszközt a [Azure stack Edge Pro telepítésének](azure-stack-edge-gpu-deploy-install.md)részletesen telepítette.


## <a name="connect-to-the-local-web-ui-setup"></a>Kapcsolódás a helyi webes felhasználói felület telepítéséhez

1. Konfigurálja az Ethernet-adaptert a számítógépén az Azure Stack Edge Pro-eszközhöz a 192.168.100.5 és az alhálózat 255.255.255.0 statikus IP-címével való csatlakozáshoz.

2. Csatlakoztassa a számítógépet az eszközön a PORT 1-hez. Ha a számítógépet közvetlenül (kapcsoló nélkül) csatlakoztatja az eszközhöz, használjon fordított bekötésű kábelt vagy USB Ethernet-adaptert. A következő ábra alapján azonosíthatja eszközén a PORT 1-et.

    ![Egy bekábelezett eszköz hátlapja](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    Az eszköz hátlapja némileg eltérő lehet a kapott modelltől függően. További információ: [az eszköz csatlakoztatása](azure-stack-edge-gpu-deploy-install.md#cable-the-device).


3. Nyissa meg egy böngészőablakot, és lépjen az eszköz helyi webes felhasználói felületére a `https://192.168.100.10` címen.  
    Ez a művelet eltarthat pár percig, miután bekapcsolta az eszközt.

    Megjelenik egy hiba vagy egy figyelmeztetés, amely azt jelzi, hogy probléma van a webhely biztonsági tanúsítványával. 
   
    ![A webhely biztonsági tanúsítványának hibaüzenete](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Válassza **a folytatás ehhez a weboldalhoz** lehetőséget.  
    Ezek a lépések eltérők lehetnek, attól függően, hogy milyen böngészőt használ.

5. Jelentkezzen be az eszköz webes felhasználói felületére. Az alapértelmezett jelszó a *jelszó1*. 
   
    ![Azure Stack Edge Pro-eszköz bejelentkezési lapja](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. A parancssorban módosítsa az eszköz rendszergazdai jelszavát.  
    Az új jelszó 8–16 karakterből állhat. A következő karakterek közül háromfélét tartalmaznia kell: kisbetűk, nagybetűk, számok és speciális karakterek.

Most már **megtekintheti** az eszköz áttekintés lapját. A következő lépés az eszköz hálózati beállításainak konfigurálása.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Kapcsolódás fizikai eszközhöz


Az Azure Stack Edge Pro-eszköz hálózati beállításainak konfigurálásáról a következő témakörben tájékozódhat:

> [!div class="nextstepaction"]
> [Hálózat konfigurálása](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
