---
title: Régiók közötti Migrálás Azure Data Lake Storage Gen1 | Microsoft Docs
description: Megtudhatja, hogy mit érdemes figyelembe vennie, amikor az új régiókban elérhetővé váló Azure Data Lake Storage Gen1 áttelepítését tervezi és végrehajtja.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: twooley
ms.openlocfilehash: c6520036f3ddb8799025129391330268b0604886
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97723806"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Azure Data Lake Storage Gen1 migrálása régiók között

Ahogy az Azure Data Lake Storage Gen1 elérhetővé válik az új régiókban, dönthet úgy, hogy egyszeri áttelepítést végez, hogy kihasználhassa az új régiót. Megtudhatja, mit érdemes figyelembe venni az áttelepítés megtervezésekor és befejezésekor.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-előfizetés**. További információkért tekintse [meg az ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/)című témakört.
* **Data Lake Storage Gen1 fiók két különböző régióban**. További információ: [Bevezetés a Azure Data Lake Storage Gen1 használatába](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. További információkért lásd: [Az Azure Data Factory bemutatása](../data-factory/introduction.md).


## <a name="migration-considerations"></a>A migrálás szempontjai

Először is azonosítsa az alkalmazáshoz legjobban illő áttelepítési stratégiát, amely Data Lake Storage Gen1 adatokat ír, olvas vagy dolgoz fel. Ha kiválaszt egy stratégiát, vegye figyelembe az alkalmazás rendelkezésre állási követelményeit, valamint az áttelepítés során felmerülő állásidőt. Előfordulhat például, hogy a legegyszerűbb módszer a "lift-SHIFT" felhőalapú áttelepítési modell használata. Ebben a megközelítésben szünetelteti az alkalmazást a meglévő régióban, miközben az összes adatait átmásolja az új régióba. A másolási folyamat befejezésekor folytassa az alkalmazást az új régióban, majd törölje a régi Data Lake Storage Gen1 fiókot. Az áttelepítés során felmerülő állásidő szükséges.

Az állásidő csökkentése érdekében azonnal elkezdheti az új adatmennyiség betöltését az új régióban. Ha rendelkezik a minimálisan szükséges adattal, futtassa az alkalmazást az új régióban. A háttérben folytassa a régebbi adatok másolását a meglévő Data Lake Storage Gen1-fiókból az új régióban lévő új Data Lake Storage Gen1-fiókba. Ennek a módszernek a használatával a váltást az új régióra állíthatja kis állásidővel. Ha az összes régebbi adatmásolt, törölje a régi Data Lake Storage Gen1 fiókot.

A Migrálás megtervezése során megfontolandó további fontos információk:

* **Az adatmennyiség**. Az adatmennyiség (GB-ban, a fájlok és mappák száma stb.) hatással van az áttelepítéshez szükséges időre és erőforrásokra.

* **Data Lake Storage Gen1 fiók neve**. Az új régióban az új fiók nevének globálisan egyedinek kell lennie. Előfordulhat például, hogy az USA 2. keleti régiójában lévő régi Data Lake Storage Gen1-fiók neve contosoeastus2.azuredatalakestore.net. Az új Data Lake Storage Gen1-fiókot az Észak-EU contosonortheu.azuredatalakestore.net lehet megnevezni.

* **Eszközök**. Javasoljuk, hogy Data Lake Storage Gen1 fájlok másolásához használja az [Azure Data Factory másolási tevékenységet](../data-factory/connector-azure-data-lake-store.md) . Data Factory támogatja az adatáthelyezést a nagy teljesítmény és megbízhatóság érdekében. Ne feledje, hogy Data Factory csak a mappa-hierarchiát és a fájlok tartalmát másolja át. A régi fiókban használt hozzáférés-vezérlési listákat (ACL-eket) manuálisan kell alkalmaznia az új fiókra. További információért, beleértve a legjobb esetben a teljesítményre vonatkozó célokat is, tekintse meg a [másolási tevékenység teljesítményére és hangolására vonatkozó útmutatót](../data-factory/copy-activity-performance.md). Ha gyorsabban szeretné átmásolni az Adatmásolást, lehetséges, hogy további Felhőbeli adatáthelyezési egységeket kell használnia. Más eszközök, például a AdlCopy, nem támogatják az adatok régiók közötti másolását.  

* **Sávszélességgel kapcsolatos díjak**. A [sávszélességre vonatkozó díjak](https://azure.microsoft.com/pricing/details/bandwidth/) érvényesek, mivel az adatok egy Azure-régióból kerülnek át.

* **Az adataihoz tartozó ACL**-EK. A fájlokra és mappákra vonatkozó ACL-ek alkalmazásával biztonságossá teheti adatait az új régióban. További információ: [Azure Data Lake Storage Gen1 tárolt adatok védelme](data-lake-store-secure-data.md). Javasoljuk, hogy az áttelepítést az ACL-ek frissítésére és módosítására használja. Előfordulhat, hogy az aktuális beállításokhoz hasonló beállításokat szeretne használni. A fájlokra alkalmazott ACL-eket a Azure Portal, a [PowerShell-parancsmagok](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)vagy az SDK-k használatával tekintheti meg.  

* **A analitikai szolgáltatások helye**. A legjobb teljesítmény érdekében az elemzési szolgáltatások, például az Azure Data Lake Analytics vagy az Azure-HDInsight ugyanabban a régióban kell lenniük, mint az adatai.  

## <a name="next-steps"></a>Következő lépések
* [A Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
