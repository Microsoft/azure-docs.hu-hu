---
title: Azure Data Lake U-SQL-parancsfájlok futtatása a helyi gépen
description: Megtudhatja, hogyan használhatja a Visual studióhoz készült Azure Data Lake-eszközöket a U-SQL-feladatok futtatásához a helyi gépen.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 5f9410cc91174420662bb5efc67c8904b5d5e647
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018987"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>U-SQL-parancsfájlok futtatása a helyi gépen

U-SQL-parancsfájlok fejlesztésekor a parancsfájlok helyi futtatásával időt és költségeket is megtakaríthat. Azure Data Lake Tools for Visual Studio támogatja a U-SQL-parancsfájlok futtatását a helyi gépen. 

## <a name="basic-concepts-for-local-runs"></a>A helyi futtatások alapvető fogalmai

A következő diagramon a helyi Futtatás összetevői láthatók, valamint hogy ezek az összetevők hogyan képezik le a felhőalapú futtatást.

|Összetevő|Helyi futtatás|Felhőbeli Futtatás|
|---------|---------|---------|
|Tárolás|Helyi adatgyökér mappa|Alapértelmezett Azure Data Lake Store fiók|
|Compute|U-SQL helyi futtatási motor|Azure Data Lake Analytics szolgáltatás|
|Környezet futtatása|Munkahelyi könyvtár a helyi gépen|Azure Data Lake Analytics fürt|

A következő szakaszokban további információk találhatók a helyi futtatási összetevőkről.

### <a name="local-data-root-folders"></a>Helyi adatgyökér mappák

A helyi adatgyökér mappa a helyi számítási fiók **helyi tárolója** . A helyi számítógép helyi fájlrendszerének bármely mappája lehet helyi adatgyökér-mappa. Ez ugyanaz, mint egy Data Lake Analytics-fiók alapértelmezett Azure Data Lake Store fiókja. Más adatgyökér-mappára való váltás ugyanúgy történik, mint egy másik alapértelmezett áruházbeli fiókra való váltás. 

Az adatgyökér mappát a következőképpen kell használni:
- Tárolja a metaadatokat. Ilyenek például az adatbázisok, táblák, táblázat értékű függvények és szerelvények.
- Keresse meg a bemeneti és kimeneti útvonalakat, amelyek a U-SQL-parancsfájlok relatív elérési útjaiként vannak meghatározva. A relatív elérési utak használatával egyszerűbbé válik az U-SQL-parancsfájlok üzembe helyezése az Azure-ban.

### <a name="u-sql-local-run-engines"></a>U-SQL helyi futtatású motorok

A U-SQL helyi futtatási motorja a U-SQL-feladatok **helyi számítási fiókja** . A felhasználók a Azure Data Lake Tools for Visual Studio használatával helyileg futtathatják az U-SQL-feladatokat. A helyi futtatásokat a Azure Data Lake U-SQL SDK parancssori felület és a programozási felületek is támogatják. További információ a [Azure Data Lake U-SQL SDK-](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)ról.

### <a name="working-directories"></a>Munkakönyvtárak

Ha U-SQL-parancsfájlt futtat, a fordítási eredmények gyorsítótárazásához, a naplók futtatásához és más függvények elvégzéséhez szükség van egy munkakönyvtári mappára. A Visual studióhoz készült Azure Data Lake Tools esetében a munkakönyvtár a U-SQL projekt munkakönyvtára. Itt található: `<U-SQL project root path>/bin/debug>` . A munkakönyvtárat minden új Futtatás indításakor megtisztítjuk.

## <a name="local-runs-in-microsoft-visual-studio"></a>Helyi futtatások a Microsoft Visual Studióban

A Visual studióhoz készült Azure Data Lake eszközök beépített helyi futtatási motorral rendelkeznek. Az eszközök helyi számítási fiókként Surface a motort. Ha helyileg szeretne futtatni egy U-SQL-parancsfájlt, válassza ki a **helyi gép** vagy a **helyi projekt** fiókot a parancsfájl szerkesztői margója legördülő menüjében. Ezután válassza a **Submit (Küldés**) lehetőséget.

![U-SQL-szkript küldése helyi fiókba](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Helyi Futtatás helyi számítógép-fiókkal

A helyi számítógép fiók egy helyi, helyi adatgyökér **-** mappával rendelkező megosztott helyi számítási fiók, amely a helyi tároló fiókja. Alapértelmezés szerint az adatgyökér mappa a következő helyen található: **C:\Users \<username> \AppData\Local\USQLDataRoot**. Emellett az **eszközökön**  >  **Data Lake**  >  **beállítások és beállítások** segítségével is konfigurálható.

![Helyi adatgyökér mappa konfigurálása](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Helyi futtatáshoz egy U-SQL-projekt szükséges. A u-SQL projekt munkakönyvtára a U-SQL helyi Futtatás munkakönyvtárához használatos. A fordítási eredmények, a futtatási naplók és az egyéb feladatok futtatásával kapcsolatos fájlok a helyi futtatás során jönnek létre és tárolódnak a munkakönyvtár mappájában. Minden alkalommal, amikor Újrafuttatja a parancsfájlt, a munkakönyvtár összes fájlját megtisztítja és újra létrehozza.

## <a name="local-runs-with-a-local-project-account"></a>Helyi Futtatás helyi Project-fiókkal

A **helyi Project** -fiók egy elkülönített helyi adatgyökérkönyvtárral rendelkező projekt elkülönített helyi számítási fiókja. A Visual Studióban Megoldáskezelő megnyíló összes aktív U-SQL-projekthez tartozik egy megfelelő `(Local-project: <project name>)` fiók. A fiókok a Visual Studióban és a U-SQL parancsfájl-szerkesztő margóján is megjelennek.  

A **helyi projekt** fiók tiszta és elszigetelt fejlesztési környezetet biztosít. A helyi **számítógép** fiók rendelkezik egy megosztott helyi adatgyökérkönyvtárral, amely a metaadatokat és a bemeneti és kimeneti adatokat tárolja az összes helyi feladathoz. Egy **helyi Project-** fiók azonban létrehoz egy ideiglenes helyi adatgyökérkönyvtárat egy u-SQL-projekt munkakönyvtárában, minden alkalommal, amikor egy u-SQL-parancsfájl fut. Ezt az ideiglenes adatgyökér-mappát a rendszer Újraépítés vagy újrafuttatás esetén megtisztítja. 

A U-SQL-projekt az elkülönített helyi futtatási környezetet egy projekt-referencián és tulajdonságon keresztül kezeli. Az U-SQL-parancsfájlok bemeneti adatforrásait a projektben és a hivatkozott adatbázis-környezetekben is konfigurálhatja.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Helyi Project-fiók bemeneti adatforrásának kezelése 

A U-SQL-projekt létrehoz egy helyi adatgyökér-mappát, és beállítja a **helyi Project-** fiókhoz tartozó összes adatát. A rendszer a U-SQL projekt munkakönyvtárában minden alkalommal megtisztítja és újra létrehozza az ideiglenes adatgyökér mappát, amikor Újraépítés és helyi Futtatás történik. A rendszer a U-SQL-projekt által konfigurált összes adatforrást a helyi feladatok futtatása előtt átmásolja erre az ideiglenes helyi adatgyökér mappájába. 

Beállíthatja az adatforrások gyökerét. Kattintson a jobb gombbal az **U-SQL Project**  >  **Property**  >  **test adatforrásra**. Ha U-SQL-parancsfájlt futtat egy **helyi projekt** fiókon, a rendszer a **teszt adatforrás** mappában található összes fájlt és almappát átmásolja az ideiglenes helyi adatgyökér mappába. Az almappákban található fájlok is szerepelnek. A helyi feladatok futtatása után a kimeneti eredmények a projekt munkakönyvtárának ideiglenes helyi adatok gyökérkönyvtárában is megtalálhatók. A rendszer ezt a kimenetet törli és megtisztítja a projekt újraépítése és tisztítása során. 

![Projekt tesztelési adatforrásának konfigurálása](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>**Helyi projekthez** tartozó hivatkozott adatbázis-környezet kezelése 

Ha egy U-SQL-lekérdezés U-SQL Database-objektumokat használ vagy kérdez le lekérdezéseket, az adatbázis-környezeteket helyileg kell elvégeznie az U-SQL-parancsfájl helyi futtatása előtt. **Helyi projekt** fiók esetében az u-SQL Database-függőségeket az u-SQL-projekt referenciái kezelhetik. U-SQL-projektre mutató hivatkozásokat adhat hozzá az u-SQL-projekthez. Mielőtt a U-SQL-parancsfájlokat egy **helyi projekt** fiókon futtatja, a rendszer az összes hivatkozott adatbázist telepíti az ideiglenes helyi adatgyökér mappájába. És minden egyes Futtatás esetén az ideiglenes adatgyökér mappa friss elkülönített környezetként van megtisztítva.

Tekintse meg a kapcsolódó cikket:
* Ismerje meg, hogyan kezelheti a U-SQL Database-definíciókat és-referenciákat az [u-SQL Database-projektekben](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>A **helyi** és a **helyi-Project** fiókok közötti különbség

A **helyi számítógép** fiók szimulál egy Azure Data Lake Analytics fiókot a felhasználók helyi gépén. A Azure Data Lake Analytics-fiókkal megegyező felhasználói élményben osztoznak. A **helyi Project-** fiók felhasználóbarát helyi fejlesztési környezetet biztosít. Ezzel a környezettel a felhasználók a parancsfájlok helyi futtatása előtt telepíthetik az adatbázis-referenciákat és a bemeneti adatokat. A **helyi számítógép** fiók olyan közös állandó környezetet biztosít, amely az összes projekten keresztül érhető el. A **helyi projekt** fiók minden projekt esetében elkülönített fejlesztési környezetet biztosít. Minden Futtatás esetén frissül. A **helyi Project-** fiók gyorsabb fejlesztési élményt nyújt az új módosítások gyors alkalmazásával.

A **helyi gépek** és a **helyi** fiókok közötti különbségek a következő táblázatban láthatók:

|Különbségi szög|Helyi gép|Helyi projekt|
|----------------|---------------|---------------|
|Helyi hozzáférés|Az összes projekt számára elérhető.|Ehhez a fiókhoz csak a megfelelő projekt férhet hozzá.|
|Helyi adatgyökér mappa|Állandó helyi mappa. **Eszközökön**  >  **Data Lake**  >  **beállítások és beállítások** segítségével konfigurálható.|A U-SQL projekt munkakönyvtárában minden helyi futtatáshoz létrehozott ideiglenes mappa. A rendszer megtisztítja a mappát, ha Újraépítés vagy újrafuttatás történik.|
|Bemeneti adatok egy U-SQL-parancsfájlhoz|A relatív elérési út az állandó helyi adatgyökér mappában.|Állítsa be a **U-SQL Project Property**  >  **test adatforrást**. A rendszer az összes fájlt és almappát a helyi Futtatás előtt az ideiglenes adatgyökér mappájába másolja.|
|Kimeneti adatokat egy U-SQL-parancsfájlhoz|Relatív elérési út az állandó helyi adatgyökér mappában.|Kimenet az ideiglenes adatgyökér mappájába. Az eredményeket a rendszer Újraépítés vagy újrafuttatás esetén megtisztítja.|
|Hivatkozott adatbázis központi telepítése|A hivatkozott adatbázisok nem települnek automatikusan a **helyi számítógép-** fiókkal való futtatáskor. Ez ugyanaz, mint egy Azure Data Lake Analytics-fiókba való beküldéshez.|A hivatkozott adatbázisok automatikusan települnek a **helyi projekt** fiókba a helyi Futtatás előtt. Az adatbázis-környezetek tisztítása és újbóli üzembe helyezése az Újraépítés vagy a Futtatás során történik.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Helyi Futtatás a U-SQL SDK-val

A Visual Studióban helyileg futtathatja az U-SQL-parancsfájlokat, és a Azure Data Lake U-SQL SDK-val is futtathatja a U-SQL-parancsfájlokat a parancssori és a programozási felületek használatával helyileg. Ezen felületek segítségével automatizálhatja a U-SQL helyi futtatásokat és teszteket.

További információ a [Azure Data Lake U-SQL SDK-](data-lake-analytics-u-sql-sdk.md)ról.

## <a name="next-steps"></a>Következő lépések

- [CI/CD-folyamat beállítása Azure Data Lake Analyticshoz](data-lake-analytics-cicd-overview.md).
- [A Azure Data Lake Analytics kód tesztelése](data-lake-analytics-cicd-test.md).
