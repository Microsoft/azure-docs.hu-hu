---
title: Az Azure Data Lake Tools for Visual Studio telepítése
description: Ez a cikk a Visual studióhoz készült Azure Data Lake-eszközök telepítését ismerteti.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: cb0183ae229c328588a31c2c0549e7e93fd19b78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019004"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>A Data Lake Tools for Visual Studio telepítése

Megtudhatja, hogyan hozhat létre Azure Data Lake Analytics-fiókokat a Visual Studióval. A [U-SQL-](data-lake-analytics-u-sql-get-started.md) ben megadhatja a feladatokat, és elküldheti a feladatokat a Data Lake Analytics szolgáltatásnak. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

## <a name="prerequisites"></a>Előfeltételek

* **Visual Studio**: Az Express kivételével minden kiadás támogatott.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK for .NET** 2.7.1-es vagy újabb verzió. Telepítse a [Webplatform-telepítővel](https://www.microsoft.com/web/downloads/platform.aspx).
* Egy **Data Lake Analytics** -fiók. Fiók létrehozásához lásd: [Az Azure Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>A Visual Studio 2017 vagy a Visual Studio 2019 Azure Data Lake eszközeinek telepítése

A Visual studióhoz készült Azure Data Lake-eszközök a Visual Studio 2017 15,3-es vagy újabb verzióiban támogatottak. Az eszköz az **adattárolási és-feldolgozási** és az **Azure-fejlesztési** számítási feladatok része. A Visual Studio telepítésének részeként engedélyezze a két számítási feladat egyikét.

Engedélyezze az **adattárolási és-feldolgozási** feladatokat az alábbiak szerint:

![Adattárolási és-feldolgozási munkaterhelés engedélyezése](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Engedélyezze az **Azure-fejlesztési** számítási feladatot az alábbiak szerint:

![Azure-beli fejlesztési munkaterhelés kiválasztása](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Az Azure Data Lake Tools for Visual Studio 2013 és 2015 telepítése

Töltse le és telepítse [a Visual studióhoz készült Microsoft Azure Data Lake és stream Analytics eszközöket ](https://aka.ms/adltoolsvs). A telepítés után a Visual Studio a következő módosításokat hajtja végre:

* A **Server Explorer**  >  **Azure** -csomópontja **Data Lake Analytics** csomópontot tartalmaz.
* Az **Eszközök** menü tartalmaz egy **Data Lake** elemet.

## <a name="next-steps"></a>Következő lépések

* A diagnosztikai információk naplózásához tekintse meg [a Azure Data Lake Analytics diagnosztikai naplóinak elérését](data-lake-analytics-diagnostic-logs.md)ismertető témakört.
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Webhelynaplók elemzése az Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md).
* A csúcspont-végrehajtási nézet használatához lásd: [a Data Lake Tools for Visual Studio csúcs-végrehajtási nézetének használata](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
