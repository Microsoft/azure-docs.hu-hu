---
title: Feladat és feladat kimenetének megőrzése egy adattárba
description: Ismerje meg a Batch-feladatok és-feladatok kimeneti adatainak megőrzésének különböző lehetőségeit. Az Azure Storage-ba vagy egy másik adattárba is megtarthatja az adatmegőrzést.
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4ebe0b6d57225eff9f3f1251d5e491c95e9b7ffc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85965110"
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

A Batch szolgáltatás támogatja a kimeneti fájlok megadását az Azure Storage-ban a feladatok adataihoz, amikor [felvesz egy feladatot](/rest/api/batchservice/add-a-task-to-a-job) egy feladathoz, vagy feladathoz adott [gyűjteményt ad hozzá](/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

A feladatok kimenetének a Batch szolgáltatás API-val való megőrzésével kapcsolatos további információkért lásd: tevékenységadatok megőrzése az [Azure Storage-ban a Batch szolgáltatás API-val](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>A .NET-hez készült batch file Conventions Library használata

A Batch egy választható konvenciót határoz meg az Azure Storage-beli feladatok kimeneti fájljainak elnevezéséhez. A [Batch file Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) leírja ezeket az egyezményeket. A file Conventions standard meghatározza a cél tároló nevét és a blob elérési útját az Azure Storage-ban egy adott kimeneti fájl számára a feladat és a feladat neve alapján.

Ön dönti el, hogy a fájl konvenciói standardot használja-e a kimeneti adatfájlok elnevezéséhez. Megadhatja azonban a cél tárolót és a blobot is. Ha a fájl-konvenciók standardot használja a kimeneti fájlok elnevezéséhez, a kimeneti fájlok megtekinthetők a [Azure Portalban][portal].

A C#-és .NET-alapú batch-megoldásokkal rendelkező fejlesztőknek a [file Conventions Library for .net][nuget_package] használatával maradhatnak a feladatok adatai egy Azure Storage-fiókban a [Batch file Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions)alapján. A file Conventions könyvtár jól ismert módon kezeli a kimeneti fájlok az Azure Storage-ba való áthelyezését, valamint a célhelyek és a Blobok elnevezését.

További információk a feladatok kimenetének a .NET-hez készült file Conventions Library-vel való [megőrzéséről: feladatok és feladatok adatainak megőrzése az Azure Storage szolgáltatásban a .net-hez készült batch file Conventions Library használatával](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>A batch-fájl konvenciói standard megvalósítása

Ha a .NET-től eltérő nyelvet használ, a [Batch file Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) alkalmazást saját alkalmazásában is megvalósíthatja.

Ha bizonyított elnevezési sémát szeretne, vagy ha a Azure Portalban szeretné megtekinteni a tevékenység kimenetét, érdemes lehet megvalósítani a fájl-konvenciók elnevezési szabványt.

### <a name="implement-a-custom-file-movement-solution"></a>Egyéni fájl-áthelyezési megoldás implementálása

Saját teljes fájlátviteli megoldást is megvalósíthat. Ezt a módszert a következő esetekben használja:

- Az Azure Storage-tól eltérő adattárba szeretné menteni a feladatokat. Ha fájlokat szeretne feltölteni egy adattárba (például az Azure SQL vagy az Azure DataLake), létrehozhat egy egyéni parancsfájlt vagy végrehajtható fájlt, amely feltölthető erre a helyre. Ezt követően az elsődleges végrehajtható fájl futtatása után meghívhatja azt a parancssorban. Egy Windows-csomóponton például meghívhatja ezt a két parancsot: `doMyWork.exe && uploadMyFilesToSql.exe`
- A kezdeti eredmények ellenőrzését vagy korai feltöltését szeretné elvégezni.
- A hibakezelés részletes szabályozását szeretné megtartani. Előfordulhat például, hogy saját megoldást szeretne megvalósítani, ha a feladat-függőségi műveletekkel bizonyos feladatokhoz tartozó kilépési kódokon alapuló feltöltési műveleteket szeretne végezni. További információ a feladat-függőségi műveletekről: feladatok [függőségeinek létrehozása a más feladatoktól függő feladatok futtatásához](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Tervezési szempontok a kimenet megőrzéséhez

A Batch-megoldás tervezésekor vegye figyelembe a feladatok és a tevékenységek kimenetével kapcsolatos következő tényezőket.

- **Számítási csomópont élettartama**: a számítási csomópontok gyakran átmenetiek, különösen az autoscale-kompatibilis készletekben. Egy csomóponton futó feladat kimenete csak akkor érhető el, ha a csomópont létezik, és csak a feladathoz beállított megőrzési időtartamon belül. Ha egy feladat olyan kimenetet hoz létre, amelyre szükség lehet a feladat befejezése után, a feladatnak fel kell töltenie a kimeneti fájljait egy tartós tárolóba, például az Azure Storage-ba.

- **Kimeneti tárterület**: az Azure Storage-t ajánlott adattárolóként használni a feladatok kimenetéhez, de bármilyen tartós tárolást használhat. A feladat kimenetének az Azure Storage-ba való írása integrálva van a Batch szolgáltatás API-ba. Ha más tartós tárterületet használ, meg kell írnia az alkalmazás logikáját a feladat kimenetének megőrzéséhez.

- **Kimenet beolvasása**: a tevékenység kimenetét közvetlenül a készletben lévő számítási csomópontokból, illetve az Azure Storage-ból vagy egy másik adattárból is lekérheti, ha megtartotta a feladat kimenetét. Ha közvetlenül egy számítási csomópontból szeretné lekérni a tevékenység kimenetét, szüksége lesz a fájl nevére és a kimeneti helyére a csomóponton. Ha a feladat kimenetét az Azure Storage-ba tartja, akkor az Azure Storage-beli fájl teljes elérési útját kell használnia a kimeneti fájlok letöltéséhez az Azure Storage SDK-val.

- **Kimenet megtekintése**: ha a Azure Portal egy batch-feladatra navigál, és kiválasztja a **fájlok a csomóponton** lehetőséget, akkor a feladathoz tartozó összes fájl, nem csak a kívánt kimeneti fájlok jelennek meg. A számítási csomópontokon lévő fájlok csak akkor érhetők el, ha a csomópont létezik, és csak a feladathoz beállított megőrzési időn belül van. Az Azure Storage-ban megőrzött tevékenység-kimenet megtekintéséhez használhatja a Azure Portal vagy egy Azure Storage-ügyfélalkalmazás, például a [Azure Storage Explorer][storage_explorer]. Ha meg szeretné tekinteni a kimeneti adatokat az Azure Storage-ban a portálon vagy egy másik eszközön, ismernie kell a fájl helyét, és közvetlenül hozzá kell navigálnia.

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg a Batch szolgáltatás API új funkcióival, hogy a Batch szolgáltatás API-val megőrzött tevékenységadatok maradjanak a [feladat adataiban az Azure Storage](batch-task-output-files.md)-ban.
- Ismerje meg, hogyan használható a Batch file Conventions Library for .NET a [feladat-és tevékenységadatok megőrzése az Azure Storage-ban a .net-hez készült batch file Conventions Library](batch-task-output-file-conventions.md)használatával.
- Tekintse meg a [PersistOutputs][github_persistoutputs] minta projektjét a githubon, amely bemutatja, hogyan használható a .net-hez készült batch ügyféloldali kódtár és a fájl konvenciók könyvtára a .net-hez a feladat kimenetének tartós tárterületre való megőrzése érdekében.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
