---
title: Azure Data Lake Analytics kód hibakeresése helyileg
description: Megtudhatja, hogyan használhatja a Visual studióhoz készült Azure Data Lake-eszközöket az U-SQL-feladatok hibakereséséhez a helyi munkaállomáson.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 1c7218deac9efba6df6c1284f2578a744e768284
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221026"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Azure Data Lake Analytics kód hibakeresése helyileg

A Azure Data Lake Tools for Visual Studio használatával Azure Data Lake Analytics kódot futtathat és kereshet a helyi munkaállomáson, ugyanúgy, mint a Azure Data Lake Analytics szolgáltatásban.

Ismerje meg, hogyan [futtathat U-SQL-szkriptet a helyi gépen](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Parancsfájlok és C#-szerelvények helyi hibakeresése

A C#-szerelvények hibakeresése a Azure Data Lake Analytics szolgáltatásba való elküldése és regisztrálása nélkül végezhető el. A töréspontokat a kód mögötti fájlban és egy hivatkozott C#-projektben is megadhatja.

### <a name="debug-local-code-in-a-code-behind-file"></a>Helyi kód hibakeresése egy programkód mögött

1. Adja meg a töréspontokat a kód mögötti fájlban.
2. A parancsfájl helyi hibakereséséhez válassza az **F5 billentyűt** .

> [!NOTE]
   > Az alábbi eljárás csak a Visual Studio 2015-es verziójában működik. A régebbi Visual Studio-verziók esetében előfordulhat, hogy manuálisan kell hozzáadnia a **PDB** -fájlokat.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Helyi kód hibakeresése egy hivatkozott C#-projektben

1. Hozzon létre egy C# szerelvény-projektet, és hozza létre a kimeneti **dll** -fájl létrehozásához.
2. Regisztrálja a **dll** -fájlt egy U-SQL-utasítás használatával:

   ```sql
   CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
   ```
   
3. Állítson be töréspontokat a C#-kódban.
4. Az **F5 billentyűt** lenyomásával hibakeresést végezhet a C# **dll** -fájl helyi hivatkozásával.


## <a name="next-steps"></a>Következő lépések

- Összetettebb lekérdezésre például lásd: [webhelyek naplófájljainak elemzése Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md).
- A feladat részleteinek megtekintéséhez lásd: [Azure Data Lake Analytics feladatok böngésző és feladat nézetének használata](data-lake-analytics-data-lake-tools-view-jobs.md).
- A csúcspont-végrehajtási nézet használatához lásd: [a Data Lake Tools for Visual Studio csúcs-végrehajtási nézetének használata](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
