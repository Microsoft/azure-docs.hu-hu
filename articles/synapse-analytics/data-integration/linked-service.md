---
title: Társított szolgáltatás biztonságossá tétele
description: Ismerje meg, hogyan hozhat létre és biztosíthat biztonságossá egy társított szolgáltatást a felügyelt VNet
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: be7ee2f4b2b8472c5edeff63e143d99c958bfc5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627217"
---
# <a name="secure-a-linked-service-with-private-links"></a>Társított szolgáltatás biztonságossá tétele privát hivatkozásokkal

Ebből a cikkből megtudhatja, hogyan védheti egy társított szolgáltatást a Szinapszisban egy privát végponttal.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) .
* **Azure Storage-fiók**: a 2. generációs Azure Data Lake használja *forrás* adattárként. Ha nem rendelkezik Storage-fiókkal, tekintse meg az [Azure Storage-fiók létrehozása](../../storage/common/storage-account-create.md) lépéseit. Győződjön meg arról, hogy a Storage-fiók rendelkezik a szinapszis Studio IP-szűréssel a hozzáféréshez, és hogy csak a **kiválasztott hálózatok** férhetnek hozzá a Storage-fiókhoz. A panel alatti **tűzfalak és a virtuális hálózatok** beállításának az alábbi ábrához hasonlóan kell kinéznie.

![Biztonságos Storage-fiók](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Társított szolgáltatás létrehozása privát hivatkozásokkal

Az Azure szinapszis Analyticsben a társított szolgáltatás a kapcsolati adatok más szolgáltatásokhoz való definiálására szolgál. Ebben a szakaszban az Azure szinapszis Analytics és a 2. generációs Azure Data Lake fogja hozzáadni társított szolgáltatásként.

1. Nyissa meg az Azure szinapszis Studio alkalmazást, és lépjen a **kezelés** lapra.
1. A **külső kapcsolatok** területen válassza a **társított szolgáltatások** elemet.
1. Társított szolgáltatás hozzáadásához válassza az **új** lehetőséget.
1. Válassza ki a listából a Azure Data Lake Storage Gen2 csempét, és válassza a **Folytatás** lehetőséget.
1. Győződjön meg arról, hogy az **interaktív szerzői műveletek** engedélyezve vannak. Előfordulhat, hogy az engedélyezése körülbelül 1 percet vesz igénybe. 
1. Adja meg a hitelesítő adatait. A fiók kulcsa, az egyszerű szolgáltatásnév és a felügyelt identitás jelenleg támogatott hitelesítési típus. Válassza a kapcsolódás tesztelése lehetőséget a hitelesítő adatok helyességének ellenőrzéséhez.
1. Válassza a **kapcsolat tesztelése** lehetőséget, mert a Storage-fiók nem engedélyezi a hozzáférést egy privát végpont létrehozása és jóváhagyása nélkül. A hibaüzenetben egy olyan hivatkozást kell látnia, amely létrehoz egy **privát végpontot** , amelyet követve a következő részre léphet. Ha követi ezt a hivatkozást, ugorja át a következő részt.
1. Miután végzett, válassza a **Létrehozás** lehetőséget.

## <a name="create-a-managed-private-endpoint"></a>Felügyelt privát végpont létrehozása

Ha a fenti kapcsolat tesztelésekor nem választotta ki a hiperhivatkozást, kövesse az alábbi elérési utat. Hozzon létre egy felügyelt magánhálózati végpontot, amelyhez csatlakozni fog a fent létrehozott társított szolgáltatáshoz.

1. Lépjen a **kezelés** lapra.
1. Lépjen a **felügyelt virtuális hálózatok** szakaszra.
1. Válassza a **+ új** lehetőséget a felügyelt privát végpont alatt.
1. Válassza ki a listából a Azure Data Lake Storage Gen2 csempét, és válassza a **Folytatás** lehetőséget.
1. Adja meg a fent létrehozott Storage-fiók nevét.
1. Kattintson a **Létrehozás** elemre.
1. Néhány másodperc várakozás után kell megjelennie, hogy a privát kapcsolat létrehozása jóváhagyást igényel.

## <a name="private-link-approval"></a>Privát hivatkozás jóváhagyása
1. Válassza ki a fent létrehozott privát végpontot. Megtekintheti azt a hiperhivatkozást, amely lehetővé teszi a magánhálózati végpont jóváhagyását a Storage-fiók szintjén. *Egy másik lehetőség, hogy közvetlenül a Azure Portal Storage-fiókba lép, és bemegy a **privát Endpoint Connections** panelre.*
1. Jelölje be a stúdióban létrehozott privát végpontot, és válassza a **jóváhagyás** lehetőséget.
1. Adjon hozzá egy leírást, és válassza az **Igen** lehetőséget.
1. Lépjen vissza a szinapszis studióhoz a **kezelés** lap **felügyelt virtuális hálózatok** szakaszában.
1. Ahhoz, hogy a jóváhagyás megjelenjen a privát végponton, körülbelül 1 percnek kell lennie.

## <a name="check-the-connection-works"></a>A kapcsolatok működésének ellenõrzése
1. Lépjen a **kezelés** lapra, és válassza ki a létrehozott társított szolgáltatást.
1. Győződjön meg arról, hogy az **interaktív szerzői műveletek** aktívak.
1. Válassza a **Kapcsolat tesztelése** elemet. Látnia kell a sikeres kapcsolatokat.

Ezzel létrehozott egy biztonságos és privát kapcsolatot a szinapszis és a társított szolgáltatás között.

## <a name="next-steps"></a>Következő lépések


A felügyelt privát végpont Azure szinapszis Analyticsben való megismeréséhez lásd: [felügyelt privát végpontok](../security/synapse-workspace-managed-private-endpoints.md).


Az Azure szinapszis Analytics adatintegrálásával kapcsolatos további információkért tekintse meg az adatok betöltését [Data Lake](data-integration-data-lake.md) cikkben.