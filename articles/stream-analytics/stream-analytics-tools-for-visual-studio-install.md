---
title: A Visual studióhoz készült Azure Stream Analytics-eszközök beállítása
description: Ez a cikk a telepítési követelményeket és a Visual studióhoz készült Azure Stream Analytics eszközök beállítását ismerteti.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: fa3990d2239da71fb27f4c9a06699f758fcfe7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354367"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>A Visual studióhoz készült Azure Stream Analytics-eszközök telepítése

A Visual Studio 2019 és a Visual Studio 2017 támogatja a Azure Data Lake és Stream Analytics eszközöket. Ez a cikk az eszközök telepítését és eltávolítását ismerteti.

További információ az eszközök használatáról: gyors útmutató [: Azure stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Telepítés

A Visual Studio Enterprise (Ultimate/prémium), Professional és Community kiadások támogatják az eszközöket. Az Express Edition és a Visual Studio for Mac nem támogatja őket.

A Visual Studio 2019 használatát javasoljuk.

### A Visual Studio 2019-es és 2017-es telepítése<a name="recommended-visual-studio-2019-and-2017"></a>

A Azure Data Lake és Stream Analytics eszközök az Azure- **fejlesztési** és az **adattárolási és-feldolgozási számítási** feladatok részét képezik. A telepítés során engedélyezze a két számítási feladat valamelyikét. Ha a Visual Studio már telepítve van, válassza az **eszközök** > **eszközök és szolgáltatások beolvasása** lehetőséget a számítási feladatok hozzáadásához.

Töltse le a [Visual studio 2019 (előzetes verzió 2 vagy újabb verzió) vagy a Visual studio 2017 (15,3 vagy újabb)](https://www.visualstudio.com/) verziót, és kövesse a telepítendő utasításokat.

Válassza ki az **adattárolási és-feldolgozási** munkaterhelést az alábbiak szerint:

![Adattárolási és-feldolgozási munkaterhelés van kiválasztva](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Válassza ki az **Azure-fejlesztési** számítási feladatot az alábbiak szerint:

![Az Azure-fejlesztési munkaterhelés ki van választva](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

A munkaterhelés hozzáadása után frissítse az eszközöket. Ez az eljárás a Visual Studio 2019-re vonatkozik:

1. Válassza a **bővítmények** > a **Bővítmények kezelése**lehetőséget.

1. A **Bővítmények kezelése**területen válassza a **frissítések** lehetőséget, és válassza ki **Azure Data Lake és stream Analytics eszközöket**.

1. A legújabb bővítmény telepítéséhez válassza a **frissítés** lehetőséget.

![Visual Studio-bővítmények és-frissítések](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### A Visual Studio 2015-es és 2013-es telepítése<a name="visual-studio-2015-2013"></a>

A Visual Studio Enterprise (Ultimate/prémium), Professional és Community kiadások támogatják az eszközöket. Az Express kiadás nem támogatja őket.

* Telepítse a Visual Studio 2015 vagy a Visual Studio 2013 4. frissítését.
* Telepítse a .NET-es vagy újabb verziójú Microsoft Azure SDK-t a [webplatform-telepítő](https://www.microsoft.com/web/downloads/platform.aspx)használatával.
* Telepítse [a Microsoft Azure Data Lake és a Visual studióhoz készült stream Analytics eszközöket](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## Frissítése<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

A Visual Studio 2019 és a Visual Studio 2017 esetében az új verzió emlékeztetője Visual Studio-értesítésként jelenik meg.

A Visual Studio 2015 és a Visual Studio 2013 esetében az eszközök automatikusan keresik az új verziókat. Kövesse az utasításokat a legújabb verzió telepítéséhez.

## <a name="uninstall"></a>Eltávolítás

Azure Data Lake és Stream Analytics eszközöket is eltávolíthat. A Visual Studio 2019 vagy a Visual Studio 2017 esetében válassza az **eszközök** > eszközök **és szolgáltatások beszerzése**lehetőséget. A **módosítás**területen törölje a jelölést **Azure Data Lake és stream Analytics eszközöket**. Az **adattárolási és-feldolgozási** munkaterhelés, illetve az **Azure-fejlesztési** számítási feladatok alatt jelenik meg.

Ha el szeretné távolítani a Visual Studio 2015 vagy a Visual Studio 2013 alkalmazást, lépjen a **vezérlőpult** > **programok és szolgáltatások**elemre. Távolítsa el **Microsoft Azure Data Lake és a Visual studióhoz készült stream Analytics eszközöket**.
