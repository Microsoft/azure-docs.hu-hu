---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: fa65a7354112a2b220686372459b348d45832dd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466638"
---
Hajtsa végre az alábbi lépéseket az eszköz helyi webes FELÜLETén. Ez a lépés körülbelül 15 percet vesz igénybe, beleértve a VPN-konfigurációs fájl (vagy a szolgáltatási címke fájljának) feltöltését. 

1. Nyissa meg a **Configuration > VPN-** t. Válassza a **Konfigurálás** lehetőséget.

    ![Helyi felhasználói felület konfigurálása 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-1.png)

2. A **VPN konfigurálása** panelen:

    - Engedélyezze a **VPN-beállításokat**.
    - Adja meg a **VPN közös titkát**. Ez a megosztott kulcs, amelyet az Azure VPN-kapcsolati erőforrás létrehozásakor adott meg.
    - Adja meg a **VPN-átjáró IP-** címét. Ez az Azure-beli helyi hálózati átjáró IP-címe.
    - A **PFS csoport** esetében válassza a **nincs** lehetőséget. 
    - A **DH csoport** esetében válassza a **Group2** lehetőséget.
    - **IPSec-integritási módszer** esetén válassza a **sha256** lehetőséget.
    - Az **IPSec titkosítási algoritmusok átalakításához** válassza a **GCMAES256** lehetőséget.
    - Az **IPSec-hitelesítés állandók** esetében válassza a **GCMAES256** lehetőséget.
    - Az **IKE titkosítási módszernél** válassza a **AES256** lehetőséget.
    - Kattintson az **Alkalmaz** gombra.

        ![Helyi felhasználói felület konfigurálása 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-2.png)

    További információ a támogatott titkosítási algoritmusokról: a [titkosítási követelmények és az Azure VPN-átjárók](../articles/vpn-gateway/vpn-gateway-about-compliance-crypto.md#ipsecike-policy-faq). 

3. A VPN-útvonal konfigurációs fájljának feltöltéséhez válassza a **feltöltés** lehetőséget. 

    ![Helyi felhasználói felület konfigurálása 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-3.png)

    - Tallózással keresse meg az előző lépésben a helyi rendszeren letöltött *JSON* -fájlt.
    - Válassza ki a régiót, mint az eszközhöz, a virtuális hálózathoz és az átjáróhoz társított Azure-régiót.
    - Kattintson az **Alkalmaz** gombra.

        ![Helyi felhasználói felület konfigurálása 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-4.png)
    
    A feltöltés körülbelül 7-8 percet vesz igénybe az eszközön.

4. Az ügyfél-specifikus útvonalak hozzáadásához konfigurálja úgy az IP-címtartományok elérését, hogy csak a VPN használatával legyenek elérhetők. 

    - Válassza a **configure (Konfigurálás**) lehetőséget **azon IP-címtartományok alatt, amelyek csak VPN-kapcsolattal érhetők el**.
    - Adjon meg egy érvényes IPv4-tartományt, és válassza a **Hozzáadás** lehetőséget. További tartományok hozzáadásához ismételje meg a lépéseket.
    - Kattintson az **Alkalmaz** gombra.

        ![Helyi UI konfigurálása 5](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/configure-vpn-local-ui-5.png)

