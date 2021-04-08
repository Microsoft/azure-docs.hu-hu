---
title: Az Azure Blockchain Workbench előzetes verziójának áttekintése
description: Az Azure Blockchain Workbench előzetes verziójának és képességeinek áttekintése.
ms.date: 05/22/2020
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: fbd6be3907dbd10b003d065dfb14031a0e378478
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87003147"
---
# <a name="what-is-azure-blockchain-workbench"></a>Mi az Azure Blockchain Workbench?

Az Azure Blockchain Workbench előzetes verziója olyan Azure-szolgáltatások és-funkciók gyűjteménye, amelyek segítségével Blockchain-alkalmazásokat hozhat létre és helyezhet üzembe más szervezetekkel való üzleti folyamatok és adatmennyiségek megosztásához. Az Azure Blockchain Workbench biztosítja az infrastruktúraszerkezetet a blokkláncalkalmazások építéséhez, lehetővé téve ezzel a fejlesztők számára, hogy az üzleti logika és az intelligens szerződések létrehozására összpontosítsanak. Emellett megkönnyíti a blokkláncalkalmazások létrehozását is, hiszen számos Azure-szolgáltatást és képességet integrál az általános fejlesztési feladatok automatizálásának elősegítése céljából.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>Blokkláncalkalmazások létrehozása

A Blockchain Workbench használatával konfigurációkkal és intelligensszerződés-kód írásával határozhat meg blokkláncalkalmazásokat. Rögtön belevághat a blokkláncalkalmazás-fejlesztésbe, és a szerződés meghatározására, illetve az üzleti logika megírására összpontosíthat ahelyett, hogy szerkezetet és támogatási szolgáltatásokat kellene felépítenie.

## <a name="manage-applications-and-users"></a>Alkalmazások és felhasználók kezelése

Az Azure Blockchain Workbench webalkalmazást és REST API-kat biztosít a blokkláncalkalmazások és a felhasználók kezeléséhez. A Blockchain Workbench rendszergazdái kezelhetik az alkalmazás-hozzáféréseket, és alkalmazás-szerepkörökhöz rendelhetik hozzá a felhasználókat. Az Azure AD felhasználói automatikusan leképeződnek az alkalmazásban lévő tagokra.

## <a name="integrate-blockchain-with-applications"></a>Blokklánc integrálása az alkalmazásokkal

A Blockchain Workbench REST API-jai és az üzenetalapú API-k a meglévő rendszerekkel való integrációra is használhatók. Az API-k olyan felületet biztosítanak, amely lehetővé teszi többféle elosztott tranzakciónapló-technológia, tárterület és adatbázis-ajánlat cseréjét vagy felhasználását.

Az Blockchain Workbench átalakíthatja az üzenet-alapú API-nak küldött üzeneteket, hogy a Blockchain natív API-ját elvárt formában hozza létre a tranzakciókat.  A Workbench aláírja és a megfelelő blokklánchoz irányítja a tranzakciókat. 

A Workbench automatikusan továbbítja az eseményeket a Service Bus és az Event Grid részére az üzenetek az alárendelt fogyasztóknak történő elküldése érdekében. A fejlesztők ezen üzenetküldő rendszerek bármelyikével elvégezhetik az integrációt a tranzakciók irányításához és az eredmények megtekintéséhez.

## <a name="deploy-a-blockchain-network"></a>Blokklánchálózat üzembe helyezése

Az Azure Blockchain Workbench előre konfigurált megoldásként egy Azure Resource Manager megoldássablon használatával egyszerűsíti a konzorcium blokklánchálózatának beállítását. A sablon egyszerűsített üzembe helyezést tesz lehetővé, amely a konzorcium futtatásához szükséges összes összetevőt telepíti. Jelenleg a Blockchain Workbench támogatja az Ethereumot.

## <a name="use-active-directory"></a>Active Directory használata

A meglévő blokkláncprotokollokkal a blokkláncidentitások címként jelennek meg a hálózaton. Az Azure Blockchain Workbench átveszi a blokkláncidentitást azáltal, hogy társítja azt egy Active Directory-identitással, így a vállalati alkalmazások Active Directory-identitásokkal történő felépítése egyszerűbbé válik.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Láncon belüli adatok szinkronizálása láncon kívüli tárolóval

Az Azure Blockchain Workbench megkönnyíti a blokkláncesemények és az adatok elemzését azáltal, hogy a blokkláncon szereplő adatokat automatikusan szinkronizálja a láncon kívüli tárolóval. Ahelyett, hogy közvetlenül a blokkláncról nyerné az adatokat, láncon kívüli adatbázisrendszerekről, például az SQL Serverről kérdezheti le azokat. Az adatelemzési feladatokat végző végfelhasználók számára nem szükséges Blockchain szakértelem.

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Az Azure Blockchain híreket az Azure [Blockchain blogon](https://azure.microsoft.com/blog/topics/blockchain/) tekintheti meg, ahol naprakészen tarthatja a Blockchain szolgáltatás ajánlatait és az Azure Blockchain Engineering csapata információit.

Ha visszajelzést szeretne küldeni, vagy új szolgáltatásokat szeretne kérni, tegye közzé vagy szavazzon a [blockchain készült Azure feedback fórumán](https://aka.ms/blockchainuservoice)keresztül.

### <a name="community-support"></a>Közösségi támogatás

Folytassa a Microsoft-mérnökökkel és az Azure Blockchain közösségi szakértőivel.

* [A Microsoft Q&az Azure Blockchain Workbench kérdéseit tartalmazó oldalt](/answers/topics/azure-blockchain-workbench.html)
* [A Microsoft technikai közössége](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench-architektúra](architecture.md)
