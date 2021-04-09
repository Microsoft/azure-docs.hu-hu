---
title: Adatátalakítás a kaptár használatával az Azure Virtual Network használatával Azure Portal
description: Ez az oktatóanyag részletes útmutatást nyújt az adatok Hive-tevékenységgel történő átalakításához az Azure Data Factoryben.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/04/2018
ms.openlocfilehash: 4c8ae67720cf6ac9d577286898b95cdd10f38152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377958"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory-using-the-azure-portal"></a>Adatátalakítás az Azure Virtual Networkban a kaptár tevékenységgel Azure Data Factory a Azure Portal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben az oktatóanyagban az Azure Portal segítségével hoz létre egy Data Factory-folyamatot, amely egy Azure virtuális hálózaton (VNet) lévő HDInsight-fürtön futó Hive-tevékenységgel alakítja át az adatokat. Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása 
> * Saját üzemeltetésű Integration Runtime létrehozása
> * Azure Storage-beli és Azure HDInsight társított szolgáltatások létrehozása
> * Folyamat létrehozása Hive-tevékenységgel
> * Folyamat futtatásának aktiválása
> * A folyamat futásának monitorozása 
> * Kimenet ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure Storage-fiók**. Létrehoz egy Hive-szkriptet, és feltölti az Azure Storage-ba. A Hive-szkript kimenetét ebben a Storage-fiókban tárolja a rendszer. A példában a HDInsight-fürt ezt az Azure Storage-fiókot használja elsődleges tárolóként. 
- **Azure Virtual Network.** Ha nem rendelkezik Azure virtuális hálózattal, hozzon létre egyet [ezeket az utasításokat](../virtual-network/quick-create-portal.md) követve. Ebben a példában a HDInsight egy Azure virtuális hálózaton található. Itt látható az Azure virtuális hálózat egy példa konfigurációja. 

    ![Virtuális hálózat létrehozása](media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png)
- **HDInsight-fürt.** Hozzon létre egy HDInsight-fürtöt, és csatolja az előző lépésben létrehozott virtuális hálózathoz az [Azure HDInsight Azure virtuális hálózat segítségével történő bővítésével](../hdinsight/hdinsight-plan-virtual-network-deployment.md) kapcsolatos cikk utasításait követve. Itt látható a virtuális hálózaton található HDInsight egy példa konfigurációja. 

    ![HDInsight virtuális hálózaton](media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png)
- **Azure PowerShell**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-Az-ps) ismertető cikkben szereplő utasításokat.
- **Egy virtuális gép**. Hozzon létre egy Azure-beli virtuális gépet, és csatlakoztassa ahhoz a virtuális hálózathoz, amelyen a HDInsight-fürt található. Részletekért tekintse meg a [virtuális gépek létrehozását](../virtual-network/quick-create-portal.md#create-virtual-machines) ismertető cikket. 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Hive-szkript feltöltése Blob Storage-fiókba

1. Hozzon létre egy **hivescript.hql** nevű Hive SQL-fájlt a következő tartalommal:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Az Azure Blob Storage-ban hozzon létre egy **adftutorial** nevű tárolót, ha még nem létezik.
3. Hozzon létre egy **hivescripts** nevű mappát.
4. Töltse fel a **hivescript.hql** fájlt a **hivescripts** almappába.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).    
2. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png)
3. Az **Új adat-előállító** lapon, a **név** mezőben adja meg a következőt: **ADFTutorialHiveFactory**. 
      
     ![Új adat-előállító lap](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png)
 
   Az Azure-beli adatgyár nevének **globálisan egyedinek** kell lennie. Ha a következő hibaüzenetet kapja, változtassa meg az adat-előállító nevét (például sajátneveMyAzureSsisDataFactoryra), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
    *A "MyAzureSsisDataFactory" nevű adatgyár nem érhető el*
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoport** esetében hajtsa végre az alábbi lépések egyikét:
     
   - Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából. 
   - Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét.   
         
     Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. A **Verzió** résznél válassza a **V2** értéket.
5. Válassza ki a Data Factory **helyét**. A listában csak az adat-előállítók létrehozását támogató helyek jelennek meg.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Létrehozás** lehetőségre.
8. Az irányítópulton a következő csempe jelenik meg, amelynek állapota: az **adatgyár üzembe helyezése**. 

     ![adat-előállító üzembe helyezése csempe](media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
    ![Data factory kezdőlap](./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png)
10. A Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson az **Author & Monitor** (Létrehozás és monitorozás) csempére.
11. Az **első lépéseket bemutató** lapon váltson a **Szerkesztés** lapra a bal oldali panelen, ahogy az az alábbi képen látható: 

    ![Szerkesztés lap](./media/tutorial-transform-data-using-hive-in-vnet-portal/get-started-page.png)

## <a name="create-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime létrehozása
Mivel a Hadoop-fürt egy virtuális hálózatban található, telepítenie kell egy helyi Integration Runtime-ot (IR) ugyanabban a virtuális hálózatban. Ebben a szakaszban egy új virtuális hálózatot fog létrehozni, amelyet aztán csatlakoztat ugyanahhoz a virtuális hálózathoz, és telepít rajta egy helyi IR-t. A helyi IR a Data Factory szolgáltatás számára lehetővé teszi a feldolgozási kérelmek kiosztását az HDInsighthoz hasonló számítási szolgáltatásokhoz a virtuális hálózaton belül. Az adatok mozgatására is lehetőséget biztosít a virtuális hálózatban található adattárak és az Azure között. Akkor kell helyi IR-t használni, ha az adattár vagy a számítási feladat szintén helyszíni környezetben található. 

1. Az Azure Data Factory felhasználói felületén kattintson az ablak alján látható **Connections** (Kapcsolatok) elemre, majd váltson át az **Integration Runtimes** lapra, és kattintson az eszköztáron a **+ New** (Új) elemre. 

   ![Új Integration Runtime menü](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png)
2. Az **Integration Runtime Setup** (Integration Runtime beállítása) ablakban válassza a **Perform data movement and dispatch activities to external computes** (Adatáthelyezés és tevékenységek kiosztása külső számításokhoz) lehetőséget, majd kattintson a **Next** (Tovább) gombra. 

   ![Az Adatáthelyezés és tevékenységek kiosztása külső számításokhoz lehetőség kiválasztása](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png)
3. Válassza a **Private Network** (Magánhálózat) lehetőséget, majd kattintson a **Next** (Tovább) gombra.
    
   ![Magánhálózat kiválasztása](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png)
4. Írja be a **MySelfHostedIR** nevet a **név** mezőjébe, és kattintson a **Next** (Tovább) gombra. 

   ![Integration Runtime nevének megadása](./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png) 
5. Másolja ki az Integration Runtime **hitelesítési kulcsát** a másolási gombra kattintva, majd mentse el. Ne zárja be az ablakot. Ezzel a kulccsal fogja regisztrálni a virtuális gépen telepített IR-t. 

   ![Hitelesítési kulcs másolása](./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png)

### <a name="install-ir-on-a-virtual-machine"></a>Integration Runtime telepítése virtuális gépre

1. Az Azure-beli virtuális gépen töltse le a [saját üzemeltetésű integrációs modult](https://www.microsoft.com/download/details.aspx?id=39717). Használja az előző lépésben kapott **hitelesítési kulcsot** a saját üzemeltetésű integrációs modul manuális regisztrálásához. 

    ![Integrációs modul regisztrálása](media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png)

2. A helyi Integration Runtime sikeres regisztrációja esetén a következő üzenet jelenik meg. 
   
    ![Sikeres regisztráció](media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png)
3. Kattintson a **Launch Configuration Manager** (Configuration Manager indítása) elemre. A csomópont a felhőszolgáltatáshoz való csatlakozásakor a következő oldal jelenik meg: 
   
    ![Csomópont csatlakoztatva](media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png)

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>Helyi IR az Azure Data Factory felhasználói felületén

1. Az **Azure Data Factory felhasználói felületén** meg kell jelennie a helyi virtuális gép nevének és állapotának.

   ![Meglévő helyi csomópontok](./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png)
2. Kattintson a **Finish** (Befejezés) gombra az **Integration Runtime Setup** (Integration Runtime beállítása) ablak bezárásához. Az Integration Runtime-ok listáján megjelenik a helyi IR.

   ![A helyi IR a listában](./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png)


## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ebben a részben két társított szolgáltatást hoz létre és helyez üzembe:
- Egy Azure Storage-beli **társított szolgáltatás** , amely egy Azure Storage-fiókot csatol az adatgyárhoz. Ez a tároló a HDInsight-fürt által használt elsődleges tároló. Ebben az esetben ezt az Azure Storage-fiókot használjuk a Hive-szkript és a szkript kimenetének tárolására.
- Egy **HDInsight társított szolgáltatás**. Az Azure Data Factory a Hive-szkriptet erre a HDInsight-fürtre küldi el végrehajtásra.

### <a name="create-azure-storage-linked-service"></a>Azure Storage társított szolgáltatás létrehozása

1. Váltson a **Linked Services** (Társított szolgáltatások) lapra, és kattintson a **New** (Új) elemre.

   ![Új társított szolgáltatás gomb](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. A **New Linked Service** (Új társított szolgáltatás) ablakban válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Continue** (Folytatás) elemre. 

   ![Select Azure Blob Storage](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png)
3. A **New Linked Service** (Új társított szolgáltatás) ablakban végezze el az alábbi lépéseket:

    1. A **Name** (Név) mezőbe írja az **AzureStorageLinkedService** nevet.
    2. Válassza a **MySelfHostedIR** elemet a **Connect via integration runtime** (Csatlakozás integrációs modulon keresztül) lehetőségnél.
    3. Válassza ki az Azure Storage-fiókját a **Storage-fiók nevéhez**. 
    4. Ha tesztelni szeretné a tárfiókkal létrejövő kapcsolatot, kattintson a **Test connection** (Kapcsolat tesztelése) lehetőségre.
    5. Kattintson a **Mentés** gombra.
   
        ![Azure Blob Storage-fiók megadása](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png)

### <a name="create-hdinsight-linked-service"></a>HDInsight társított szolgáltatás létrehozása

1. Kattintson ismét a **New** (Új) gombra egy további társított szolgáltatás létrehozásához. 
    
   ![Új társított szolgáltatás gomb](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. A **Compute** (Számítás) lapon válassza az **Azure HDInsight** lehetőséget, majd kattintson a **Continue** (Folytatás) gombra.

    ![Az Azure HDInsight kiválasztása](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png)
3. A **New Linked Service** (Új társított szolgáltatás) ablakban végezze el az alábbi lépéseket:

    1. A **Name** (Név) mezőben adja meg a következőt: **AzureHDInsightLinkedService**.
    2. Válassza a **Bring your own HDInsight** (Saját HDInsight használata) lehetőséget. 
    3. A **HDI cluster** (HDI-fürt) elemnél válassza ki saját HDInsight-fürtjét. 
    4. Adja meg a HDInsight-fürthöz tartozó **felhasználónevet**.
    5. Adja meg a felhasználó **jelszavát** . 
    
        ![Az Azure HDInsight beállításai](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png)

A cikk azt feltételezi, hogy a fürtöt az interneten éri el. Például hogy a `https://clustername.azurehdinsight.net` címen tud csatlakozni a fürthöz. Ez a cím a nyilvános átjárót használja, amely nem érhető el, ha az internetes hozzáférés korlátozva lett hálózati biztonsági csoportokkal (NSG-kkel) vagy felhasználó által definiált útvonalakkal (UDR-ekkel). Ahhoz, hogy a Data Factory feladatokat küldhessen az Azure virtuális hálózaton található HDInsight-fürtre, az Azure virtuális hálózatot úgy kell konfigurálni, hogy az URL-cím feloldható legyen a HDInsight által használt átjáró magánhálózati IP-címére.

1. Az Azure Portalról nyissa meg a virtuális hálózatot, amelyen a HDInsight található. Nyissa meg a hálózati adaptert, amelynek a neve a következő karakterlánccal kezdődik: `nic-gateway-0`. Jegyezze fel a magánhálózati IP-címét. Például: 10.6.0.15. 
2. Ha az Azure virtuális hálózaton van DNS-kiszolgáló, frissítse a DNS-rekordot, hogy a HDInsight-fürt URL-címe (`https://<clustername>.azurehdinsight.net`) feloldható legyen a `10.6.0.15` címre. Ha nincs DNS-kiszolgáló az Azure virtuális hálózaton, akkor átmeneti megoldást jelenthet, ha szerkeszti az összes, helyi Integration Runtime-csomópontként regisztrált virtuális gépet tartalmazó fájlt (C:\Windows\System32\drivers\etc) egy, a következőhöz hasonló bejegyzés hozzáadásával: 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>Folyamat létrehozása 
Ebben a lépésben létrehoz egy Hive-tevékenységgel rendelkező új folyamatot. A tevékenység egy Hive-szkript végrehajtásával ad vissza adatokat egy minta táblából, és menti azokat egy megadott elérési útra.

Vegye figyelembe a következő szempontokat:

- A **scriptPath** annak a Hive-szkriptnek az elérési útjára mutat az Azure Storage-fiókon, amelyet a MyStorageLinkedService szolgáltatáshoz használt. Az elérési út megkülönbözteti a kis- és nagybetűket.
- Az **Output** egy, a Hive-szkriptben használt argumentum. Használja a `wasbs://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` formátumot, hogy egy létező mappára mutasson az Azure Storage-ban. Az elérési út megkülönbözteti a kis- és nagybetűket. 

1. A Data Factory felhasználói felületén, a bal oldali panelen kattintson a **+ (plusz)** jelre, majd a **Pipeline** (Folyamat) elemre. 

    ![Új folyamat menü](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png)
2. Az **Activities** (Tevékenységek) eszközkészletben bontsa ki a **HDInsight** elemet, és húzza a **Hive** tevékenységet a folyamat tervezőfelületére. 

    ![Hive tevékenység áthúzása](./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png)
3. A tulajdonságok ablakban váltson a **HDI Cluster** (HDI-fürt) lapra, majd válassza az **AzureHDInsightLinkedService** lehetőséget a **HDInsight Linked Service** (HDInsight társított szolgáltatás) elemnél.

    ![HDInsight társított szolgáltatás kiválasztása](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png)
4. Váltson a **Scripts** (Szkriptek) lapra, és végezze el az alábbi lépéseket: 

    1. A **Script Linked Service** (Szkripthez társított szolgáltatás) elemnél válassza az **AzureStorageLinkedService** lehetőséget. 
    2. A **File Path** (Fájl elérési útja) elemnél kattintson a **Browse Storage** (Tallózás a tárolóban) lehetőségre. 
 
        ![Tallózás a tárolóban](./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png)
    3. A **Choose a file or folder** (Fájl vagy mappa választása) ablakban keressem meg a **hivescripts** mappát az **adftutorial** tárolóban, válassza ki a **hivescript.hql** fájlt, és kattintson a **Finish** (Befejezés) elemre.  
        
        ![Fájl vagy mappa kiválasztása](./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png) 
    4. Ellenőrizze, hogy megjelenik-e az **adftutorial/hivescripts/hivescript.hql** a **fájl elérési útjánál**.

        ![Szkript beállításai](./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png)
    5. A **Script lapon** bontsa ki az **Advanced** (Speciális) szakaszt. 
    6. Kattintson az **Auto-fill from script** (Automatikus kitöltés szkript alapján) elemre a **Parameters** (Paraméterek) menüpontban. 
    7. Adja meg a **kimeneti** paraméter értékét a következő formátumban: `wasbs://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`. Példa: `wasbs://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`.
 
        ![Szkript argumentumai](./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png)
1. Ahhoz, hogy közzétehesse az összetevőket a Data Factoryn, kattintson a **Publish** (Közzététel) gombra.

    ![A képernyőképen a Data Factory közzétételének lehetősége látható.](./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása

1. Először érvényesítse a folyamatot: kattintson a **Validation** (Érvényesítés) gombra az eszköztáron. Zárja be a **Pipeline Validation Output** (Folyamat érvényesítésének eredménye) ablakot a **jobbra mutató nyílra (>>)** kattintva. 

    ![Folyamat érvényesítése](./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png) 
2. A folyamat futásának aktiválásához kattintson a Trigger (Aktiválás) gombra az eszköztáron, majd a Trigger Now (Aktiválás most) elemre. 

    ![Aktiválás most](./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Váltson a bal oldali **Monitorozás** lapra. Ekkor a folyamat futása megjelenik a **Pipeline Runs** (Folyamatfuttatások) listában. 

    ![Folyamatfuttatások monitorozása](./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png)
2. A lista frissítéséhez kattintson a **Refresh** (Frissítés) elemre.
4. A folyamat futásához kapcsolódó tevékenységfuttatások megtekintéséhez kattintson a **View Activity Runs** (Tevékenységfuttatások megtekintése) műveletre az **Actions** (Műveletek) oszlopban. A többi műveleti hivatkozás a folyamat leállítására/újbóli futtatására szolgál. 

    ![Tevékenységfuttatások megtekintése](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png)
5. Csak egy tevékenységfuttatás látható, mivel csak egy tevékenység fut a **HDInsightHive** típusú folyamaton. Az előző nézetre való visszaváltáshoz kattintson a fenti **Pipelines** (Folyamatok) hivatkozásra.

    ![Tevékenységfuttatások](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png)
6. Ellenőrizze, hogy látja-e a kimeneti fájlt az **adftutorial** tároló **outputfolder** mappájában. 

    ![Kimeneti fájl](./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png)

## <a name="next-steps"></a>Következő lépések
Az oktatóanyagban az alábbi lépéseket hajtotta végre: 

> [!div class="checklist"]
> * Adat-előállító létrehozása 
> * Saját üzemeltetésű Integration Runtime létrehozása
> * Azure Storage-beli és Azure HDInsight társított szolgáltatások létrehozása
> * Folyamat létrehozása Hive-tevékenységgel
> * Folyamat futtatásának aktiválása
> * A folyamat futásának monitorozása 
> * Kimenet ellenőrzése

Folytassa a következő oktatóanyaggal, amelyben az adatok Azure Spark-fürtök használatával való átalakítását ismerheti meg:

> [!div class="nextstepaction"]
>[Elágaztatási és láncolási Data Factory-folyamvezérlés](tutorial-control-flow-portal.md)