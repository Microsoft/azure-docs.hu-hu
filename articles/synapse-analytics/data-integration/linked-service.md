---
title: Csatolt szolgáltatás biztonságossá tere
description: Megtudhatja, hogyan létesítsen és biztosítjon biztonságossá egy összekapcsolt szolgáltatást felügyelt virtuális hálózatokkal
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 6be76878a9a07c5f4a1e2a9348bb7b09cb1b10eb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567587"
---
# <a name="secure-a-linked-service-with-private-links"></a>Csatolt szolgáltatás biztonságossá szolgáltatása privát kapcsolatokkal

Ebből a cikkből megtudhatja, hogyan biztosíthatja egy privát végponttal a csatolt szolgáltatások biztonságát a Synapse-ban.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés:** Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt hozzon létre egy ingyenes [Azure-fiókot.](https://azure.microsoft.com/free/)
* **Azure Storage-fiók:** Az Azure Data Lake Gen 2-t használja *forrásadattárként.* Ha még nincs tárfiókja, a létrehozás lépéseit lásd: [Azure Storage-fiók](../../storage/common/storage-account-create.md) létrehozása. Győződjön meg arról, hogy a Storage-fiók rendelkezik Synapse Studio IP-szűréssel  a hozzáféréshez, és hogy csak a kijelölt hálózatok férhetnek hozzá a Storage-fiókhoz. A panelen található **Tűzfalak és virtuális** hálózatok beállításnak az alábbi képen láthatóhoz hasonlónak kell lennie.

![Biztonságos tárfiók](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Privát kapcsolatokkal összekapcsolt szolgáltatás létrehozása

Ebben Azure Synapse Analytics szolgáltatásban definiálhatja a kapcsolati adatokat más szolgáltatásokhoz. Ebben a szakaszban hozzáadja a Azure Synapse Analytics Azure Data Lake Gen2-t csatolt szolgáltatásként.

1. Nyissa meg a Azure Synapse Studiót, és nyissa meg a **Kezelés** lapot.
1. A **Külső kapcsolatok alatt** válassza a Csatolt szolgáltatások **lehetőséget.**
1. Csatolt szolgáltatás hozzáadásához válassza az Új **lehetőséget.**
1. Válassza a Azure Data Lake Storage Gen2 csempét a listából, majd válassza a **Folytatás lehetőséget.**
1. Győződjön meg arról, hogy engedélyezi **az interaktív szerzői funkciókat.** Az engedélyezése körülbelül 1 percet is igénybe vehet. 
1. Adja meg a hitelesítő adatait. A fiókkulcs, a szolgáltatásnév és a felügyelt identitás jelenleg támogatott hitelesítési típusok. Válassza a Kapcsolat tesztelése lehetőséget a hitelesítő adatok helyességének ellenőrzéséhez.
1. Válassza **a Kapcsolat tesztelése** lehetőséget, ennek sikertelennek kell lennie, mert a tárfiók nem engedélyezi a hozzáférést egy privát végpont létrehozása és jóváhagyása nélkül. A hibaüzenetben megjelenik egy hivatkozás, amely létrehoz egy privát végpontot, amely **a** következő részhez használhatja. Ha követi ezt a hivatkozást, hagyja ki a következő részt.
1. Miután végzett, válassza a **Létrehozás** lehetőséget.

## <a name="create-a-managed-private-endpoint"></a>Felügyelt privát végpont létrehozása

Ha a fenti kapcsolat tesztelése során nem kattintott a hivatkozásra, kövesse az alábbi útvonalat. Hozzon létre egy felügyelt privát végpontot, amely a fent létrehozott csatolt szolgáltatáshoz csatlakozik.

1. Ugrás a **Kezelés lapra.**
1. Ugrás a **Felügyelt virtuális hálózatok szakaszra.**
1. A **Felügyelt privát végpont alatt** válassza az + Új lehetőséget.
1. Válassza a Azure Data Lake Storage Gen2 csempét a listából, majd válassza a **Folytatás lehetőséget.**
1. Adja meg a fent létrehozott tárfiók nevét.
1. Kattintson a **Létrehozás** elemre.
1. Néhány másodperc várakozás után látnia kell, hogy a létrehozott privát kapcsolat jóváhagyást vár.

## <a name="private-link-approval"></a>Privát kapcsolat jóváhagyása
1. Válassza ki a fent létrehozott privát végpontot. Egy hivatkozással jóváhagyhatja a privát végpontot a Tárfiók szintjén. *Másik lehetőségként közvetlenül a Tárfiók Azure Portal a Privát **végponti kapcsolatok panelre.***
1. Jelölje be a Studióban létrehozott privát végpontot, és válassza a **Jóváhagyás lehetőséget.**
1. Adjon meg egy leírást, és válassza az **Igen lehetőséget**
1. Vissza a Synapse Studio a Kezelés lap  Felügyelt virtuális hálózatok **szakaszában.**
1. A jóváhagyásnak körülbelül 1 percet kell igénybe vennie, hogy a privát végpont is tükrözve legyen.

## <a name="check-the-connection-works"></a>Ellenőrizze, hogy működik-e a kapcsolat
1. A Kezelés **lapon** válassza ki a létrehozott csatolt szolgáltatást.
1. Győződjön meg arról, **hogy az interaktív szerzői mód** aktív.
1. Válassza a **Kapcsolat tesztelése** elemet. A kapcsolat sikeresnek kell lennie.

Ezzel biztonságos és privát kapcsolatot létesítet a Synapse és a csatolt szolgáltatás között.

## <a name="next-steps"></a>Következő lépések


A felügyelt privát végpontokkal kapcsolatos további Azure Synapse Analytics lásd: [Felügyelt privát végpontok.](../security/synapse-workspace-managed-private-endpoints.md)


A data lake-ek adatintegrációval kapcsolatos Azure Synapse Analytics adatok Data Lake-be való bemenő [cikkében talál.](data-integration-data-lake.md)