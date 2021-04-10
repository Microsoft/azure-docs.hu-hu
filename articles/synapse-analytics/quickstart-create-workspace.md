---
title: 'Rövid útmutató: szinapszis-munkaterület létrehozása'
description: Hozzon létre egy szinapszis-munkaterületet az útmutató lépéseit követve.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: d38f1f294f60b73e8f1e69169a75333eb175c9f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600156"
---
# <a name="quickstart-create-a-synapse-workspace"></a>Rövid útmutató: szinapszis-munkaterület létrehozása
Ez a rövid útmutató ismerteti, hogyan hozhat létre egy Azure szinapszis-munkaterületet a Azure Portal használatával.

## <a name="create-a-synapse-workspace"></a>Synapse-munkaterület létrehozása

1. Nyissa meg a [Azure Portal](https://portal.azure.com), és a legfelső szintű keresés a **szinapszisban**.
1. A keresési eredmények között, a **szolgáltatások** területen válassza az **Azure szinapszis Analytics** lehetőséget.
1. Munkaterület létrehozásához válassza a **Hozzáadás** lehetőséget.
1. Az **alapvető beállítások** lapon adjon meg egy egyedi nevet a munkaterületnek. Ebben a dokumentumban a **mysworkspace** -t fogjuk használni
1. Munkaterület létrehozásához ADLSGEN2-fiókra van szükség. A legegyszerűbb választás egy új létrehozása. Ha újra szeretné használni a meglévőket, néhány további konfigurálást is végre kell hajtania. 
1. 1. lehetőség új ADLSGEN2-fiók létrehozása 
    1. A **2. generációs Data Lake Storage kiválasztása** területen kattintson az **új létrehozása** elemre, és nevezze el **contosolake**.
    1. A **2. generációs Data Lake Storage kiválasztása** területen kattintson a **fájlrendszer** elemre, és nevezze el a **felhasználók** nevet.
1. 2. lehetőség: a **Storage-fiók előkészítési** utasításai a dokumentum alján találhatók.
1. Az Azure szinapszis-munkaterülete ezt a Storage-fiókot fogja használni az "elsődleges" Storage-fiók és a munkaterület-adattárolási tároló számára. A munkaterület Apache Spark táblákban tárolja az adattárakat. Egy **/Synapse/workspacename** nevű mappában tárolja a Spark-alkalmazás naplóit.
1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A munkaterület pár percen belül elkészül.

> [!NOTE]
> Az Azure szinapszis-munkaterület létrehozása után nem fogja tudni áthelyezni a munkaterületet egy másik Azure Active Directory bérlőbe. Ha ezt az előfizetések migrálása vagy más műveletek útján mégis megkísérli, elveszítheti a hozzáférést a munkaterületen belüli összetevőkhöz.
> Emellett jelenleg nem hozhat létre a szinapszis Analytics-munkaterületet egy [felhőalapú megoldás-szolgáltatói (CSP)](/partner-center/csp-overview) előfizetésben.

## <a name="open-synapse-studio"></a>A szinapszis Studio megnyitása

Az Azure szinapszis-munkaterület létrehozása után kétféleképpen nyithatja meg a szinapszis Studio alkalmazást:

* Nyissa meg a szinapszis munkaterületet a [Azure Portal](https://portal.azure.com). Az **Áttekintés** szakasz tetején válassza a **szinapszis Studio elindítása** lehetőséget.
* Lépjen a `https://web.azuresynapse.net` munkaterületre, és jelentkezzen be.

## <a name="prepare-an-existing-storage-account-for-use-with-azure-synapse-analytics"></a>Meglévő Storage-fiók előkészítése az Azure szinapszis Analytics szolgáltatással való használatra

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Nyisson meg egy meglévő ADLSGEN2 Storage-fiókot
1. A bal oldali panelen válassza a **hozzáférés-vezérlés (iam)** lehetőséget. Ezután rendelje hozzá a következő szerepköröket, vagy győződjön meg arról, hogy már hozzá van rendelve:
    * Rendelje hozzá magát a **tulajdonosi** szerepkörhöz.
    * Rendeljen hozzá saját magát a **Storage blob-adat tulajdonosi** szerepköréhez.
1. A bal oldali ablaktáblán válassza a **tárolók** lehetőséget, és hozzon létre egy tárolót.
1. Megadhatja a tároló nevét. Ebben a dokumentumban a Container **Users** nevet fogjuk megkeresni.
1. Fogadja el az alapértelmezett **nyilvános hozzáférési szint** beállítást, majd válassza a **Létrehozás** lehetőséget.

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>A Storage-fiókhoz való hozzáférés konfigurálása a munkaterületről

Előfordulhat, hogy az Azure szinapszis-munkaterülethez tartozó felügyelt identitások már hozzáférnek a Storage-fiókhoz. Az alábbi lépéseket követve győződjön meg arról, hogy:

1. Nyissa meg a munkaterülethez kiválasztott [Azure Portal](https://portal.azure.com) és elsődleges Storage-fiókot.
1. A bal oldali panelen válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
1. Rendelje hozzá a következő szerepköröket, vagy győződjön meg arról, hogy már hozzá van rendelve. Ugyanazt a nevet használjuk a munkaterület-identitáshoz és a munkaterület nevéhez.
    * A Storage- **blob adatközreműködői** szerepköréhez a Storage-fiókban rendeljen **sajátmunkaterület** a munkaterület-identitáshoz.
    * Rendelje hozzá a **sajátmunkaterület** a munkaterület neveként.

1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

* [Dedikált SQL-készlet létrehozása](quickstart-create-sql-pool-studio.md) 
* [Kiszolgáló nélküli Apache Spark készlet létrehozása](quickstart-create-apache-spark-pool-portal.md)
* [Kiszolgáló nélküli SQL-készlet használata](quickstart-sql-on-demand.md)