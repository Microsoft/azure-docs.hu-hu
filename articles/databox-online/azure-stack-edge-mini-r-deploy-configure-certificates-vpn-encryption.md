---
title: Oktatóanyag a Azure Stack Edge mini R-eszköz tanúsítványainak konfigurálásához Azure Portalban | Microsoft Docs
description: Az Azure Stack Edge mini R üzembe helyezésére vonatkozó oktatóanyag arra utasítja a tanúsítványt, hogy konfigurálja a fizikai eszközön lévő tanúsítványokat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: c3a09242b895234c96c64d9e23449d980e47e387
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546737"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>Oktatóanyag: tanúsítványok, VPN és titkosítás konfigurálása a Azure Stack Edge mini R-hez

Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja a tanúsítványokat, a VPN-t és a titkosítást a Azure Stack Edge mini R-eszközhöz a helyi webes felhasználói felület használatával.

Az ehhez a lépéshez szükséges idő a választott beállítástól és a tanúsítvány folyamatának a környezetében való megadásának módjától függően változhat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Tanúsítványok konfigurálása a fizikai eszközhöz
> * A virtuális magánhálózat konfigurálása
> * A titkosítás konfigurálása nyugalmi állapotban

## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge mini R-eszköz konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* Telepítette a fizikai eszközt a [Azure stack Edge mini R telepítésének](azure-stack-edge-mini-r-deploy-install.md)részletesen.

* Ha azt tervezi, hogy saját tanúsítványokat hoz:
    - A tanúsítványokat a megfelelő formátumban kell megadni, beleértve az aláíró lánc tanúsítványát. A tanúsítványról a további tudnivalókat lásd: [tanúsítványok kezelése](azure-stack-edge-gpu-manage-certificates.md)

    - Ha az eszköz üzembe helyezése Azure Government vagy Azure Government Secret vagy Azure Government legfelső szintű titkos felhőben történik, és nem az Azure nyilvános felhőben van telepítve, akkor az eszköz aktiválása előtt regisztrálni kell egy aláíró láncot. 
    A tanúsítvány részleteiért lépjen a [tanúsítványok kezelése](azure-stack-edge-gpu-manage-certificates.md)elemre.


## <a name="configure-certificates-for-device"></a>Tanúsítványok konfigurálása az eszközhöz

1. A **tanúsítványok** lapon konfigurálni fogja a tanúsítványokat. Attól függően, hogy módosította-e az eszköz nevét vagy a DNS-tartományt az **eszköz** lapon, az alábbi beállítások közül választhat a tanúsítványok közül.

    - Ha nem módosította az alapértelmezett eszköznév vagy az alapértelmezett DNS-tartományt a korábbi lépésben, és nem kívánja saját tanúsítványokat használni, akkor kihagyhatja ezt a lépést, és folytathatja a következő lépéssel. Az eszköz automatikusan létrehozta az önaláírt tanúsítványokat. 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - Ha módosította az eszköz nevét vagy a DNS-tartományt, a tanúsítványok **állapota érvénytelenként jelenik meg.** 

        ![Helyi webes felhasználói felület "tanúsítványok" 2. lapja](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        Válasszon ki egy tanúsítványt az állapot részleteinek megtekintéséhez.

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        A tanúsítvány **állapota érvénytelen, mert a** tanúsítványok nem tükrözik a frissített eszköz nevét és DNS-tartományát (amelyet a tulajdonos neve és a tulajdonos alternatív alternatívája használ). Az eszköz sikeres aktiválásához saját aláírt végponti tanúsítványokat és a hozzájuk tartozó aláírási láncokat is használhatja. Először adja hozzá az aláíró láncot, majd töltse fel a végponti tanúsítványokat. További információért nyissa [meg saját tanúsítványait a Azure stack Edge mini R-eszközön](#bring-your-own-certificates).


    - Ha módosította az eszköz nevét vagy a DNS-tartományt, és nem hoz saját tanúsítványokat, az aktiválás le lesz **tiltva**.


#### <a name="bring-your-own-certificates"></a>Saját tanúsítványok használata

Az aláíró láncot már hozzáadta egy korábbi lépésben az eszközön. Most már feltöltheti a végponti tanúsítványokat, a csomópont-tanúsítványt, a helyi felhasználói felületet és a VPN-tanúsítványt. A saját tanúsítványok hozzáadásához kövesse az alábbi lépéseket.

1. A tanúsítvány feltöltéséhez a **tanúsítvány** lapon válassza a **+ tanúsítvány hozzáadása** elemet.

    [![Helyi webes felhasználói felület "tanúsítványok" – 4. oldal](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. Más tanúsítványokat is feltölthet. Feltöltheti például a Azure Resource Manager és a blob Storage-végpont tanúsítványait.

    ![Helyi webes felhasználói felület "tanúsítványok" 6. oldal](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. Fel is töltheti a helyi webes felhasználói felület tanúsítványát. A tanúsítvány feltöltése után el kell indítania a böngészőt, és törölnie kell a gyorsítótárat. Ezután csatlakoznia kell az eszköz helyi webes felhasználói felületéhez.  

    ![Helyi webes felhasználói felület "tanúsítványok" 7. oldal](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. Fel is töltheti a csomópont-tanúsítványt.


    ![Helyi webes felhasználói felület "tanúsítványok", 8. oldal](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. Végül fel is töltheti a VPN-tanúsítványt.
        
    ![Helyi webes felhasználói felület "tanúsítványok" lapja](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. Bármikor kiválaszthat egy tanúsítványt, és megtekintheti a részleteket, így biztosíthatja, hogy ezek egyeznek a feltöltött tanúsítvánnyal.

    [![Helyi webes felhasználói felület "tanúsítványok" 9. lapja](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    A tanúsítvány oldalának frissítenie kell, hogy tükrözze az újonnan hozzáadott tanúsítványokat.

    [![Helyi webes felhasználói felület "tanúsítványok" – 10. oldal](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > Az Azure nyilvános felhő kivételével az összes felhőalapú konfiguráció (Azure Government vagy Azure Stack hub) aktiválása előtt be kell állítani az aláírási lánc tanúsítványait.


## <a name="configure-vpn"></a>A virtuális magánhálózat konfigurálása

1. A **Biztonság** csempén válassza a **Konfigurálás** VPN-hez lehetőséget. 

    A VPN konfigurálásához először meg kell győződnie arról, hogy az összes szükséges konfiguráció az Azure-ban elkészült. Részletekért lásd: [VPN konfigurálása a PowerShell-lel a Azure stack Edge mini R-eszközhöz](azure-stack-edge-placeholder.md). Ha ez befejeződik, a konfigurációt a helyi felhasználói felületen végezheti el.
    
    1. A VPN lapon válassza a **Konfigurálás** lehetőséget.
        ![VPN helyi felhasználói felületének konfigurálása 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. A **VPN konfigurálása** panelen:

        1. Adja meg a telefonkönyvet bemenetként.
        2. Adja meg bemenetként az Azure adatközponti IP-címtartomány JSON-fájlját. A fájl letöltése a következő helyről: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
        3. Válassza a **eastus** lehetőséget a régióként.
        4. Kattintson az **Alkalmaz** gombra.

        ![VPN helyi felhasználói felületének konfigurálása 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. Konfigurálja az IP-címtartományok elérését csak VPN használatával. 
    
        - Válassza a **configure (Konfigurálás**) lehetőséget **azon IP-címtartományok alatt, amelyek csak VPN-kapcsolattal érhetők el**.
        - Adja meg az Azure-Virtual Network kiválasztott VNET IPv4-tartományt.
        - Kattintson az **Alkalmaz** gombra.
    
        ![VPN helyi felhasználói felületének konfigurálása 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

Az eszköz most már készen áll a titkosításra. Konfigurálja a titkosítást nyugalmi állapotban.


## <a name="enable-encryption"></a>Titkosítás engedélyezése

1. A **Biztonság** csempén válassza a **Konfigurálás** a titkosításhoz – a REST lehetőséget. Ez egy kötelező beállítás, és amíg a konfigurálás nem sikerült, nem aktiválhatja az eszközt. 

    ![Helyi webes felhasználói felület "titkosítás a REST-ben" 1. oldal](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    A gyárban az eszközök rendszerképének bekapcsolását követően engedélyezve van a kötet szintjének BitLocker-titkosítása. Az eszköz fogadása után konfigurálnia kell a titkosítást. A rendszer újból létrehozza a tárolót és a köteteket, és lehetővé teszi a BitLocker-kulcsok használatát a REST titkosítás engedélyezéséhez, és így egy második titkosítási réteget hoz létre az adatokhoz.

1. A **titkosítás – a REST** ablaktáblán adjon meg egy 32 karakter hosszú (AES-256 bites) Base-64 kódolású kulcsot. Ez egy egyszeri konfiguráció, és ez a kulcs a tényleges titkosítási kulcs megvédésére szolgál. 

    ![Helyi webes felhasználói felület "titkosítás a REST-ben" 2. oldal](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    Dönthet úgy is, hogy automatikusan létrehozza ezt a kulcsot is.

    ![Helyi webes felhasználói felület "titkosítás a REST-ben" 3. oldal](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Kattintson az **Alkalmaz** gombra. Ez a művelet több percet vesz igénybe, és a művelet állapota megjelenik a **biztonsági** csempén.

    ![Helyi webes felhasználói felület "titkosítás a REST-ben" 4. oldal](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Ha az állapot **befejezettként** jelenik meg, térjen vissza **a kezdéshez**.

Az eszköz már készen áll az aktiválásra.

## <a name="next-steps"></a>Következő lépések

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Tanúsítványok konfigurálása a fizikai eszközhöz
> * A virtuális magánhálózat konfigurálása
> * A titkosítás konfigurálása nyugalmi állapotban

Az Azure Stack Edge mini R-eszköz aktiválásával kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Azure Stack Edge mini R-eszköz aktiválása](./azure-stack-edge-mini-r-deploy-activate.md)
