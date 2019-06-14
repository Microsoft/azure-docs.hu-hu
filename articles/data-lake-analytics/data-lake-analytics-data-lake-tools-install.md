---
title: Az Azure Data Lake Tools for Visual Studio telepítése
description: Ez a cikk ismerteti az Azure Data Lake Tools for Visual Studio telepítése.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 05/03/2018
ms.openlocfilehash: 3269d38b691ec4dd9573a241c89dadc285787143
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60408135"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>A Data Lake Tools for Visual Studio telepítése

Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studiót Azure Data Lake Analytics-fiókok létrehozásához, feladatok definiálásához [U-SQL](data-lake-analytics-u-sql-get-started.md) segítségével, valamint feladatok Data Lake Analytics-szolgáltatásokba való elküldéséhez. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

## <a name="prerequisites"></a>Előfeltételek

* **Visual Studio**: Express kivételével minden kiadás támogatott.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Microsoft Azure SDK for .NET** 2.7.1-es vagy újabb verzió.  Telepítse a [Webplatform-telepítővel](https://www.microsoft.com/web/downloads/platform.aspx).
* Egy **Data Lake Analytics**-fiók. Fiók létrehozásához lásd: [Az Azure Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>Az Azure Data Lake Tools for Visual Studio 2017 telepítése

Az Azure Data Lake Tools for Visual Studiot a Visual Studio 2017 15.3-as vagy újabb verziói támogatják. Az eszköz az **Adattárolási és -feldolgozási** és az **Azure-fejlesztési** számítási feladat része a Visual Studio telepítőjében. A Visual Studio telepítésének részeként engedélyezze a két számítási feladat egyikét.  

Engedélyezze a **adattárolási és feldolgozási** számítási feladat látható módon: ![Adattárolási és -feldolgozási számítási feladat engedélyezése](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

Engedélyezze a **Azure-fejlesztési** számítási feladat látható módon: ![Azure-fejlesztési számítási feladat engedélyezése](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Az Azure Data Lake Tools for Visual Studio 2013 és 2015 telepítése

Töltse le és telepítse az Azure Data Lake Tools for Visual Studio eszközt [a letöltőközpontból](https://aka.ms/adltoolsvs). A telepítést követően vegye figyelembe a következőket:
* A **Kiszolgálókezelő** > **Azure** csomópont tartalmaz egy **Data Lake Analytics** csomópontot. 
* Az **Eszközök** menü tartalmaz egy **Data Lake** elemet.


## <a name="next-steps"></a>További lépések
* A diagnosztikai információk naplózása: [Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](data-lake-analytics-diagnostic-logs.md).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Webhelynaplók elemzése az Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md).
* A vertex végrehajtási nézet használata, lásd: [a Vertex végrehajtási nézet használata a Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
