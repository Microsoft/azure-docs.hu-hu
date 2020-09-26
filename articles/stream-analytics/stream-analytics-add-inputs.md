---
title: Azure Stream Analytics-bemenetek ismertetése
description: Ez a cikk ismerteti a bemenetek fogalmát egy Azure Stream Analytics feladatban, összehasonlítva a streaming inputot a hivatkozott adatok beviteléhez.
author: jseb225
ms.author: krishmam
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b58a2b4cb5cd083f837eb598d4e5f330783181c6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91279994"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Azure Stream Analytics-bemenetek ismertetése

Azure Stream Analytics feladatok egy vagy több adatbemenethez csatlakoznak. Minden bemenet definiál egy meglévő adatforráshoz való kapcsolódást. Stream Analytics fogadja a különböző típusú eseményforrás bejövő adatait, beleértve a Event Hubs, a IoT Hub és a blob Storage-ot. A bemeneteket az egyes feladatokhoz írt streaming SQL-lekérdezés neve szerint hivatkozik. A lekérdezésben több bemenet is csatlakoztatható az adatokhoz, illetve összehasonlíthatja a folyamatos adatátviteli adatokat, és átadhatja az eredményeket a kimeneteknek. 

Stream Analytics rendelkezik az első osztályú, négy típusú erőforrással bemenetként:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) 

Ezek a bemeneti erőforrások ugyanabban az Azure-előfizetésben, mint a Stream Analytics-feladatban, vagy egy másik előfizetésben is elérhetők.

A [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-input), a  [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), a [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), a [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)és a [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) segítségével stream Analytics feladatok bemeneteit hozhatja létre, szerkesztheti és tesztelheti.

## <a name="stream-and-reference-inputs"></a>Stream-és hivatkozási bemenetek
Ahogy az adatok egy adatforrásba kerülnek, a Stream Analytics feladatainak felhasználása és valós idejű feldolgozása történik. A bemenetek két típusra oszthatók: adatstream-bemenetekre és referenciaadat-bemenetekre.

### <a name="data-stream-input"></a>Adatfolyam bemenete
Az adatfolyamok az események nem kötött sorozatából állnak az idő múlásával. A Stream Analytics-feladatoknak tartalmazniuk kell legalább egy adatstream-bemenetet. A Event Hubs, a IoT Hub, a Azure Data Lake Storage Gen2 és a blob Storage adatfolyam-bemeneti forrásként támogatott. Event Hubs a több eszközről és szolgáltatásból származó esemény-adatfolyamok gyűjtésére szolgál. Ezek a streamek lehetnek a közösségi média tevékenységi hírcsatornái, a tőzsdei információk vagy az érzékelőkből származó adatok. A IoT hubok a csatlakoztatott eszközökről eszközök internetes hálózata (IoT) forgatókönyvekben gyűjtött adatok gyűjtésére vannak optimalizálva.  A blob Storage használható bemeneti forrásként a tömeges adatok adatfolyamként való betöltéséhez, például a naplófájlokhoz.  

További információ a streaming adatbevitelekről: [stream-adatok bevitele stream Analyticsba](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Hivatkozási adatok bevitele
A Stream Analytics a *hivatkozási adatok*néven ismert bemenetet is támogatja. A hivatkozási adatértékek teljesen statikusak vagy lassan változnak. Általában a korrelációk és a keresések végrehajtásához használatos. Előfordulhat például, hogy az adatfolyam bemenetében lévő adatokat a hivatkozási adatokban lévő adatokhoz csatlakoztatja, ugyanúgy, mint az SQL Joint a statikus értékek kereséséhez. Az Azure Blob Storage, a Azure Data Lake Storage Gen2 és a Azure SQL Database jelenleg a hivatkozási adatok bemeneti forrásaként használható. A hivatkozás adatforrása Blobok legfeljebb 300 MB méretűek lehetnek, a lekérdezés bonyolultsága és a lefoglalt folyamatos átviteli egységek függvényében (további részletekért lásd a hivatkozási adatok dokumentációjának [méret korlátozása](stream-analytics-use-reference-data.md#size-limitation) című szakaszát).

További információ a hivatkozásokat használó adatbevitelekről: a [stream Analyticsban lévő keresések hivatkozási adatainak használata](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
