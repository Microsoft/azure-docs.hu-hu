---
title: Integrációs modul
description: Ismerje meg az integrációs modult az Azure Data Factoryban.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: 911674a80b531a50cfb429c5dc0ff41f1aaceb08
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389943"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integrációs modul az Azure Data Factoryban 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az integrációs modul (Integration Runtime, IR), az Azure Data Factory által használt számítási infrastruktúra a következő adatintegrációs képességeket biztosítja különböző hálózati környezetekben:

- **Adatfolyam**: [adatfolyamatok](concepts-data-flow-overview.md) végrehajtása felügyelt Azure számítási környezetben.  
- **Adatáthelyezés**: a nyilvános hálózatban lévő adattárakban és a magánhálózaton (helyszíni vagy virtuális magánhálózat) lévő adattárakban tárolt Adatmásolás. Támogatást biztosít a beépített összekötőkhöz, a formátum átalakításához, az oszlopleképezéshez és a nagy teljesítményű, méretezhető adatátvitelhez.
- **Tevékenység elküldése**: különböző számítási szolgáltatásokon, például az Azure Databrickson, az Azure HDInsight, a Azure Machine Learningon, a Azure SQL Databaseon, a SQL Serveron és egyebeken futó átalakítási tevékenységek elküldése és monitorozása.
- **SSIS-csomag végrehajtása**: Natívan végrehajthat SQL Server Integration Services- (SSIS-) csomagokat egy Azure-beli felügyelt számítási környezetben.

A Data Factoryban a végrehajtandó műveletet egy tevékenység határozza meg. A társított szolgáltatások a céladattárat vagy a számítási szolgáltatást határozzák meg. Az integrációs modulok hídként szolgálnak a tevékenység és a társított szolgáltatások között.  A társított szolgáltatás vagy tevékenység hivatkozik rá, és megadja azt a számítási környezetet, ahol a tevékenység vagy a szolgáltatás fut, vagy amelyről elküldi a szolgáltatást. Ily módon a tevékenység végrehajtható a céladattárhoz vagy számítási szolgáltatáshoz lehető legközelebb eső régióban, a lehető leghatékonyabban, a biztonsági és megfelelőségi igényeknek is megfelelően.

Az integrációs modulokat a Azure Data Factory UX-ben hozhatja létre a [felügyeleti központban](author-management-hub.md) , valamint a rájuk hivatkozó tevékenységeket, adatkészleteket vagy adatfolyamatokat.

## <a name="integration-runtime-types"></a>Integrációsmodul-típusok

A Data Factory háromféle Integration Runtime (IR) típust kínál, és ki kell választania azt a típust, amely a legjobban kiszolgálja az adatintegrációs képességeket és a hálózati környezet igényeit.  A három típus a következő:

- Azure
- Saját üzemeltetésű
- Azure SSIS

Az alábbi táblázat ismerteti az integrációs modulok egyes típusainak képességeit és hálózati támogatását:

Integrációs modul típusa | Nyilvános hálózat | Magánhálózat
------- | -------------- | ---------------
Azure | Adatfolyam<br/>Adatáthelyezés<br/>Tevékenység küldése | Adatfolyam<br/>Adatáthelyezés<br/>Tevékenység küldése
Saját üzemeltetésű | Adatáthelyezés<br/>Tevékenység küldése | Adatáthelyezés<br/>Tevékenység küldése
Azure SSIS | SSIS-csomag végrehajtása | SSIS-csomag végrehajtása


## <a name="azure-integration-runtime"></a>Azure-beli integrációs modul

Az Azure Integration Runtime a következőket teheti:

- Adatfolyamatok futtatása az Azure-ban 
- Másolási tevékenység futtatása a felhőalapú adattárak között
- A következő átalakítási tevékenységek küldése a nyilvános hálózatban: Databricks notebook/jar/Python tevékenység, HDInsight struktúra tevékenység, HDInsight Pig tevékenység, HDInsight MapReduce tevékenység, HDInsight Spark-tevékenység, HDInsight streaming Activity, Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenység, Azure Machine Learning Studio (klasszikus) frissítési erőforrás-tevékenységek, tárolt eljárási tevékenység, Data Lake Analytics U

### <a name="azure-ir-network-environment"></a>Azure-beli integrációs modul hálózati környezete

Azure Integration Runtime támogatja az adattárakhoz való csatlakozást és a nyilvános elérhetőségű végpontokkal rendelkező számítási szolgáltatásokat. A felügyelt Virtual Network engedélyezése Azure Integration Runtime támogatja az adattárakhoz való kapcsolódást magánhálózati környezetben a Private link Service használatával.

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure-beli integrációs modul számítási erőforrásai és skálázása
Az Azure-beli integrációs modul teljesen felügyelt, kiszolgáló nélküli számítást biztosít az Azure-ban.  Nem kell aggódnia az infrastruktúra kiépítésével, a Szoftvertelepítés, a javítások vagy a kapacitás skálázásával kapcsolatban.  Ráadásul csak a tényleges használat időtartamára fizet.

Az Azure-beli integrációs modul biztosítja a natív számítást az adatok felhőalapú adattárak közötti biztonságos, megbízható és nagy teljesítményű módon való mozgatásához.  Beállíthatja a másolási tevékenységhez használni kívánt adatintegrálási egységek mennyiségét, és az Azure IR számítási mérete rugalmasan felskálázható vertikálisan ennek megfelelően anélkül, hogy explicit módon módosítania kellene az Azure-beli integrációs modul méretét. 

A tevékenység elküldése egy könnyű művelet, amellyel a tevékenység átirányítható a cél számítási szolgáltatásba, így nincs szükség a forgatókönyv számítási méretének skálázására.

Azure IR létrehozásával és konfigurálásával kapcsolatos információkért lásd: [Azure Integration Runtime létrehozása és konfigurálása](create-azure-integration-runtime.md). 

> [!NOTE] 
> Az Azure Integration Runtime az adatfolyam-futtatókörnyezethez kapcsolódó tulajdonságokkal rendelkezik, amely meghatározza a mögöttes számítási infrastruktúrát, amely az adatfolyamatok futtatására szolgál. 

## <a name="self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul

Egy saját üzemeltetésű IR a következőkre képes:

- Másolási tevékenység futtatása felhőalapú adattárak és egy magánhálózaton lévő adattár között.
- A következő átalakítási tevékenységek elküldése a helyszíni vagy az Azure Virtual Network számítási erőforrásaira: HDInsight-struktúra tevékenység (BYOC-saját fürt), HDInsight Pig-tevékenység (BYOC), HDInsight MapReduce tevékenység (BYOC), HDInsight Spark-tevékenység (BYOC), HDInsight streaming Activity (BYOC), Azure Machine Learning Studio (klasszikus) kötegelt végrehajtási tevékenység, Azure Machine Learning Studio (klasszikus) frissítési erőforrás-tevékenységek, tárolt eljárási tevékenység, Data Lake Analytics U-SQL-tevékenység , Egyéni tevékenység (Azure Batchon fut), keresési tevékenység és metaadatok beolvasása tevékenység.

> [!NOTE] 
> A saját üzemeltetésű integrációs modult használva olyan adattárakat támogathat, amelyek a saját illesztőprogramot, például az SAP Hana-t vagy a MySQL-t igénylik.  További információ: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE] 
> A Java Runtime Environment (JRE) a saját üzemeltetésű IR függősége. Győződjön meg arról, hogy a JRE ugyanarra a gazdagépre van telepítve.

### <a name="self-hosted-ir-network-environment"></a>Saját üzemeltetésű integrációs modul hálózati környezete

Ha biztonságos adatintegrációt szeretne végezni egy magánhálózati környezetben, amely nem rendelkezik közvetlen, nyilvános Felhőbeli környezettel, akkor a vállalati tűzfal mögött vagy egy virtuális magánhálózaton belül telepítheti a helyi környezetet a helyszíni környezetbe.  A saját üzemeltetésű integrációs modul csak kimenő HTTP-alapú kapcsolatokat hoz létre az internet megnyitásához.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>Saját üzemeltetésű integrációs modul erőforrásai és skálázása

Telepítse a saját üzemeltetésű integrációs modult egy helyszíni gépre vagy egy magánhálózaton belüli virtuális gépre. Jelenleg csak Windows operációs rendszeren támogatjuk a saját üzemeltetésű integrációs modulok futtatását.  

A magas rendelkezésre állás és a méretezhetőség érdekében horizontálisan felskálázhatja saját üzemeltetésű integrációs modulját, ha több helyszíni géppel aktív-aktív módban társít hozzá egy logikai példányt.  További információ: a saját üzemeltetésű integrációs modul [létrehozása és konfigurálása](create-self-hosted-integration-runtime.md) című cikk, útmutató a részletekhez.

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS integrációs modul

A meglévő SSIS számítási feladat átemeléséhez létrehozhat egy Azure-SSIS integrációs modult az SSIS-csomagok natív létrehozásához.

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS integrációs modul hálózati környezete

Az Azure-SSIS integrációs modul kiépíthető nyilvános hálózaton vagy magánhálózaton is.  A helyszíni adathozzáférés támogatása az Azure-SSIS integrációs modulnak a helyszíni hálózathoz csatlakoztatott virtuális hálózattal való összekapcsolása révén történik.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS integrációs modul számítási erőforrásai és skálázása

Az Azure-SSIS integrációs modul egy, az SSIS-csomagok futtatására dedikált Azure-beli virtuális gépekből álló teljesen felügyelt fürt. A SSIS-projektek/csomagok (SSISDB) katalógusához saját Azure SQL Database vagy SQL felügyelt példányt hozhat. A számítási teljesítmény vertikális felskálázásához adjon meg egy csomópontméretet, és skálázza fel horizontálisan a fürt csomópontszámának megadásával. Az Azure-SSIS integrációs modul futtatási költségének kezeléséhez igény szerint leállíthatja és elindíthatja azt.

További információkért tekintse meg az útmutatók között az Azure-SSIS integrációs modul létrehozását és konfigurálását ismertető cikket.  Létrehozásukat követően kevés vagy szinte semmilyen módosítással, a helyszíni SSIS-csomagokkal megegyező módon üzembe helyezheti és kezelheti meglévő SSIS-csomagjait az olyan jól ismert eszközök használatával, mint az SQL Server Data Tools (SSDT) és az SQL Server Management Studio (SSMS).

Az Azure-SSIS integrációs modullal kapcsolatos további információkért tekintse át a következő cikkeket: 

- [Oktatóanyag: SSIS-csomagok üzembe helyezése az Azure-ban](./tutorial-deploy-ssis-packages-azure.md). Ez a cikk részletes útmutatást nyújt egy Azure-SSIS IR létrehozásához, és egy Azure SQL Database használatával futtatja az SSIS-katalógust. 
- [Útmutató: Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md). Ez a cikk az oktatóanyagon alapul, és útmutatást nyújt az SQL felügyelt példányának használatáról és az IR-nek a virtuális hálózathoz való csatlakoztatásáról. 
- [Azure-SSIS integrációs modul monitorozása](monitor-integration-runtime.md#azure-ssis-integration-runtime). Ez a cikk bemutatja, hogyan kérhet le információkat egy Azure-SSIS integrációs modulról, és ismerteti a visszaadott információkban található állapotok leírását. 
- [Azure-SSIS integrációs modul kezelése](manage-azure-ssis-integration-runtime.md). Ez a cikk bemutatja, hogyan lehet leállítani, elindítani vagy eltávolítani egy Azure-SSIS integrációs modult. Azt is bemutathatja, hogyan skálázhatja fel horizontálisan az Azure-SSIS integrációs modult úgy, hogy további csomópontokat ad hozzá. 
- [Azure-SSIS integrációs modul csatlakoztatása virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md). Ez a cikk egy Azure-SSIS integrációs modul Azure virtuális hálózathoz való csatlakoztatásával kapcsolatos elméleti információkat tartalmaz. Azt is ismerteti, hogyan használható az Azure Portal a virtuális hálózat oly módon való konfigurálására, hogy az Azure-SSIS integrációs modul csatlakozhasson ahhoz. 

## <a name="integration-runtime-location"></a>Az integrációs modul helye

### <a name="relationship-between-factory-location-and-ir-location"></a>A gyári hely és az IR-hely közötti kapcsolat

Amikor az ügyfél létrehoz egy adatelőállító-példányt, meg kell adnia az adatelőállító helyét. A Data Factory ott található, ahol az adat-előállító metaadatait tárolja a rendszer, és ahonnan a folyamat aktiválása indul. A gyári metaadatok csak az ügyfél által választott régióban tárolódnak, és nem lesznek más régiókban tárolva.

Az adat-előállító ilyenkor más Azure-régiókban lévő adattárakhoz és számítási szolgáltatásokhoz is hozzáférhet az adatok adattárak közötti áthelyezése vagy az adatok számítási szolgáltatásokkal történő feldolgozása érdekében. Ez a viselkedés az adatok megfelelősége, a hatékonyság és a hálózati kimeneti forgalmának alacsonyabb költségei érdekében a [globálisan elérhető integrációs modulon](https://azure.microsoft.com/global-infrastructure/services/) keresztül valósul meg.

Az integrációs modul helye meghatározza a háttérszámítások helyét és azt a helyet, ahol az adatok áthelyezése, a tevékenységküldés és az SSIS-csomag végrehajtása történik. Az integrációs modul helye eltérhet annak az adat-előállítónak a helyétől, amelyhez tartozik. 

### <a name="azure-ir-location"></a>Az Azure-beli integrációs modul helye

Beállíthat egy Azure IR adott helyét, amely esetben a tevékenység végrehajtása vagy a feladás az adott régióban fog történni.

Ha úgy dönt, hogy az automatikus feloldás Azure IR a nyilvános hálózaton, amely az alapértelmezett,

- Másolási tevékenység esetén az ADF a legjobb erőfeszítést tesz a fogadó adattár helyének automatikus észleléséhez, majd az IR-t az azonos régióban, ha van ilyen, vagy a legközelebbi ugyanazon a földrajzi helyen; Ha a fogadó adattárának régiója nem észlelhető, a rendszer Alternatív megoldásként az adatfeldolgozó régióban lévő IR-t használja.

  Tegyük fel, hogy a gyára az USA keleti régiójában lett létrehozva. 
  
  - Az USA nyugati régiójában az Azure Blobba való adatmásoláskor, ha az ADF sikeresen észlelte, hogy a blob az USA nyugati régiójában található, a másolási tevékenység az USA nyugati régiójában lévő IR-ben történik Ha a régió észlelése sikertelen, a másolási tevékenységet az USA keleti régiójában hajtja végre az IR-ben.
  - Ha olyan Salesforce másol, amelyből a régió nem észlelhető, a másolási tevékenységet az USA keleti régiójában hajtja végre az IR-ben.

  >[!TIP] 
  >Ha adatmegfelelőségi követelmények szigorúak, és biztosítania kell, hogy az adatok nem hagynak el egy adott földrajzi helyet, az adott régióban létrehozhat egy Azure-beli integrációs modult, és a ConnectVia tulajdonság használatával ehhez irányíthatja a társított szolgáltatást. Ha például át szeretné másolni a Blobból Egyesült Királyság déli régiója az Azure szinapszis Analytics-be Egyesült Királyság déli régiója, és biztosítani szeretné, hogy az adatok ne legyenek az Egyesült Királyságból, hozzon létre egy Azure IR a Egyesült Királyság déli régiója, és csatolja mindkét társított szolgáltatást ehhez az IR-hez.

- A keresési/GetMetadata/törlési tevékenység végrehajtásához (más néven folyamat tevékenységekhez), az átalakítási tevékenység elküldéséhez (más néven külső tevékenységekhez) és a szerzői műveletekhez (a kapcsolat teszteléséhez, a mappák listájának megtekintéséhez és a táblázatok listájához, az előzetes verzióhoz tartozó adatokhoz) Az ADF a következőt használja: az IR

- Az adatforgalom esetében az ADF az IR-t használja az adatfeldolgozó régióban. 

  > [!TIP] 
  > Jó gyakorlat lenne annak biztosítása, hogy az adatforgalom ugyanabban a régióban fusson, mint a megfelelő adattárak (ha lehetséges). Ezt megteheti a Azure IR automatikus feloldásával (ha az adattár helye megegyezik a Data Factory helyével), vagy egy új Azure IR példány létrehozásával ugyanabban a régióban, ahol az adattár található, majd végrehajtja az adatfolyamot. 

Ha engedélyezte a felügyelt Virtual Network a Azure IR automatikus feloldásához, az ADF a következőt használja: az adatgyár régiójában. 

A folyamattevékenységek monitorozása nézetben a felhasználói felületen vagy a tevékenységfigyelés hasznos adatainál figyelemmel követheti, melyik integrációs modul lép életbe a tevékenység végrehajtása során.

### <a name="self-hosted-ir-location"></a>A saját üzemeltetésű integrációs modul helye

A saját üzemeltetésű integrációs modul logikailag van regisztrálva a Data Factoryban, és a funkciók támogatásához használt számítást meg kell adnia. Ezért a saját üzemeltetésű integrációs modulok esetében nincs explicit hely tulajdonság. 

Ha adatmozgás végrehajtásához használja, a saját üzemeltetésű integrációs modul kinyeri az adatokat a forrásból, és a célra írja.

### <a name="azure-ssis-ir-location"></a>Az Azure-SSIS integrációs modul helye

Az Azure-SSIS integrációs modul számára a megfelelő helyet kiválasztása az ETL folyamatokban létfontosságú a magas teljesítmény eléréséhez.

- A Azure-SSIS IR helyének nem kell megegyeznie az adatgyár helyével, de meg kell egyeznie a saját Azure SQL Database vagy a felügyelt SQL-példány helyével, ahol a SSISDB. Ily módon az Azure-SSIS integrációs modul könnyen hozzáfér az SSISDB-hez anélkül, hogy jelentős adatforgalom zajlana különböző helyek között.
- Ha nem rendelkezik meglévő SQL Database vagy SQL felügyelt példánnyal, de helyszíni adatforrásokkal/célhelyekkel rendelkezik, hozzon létre egy új Azure SQL Database vagy SQL felügyelt példányt a helyszíni hálózathoz csatlakoztatott virtuális hálózat ugyanazon a helyén.  Így a Azure-SSIS IR az új Azure SQL Database vagy SQL felügyelt példány használatával hozhatja létre, és az adott virtuális hálózathoz csatlakozva mind ugyanazon a helyen található, ami gyakorlatilag minimalizálja a különböző helyek közötti adatáthelyezést.
- Ha a meglévő Azure SQL Database vagy SQL felügyelt példány helye nem egyezik meg a helyszíni hálózathoz csatlakoztatott virtuális hálózat helyével, először hozza létre a Azure-SSIS IR egy meglévő Azure SQL Database vagy SQL felügyelt példány használatával, és csatlakozzon egy másik virtuális hálózathoz ugyanazon a helyen, majd konfigurálja a virtuális hálózatot a különböző helyek közötti virtuális hálózati kapcsolathoz.

Az alábbi ábrán a Data Factory és a hozzá tartozó integrációs modul beállításai találhatók:

![Az integrációs modul helye](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>A használandó integrációs modul meghatározása

### <a name="copy-activity"></a>Másolási tevékenység

A másolási tevékenységhez szükséges egy forrás és fogadó társított szolgáltatás az adatfolyam irányának meghatározására. A rendszer az alábbi logikával határozza meg, melyik integrációsmodell-példányt használja a másolás végrehajtásához: 

- **Másolás két felhőalapú adatforrás között**: Ha a forrás és a fogadó társított szolgáltatás is Azure IR használ, az ADF a helyi Azure IR használja, vagy ha az [integrációs modul helye](#integration-runtime-location) című szakaszban leírtak szerint automatikusan meghatározza a Azure IR helyét.
- **Egy felhőalapú adatforrás és egy magánhálózaton lévő adatforrás közötti másolás**: ha a forrás vagy a fogadó társított szolgáltatása egy saját üzemeltetésű integrációs modulra mutat, a rendszer azon a saját üzemeltetésű integrációs modulon hajtja végre a másolási tevékenységet.
- **Másolás két magánhálózati adatforrás között**: a forrás-és a fogadó társított szolgáltatásnak az integrációs modul ugyanazon példányára kell mutatnia, és az integrációs modul a másolási tevékenység végrehajtásához használatos.

### <a name="lookup-and-getmetadata-activity"></a>Keresési és metaadat-beolvasási tevékenység

A keresési és metaadat-beolvasási tevékenységet a rendszer az adattár társított szolgáltatásához rendelt integrációs modulon hajtja végre.

### <a name="external-transformation-activity"></a>Külső átalakítási tevékenység

A külső számítási motort használó minden külső átalakítási tevékenységhez tartozik egy cél számítási társított szolgáltatás, amely egy integrációs modulra mutat. Ez az Integration Runtime-példány meghatározza azt a helyet, ahol a külső, kézzel kódolt átalakítási tevékenység elküldése megtörténik.

### <a name="data-flow-activity"></a>Adatfolyam-tevékenység

Az adatfolyam-tevékenységek végrehajtása a hozzá társított Azure Integration Runtime-on történik. Az adatfolyamatok által használt Spark-számításokat a Azure Integration Runtime adatáramlási tulajdonságai határozzák meg, és teljes mértékben az ADF kezelik.

## <a name="next-steps"></a>Következő lépések

Lásd az alábbi cikkeket:

- [Azure Integration Runtime létrehozása](create-azure-integration-runtime.md)
- [Saját üzemeltetésű integrációs modul létrehozása](create-self-hosted-integration-runtime.md)
- [Hozzon létre egy Azure-SSIS integrációs](create-azure-ssis-integration-runtime.md)modult. Ez a cikk az oktatóanyagon alapul, és útmutatást nyújt az SQL felügyelt példányának használatáról és az IR-nek a virtuális hálózathoz való csatlakoztatásáról.