---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466644"
---
A VPN ellenőrzéséhez létrehozhat egy olyan Storage-fiókot, amelyet csak a létrehozott virtuális hálózat érhet el. A következő lépésekkel hozhatja létre és társíthatja ezt a Storage-fiókot a létrehozott virtuális hálózattal:

1. Tárfiók létrehozása. Használhatja azt a Storage-fiókot, amelyet az Azure Stack Edge-eszközzel használni fog. Próbálja meg elérni a Storage-fiókot egy külső hálózatról (a kijelölt hálózaton kívül). A Storage-fióknak elérhetőnek kell lennie.
2. A Azure Portal nyissa meg a Storage-fiókot. 
3. Nyissa meg a **tűzfalakat és a virtuális hálózatokat**. A **hozzáférés engedélyezése** a jobb oldali ablaktáblán válassza a **kiválasztott hálózatok** elemet. A **Storage-fiók biztonságossá tétele virtuális hálózatokkal** területen válassza a **+ meglévő virtuális hálózat hozzáadása elemet.**

    ![VPN 1 ellenőrzése](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. A **hálózatok hozzáadása** panelen:

    - Válassza ki az előfizetést. Ez ugyanaz az előfizetés, amely a Azure Stack Edge/Data Box Gateway erőforráshoz van társítva. 
    - A legördülő listából válassza ki azt a virtuális hálózatot, amelyet hozzá szeretne rendelni ehhez a Storage-fiókhoz. Válassza ki az előző lépésben létrehozott virtuális hálózatot.
    - Az **alhálózatok** területen válassza a **_default_* _ és a _GatewaySubnet * elemet. A szolgáltatási végpontok engedélyezve lesznek ehhez a virtuális hálózathoz/alhálózat-kombinációhoz. A hozzáférés engedélyezése akár 15 percet is igénybe vehet.
    - Válassza az **Engedélyezés** lehetőséget.

    ![VPN 2 ellenőrzése](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. **Beállítások** mentése.

    ![3. VPN ellenőrzése](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. A beállítások mentése után megtekintheti azokat az alhálózatokat, amelyeken engedélyezve van a virtuális hálózat.

    ![VPN 4 ellenőrzése](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. Az alábbi lépéseket követve ellenőrizheti, hogy az adat most már csak VPN-en keresztül fog-e járni: 
    - Próbálja meg elérni a Storage-fiókot egy külső hálózatról (a kijelölt hálózaton kívül). A Storage-fiók nem érhető el. 
    - Próbálja meg elérni a Storage-fiókot a kiválasztott hálózatokon engedélyezett virtuális hálózatból/alhálózatokból. A Storage-fióknak elérhetőnek kell lennie. 
 
Ezt a Storage-fiókot csak akkor érheti el, ha engedélyezve van a VPN. Ha letiltja a VPN-t, a Storage-fiók hálózati beállításait is módosítania kell. 

További információért látogasson el az [Azure Storage-tűzfalak és a virtuális hálózatok konfigurálására](../articles/storage/common/storage-network-security.md). 

