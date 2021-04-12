---
title: Oktatóanyag a Azure Stack Edge Pro R-eszköz tanúsítványának konfigurálásához Azure Portalban | Microsoft Docs
description: Az Azure Stack Edge Pro R üzembe helyezésére vonatkozó oktatóanyag arra utasítja Önt, hogy konfigurálja a tanúsítványokat a fizikai eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
ms.openlocfilehash: 99a0f93b73e3aa3d1cb031301150ec528870ab38
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059972"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>Oktatóanyag: tanúsítványok konfigurálása a Azure Stack Edge Pro R-hez

Ez az oktatóanyag azt ismerteti, hogyan konfigurálhat tanúsítványokat a Azure Stack Edge Pro R-eszközhöz a helyi webes felhasználói felület használatával.

Az ehhez a lépéshez szükséges idő a választott beállítástól és a tanúsítvány folyamatának a környezetében való megadásának módjától függően változhat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Tanúsítványok konfigurálása a fizikai eszközhöz
> * A virtuális magánhálózat konfigurálása
> * A titkosítás konfigurálása – Rest

## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge Pro R-eszköz konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* A fizikai eszközt a [Azure stack Edge Pro R telepítésének](azure-stack-edge-pro-r-deploy-install.md)részletesen telepítette.
* Ha azt tervezi, hogy saját tanúsítványokat hoz:
    - A tanúsítványokat a megfelelő formátumban kell megadni, beleértve az aláíró lánc tanúsítványát. A tanúsítványról a további tudnivalókat lásd: [tanúsítványok kezelése](azure-stack-edge-gpu-manage-certificates.md)



## <a name="configure-certificates-for-device"></a>Tanúsítványok konfigurálása az eszközhöz

1. A **tanúsítványok** lapon konfigurálni fogja a tanúsítványokat. Attól függően, hogy módosította-e az eszköz nevét vagy a DNS-tartományt az **eszköz** lapon, az alábbi beállítások közül választhat a tanúsítványok közül.

    - Ha nem módosította az eszköz nevét vagy a DNS-tartományt a korábbi lépésben, akkor kihagyhatja ezt a lépést, és folytathatja a következő lépéssel. Az eszköz automatikusan létrehozta az önaláírt tanúsítványokat. 

    - Ha módosította az eszköz nevét vagy a DNS-tartományt, a tanúsítványok **állapota érvénytelenként jelenik meg.** 

        ![Helyi webes felhasználói felület "tanúsítványok" 2. lapja](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        Válasszon ki egy tanúsítványt az állapot részleteinek megtekintéséhez.

        ![Helyi webes felhasználói felület "tanúsítványok" 3. oldala](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        Ennek az az oka, hogy a tanúsítványok nem tükrözik a frissített eszköznév és a DNS-tartományt (amelyek a tulajdonos nevében és a tulajdonos alternatív megoldásában használatosak). Az eszköz sikeres aktiválásához saját aláírt végponti tanúsítványokat és a hozzájuk tartozó aláírási láncokat is használhatja. Először adja hozzá az aláíró láncot, majd töltse fel a végponti tanúsítványokat. További információ: [saját tanúsítványok használata az Azure stack Edge Pro R-eszközön](#bring-your-own-certificates).

    - Ha módosította az eszköz nevét vagy a DNS-tartományt, és nem hoz saját tanúsítványokat, az aktiválás le lesz **tiltva**.

    
#### <a name="bring-your-own-certificates"></a>Saját tanúsítványok használata

A következő lépésekkel adhatja hozzá saját tanúsítványait, beleértve az aláíró láncot.

1. A tanúsítvány feltöltéséhez a **tanúsítvány** lapon válassza a **+ tanúsítvány hozzáadása** elemet.

    ![Helyi webes felhasználói felület "tanúsítványok" – 4. oldal](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. Először töltse fel az aláíró láncot, és válassza az **érvényesítés & Hozzáadás** lehetőséget.

    ![Helyi webes felhasználói felület "tanúsítványok" 5. oldal](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. Most már tölthet fel más tanúsítványokat. Feltöltheti például a Azure Resource Manager és a blob Storage-végpont tanúsítványait.

    ![Helyi webes felhasználói felület "tanúsítványok" 6. oldal](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    Fel is töltheti a helyi webes felhasználói felület tanúsítványát. A tanúsítvány feltöltése után el kell indítania a böngészőt, és törölnie kell a gyorsítótárat. Ezután csatlakoznia kell az eszköz helyi webes felhasználói felületéhez.  

    ![Helyi webes felhasználói felület "tanúsítványok" 7. oldal](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    Fel is töltheti a csomópont-tanúsítványt.

    ![Helyi webes felhasználói felület "tanúsítványok", 8. oldal](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Végül feltöltheti a VPN-tanúsítványt.

    ![Helyi webes felhasználói felület "tanúsítványok" 9. lapja](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Bármikor kiválaszthat egy tanúsítványt, és megtekintheti a részleteket, így biztosíthatja, hogy ezek egyeznek a feltöltött tanúsítvánnyal.

    A tanúsítvány oldalának frissítenie kell, hogy tükrözze az újonnan hozzáadott tanúsítványokat.

    ![Helyi webes felhasználói felület "tanúsítványok" – 10. oldal](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > Az Azure nyilvános felhő kivételével az összes felhőalapú konfiguráció (Azure Government vagy Azure Stack) aktiválása előtt be kell állítani az aláírási lánc tanúsítványait.

1. **A kezdéshez válassza< vissza** lehetőséget.

## <a name="configure-vpn"></a>A virtuális magánhálózat konfigurálása

1. A **Biztonság** csempén válassza a **Konfigurálás** VPN-hez lehetőséget.

    ![Helyi webes felhasználói felület "VPN" lapja](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    A VPN konfigurálásához először meg kell győződnie arról, hogy az összes szükséges konfiguráció az Azure-ban elkészült. Részletekért lásd: [Előfeltételek konfigurálása](azure-stack-edge-placeholder.md) és [Azure-erőforrások konfigurálása VPN-hez](azure-stack-edge-placeholder.md). Ha ez befejeződik, a konfigurációt a helyi felhasználói felületen végezheti el.
    
    1. A VPN lapon válassza a **Konfigurálás** lehetőséget.
    2. A **VPN konfigurálása** panelen:

    - Engedélyezze a **VPN-beállításokat**.
    - Adja meg a **VPN közös titkát**. Ez az Azure VPN-kapcsolati objektum létrehozásakor megadott megosztott kulcs.
    - Adja meg a **VPN-átjáró IP-** címét. Ez az Azure-beli helyi hálózati átjáró IP-címe.
    - A **PFS csoport** esetében válassza a **nincs** lehetőséget. 
    - A **DH csoport** esetében válassza a **Group2** lehetőséget.
    - **IPSec-integritási módszer** esetén válassza a **sha256** lehetőséget.
    - A **IPseccipher átalakítási állandók** esetében válassza a **GCMAES256** lehetőséget.
    - Az **IPSec-hitelesítés állandók** esetében válassza a **GCMAES256** lehetőséget.
    - Az **IKE titkosítási módszernél** válassza a **AES256** lehetőséget.
    - Kattintson az **Alkalmaz** gombra.

        ![Helyi felhasználói felület konfigurálása 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. A VPN-útvonal konfigurációs fájljának feltöltéséhez válassza a **feltöltés** lehetőséget. 
    
        ![Helyi felhasználói felület konfigurálása 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - Keresse meg az előző lépésben a helyi rendszeren letöltött VPN-konfigurációs *JSON* -fájlt.
        - Válassza ki a régiót, mint az eszközhöz, a virtuális hálózathoz és az átjáróhoz társított Azure-régiót.
        - Kattintson az **Alkalmaz** gombra.
    
            ![Helyi felhasználói felület konfigurálása 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. Az ügyfél-specifikus útvonalak hozzáadásához konfigurálja úgy az IP-címtartományok elérését, hogy csak a VPN használatával legyenek elérhetők. 
    
        - Válassza a **configure (Konfigurálás**) lehetőséget **azon IP-címtartományok alatt, amelyek csak VPN-kapcsolattal érhetők el**.
        - Adjon meg egy érvényes IPv4-tartományt, és válassza a **Hozzáadás** lehetőséget. További tartományok hozzáadásához ismételje meg a lépéseket.
        - Kattintson az **Alkalmaz** gombra.
    
            ![Helyi UI konfigurálása 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. **A kezdéshez válassza< vissza** lehetőséget.

## <a name="configure-encryption-at-rest"></a>A titkosítás konfigurálása – Rest

1. A **Biztonság** csempén válassza a **Konfigurálás** a titkosításhoz – a REST lehetőséget. Ez egy kötelező beállítás, és amíg a konfigurálás nem sikerült, nem aktiválhatja az eszközt. 

    A gyárban az eszközök rendszerképének bekapcsolását követően engedélyezve van a kötet szintjének BitLocker-titkosítása. Az eszköz fogadása után konfigurálnia kell a titkosítást. A rendszer újból létrehozza a tárolót és a köteteket, és lehetővé teszi a BitLocker-kulcsok használatát a REST titkosítás engedélyezéséhez, és így egy második titkosítási réteget hoz létre az adatokhoz.

1. A **titkosítás – a REST** ablaktáblán adjon meg egy 32 karakter hosszú Base-64 kódolású kulcsot. Ez egy egyszeri konfiguráció, és ez a kulcs a tényleges titkosítási kulcs megvédésére szolgál. Dönthet úgy, hogy automatikusan létrehoz egy kulcsot, vagy megadhat egy értéket.

    ![Helyi webes felhasználói felület "titkosítás nyugalmi állapotban" panel](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    A kulcsot a rendszer az eszköz aktiválása után a **felhő részletei** oldalon található kulcsfájl menti.

1. Kattintson az **Alkalmaz** gombra. Ez a művelet több percet vesz igénybe, és a művelet állapota megjelenik a **biztonsági** csempén.

    ![Helyi webes felhasználói felület "titkosítás a REST-ben" lap](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. Miután az állapot **befejezettként** jelenik meg, válassza **< vissza lehetőséget a kezdéshez**.

Az eszköz már készen áll az aktiválásra. 


## <a name="next-steps"></a>Következő lépések

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Tanúsítványok konfigurálása a fizikai eszközhöz
> * A virtuális magánhálózat konfigurálása
> * A titkosítás konfigurálása – Rest

Az Azure Stack Edge Pro R-eszköz aktiválásával kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro R-eszköz aktiválása](./azure-stack-edge-pro-r-deploy-activate.md)
