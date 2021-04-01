---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: d01cf90c42efbe611748027d0ea47ff8af3e5621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553187"
---
1. Jelentkezzen be a Data Box eszközre. Győződjön meg róla, hogy fel van oldva.

    ![Képernyőfelvétel: az irányítópultot az eszköz zárolt állapotban jeleníti meg.](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Válassza a **hálózati adapterek beállítása** lehetőséget. Jegyezze fel az eszköz IP-címét az ügyfélhez való kapcsolódáshoz használt hálózati adapterhez.

    ![A képernyőfelvételen a hálózati beállítások láthatók, ahol megtekintheti az I P-címeket.](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Lépjen a **Kapcsolódás és másolás** lehetőségre, és kattintson a **Rest** elemre.

    ![Képernyőfelvétel: a kapcsolódás és a másolás ablaktábla, ahol kiválaszthatja a REST lehetőséget hozzáférési beállításként.](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. A **hozzáférési Storage-fiók és az adatok feltöltése** párbeszédpanelen másolja a **blob Service-végpontot**.

    ![Képernyőfelvétel: a hozzáférési Storage-fiók és az adatok feltöltése párbeszédpanel, ahol a blob Service-végpontot másolhatja.](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Indítsa el a **jegyzettömböt** rendszergazdaként, majd nyissa meg a következő helyen található **hosts** fájlt: `C:\Windows\System32\Drivers\etc` .
6. Adja hozzá a következő bejegyzést a **gazdagépek** fájljához: `<device IP address> <Blob service endpoint>`
7. A hivatkozáshoz használja az alábbi képet. Mentse a **hosts** fájlt.

    ![Képernyőfelvétel: az I P és a blob Service-végpont hozzáadására szolgáló Jegyzettömb-dokumentum.](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
