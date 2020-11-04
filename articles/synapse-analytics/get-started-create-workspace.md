---
title: 'Gyors útmutató: az első lépések – szinapszis-munkaterület létrehozása'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy szinapszis-munkaterületet, egy dedikált SQL-készletet és egy kiszolgáló nélküli Apache Spark készletet.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 6e1eeba99e3ad98aa0fee2e6709bb817ff829ed9
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93304755"
---
# <a name="creating-a-synapse-workspace"></a>Szinapszis-munkaterület létrehozása

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy szinapszis-munkaterületet, egy dedikált SQL-készletet és egy kiszolgáló nélküli Apache Spark készletet. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseinek elvégzéséhez hozzáféréssel kell rendelkeznie egy olyan erőforráscsoporthoz, amelyhez hozzá van rendelve a **tulajdonosi** szerepkör. Hozza létre a szinapszis munkaterületet ebben az erőforráscsoportban.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Szinapszis-munkaterület létrehozása a Azure Portalban

1. Nyissa meg a [Azure Portal](https://portal.azure.com), és a legfelső szintű keresés a **szinapszisban**.
1. A keresési eredmények között, a **szolgáltatások** területen válassza az **Azure szinapszis Analytics (munkaterületek előzetes verzió)** lehetőséget.
1. Munkaterület létrehozásához válassza a **Hozzáadás** lehetőséget.
1. Az **alapvető beállítások** területen adja meg a kívánt **előfizetést** , **erőforráscsoportot** , **régiót** , majd válassza ki a munkaterület nevét. Ebben az oktatóanyagban a **sajátmunkaterület** -t fogjuk használni.
1. Munkaterület létrehozásához szükség van egy ADLSGEN2-fiókra és egy tárolóra a fiókban. A legegyszerűbb lehetőség, hogy újat hozzon létre. Ha újra szeretné használni a meglévőket, néhány további konfigurálást is végre kell hajtania. 
    1. A szinapszis munkaterület ezt a tárolót fogja használni a Spark-naplók és a Spark-táblákra vonatkozó adattárolás alapértelmezett helyeként.
1. 1. lehetőség új ADLSGEN2-fiók létrehozása 
    1. Navigáljon a **2. generációs Data Lake Storage kiválasztásához**. 
    1. Kattintson az **új létrehozása** elemre, és nevezze el **contosolake**.
    1. Kattintson a **fájlrendszer** elemre, és nevezze el a **felhasználókat**. Ekkor létrejön egy **felhasználó** nevű tároló.
1. 2. lehetőség meglévő ADLSGEN2-fiók használatával. Tekintse meg a jelen dokumentum alján található **ADLSGEN2-fiók előkészítésével** kapcsolatos utasításokat.
1. Az Azure szinapszis-munkaterülete ezt a Storage-fiókot fogja használni az "elsődleges" Storage-fiók és a munkaterület-adattárolási tároló számára. A munkaterület Apache Spark táblákban tárolja az adattárakat. Egy **/Synapse/workspacename** nevű mappában tárolja a Spark-alkalmazás naplóit.
1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A munkaterület pár percen belül elkészül.

## <a name="open-synapse-studio"></a>A szinapszis Studio megnyitása

Az Azure szinapszis-munkaterület létrehozása után kétféleképpen nyithatja meg a szinapszis Studio alkalmazást:

* Nyissa meg a szinapszis munkaterületet a [Azure Portal](https://portal.azure.com). Az **Áttekintés** szakasz tetején válassza a **szinapszis Studio elindítása** lehetőséget.
* Lépjen a `https://web.azuresynapse.net` munkaterületre, és jelentkezzen be.

## <a name="create-a-dedicated-sql-pool"></a>Dedikált SQL-készlet létrehozása

1. A szinapszis Studióban a bal oldali ablaktáblán válassza az **Manage**  >  **SQL-készletek** kezelése lehetőséget.
1. Válassza az **új** lehetőséget, és adja meg a következő beállításokat:

    |Beállítás | Ajánlott érték | 
    |---|---|---|
    |**SQL-készlet neve**| **SQLDB1**|
    |**Teljesítményszint**|**DW100C**|
    |||

1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A dedikált SQL-készlet néhány percen belül elkészül. A dedikált SQL-készlet egy dedikált SQL Pool-adatbázishoz van társítva, amely más néven **SQLDB1**.

A dedikált SQL-készletek számlázható erőforrásokat használnak, amíg aktívak. A készletet később is szüneteltetheti a költségek csökkentése érdekében.

## <a name="create-a-serverless-apache-spark-pool"></a>Kiszolgáló nélküli Apache Spark készlet létrehozása

1. A szinapszis Studióban a bal oldali ablaktáblán válassza a **Manage**  >  **Apache Spark készletek** kezelése lehetőséget.
1. Válassza az **új** lehetőséget, és adja meg a következő beállításokat:

    |Beállítás | Ajánlott érték | 
    |---|---|---|
    |**Apache Spark készlet neve**|**Spark1**
    |**Csomópont mérete**| **Kicsi**|
    |**Csomópontok száma**| Állítsa be a minimumot 3 értékre, a maximumot 3 értékre|

1. Válassza a **Felülvizsgálat + létrehozás** > **Létrehozás** lehetőséget. A Apache Spark-készlet néhány másodpercen belül elkészül.

Amikor Spark-tevékenységet hajt végre az Azure Szinapszisban, meg kell adnia a használni kívánt Spark-készletet. A készlet azt jelzi, hogy az Azure szinapszis hány Spark-erőforrást használ. Ön csak a felhasznált erőforrásokért fizet. Ha aktívan abbahagyja a készlet használatát, az erőforrások automatikusan időtúllépést és újrahasznosítást végeznek.

> [!NOTE]
> A Spark-adatbázisok a Spark-készletektől függetlenül jönnek létre. A munkaterületnek mindig van egy **alapértelmezett** nevű Spark-adatbázisa. További Spark-adatbázisokat is létrehozhat.

## <a name="the-serverless-sql-pool"></a>A kiszolgáló nélküli SQL-készlet

Minden munkaterülethez **beépített, beépített** készlet tartozik. Ez a készlet nem törölhető. A kiszolgáló nélküli SQL-készlet lehetővé teszi az SQL használatát anélkül, hogy létre kellene hoznia egy kiszolgáló nélküli SQL-készletet az Azure Szinapszisban.


A más típusú készletektől eltérően a kiszolgáló nélküli SQL-készlet számlázása a lekérdezés futtatásához beolvasott adatmennyiségen alapul, nem a lekérdezés végrehajtásához használt erőforrások számától.

* A kiszolgáló nélküli SQL-készlet saját adatbázisokkal rendelkezik, amelyek a többi kiszolgáló nélküli SQL-készlettől függetlenül léteznek.
* A munkaterületek mindig pontosan egy **beépített SQL-** készlettel rendelkeznek.

## <a name="preparing-a-adlsgen2-storage-account"></a>ADLSGEN2 Storage-fiók előkészítése

### <a name="perform-the-following-steps-before-you-create-your-workspace"></a>A munkaterület létrehozása előtt hajtsa végre a következő lépéseket

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Navigáljon a meglévő Storage-fiókjához
1. A bal oldali panelen válassza a **hozzáférés-vezérlés (iam)** lehetőséget. 
1. Rendelje hozzá a következő szerepköröket, vagy győződjön meg róla, hogy már hozzá van rendelve:
    * Rendelje hozzá magát a **tulajdonosi** szerepkörhöz.
    * Rendeljen hozzá saját magát a **Storage blob-adat tulajdonosi** szerepköréhez.
1. A bal oldali ablaktáblán válassza a **tárolók** lehetőséget, és hozzon létre egy tárolót.
1. Megadhatja a tároló nevét. Ebben a dokumentumban a  **felhasználók** nevet használjuk.
1. Fogadja el az alapértelmezett **nyilvános hozzáférési szint** beállítást, majd válassza a **Létrehozás** lehetőséget.

### <a name="perform-the-following-steps-after-you-create-your-workspace"></a>A munkaterület létrehozása után végezze el a következő lépéseket

Konfigurálja a Storage-fiókhoz való hozzáférést a munkaterületről. Előfordulhat, hogy az Azure szinapszis-munkaterülethez tartozó felügyelt identitások már hozzáférnek a Storage-fiókhoz. Az alábbi lépéseket követve győződjön meg arról, hogy:

1. Nyissa meg a munkaterülethez kiválasztott [Azure Portal](https://portal.azure.com) és elsődleges Storage-fiókot.
1. A bal oldali panelen válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
1. Rendelje hozzá a következő szerepköröket, vagy győződjön meg arról, hogy már hozzá van rendelve. Ugyanazt a nevet használjuk a munkaterület-identitáshoz és a munkaterület nevéhez.
    * A Storage- **blob adatközreműködői** szerepköréhez a Storage-fiókban rendeljen **sajátmunkaterület** a munkaterület-identitáshoz.
    * Rendelje hozzá a **sajátmunkaterület** a munkaterület neveként.
1. Válassza a **Mentés** lehetőséget.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Elemzés dedikált SQL-készlet használatával](get-started-analyze-sql-pool.md)
