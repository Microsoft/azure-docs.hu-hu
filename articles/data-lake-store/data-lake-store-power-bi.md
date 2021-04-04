---
title: Az Azure Data Lake Storage Gen1-Power BIban lévő adatelemzés
description: Ismerje meg, hogyan elemezheti és jelenítheti meg a Azure Data Lake Storage Gen1ban tárolt adatPower BI Desktopokat.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bcc561cd5eea4372d798fff4580362ba0879c3a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91574194"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Azure Data Lake Storage Gen1i adatelemzés Power BI használatával
Ebből a cikkből megtudhatja, hogyan elemezheti és jelenítheti meg a Azure Data Lake Storage Gen1ban tárolt információkat a Power BI Desktop használatával.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Data Lake Storage Gen1-fiók**. Kövesse a [Azure Data Lake Storage Gen1 használatának első lépései a Azure Portal használatával](data-lake-store-get-started-portal.md)című témakör utasításait. Ez a cikk azt feltételezi, hogy már létrehozott egy **myadlsg1** nevű Data Lake Storage Gen1 fiókot, és feltöltött egy minta adatfájlt (**Drivers.txt**). Ez a mintakód [Azure Data Lake git-tárházból](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)tölthető le.
* **Power BI Desktop**. Ezt letöltheti a [Microsoft letöltőközpontból](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Jelentés készítése a Power BI Desktopban
1. Power BI Desktop elindítása a számítógépen.
2. A **Kezdőlap** menüszalagon kattintson az **adatok lekérése** elemre, majd kattintson a továbbiak elemre. Az **adatlekérdezés** párbeszédpanelen kattintson az **Azure** elemre, majd a **Azure Data Lake Store** elemre, végül a **kapcsolat** elemre.
   
    ![Képernyőkép az adatlekérdezés párbeszédpanelről, a Kiemelt Azure Data Lake Store lehetőséggel és a csatlakozási lehetőséggel.](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Kapcsolódás Data Lake Storage Gen1hoz")
3. Ha megjelenik egy párbeszédpanel arról, hogy az összekötő egy fejlesztési fázisban van, a folytatáshoz válassza a következőt:.
4. A **Azure Data Lake Store** párbeszédpanelen adja meg a Data Lake Storage Gen1-fiók URL-címét, majd kattintson az **OK** gombra.
   
    ![Data Lake Storage Gen1 URL-címe](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Data Lake Storage Gen1 URL-címe")
5. A következő párbeszédpanelen kattintson a **Bejelentkezés** gombra a Data Lake Storage Gen1 fiókba való bejelentkezéshez. A rendszer átirányítja a szervezet bejelentkezési oldalára. Az utasításokat követve jelentkezzen be a fiókba.
   
    ![Bejelentkezés Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Bejelentkezés Data Lake Storage Gen1")
6. Miután sikeresen bejelentkezett, kattintson a **kapcsolat** gombra.
   
    ![Képernyőkép a Azure Data Lake Store párbeszédpanelről, a csatlakozási lehetőséggel.](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Kapcsolódás Data Lake Storage Gen1hoz")
7. A következő párbeszédpanel megjeleníti a Data Lake Storage Gen1-fiókjába feltöltött fájlt. Ellenőrizze az adatokat, majd kattintson a **Betöltés** elemre.
   
    ![Adatok betöltése a Data Lake Storage Gen1ból](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Adatok betöltése a Data Lake Storage Gen1ból")
8. Miután az adat sikeresen betöltődött Power BIba, a **mezők** lapon a következő mezők jelennek meg.
   
    ![Importált mezők](./media/data-lake-store-power-bi/imported-fields.png "Importált mezők")
   
    Az adat megjelenítéséhez és elemzéséhez azonban inkább az alábbi mezőkben elérhetővé tettük az adatmennyiséget.
   
    ![Kívánt mezők](./media/data-lake-store-power-bi/desired-fields.png "Kívánt mezők")
   
    A következő lépésekben frissíteni fogjuk a lekérdezést, hogy az importált adattípusokat a kívánt formátumban alakítsa át.
9. A **Kezdőlap** menüszalagon kattintson a **lekérdezések szerkesztése** elemre.
   
    ![Képernyőkép a Kezdőlap menüszalagról a lekérdezések szerkesztése lehetőség kiválasztásával.](./media/data-lake-store-power-bi/edit-queries.png "Lekérdezések szerkesztése")
10. A lekérdezés-szerkesztő **tartalom** oszlopában kattintson a **bináris** elemre.
    
    ![Képernyőkép: a lekérdezés-szerkesztő és a kinevezett tartalom oszlop.](./media/data-lake-store-power-bi/convert-query1.png "Lekérdezések szerkesztése")
11. Egy fájl ikon jelenik meg, amely a feltöltött **Drivers.txt** fájlt jelöli. Kattintson a jobb gombbal a fájlra, majd kattintson a **CSV** elemre.    
    
    ![Képernyőkép a lekérdezési Szerkesztőről a CSV-beállítással.](./media/data-lake-store-power-bi/convert-query2.png "Lekérdezések szerkesztése")
12. Az alább látható kimenetnek kell megjelennie. Az adatai mostantól a vizualizációk létrehozásához használható formátumban érhetők el.
    
    ![Képernyőkép a lekérdezési Szerkesztőről a várt módon megjelenített kimenettel.](./media/data-lake-store-power-bi/convert-query3.png "Lekérdezések szerkesztése")
13. A **Kezdőlap** menüszalagon kattintson a **Bezárás** gombra, majd kattintson a **Bezárás** gombra, és válassza az alkalmaz elemet.
    
    ![Képernyőkép a Kezdőlap menüszalagján a Bezárás és az alkalmaz lehetőséggel.](./media/data-lake-store-power-bi/load-edited-query.png "Lekérdezések szerkesztése")
14. A lekérdezés frissítése után a **mezők** lapon megjelennek a vizualizációhoz elérhető új mezők.
    
    ![Frissített mezők](./media/data-lake-store-power-bi/updated-query-fields.png "Frissített mezők")
15. Hozzunk létre egy tortadiagramot, amely az egyes városokban lévő illesztőprogramokat jelöli az adott ország/régió számára. Ehhez végezze el a következő beállításokat.
    
    1. A vizualizációk lapon kattintson a tortadiagram szimbólumára.
       
        ![Tortadiagram létrehozása](./media/data-lake-store-power-bi/create-pie-chart.png "Tortadiagram létrehozása")
    2. A használni kívánt oszlopok a **4. oszlop** (a város neve) és a **7-es oszlop** (az ország/régió neve). Húzza ezeket az oszlopokat a **mezők** lapról a **vizualizációk** lapra az alább látható módon.
       
        ![Vizualizációk létrehozása](./media/data-lake-store-power-bi/create-visualizations.png "Vizualizációk létrehozása")
    3. A tortadiagramnak most az alább láthatóhoz hasonlónak kell lennie.
       
        ![Tortadiagram](./media/data-lake-store-power-bi/pie-chart.png "Vizualizációk létrehozása")
16. Ha kijelöl egy adott országot vagy régiót az oldal szintjének szűrők közül, akkor a kiválasztott ország/régió minden városában megjelenik az illesztőprogramok száma. Például a **vizualizációk** lap **oldal szintű szűrők** területén válassza a **Brazília** elemet.
    
    ![Ország/régió kiválasztása](./media/data-lake-store-power-bi/select-country.png "Ország/régió kiválasztása")
17. A tortadiagramot a rendszer automatikusan frissíti a brazil városokban lévő illesztőprogramok megjelenítéséhez.
    
    ![Az országok/régiók illesztőprogramjai](./media/data-lake-store-power-bi/driver-per-country.png "Illesztőprogramok országonként/régiónként")
18. A **fájl** menüben kattintson a **Mentés** gombra a vizualizáció Power bi Desktop fájlként való mentéséhez.

## <a name="publish-report-to-power-bi-service"></a>Jelentés közzététele Power BI szolgáltatás
Miután létrehozta a vizualizációkat a Power BI Desktopban, megoszthatja másokkal, ha közzéteszi azt a Power BI szolgáltatás. Ennek módjával kapcsolatos útmutatásért lásd: [közzététel Power bi Desktopról](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Lásd még
* [Data Lake Storage Gen1 adatai elemzése a Data Lake Analytics használatával](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

