---
title: Azure Stream Analytics-bemenetek ismertetése
description: Ez a cikk ismerteti a bemenetek fogalmát egy Azure Stream Analytics feladatban, összehasonlítva a streaming inputot a hivatkozott adatok beviteléhez.
ms.service: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 442c5a1174c4a91ea9401315bb3e518e4fe6cc4e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183913"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Azure Stream Analytics-bemenetek ismertetése

Azure Stream Analytics feladatok egy vagy több adatbemenethez csatlakoznak. Minden bemenet definiál egy meglévő adatforráshoz való kapcsolódást. Stream Analytics fogadja a különböző típusú eseményforrás bejövő adatait, beleértve a Event Hubs, a IoT Hub és a blob Storage-ot. A bemeneteket az egyes feladatokhoz írt streaming SQL-lekérdezés neve szerint hivatkozik. A lekérdezésben több bemenet is csatlakoztatható az adatokhoz, illetve összehasonlíthatja a folyamatos adatátviteli adatokat, és átadhatja az eredményeket a kimeneteknek. 

Stream Analytics rendelkezik az első osztályú, négy típusú erőforrással bemenetként:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 

Ezek a bemeneti erőforrások ugyanabban az Azure-előfizetésben, mint a Stream Analytics-feladatban, vagy egy másik előfizetésben is elérhetők.

A [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-input), a  [Azure PowerShell](/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), a [.NET API](/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), a [REST API](/rest/api/streamanalytics/2016-03-01/inputs)és a [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) segítségével stream Analytics feladatok bemeneteit hozhatja létre, szerkesztheti és tesztelheti.

## <a name="stream-and-reference-inputs"></a>Stream-és hivatkozási bemenetek
Ahogy az adatok egy adatforrásba kerülnek, a Stream Analytics feladatainak felhasználása és valós idejű feldolgozása történik. A bemenetek két típusra oszthatók: adatstream-bemenetekre és referenciaadat-bemenetekre.

### <a name="data-stream-input"></a>Adatfolyam bemenete
Az adatfolyamok az események nem kötött sorozatából állnak az idő múlásával. A Stream Analytics-feladatoknak tartalmazniuk kell legalább egy adatstream-bemenetet. A Event Hubs, a IoT Hub, a Azure Data Lake Storage Gen2 és a blob Storage adatfolyam-bemeneti forrásként támogatott. Event Hubs a több eszközről és szolgáltatásból származó esemény-adatfolyamok gyűjtésére szolgál. Ezek a streamek lehetnek a közösségi média tevékenységi hírcsatornái, a tőzsdei információk vagy az érzékelőkből származó adatok. A IoT hubok a csatlakoztatott eszközökről eszközök internetes hálózata (IoT) forgatókönyvekben gyűjtött adatok gyűjtésére vannak optimalizálva.  A blob Storage használható bemeneti forrásként a tömeges adatok adatfolyamként való betöltéséhez, például a naplófájlokhoz.  

További információ a streaming adatbevitelekről: [stream-adatok bevitele stream Analyticsba](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Hivatkozási adatok bevitele
A Stream Analytics a *hivatkozási adatok* néven ismert bemenetet is támogatja. A hivatkozási adatértékek teljesen statikusak vagy lassan változnak. Általában a korrelációk és a keresések végrehajtásához használatos. Előfordulhat például, hogy az adatfolyam bemenetében lévő adatokat a hivatkozási adatokban lévő adatokhoz csatlakoztatja, ugyanúgy, mint az SQL Joint a statikus értékek kereséséhez. Az Azure Blob Storage, a Azure Data Lake Storage Gen2 és a Azure SQL Database jelenleg a hivatkozási adatok bemeneti forrásaként használható. A hivatkozás adatforrása Blobok legfeljebb 300 MB méretűek lehetnek, a lekérdezés bonyolultsága és a lefoglalt folyamatos átviteli egységek függvényében (további részletekért lásd a hivatkozási adatok dokumentációjának [méret korlátozása](stream-analytics-use-reference-data.md#size-limitation) című szakaszát).

További információ a hivatkozásokat használó adatbevitelekről: a [stream Analyticsban lévő keresések hivatkozási adatainak használata](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)