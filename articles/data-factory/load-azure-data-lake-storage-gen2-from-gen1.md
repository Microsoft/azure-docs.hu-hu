---
title: Adatok másolása Azure Data Lake Storage Gen1ról Gen2
description: Adatok másolása Azure Data Lake Storage Gen1ból a Gen2-be a Azure Data Factory használatával
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 4a4bc0b55928fcaa40cd1cb03ca0522e611df476
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727719"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Adatok másolása Azure Data Lake Storage Gen1ból a Gen2-be Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Lake Storage Gen2 az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)-ba beépített, Big Data Analitika számára kialakított képességek összessége. Az adatok kezeléséhez használhatja fájlrendszerbeli és objektumtárolási módszerekkel egyaránt.

Ha jelenleg Azure Data Lake Storage Gen1 használ, Azure Data Lake Storage Gen2 kiértékelheti az adatok Data Lake Storage Gen1ból Gen2 Azure Data Factory használatával történő másolásával.

A Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás. A szolgáltatással feltöltheti a tavat a helyszíni és felhőalapú adattárak gazdag készletéről származó adatokkal, és időt takaríthat meg az elemzési megoldások létrehozásakor. A támogatott összekötők listáját a [támogatott adattárakkal](copy-activity-overview.md#supported-data-stores-and-formats)foglalkozó táblázatban tekintheti meg.

A Azure Data Factory kibővíthető, felügyelt adatátviteli megoldást kínál. A Data Factory kibővíthető architektúrája miatt az adatok nagy adatátviteli sebességgel képesek befogadni. További információ: [másolási tevékenység teljesítménye](copy-activity-performance.md).

Ebből a cikkből megtudhatja, hogyan másolhat adatok Azure Data Lake Storage Gen1ból a Azure Data Lake Storage Gen2ba az adatok másolása Data Factory eszközzel. Az adatok más típusú adattárakból történő másolásához hasonló lépéseket kell követnie.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Azure Data Lake Storage Gen1 a fiókban lévő adattal.
* Data Lake Storage Gen2 engedélyezett Azure Storage-fiók. Ha nincs Storage-fiókja, [hozzon létre egy fiókot](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **adatok és Analitika**  >  **Data Factory** lehetőséget.
   
   ![Data Factory kijelölés az új panelen](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az **új adat-előállító** lapon adja meg az alábbi képen látható mezők értékét: 
      
   ![Új adatfeldolgozó lap](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Név**: adjon meg egy globálisan egyedi nevet az Azure-beli adatgyár számára. Ha "a \" LoadADLSDemo nem érhető el" hibaüzenet jelenik \" meg, adjon meg egy másik nevet az adatelőállító számára. Például: _**sajátneve**_**ADFTutorialDataFactory**. Hozza létre újra az adatelőállítót. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: válassza ki azt az Azure-előfizetést, amelyben létre kívánja hozni az adatelőállítót. 
    * **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot a legördülő listából. Kiválaszthatja az **új létrehozása** lehetőséget is, és megadhatja az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 
    * **Verzió**: Válassza a **V2** lehetőséget.
    * **Hely**: válassza ki az adatelőállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállító által használt adattárak más helyeken és régiókban is lehetnek. 

3. Válassza a **Létrehozás** lehetőséget.
4. A létrehozás befejezése után nyissa meg az adatait a gyárban. Megjelenik a **Data Factory** kezdőlapja, ahogy az a következő képen látható: 
   
   ![Data factory kezdőlap](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Az adatintegrációs alkalmazás külön lapon történő elindításához válassza a **szerző & figyelése** csempét.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Adatok betöltése az Azure Data Lake Storage Gen2-be

1. Az **első lépések** lapon válassza a **adatok másolása** csempét az Adatmásolás eszköz elindításához. 

   ![Adatmásolási eszköz csempe](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. A **Tulajdonságok** lapon adja meg a **CopyFromADLSGen1ToGen2** mezőt a **feladat neve** mezőben. Kattintson a **Tovább** gombra.

    ![Tulajdonságok lap](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. A **forrás adattár** lapon válassza az **+ új kapcsolatok létrehozása** lehetőséget.

    ![Forrásadattár lap](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Válassza az **Azure Data Lake Storage Gen1** elemet az összekötők katalógusában, majd a **Folytatás** gombot.
    
    ![Forrás adattár Azure Data Lake Storage Gen1 lapja](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Az **Azure Data Lake Storage Gen1-kapcsolatok meghatározása** lapon kövesse az alábbi lépéseket:

   a. Válassza ki a fiók neve Data Lake Storage Gen1, majd adja meg vagy ellenőrizze a **bérlőt**.
  
   b. Válassza a **Kapcsolódás tesztelése** lehetőséget a beállítások ellenőrzéséhez. Ezután válassza a **Befejezés** lehetőséget.
  
   c. Láthatja, hogy az új kapcsolatok létrejöttek. Kattintson a **Tovább** gombra.
   
   > [!IMPORTANT]
   > Ebben az útmutatóban az Azure-erőforrások felügyelt identitását használja a Azure Data Lake Storage Gen1 hitelesítéséhez. Ha a felügyelt identitást a Azure Data Lake Storage Gen1 megfelelő engedélyekkel szeretné biztosítani, kövesse az [alábbi utasításokat](connector-azure-data-lake-store.md#managed-identity).
   
   ![Azure Data Lake Storage Gen1 fiók meghatározása](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. A **bemeneti fájl vagy mappa kiválasztása** lapon keresse meg azt a mappát és fájlt, amelyet át szeretne másolni. Válassza ki a mappát vagy a fájlt, majd válassza **a kiválasztás lehetőséget.**

    ![Bemeneti fájl vagy mappa kiválasztása](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. A másolási viselkedést a **fájlok rekurzív** és **bináris másolási** beállítások másolása lehetőség kiválasztásával határozhatja meg. Kattintson a **Tovább** gombra.

    ![Képernyőfelvétel: a bemeneti fájl vagy mappa kiválasztása, ahol kiválaszthatja a fájl rekurzív és bináris másolatának másolása lehetőséget.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. A **cél adattár** lapon válassza az **+ új kapcsolatok létrehozása**  >  **Azure Data Lake Storage Gen2**  >  **Folytatás** lehetőséget.

    ![Céladattár lap](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Az **Azure Data Lake Storage Gen2-kapcsolatok meghatározása** lapon kövesse az alábbi lépéseket:

   a. Válassza ki a Data Lake Storage Gen2 képes fiókot a **Storage-fiók neve** legördülő listából.
   
   b. Kattintson a **Befejezés** gombra a kapcsolat létrehozásához. Ezután kattintson a **Tovább** gombra.
   
   ![Azure Data Lake Storage Gen2 fiók meghatározása](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. A **kimeneti fájl vagy mappa kiválasztása** lapon adja meg a **copyfromadlsgen1** a kimeneti mappa neveként, majd kattintson a **tovább** gombra. Data Factory létrehozza a megfelelő Azure Data Lake Storage Gen2 fájlrendszert és almappákat a másolás során, ha nem léteznek.

    ![Képernyőfelvétel: a megadott mappa elérési útja.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. A **Beállítások** lapon kattintson a **Tovább** gombra az alapértelmezett beállítások használatához.

12. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Tovább gombra**.

    ![Összefoglaló lap](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. A folyamat figyeléséhez az **üzembe helyezés lapon** válassza a **figyelő** lehetőséget.

    ![Üzembe helyezés lap](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozások a **Műveletek** oszlopban találhatók.

    ![Folyamatfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Ha meg szeretné tekinteni a folyamat futtatásához társított tevékenység-futtatásokat, válassza a **műveletek** oszlop **Megtekintés tevékenység futtatása** hivatkozását. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Ha vissza szeretne váltani a folyamat futási nézetére, válassza a felül található **folyamatok** hivatkozást. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Tevékenységfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Az egyes másolási tevékenységek végrehajtási részleteinek figyeléséhez válassza a tevékenységek figyelése nézet **műveletek** területén található **részletek** hivatkozást (szemüvegek képe). A forrásról a fogadóra másolt adatok mennyiségét, az adatátvitelt, a végrehajtási lépéseket és a megfelelő időtartamot, valamint a használt konfigurációkat is figyelheti.

    ![Tevékenység-futtatási részletek figyelése](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Ellenőrizze, hogy a rendszer átmásolta-e az adatait a Azure Data Lake Storage Gen2-fiókjába.

## <a name="best-practices"></a>Ajánlott eljárások

A Azure Data Lake Storage Gen1ról Azure Data Lake Storage Gen2ra történő verziófrissítés értékeléséhez tekintse [meg a Big Data Analytics-megoldások Azure Data Lake Storage Gen1ról Azure Data Lake Storage Gen2ra történő frissítését](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)ismertető témakört. Az alábbi fejezetek az Data Factory használatának ajánlott eljárásait ismertetik Data Lake Storage Gen1ról Data Lake Storage Gen2re történő adatfrissítéshez.

### <a name="data-partition-for-historical-data-copy"></a>Adatpartíció a korábbi adatmásoláshoz

- Ha Data Lake Storage Gen1 a teljes adatméret 30 TB-nál kisebb, és a fájlok száma kevesebb, mint 1 000 000, akkor egyetlen másolási tevékenység futtatásával másolhatja az összes adatát.
- Ha nagyobb mennyiségű adattal szeretne másolni, vagy ha rugalmasan szeretné kezelni a kötegekben lévő adatáttelepítést, és mindegyiket egy adott időkereten belül be kell fejeznie, akkor particionálja az adategységeket. A particionálás emellett csökkenti a váratlan problémák kockázatát is.

A koncepció igazolásával ellenőrizheti a végpontok közötti megoldást, és tesztelheti a másolási sebességet a környezetben. A főbb megvalósíthatósági lépések: 

1. Hozzon létre egy Data Factory folyamatot egyetlen másolási tevékenységgel, hogy több TBs-adat másolását Data Lake Storage Gen1ról Data Lake Storage Gen2re, hogy lekérje a másolási teljesítmény alaptervét. Kezdje az [adatintegrációs egységekkel (DIUs) a 128-as értékkel](copy-activity-performance-features.md#data-integration-units) . 
2. A másolási átviteli sebesség alapján, amelyet az 1. lépésben kap, a teljes adatáttelepítéshez szükséges becsült időt számítja ki. 
3. Választható Hozzon létre egy vezérlő táblát, és határozza meg a fájl szűrőt az áttelepítendő fájlok particionálásához. A fájlok particionálásának módja a következő: 

    - A partíció neve vagy mappanév, helyettesítő szűrő használatával. Ezt a módszert ajánljuk.
    - Particionálás egy fájl utolsó módosításának időpontjával.

### <a name="network-bandwidth-and-storage-io"></a>Hálózati sávszélesség és tárolás I/O 

Data Factory másolási feladatok párhuzamosságát szabályozhatja, amelyek beolvassák az adatok Data Lake Storage Gen1 és az adatok Data Lake Storage Gen2ba írását. Ily módon kezelheti a tárolási I/O-használatot, hogy elkerülje a szokásos üzleti munkát Data Lake Storage Gen1 az áttelepítés során.

### <a name="permissions"></a>Engedélyek 

Data Factory az Data Lake Storage Gen1- [összekötő](connector-azure-data-lake-store.md) támogatja az Azure erőforrás-hitelesítésekhez tartozó egyszerű szolgáltatásnév és felügyelt identitás használatát. Az [Data Lake Storage Gen2-összekötő](connector-azure-data-lake-storage.md) támogatja az Azure erőforrás-hitelesítésekhez tartozó fiók kulcsát, egyszerű szolgáltatásnév és felügyelt identitását. Annak érdekében, hogy Data Factory képes legyen az összes szükséges fájl vagy hozzáférés-vezérlési listák (ACL-ek) megkeresésére és másolására, adjon meg elég magas engedélyeket az összes fájl eléréséhez, olvasásához vagy írásához, és állítsa be az ACL-eket, ha úgy dönt, hogy. Az áttelepítési időszak alatt adja meg egy felügyelői vagy tulajdonosi szerepkört. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>ACL-ek megőrzése Data Lake Storage Gen1

Ha az ACL-eket az adatfájlokkal együtt szeretné replikálni Data Lake Storage Gen1ról Data Lake Storage Gen2re való frissítéskor, tekintse meg a következőt: [ACL-ek megőrzése Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls). 

### <a name="incremental-copy"></a>Növekményes másolat 

Több módszer is használható az új vagy frissített fájlok betöltésére Data Lake Storage Gen1:

- Töltse be az új vagy frissített fájlokat az idő particionált mappája vagy fájlneve alapján. Példa:/2019/05/13/*.
- Új vagy frissített fájlok betöltése LastModifiedDate szerint.
- Azonosítsa az új vagy frissített fájlokat bármely harmadik féltől származó eszköz vagy megoldás alapján. Ezután adja át a fájl-vagy mappanév nevét a Data Factory folyamatnak paraméter vagy tábla vagy fájl használatával. 

A növekményes terhelés végrehajtásának megfelelő gyakorisága a Azure Data Lake Storage Gen1ban található fájlok teljes számától, valamint az új vagy frissített, minden alkalommal betöltendő fájlok mennyiségétől függ. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Másolási tevékenység – áttekintés](copy-activity-overview.md) 
>  [Azure Data Lake Storage Gen1-összekötő](connector-azure-data-lake-store.md) 
>  [Azure Data Lake Storage Gen2-összekötő](connector-azure-data-lake-storage.md)