---
title: Oktatóanyag a Azure Stack Edge Pro-eszközhöz tartozó tanúsítványok konfigurálásához a GPU-val Azure Portalban | Microsoft Docs
description: Az Azure Stack Edge Pro és a GPU üzembe helyezésére vonatkozó oktatóanyag arra utasítja a tanúsítványt, hogy konfigurálja a fizikai eszközön lévő tanúsítványokat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: d8d21f9eea0258175195529c4d8b72ee9085dc77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546975"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-with-gpu"></a>Oktatóanyag: a Azure Stack Edge Pro tanúsítványának konfigurálása GPU-val

Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja a Azure Stack Edge Pro-eszközhöz tartozó tanúsítványokat a helyi webes felhasználói felület használatával a beépített GPU-val.

Az ehhez a lépéshez szükséges idő a választott beállítástól és a tanúsítvány folyamatának a környezetében való megadásának módjától függően változhat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Tanúsítványok konfigurálása a fizikai eszközhöz

## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge Pro-eszköz GPU-val való konfigurálása előtt győződjön meg az alábbiakról:

* A fizikai eszközt a [Azure stack Edge Pro telepítésének](azure-stack-edge-gpu-deploy-install.md)részletesen telepítette.
* Ha azt tervezi, hogy saját tanúsítványokat hoz:
    - A tanúsítványokat a megfelelő formátumban kell megadni, beleértve az aláíró lánc tanúsítványát. A tanúsítványról a további tudnivalókat lásd: [tanúsítványok kezelése](azure-stack-edge-gpu-manage-certificates.md)

<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-gpu-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>Tanúsítványok konfigurálása az eszközhöz

1. A **tanúsítványok** lapon konfigurálni fogja a tanúsítványokat. Attól függően, hogy módosította-e az eszköz nevét vagy a DNS-tartományt az **eszköz** lapon, az alábbi beállítások közül választhat a tanúsítványok közül.

    - Ha nem módosította az eszköz nevét vagy a DNS-tartományt a korábbi lépésben, és nem kívánja saját tanúsítványokat használni, akkor kihagyhatja ezt a lépést, és folytathatja a következő lépéssel. Az eszköz automatikusan létrehozta az önaláírt tanúsítványokat. 

        ![Helyi webes felhasználói felület "tanúsítványok" lapja](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Ha módosította az eszköz nevét vagy a DNS-tartományt, a tanúsítványok **állapota érvénytelenként jelenik meg.** 

        ![Helyi webes felhasználói felület "tanúsítványok" 2. lapja](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)    

        Válasszon ki egy tanúsítványt az állapot részleteinek megtekintéséhez.

        ![Helyi webes felhasználói felület "tanúsítványok" 3. oldala](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)  

        Ennek az az oka, hogy a tanúsítványok nem tükrözik a frissített eszköznév és a DNS-tartományt (amelyek a tulajdonos nevében és a tulajdonos alternatív megoldásában használatosak). Az eszköz sikeres aktiválásához válasszon a következő lehetőségek közül: 
    
        - **Az összes eszköz tanúsítványának előállítása**. Ezeket az eszköz tanúsítványait csak tesztelésre kell használni, és nem használhatók éles számítási feladatokhoz. További információ: [eszközök tanúsítványainak előállítása a Azure stack Edge Pro](#generate-device-certificates)-ban.

        - **Saját tanúsítványok** használata. Saját aláírt végponti tanúsítványokat és a hozzájuk tartozó aláíró láncokat is használhatja. Először adja hozzá az aláíró láncot, majd töltse fel a végponti tanúsítványokat. **Javasoljuk, hogy mindig saját tanúsítványokat használjon az éles számítási feladatokhoz.** További információ: [saját tanúsítványok használata az Azure stack Edge Pro-eszközön](#bring-your-own-certificates).
    
        - Saját tanúsítványokat is létrehozhat, és létrehozhat néhány eszköz tanúsítványát. A **tanúsítványok előállítása** lehetőség csak az eszköz tanúsítványait hozza újra.

    - Ha módosította az eszköz nevét vagy a DNS-tartományt, és nem állít elő tanúsítványokat, vagy saját tanúsítványokat hoz, akkor az aktiválás le lesz **tiltva**.


### <a name="generate-device-certificates"></a>Eszközök tanúsítványainak előállítása

Az eszközök tanúsítványainak létrehozásához kövesse az alábbi lépéseket.

Ezekkel a lépésekkel újragenerált és letöltheti az Azure Stack Edge Pro-eszköz tanúsítványait:

1. Az eszköz helyi felhasználói felületén lépjen a **Configuration > tanúsítványok** elemre. Válassza a **tanúsítványok előállítása** lehetőséget.

    ![Tanúsítvány előállítása és letöltése 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. Az **eszközök tanúsítványainak előállítása** lapon válassza a **készítés** lehetőséget. 

    ![A 2. tanúsítvány előállítása és letöltése](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    Az eszközök tanúsítványainak létrehozása és alkalmazása megtörtént. A tanúsítványok létrehozása és alkalmazása néhány percet vesz igénybe.
    
    > [!IMPORTANT]
    > Amíg a tanúsítvány-létrehozási művelet folyamatban van, ne hozza létre a saját tanúsítványait, és próbálja meg hozzáadni ezeket a **+ tanúsítvány hozzáadása** lehetőség használatával.

    Értesítést kap, ha a művelet sikeresen befejeződött. **A lehetséges gyorsítótár-problémák elkerüléséhez indítsa újra a böngészőt.**
    
    ![Tanúsítvány készítése és letöltése 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. A tanúsítványok létrehozása után: 

    - Az összes tanúsítvány állapota **érvényesként** jelenik meg. 

        ![Tanúsítvány készítése és letöltése 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

    - Kiválaszthat egy adott tanúsítvány nevét, és megtekintheti a tanúsítvány részleteit. 

        ![Tanúsítvány készítése és letöltése 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6a.png)

    - A **letöltési** oszlop már fel van töltve. Az oszlop az újragenerált tanúsítványok letöltésére mutató hivatkozásokat tartalmaz. 

        ![A 7. tanúsítvány előállítása és letöltése](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6b.png)


4. Válassza ki a tanúsítvány letöltési hivatkozását, és amikor a rendszer kéri, mentse a tanúsítványt. 

    ![Tanúsítvány készítése és letöltése 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Ismételje meg a folyamatot a letölteni kívánt összes tanúsítvány esetében. 
    
    ![A 9. tanúsítvány előállítása és letöltése](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    Az eszköz által generált tanúsítványok a következő formátumú DER-tanúsítványként lesznek mentve: 

    `<Device name>_<Endpoint name>.cer`. Ezek a tanúsítványok az eszközre telepített megfelelő tanúsítványok nyilvános kulcsát tartalmazzák. 

Ezeket a tanúsítványokat telepítenie kell arra az ügyfél-rendszerre, amelyet a szolgáltatói eszközön lévő végpontokhoz való hozzáféréshez használ. Ezek a tanúsítványok megbízhatósági kapcsolatot létesít az ügyfél és az eszköz között.

A tanúsítványok importálásához és telepítéséhez azon az ügyfélen, amelyet az eszköz eléréséhez használ, kövesse a [tanúsítványok importálása az Azure stack Edge Pro-eszközhöz hozzáférő ügyfeleken](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)című témakör lépéseit. 

Azure Storage Explorer használata esetén PEM formátumban kell telepítenie a tanúsítványokat az ügyfélre, és az eszköz által létrehozott tanúsítványokat PEM formátumba kell alakítania. 

> [!IMPORTANT]
> - A letöltési hivatkozás csak az eszköz által generált tanúsítványokhoz érhető el, és nem, ha saját tanúsítványokat hoz létre.
> - Dönthet úgy, hogy az eszköz által generált tanúsítványokat kombinálja, és saját tanúsítványokat használ, amíg a többi tanúsítványra vonatkozó követelmény teljesül. További információért lépjen a [tanúsítványokra vonatkozó követelményeknél](azure-stack-edge-gpu-certificate-requirements.md).
    

### <a name="bring-your-own-certificates"></a>Saját tanúsítványok használata

A következő lépésekkel adhatja hozzá saját tanúsítványait, beleértve az aláíró láncot.

1. A tanúsítvány feltöltéséhez a **tanúsítvány** lapon válassza a **+ tanúsítvány hozzáadása** elemet.

    ![Helyi webes felhasználói felület "tanúsítványok" – 4. oldal](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Először töltse fel az aláíró láncot, és válassza az **érvényesítés & Hozzáadás** lehetőséget.

    ![Helyi webes felhasználói felület "tanúsítványok" 5. oldal](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. Most már tölthet fel más tanúsítványokat. Feltöltheti például a Azure Resource Manager és a blob Storage-végpont tanúsítványait.

    ![Helyi webes felhasználói felület "tanúsítványok" 6. oldal](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    Fel is töltheti a helyi webes felhasználói felület tanúsítványát. A tanúsítvány feltöltése után el kell indítania a böngészőt, és törölnie kell a gyorsítótárat. Ezután csatlakoznia kell az eszköz helyi webes felhasználói felületéhez.  

    ![Helyi webes felhasználói felület "tanúsítványok" 7. oldal](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    Fel is töltheti a csomópont-tanúsítványt.

    ![Helyi webes felhasználói felület "tanúsítványok", 8. oldal](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    Bármikor kiválaszthat egy tanúsítványt, és megtekintheti a részleteket, így biztosíthatja, hogy ezek egyeznek a feltöltött tanúsítvánnyal.

    ![Helyi webes felhasználói felület "tanúsítványok" 9. lapja](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)

    A tanúsítvány oldalának frissítenie kell, hogy tükrözze az újonnan hozzáadott tanúsítványokat.

    ![Helyi webes felhasználói felület "tanúsítványok" – 10. oldal](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-7.png)  

    > [!NOTE]
    > Az Azure nyilvános felhő kivételével az összes felhőalapú konfiguráció (Azure Government vagy Azure Stack) aktiválása előtt be kell állítani az aláírási lánc tanúsítványait.


Az eszköz már készen áll az aktiválásra. **A kezdéshez válassza< vissza** lehetőséget.


## <a name="next-steps"></a>Következő lépések

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Tanúsítványok konfigurálása a fizikai eszközhöz

Az Azure Stack Edge Pro-eszköz aktiválásával kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro-eszköz aktiválása](./azure-stack-edge-gpu-deploy-activate.md)
