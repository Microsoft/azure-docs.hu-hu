---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 10feccb53b28181d260e7738ab99bdc2e3c9340c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94553110"
---
Kövesse az alábbi lépéseket a Storage-fiókhoz való kapcsolódáshoz és a kapcsolat ellenőrzéséhez.

1. A Storage Explorerban nyissa meg a **Kapcsolódás az Azure Storage-hoz** párbeszédpanelt. A **Kapcsolódás az Azure Storage-hoz** párbeszédpanelen válassza **a Storage-fiók nevének és kulcsának használata** lehetőséget.

    ![Képernyőfelvétel: a Kapcsolódás az Azure Storage-hoz párbeszédpanel, amelyen a Storage-fiók neve és kulcsa van kiválasztva.](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Illessze be a **fiók nevét** és a **fiók kulcsát** (1. kulcs értékét a helyi webes felhasználói felületen a **kapcsolat és a másolás** lapról). Válassza ki a tárolási végpontok tartományt **(adja meg az alábbit)** , majd adja meg a blob Service-végpontot az alábbi ábrán látható módon. Ellenőrizze, hogy csak *a HTTP protokollon keresztüli* átvitelt kell-e **használni** . Ha a *HTTPS protokollt* használja, hagyja meg a jelölőnégyzet jelölését. Kattintson a **Tovább** gombra.

    ![Képernyőfelvétel: a kapcsolat neve és kulcsa párbeszédpanel, ahol a beírt értékek szerepelnek.](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. A **kapcsolatok összegzése** párbeszédpanelen tekintse át a megadott adatokat. Válassza a **Kapcsolódás** lehetőséget.

    ![Képernyőfelvétel: a kapcsolat összefoglalása párbeszédpanel, amelyen a csatlakoztatás van kiválasztva.](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. A sikeresen hozzáadott fiók megjelenik a Storage Explorer bal oldali ablaktábláján (külső, egyéb) a nevéhez hozzáfűzve. A tároló megtekintéséhez kattintson a **blob-tárolók** elemre.

    ![Képernyőfelvétel: az Explorer menü, amelyen a blob-tárolók ki vannak választva.](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
