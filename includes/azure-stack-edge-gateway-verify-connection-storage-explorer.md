---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 203c977fe9109cd8b2b6de561e975e20aacf700e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185572"
---
Ha első alkalommal használja a Storage Explorer, a következő lépéseket kell elvégeznie.

1. A felső parancssáv lépjen a **> cél Azure stack API**-k szerkesztése elemre.

    ![Storage Explorer konfigurálása](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. A módosítások életbe léptetéséhez indítsa újra a Storage Explorer.


Kövesse az alábbi lépéseket a Storage-fiókhoz való kapcsolódáshoz és a kapcsolat ellenőrzéséhez.

1. A Storage Explorer területen válassza a Storage-fiókok lehetőséget. Kattintson a jobb gombbal, és válassza a **Kapcsolódás az Azure Storage-hoz** lehetőséget. 

    ![Storage Explorer 2 konfigurálása](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. A **Kapcsolódás az Azure Storage-hoz** párbeszédpanelen válassza **a Storage-fiók nevének és kulcsának használata** lehetőséget.

    ![Storage Explorer 3 konfigurálása](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. A **kapcsolat neve és kulcsa** párbeszédpanelen hajtsa végre a következő lépéseket:

    1. Adja meg az Edge Storage-fiók megjelenítendő nevét. 
    2. Adja meg az Edge Storage-fiók nevét.
    3. Illessze be az eszköz helyi API-jai által Azure Resource Manager használatával kapott hozzáférési kulcsot.
    4. Válassza a tárolási tartomány lehetőséget **(adja meg az alábbit)** , majd adja meg a blob Service-végpont utótagját a következő formátumban: `<appliance name>.<DNSdomain>` . 
    5. Az átvitel HTTP *-kapcsolaton* keresztüli **használatának** engedélyezése. 
    6. Kattintson a **Tovább** gombra.

    ![Storage Explorer 4 konfigurálása](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. A **kapcsolatok összegzése** párbeszédpanelen tekintse át a megadott adatokat. Válassza a **Kapcsolódás** lehetőséget.

    ![Storage Explorer 5 konfigurálása](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. A sikeresen hozzáadott fiók megjelenik a Storage Explorer bal oldali ablaktábláján (külső, egyéb) a nevéhez hozzáfűzve. A tároló megtekintéséhez válassza a **blob-tárolók** lehetőséget.

    ![BLOB-tárolók megtekintése](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

A következő lépésben ellenőrizheti, hogy az adatátvitel ténylegesen működik-e a kapcsolaton keresztül.

Az alábbi lépéseket követve töltse be az adatait a peremhálózati Storage-fiókjába az eszközön, és automatikusan a leképezett Azure Storage-fiókba.

1. Válassza ki azt a tárolót, amelyhez be szeretné tölteni az adatait a peremhálózati Storage-fiókban. Válassza a **feltöltés** lehetőséget, majd válassza a **fájlok feltöltése** lehetőséget.

    ![Adatátvitel ellenőrzése](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. A **fájlok feltöltése** párbeszédpanelen navigáljon, és válassza ki a feltölteni kívánt fájlokat. Kattintson a **Tovább** gombra.

    ![Adatátvitel ellenőrzése 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. Ellenőrizze, hogy a fájlok feltöltése megtörtént-e. A feltöltött fájlok megjelennek a tárolóban.

    ![Adatátviteli adatok ellenőrzése 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. Ezután csatlakozni fog ahhoz az Azure Storage-fiókhoz, amelyet ehhez a peremhálózati Storage-fiókhoz rendeltek. Az Edge Storage-fiókba feltöltött összes adattal automatikusan fel kell venni az Azure Storage-fiókot. 
    
    Az Azure Storage-fiókhoz tartozó kapcsolati karakterlánc beszerzéséhez nyissa meg az **Azure Storage-fiók > a hozzáférési kulcsokat** , és másolja a kapcsolati karakterláncot.

    ![Adatátvitel ellenőrzése 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    Az Azure Storage-fiókhoz való csatoláshoz használja a kapcsolódási karakterláncot.  

    ![Adatátviteli adatok ellenőrzése 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. A **kapcsolatok összegzése** párbeszédpanelen tekintse át a megadott adatokat. Válassza a **Kapcsolódás** lehetőséget.

    ![Adatátviteli adatok ellenőrzése 6](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. Látni fogja, hogy a peremhálózati Storage-fiókban feltöltött fájlok átkerültek az Azure Storage-fiókba.

    ![Adatátviteli szolgáltatás ellenőrzése 7](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)
