---
title: Helyileg Azure Data Lake Analytics-kód hibaelhárítása
description: Ismerje meg, hogyan használható az Azure Data Lake Tools for Visual Studio helyi munkaállomáson U-SQL-feladatok hibakereséséhez.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 0827311218202de447e5cf27356e00c4da020e94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472991"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Helyileg Azure Data Lake Analytics-kód hibaelhárítása

Használhatja az Azure Data Lake Tools for Visual Studio futtatásához és az Azure Data Lake Analytics-kód hibaelhárítása a helyi munkaállomáson, mint az Azure Data Lake Analytics szolgáltatásban.

Ismerje meg, hogyan [U-SQL-szkript futtatása helyi gépen](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Parancsfájlok és C#-szerelvények helyi hibakeresése

A hibakeresési C#-szerelvények nélkül elküldené és regisztrálná őket az Azure Data Lake Analytics szolgáltatásba. Többek között töréspontokat állíthat be, mind a háttérkód-fájlban, és a egy hivatkozott C#-projektben.

### <a name="debug-local-code-in-a-code-behind-file"></a>A háttérkód fájl helyi kódok hibakeresése

1. Állítson be töréspontokat a háttérkód-fájlban.
2. Válassza ki **F5** a parancsfájl helyi hibakereséséhez.

> [!NOTE]
   > Az alábbi eljárás csak a Visual Studio 2015 működik. A Visual Studio régebbi verzióit, akkor előfordulhat, hogy manuálisan kell hozzáadnia a **PDB** fájlokat.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Egy hivatkozott C#-projektben helyi kódok hibakeresése

1. Hozzon létre egy C#-szerelvényprojektet, és építse fel a kimenet előállítása **DLL** fájlt.
2. Regisztrálja a **DLL** fájlt egy U-SQL-utasítás használatával:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Állítson be töréspontokat a C#-kódban.
4. Válassza ki **F5** a hibakeresést a szkripten lére a C# **DLL** fájlt helyileg.


## <a name="next-steps"></a>További lépések

- Egy összetettebb lekérdezés egy példa: [használata Azure Data Lake Analytics webhelyek naplóinak elemzése](data-lake-analytics-analyze-weblogs.md).
- Feladatok részleteinek megtekintéséhez: [használja a Feladatböngésző és a feladat nézet az Azure Data Lake Analytics-feladatok](data-lake-analytics-data-lake-tools-view-jobs.md).
- A vertex végrehajtási nézet használata, lásd: [a Vertex végrehajtási nézet használata a Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
