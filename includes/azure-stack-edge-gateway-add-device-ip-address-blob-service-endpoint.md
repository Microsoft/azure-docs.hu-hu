---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/21/2019
ms.author: alkohli
ms.openlocfilehash: 0c6845f081ccbe42e70964eaa939d58597e3b69b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89087795"
---
1. Nyissa meg az eszköz helyi webes FELÜLETét, és jelentkezzen be az eszközre. Győződjön meg arról, hogy az eszköz fel van oldva.

2. Nyissa meg a **hálózati beállítások** lapot. Jegyezze fel az eszköz IP-címét az ügyfélhez való kapcsolódáshoz használt hálózati adapterhez.

3. Ha távoli Windows-ügyféllel dolgozik, indítsa el a **jegyzettömböt** rendszergazdaként, majd nyissa meg a következő helyen található hosts fájlt: `C:\Windows\System32\Drivers\etc` .

4. Adja hozzá a következő bejegyzést a gazdagépek fájljához: `<Device IP address> <Blob service endpoint>`

    A blob Service-végpontot a Azure Portal létrehozott Edge Storage-fiókból kapta. Csak a blob Service-végpont utótagját fogja használni.

    A hivatkozáshoz használja az alábbi képet. Mentse a `hosts` fájlt.

    ![Hosts fájl módosítása Windows-ügyfélen](media/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint/hosts-file-1.png)