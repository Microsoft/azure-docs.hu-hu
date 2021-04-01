---
title: Azure Data Lake Analytics kezelése a Azure Portal használatával
description: Ez a cikk azt ismerteti, hogyan használható a Azure Portal a Data Lake Analytics fiókok, adatforrások, felhasználók és & feladatok kezelésére.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: c1057478d9ae7d63bef9477a0b1297fa2c507ffa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218425"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Az Azure Data Lake Analytics kezelése az Azure Portal használatával
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ez a cikk ismerteti, hogyan kezelheti Azure Data Lake Analytics fiókokat, adatforrásokat, felhasználókat és feladatokat a Azure Portal használatával.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Data Lake Analytics-fiókok kezelése

### <a name="create-an-account"></a>Fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson **az erőforrás létrehozása**  >  **Intelligence + Analytics**  >  **Data Lake Analytics** elemre.
3. Adja meg az alábbi elemek értékeit: 
   1. **Name (név**): az Data Lake Analytics fiók neve.
   2. **Előfizetés**: a fiókhoz használt Azure-előfizetés.
   3. **Erőforráscsoport**: az az Azure-erőforráscsoport, amelyben létre kell hozni a fiókot. 
   4. **Hely**: az Data Lake Analytics-fiókhoz tartozó Azure-adatközpont. 
   5. **Data Lake Store**: a Data Lake Analytics-fiókhoz használandó alapértelmezett tároló. A Azure Data Lake Store fióknak és a Data Lake Analytics fióknak ugyanazon a helyen kell lennie.
4. Kattintson a **Létrehozás** lehetőségre. 

### <a name="delete-a-data-lake-analytics-account"></a>Data Lake Analytics fiók törlése

Data Lake Analytics fiók törlése előtt törölje az alapértelmezett Data Lake Store-fiókját.

1. A Azure Portal lépjen a Data Lake Analytics-fiókra.
2. Kattintson a **Törlés** gombra.
3. Írja be a fiók nevét.
4. Kattintson a **Törlés** gombra.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Adatforrások kezelése

A Data Lake Analytics a következő adatforrásokat támogatja:

* Data Lake Storage
* Azure Storage

A Adatkezelő használatával böngészheti az adatforrásokat, és alapszintű fájlkezelési műveleteket hajthat végre. 

### <a name="add-a-data-source"></a>Adatforrás hozzáadása

1. A Azure Portal lépjen a Data Lake Analytics-fiókra.
2. Kattintson **az adatforrások** elemre.
3. Kattintson **az adatforrás hozzáadása** lehetőségre.
    
   * Data Lake Store fiók hozzáadásához szüksége lesz a fiók nevére, és hozzáfér a fiókhoz, hogy le tudja kérdezni.
   * Az Azure Blob Storage hozzáadásához szüksége lesz a Storage-fiókra és a fiók kulcsára. A megkereséséhez nyissa meg a Storage-fiókot a portálon.

## <a name="set-up-firewall-rules"></a>Tűzfalszabályok beállítása

A Data Lake Analytics a hálózati szinten lévő Data Lake Analytics-fiókhoz való hozzáférés további zárolására is használható. Engedélyezheti a tűzfalat, megadhat egy IP-címet, vagy megadhat egy IP-címtartományt a megbízható ügyfelek számára. Miután engedélyezte ezeket a mértékeket, csak a megadott tartományon belüli IP-címmel rendelkező ügyfelek csatlakozhatnak az áruházhoz.

Ha más Azure-szolgáltatások, például Azure Data Factory vagy virtuális gépek, akkor kapcsolódjon a Data Lake Analytics-fiókhoz, és győződjön **meg** arról, hogy az **Azure-szolgáltatások engedélyezése** be van kapcsolva. 

### <a name="set-up-a-firewall-rule"></a>Tűzfalszabály beállítása

1. A Azure Portal lépjen a Data Lake Analytics-fiókra.
2. A bal oldali menüben kattintson a **tűzfal** elemre.

## <a name="add-a-new-user"></a>Új felhasználó hozzáadása

A **felhasználó hozzáadása varázslóval** egyszerűen kiépítheti az új Data Lake felhasználókat.

1. A Azure Portal lépjen a Data Lake Analytics-fiókra.
2. A bal oldalon **első lépések** alatt kattintson a **felhasználó hozzáadása varázsló** elemre.
3. Válasszon ki egy felhasználót, majd kattintson a **kiválasztás** elemre.
4. Válasszon ki egy szerepkört, majd kattintson a **kiválasztás** elemre. Ha új fejlesztőt szeretne beállítani Azure Data Lake használatára, válassza ki a **Data Lake Analytics fejlesztői** szerepkört.
5. Válassza ki a U-SQL-adatbázisokhoz tartozó hozzáférés-vezérlési listákat (ACL-eket). Ha elégedett a lehetőségeivel, kattintson a **kiválasztás** elemre.
6. Válassza ki az ACL-eket a fájlokhoz. Az alapértelmezett tároló esetében ne módosítsa a "/" gyökérmappa és a/System mappához tartozó ACL-eket. Kattintson a **Kiválasztás** elemre.
7. Tekintse át a kiválasztott módosításokat, majd kattintson a **Futtatás** gombra.
8. A varázsló befejezése után kattintson a **kész** gombra.

## <a name="manage-azure-role-based-access-control"></a>Azure szerepköralapú hozzáférés-vezérlés kezelése

A többi Azure-szolgáltatáshoz hasonlóan az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével szabályozhatja, hogy a felhasználók hogyan használják a szolgáltatást.

A standard Azure-szerepkörök a következő képességekkel rendelkeznek:
* **Tulajdonos**: feladatokat küldhet be, figyelheti a feladatokat, megszakíthatja a feladatokat bármely felhasználótól, és konfigurálhatja a fiókot.
* **Közreműködő**: feladatokat küldhet be, figyelheti a feladatokat, megszakíthatja a feladatokat bármely felhasználótól, és konfigurálhatja a fiókot.
* **Olvasó**: nyomon követheti a feladatokat.

A Data Lake Analytics fejlesztői szerepkör használatával engedélyezheti a U-SQL-fejlesztők számára a Data Lake Analytics szolgáltatás használatát. A Data Lake Analytics fejlesztői szerepkört a következőre használhatja:
* Feladatok elküldése.
* A feladat állapotának figyelése és a felhasználók által benyújtott feladatok előrehaladásának monitorozása.
* Tekintse meg az U-SQL-szkripteket bármely felhasználó által küldött feladatokból.
* Csak a saját feladatok megszakítása.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Felhasználók vagy biztonsági csoportok hozzáadása egy Data Lake Analytics-fiókhoz

1. A Azure Portal lépjen a Data Lake Analytics-fiókra.
2. Kattintson a **hozzáférés-vezérlés (iam)**  >  **szerepkör-hozzárendelés hozzáadása** elemre.
3. Válasszon egy szerepkört.
4. Adjon hozzá egy felhasználót.
5. Kattintson az **OK** gombra.

>[!NOTE]
>Ha egy felhasználónak vagy biztonsági csoportnak feladatokat kell elküldenie, akkor is engedélyre van szüksége az áruház fiókján. További információ: [Data Lake Store tárolt adatok védelme](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Feladatok kezelése

### <a name="submit-a-job"></a>Feladatok elküldése

1. A Azure Portal lépjen a Data Lake Analytics-fiókra.

2. Kattintson az **új feladatok** lehetőségre. Az egyes feladatokhoz konfigurálja a következőt:

    1. **Feladatok neve**: a feladattípus neve.
    2. **Prioritás**: az alacsonyabb számok magasabb prioritással rendelkeznek. Ha két feladat van várólistán, az alacsonyabb prioritású érték fut először.
    3. **Párhuzamosság**: a feladatokhoz fenntartott számítási folyamatok maximális száma.

3. Kattintson a **Feladat elküldése** elemre.

### <a name="monitor-jobs"></a>Feladatok figyelése

1. A Azure Portal lépjen a Data Lake Analytics-fiókra.
2. Kattintson **az összes feladat megtekintése** elemre. Megjelenik a fiók összes aktív és legutóbb befejezett feladatának listája.
3. A **szűrő** lehetőségre kattintva megkeresheti a feladatokat az **időtartomány**, a **feladat neve** és a **Szerző** értékei alapján. 

### <a name="monitoring-pipeline-jobs"></a>Folyamat-feladatok figyelése
A folyamat részét képező feladatok együttesen, általában egymás után, egy adott forgatókönyv megvalósítására szolgálnak. Például rendelkezhet egy olyan folyamattal, amely megtisztítja, kinyeri, átalakítja, összesíti a vásárlói adatok felhasználását. A folyamat-feladatok a feladat elküldésekor a "folyamat" tulajdonság használatával azonosíthatók. Az ADF V2 használatával ütemezett feladatok automatikusan feltöltve lesznek a tulajdonsággal. 

A folyamatok részét képező U-SQL-feladatok listájának megtekintése: 

1. A Azure Portal lépjen a Data Lake Analytics-fiókok elemre.
2. Kattintson a **Job bepillantások** elemre. A "minden feladat" lap alapértelmezésben megjelenik, amely megjeleníti a futó, a várólistán lévő és a Befejezett feladatok listáját.
3. Kattintson a **folyamat feladatai** fülre. A folyamat-feladatok listája az egyes folyamatokhoz tartozó összesített statisztikával együtt jelenik meg.

### <a name="monitoring-recurring-jobs"></a>Ismétlődő feladatok figyelése
Az ismétlődő feladatok olyanok, amelyek ugyanazt az üzleti logikát használják, de minden egyes futtatásakor különböző bemeneti adatokat használnak. Ideális esetben az ismétlődő feladatok mindig sikeresek lesznek, és viszonylag stabil végrehajtási idővel rendelkeznek; ezen viselkedések monitorozásával biztosítható, hogy a feladatok kifogástalanok legyenek. Az ismétlődő feladatok az "Ismétlődés" tulajdonság használatával azonosíthatók. Az ADF V2 használatával ütemezett feladatok automatikusan feltöltve lesznek a tulajdonsággal.

Az ismétlődő U-SQL-feladatok listájának megtekintése: 

1. A Azure Portal lépjen a Data Lake Analytics-fiókok elemre.
2. Kattintson a **Job bepillantások** elemre. A "minden feladat" lap alapértelmezésben megjelenik, amely megjeleníti a futó, a várólistán lévő és a Befejezett feladatok listáját.
3. Kattintson az **ismétlődő feladatok** fülre. Az ismétlődő feladatok listája az egyes ismétlődő feladatokhoz tartozó összesített statisztikával együtt jelenik meg.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Azure Data Lake Analytics kezelése Azure PowerShell használatával](data-lake-analytics-manage-use-powershell.md)
* [Az Azure Data Lake Analytics kezelése szabályzatokkal](data-lake-analytics-account-policies.md)
