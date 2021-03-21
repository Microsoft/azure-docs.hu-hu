---
title: Oktatóanyag Azure Stack Edge mini R-hez való kapcsolódáshoz Azure Portal
description: Megtudhatja, hogyan csatlakozhat a Azure Stack Edge mini R-eszközhöz a helyi webes felhasználói felület használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: fe76391a5cfce8d7d39e47131db108ab87e5aed5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96468528"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>Oktatóanyag: Kapcsolódás Azure Stack Edge mini R-hez

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a Azure Stack Edge mini R-eszközhöz a helyi webes KEZELŐFELÜLET használatával.

A csatlakozási folyamat körülbelül 5 percig tart.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Kapcsolódás fizikai eszközhöz



## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge-eszköz konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* A fizikai eszközt a [Azure stack Edge telepítése](azure-stack-edge-mini-r-deploy-install.md)című részletesen telepítette.


## <a name="connect-to-the-local-web-ui-setup"></a>Kapcsolódás a helyi webes felhasználói felület telepítéséhez

1. Konfigurálja az Ethernet-adaptert a számítógépén az Azure Stack Edge Pro-eszközhöz a 192.168.100.5 és az alhálózat 255.255.255.0 statikus IP-címével való csatlakozáshoz.

2. Csatlakoztassa a számítógépet az eszközön a PORT 1-hez. Ha a számítógépet közvetlenül (kapcsoló nélkül) csatlakoztatja az eszközhöz, használjon fordított bekötésű kábelt vagy USB Ethernet-adaptert. A következő ábra alapján azonosíthatja eszközén a PORT 1-et.

    ![Wi-Fi kábelezése](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Kapcsolódás fizikai eszközhöz


Az eszköz hálózati beállításainak konfigurálásáról további információt a következő témakörben talál:

> [!div class="nextstepaction"]
> [Hálózat konfigurálása](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
