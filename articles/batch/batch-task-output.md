---
title: Eredmények vagy naplók megőrzése befejezett feladatokból és feladatokból egy adattárba – Azure Batch | Microsoft Docs
description: Ismerje meg a Batch-feladatok és-feladatok kimeneti adatainak megőrzésének különböző lehetőségeit. Az Azure Storage-ba vagy egy másik adattárba is megtarthatja az adatmegőrzést.
services: batch
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d03fd754e5a8e2872063b8a10bd1293b94d8f3b6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094430"
---
# <a name="persist-job-and-task-output"></a>Feladatok és tevékenységek kimenetének megőrzése

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

A feladatok kimenetének gyakori példái a következők:

- A bemeneti adatokat feldolgozó feladat során létrehozott fájlok.
- A feladatok végrehajtásához társított naplófájlok.

Ez a cikk a tevékenységek kimenetének megőrzésére szolgáló különböző lehetőségeket ismerteti.

## <a name="options-for-persisting-output"></a>A kimenet megőrzésének lehetőségei

A forgatókönyvtől függően van néhány különböző megközelítés, amelyekkel megtarthatja a feladat kimenetét:

- [Használja a Batch szolgáltatás API](batch-task-output-files.md)-ját.  
- [Használja a Batch file Conventions kódtárat a .net-hez](batch-task-output-file-conventions.md).  
- Implementálja a Batch file Conventions standard alkalmazást az alkalmazásban.
- Egyéni fájl-áthelyezési megoldás implementálása.

Az alábbi szakaszok röviden leírják az egyes megközelítéseket, valamint a kimenet megőrzésére vonatkozó általános tervezési szempontokat.

### <a name="use-the-batch-service-api"></a>A Batch szolgáltatás API használata

A Batch szolgáltatás támogatja a kimeneti fájlok megadását az Azure Storage-ban a feladatok adataihoz, amikor [felvesz egy feladatot](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) egy feladathoz, vagy feladathoz adott [gyűjteményt ad hozzá](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

A feladatok kimenetének a Batch szolgáltatás API-val való megőrzésével kapcsolatos további információkért lásd: tevékenységadatok megőrzése az [Azure Storage-ban a Batch szolgáltatás API-val](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>A .NET-hez készült batch file Conventions Library használata

A Batch egy választható konvenciót határoz meg az Azure Storage-beli feladatok kimeneti fájljainak elnevezéséhez. A [Batch file Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) leírja ezeket az egyezményeket. A file Conventions standard meghatározza a cél tároló nevét és a blob elérési útját az Azure Storage-ban egy adott kimeneti fájl számára a feladat és a feladat neve alapján.

Ön dönti el, hogy a fájl konvenciói standardot használja-e a kimeneti adatfájlok elnevezéséhez. Megadhatja azonban a cél tárolót és a blobot is. Ha a fájl-konvenciók standardot használja a kimeneti fájlok elnevezéséhez, a kimeneti fájlok megtekinthetők a [Azure Portalban][portal].

A és a .NET- C# hez készült batch-megoldásokat fejlesztő fejlesztők a [file Conventions Library for .net][nuget_package] használatával tárolják a feladatait egy Azure Storage-fiókban a [Batch file Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)alapján. A file Conventions könyvtár jól ismert módon kezeli a kimeneti fájlok az Azure Storage-ba való áthelyezését, valamint a célhelyek és a Blobok elnevezését.

További információk a feladatok kimenetének a .NET-hez készült file Conventions Library-vel való megőrzéséről: feladatok [és feladatok adatainak megőrzése az Azure Storage szolgáltatásban a .net-hez készült batch file Conventions Library használatával](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>A batch-fájl konvenciói standard megvalósítása

Ha a .NET-től eltérő nyelvet használ, a [Batch file Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) alkalmazást saját alkalmazásában is megvalósíthatja.

Ha bizonyított elnevezési sémát szeretne, vagy ha a Azure Portalban szeretné megtekinteni a tevékenység kimenetét, érdemes lehet megvalósítani a fájl-konvenciók elnevezési szabványt.

### <a name="implement-a-custom-file-movement-solution"></a>Egyéni fájl-áthelyezési megoldás implementálása

Saját teljes fájlátviteli megoldást is megvalósíthat. Ezt a módszert a következő esetekben használja:

- Az Azure Storage-tól eltérő adattárba szeretné menteni a feladatokat. Ha fájlokat szeretne feltölteni egy adattárba (például az Azure SQL vagy az Azure DataLake), létrehozhat egy egyéni parancsfájlt vagy végrehajtható fájlt, amely feltölthető erre a helyre. Ezt követően az elsődleges végrehajtható fájl futtatása után meghívhatja azt a parancssorban. Egy Windows-csomóponton például meghívhatja ezt a két parancsot:`doMyWork.exe && uploadMyFilesToSql.exe`
- A kezdeti eredmények ellenőrzését vagy korai feltöltését szeretné elvégezni.
- A hibakezelés részletes szabályozását szeretné megtartani. Előfordulhat például, hogy saját megoldást szeretne megvalósítani, ha a feladat-függőségi műveletekkel bizonyos feladatokhoz tartozó kilépési kódokon alapuló feltöltési műveleteket szeretne végezni. További információ a feladat-függőségi műveletekről: feladatok [függőségeinek létrehozása a más feladatoktól függő feladatok futtatásához](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Tervezési szempontok a kimenet megőrzéséhez

A Batch-megoldás tervezésekor vegye figyelembe a feladatok és a tevékenységek kimenetével kapcsolatos következő tényezőket.

- **Számítási csomópont élettartama**: A számítási csomópontok gyakran átmenetiek, különösen az autoscale-kompatibilis készletekben. Egy csomóponton futó feladat kimenete csak akkor érhető el, ha a csomópont létezik, és csak a feladathoz beállított megőrzési időtartamon belül. Ha egy feladat olyan kimenetet hoz létre, amelyre szükség lehet a feladat befejezése után, a feladatnak fel kell töltenie a kimeneti fájljait egy tartós tárolóba, például az Azure Storage-ba.

- **Kimeneti tárterület**: Az Azure Storage szolgáltatás a feladat kimenetének adattáraként ajánlott, de bármilyen tartós tárolást használhat. A feladat kimenetének az Azure Storage-ba való írása integrálva van a Batch szolgáltatás API-ba. Ha más tartós tárterületet használ, meg kell írnia az alkalmazás logikáját a feladat kimenetének megőrzéséhez.

- **Kimenet**beolvasása: A feladat kimenetét közvetlenül a készletben lévő számítási csomópontokból, illetve az Azure Storage-ból vagy más adattárból is lekérheti, ha a feladat kimenete megmaradt. Ha közvetlenül egy számítási csomópontból szeretné lekérni a tevékenység kimenetét, szüksége lesz a fájl nevére és a kimeneti helyére a csomóponton. Ha a feladat kimenetét az Azure Storage-ba tartja, akkor az Azure Storage-beli fájl teljes elérési útját kell használnia a kimeneti fájlok letöltéséhez az Azure Storage SDK-val.

- **Kimenet megtekintése**: Amikor egy batch-feladathoz navigál a Azure Portal, majd kiválasztja a **fájlok csomóponton**lehetőséget, a feladathoz tartozó összes fájl megjelenik, nem csak a kimeneti fájlok, amelyekre kíváncsi. A számítási csomópontokon lévő fájlok csak akkor érhetők el, ha a csomópont létezik, és csak a feladathoz beállított megőrzési időn belül van. Az Azure Storage-ban megőrzött tevékenység-kimenet megtekintéséhez használhatja a Azure Portal vagy egy Azure Storage-ügyfélalkalmazás, például a [Azure Storage Explorer][storage_explorer]. Ha meg szeretné tekinteni a kimeneti adatokat az Azure Storage-ban a portálon vagy egy másik eszközön, ismernie kell a fájl helyét, és közvetlenül hozzá kell navigálnia.

## <a name="next-steps"></a>További lépések

- Ismerkedjen meg a Batch szolgáltatás API új funkcióival, hogy [a Batch szolgáltatás API-val](batch-task-output-files.md)megőrzött tevékenységadatok maradjanak a feladat adataiban az Azure Storage-ban.
- Ismerje meg, hogyan használható a Batch file Conventions Library for .NET a [feladat-és tevékenységadatok megőrzése az Azure Storage-ban a .net-hez készült batch file Conventions Library](batch-task-output-file-conventions.md)használatával.
- Tekintse meg a [PersistOutputs][github_persistoutputs] minta projektjét a githubon, amely bemutatja, hogyan használható a .net-hez készült batch ügyféloldali kódtár és a fájl konvenciók könyvtára a .net-hez a feladat kimenetének tartós tárterületre való megőrzése érdekében.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
