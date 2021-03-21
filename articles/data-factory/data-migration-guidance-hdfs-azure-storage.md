---
title: Adatok áttelepíthetők egy helyszíni Hadoop-fürtről az Azure Storage-ba
description: Ismerje meg, hogyan telepítheti át a helyszíni Hadoop-fürtökről az Azure Storage-ba az Azure Data Factory használatával.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: 9959a37d9b68d756437a3b4f0d75a2d63385758e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367792"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>A helyszíni Hadoop-fürtről az Azure Storage-ba történő Migrálás Azure Data Factory használata 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory a helyszíni HDFS az Azure Blob Storage-ba vagy Azure Data Lake Storage Gen2-be történő áttelepítésére szolgáló, nagy teljesítményű, robusztus és költséghatékony mechanizmust biztosít. 

A Data Factory két alapvető megközelítést biztosít az adatok helyszíni HDFS az Azure-ba történő áttelepítéséhez. A megközelítést a forgatókönyv alapján választhatja ki. 

- **Data Factory DistCp mód** (ajánlott): a Data Factoryban a [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (elosztott másolat) használatával fájlokat másolhat az Azure Blob Storage-ba (beleértve a [szakaszos másolást](./copy-activity-performance.md#staged-copy)) vagy a Azure Data Lake Store Gen2. A DistCp-mel integrált Data Factory használatával kihasználhatja a meglévő hatékony fürt előnyeit a legjobb másolási sebesség eléréséhez. A rugalmas ütemezést és az Data Factory-ból származó egységes monitorozási élményt is élvezheti. A Data Factory konfigurációjától függően a másolási tevékenység automatikusan létrehoz egy DistCp-parancsot, elküldi az adatait a Hadoop-fürtnek, majd figyeli a másolási állapotot. A helyszíni Hadoop-fürtökről az Azure-ba történő áttelepítéshez Data Factory DistCp módot ajánljuk.
- **Data Factory natív integrációs futtatókörnyezeti mód**: a DistCp nem minden esetben választható. Egy Azure-beli virtuális hálózati környezetben például a DistCp eszköz nem támogatja az Azure ExpressRoute Private-társítást egy Azure Storage virtuális hálózati végponttal. Emellett bizonyos esetekben nem kívánja használni a meglévő Hadoop-fürtöt az adatok áttelepítéséhez, hogy ne helyezzen el nagy terhelést a fürtön, ami hatással lehet a meglévő ETL-feladatok teljesítményére. Ehelyett a Data Factory Integration Runtime natív képességét használhatja olyan motorként, amely az adatok helyszíni HDFS az Azure-ba történő másolását végzi.

Ez a cikk a következő információkat tartalmazza mindkét megközelítéssel kapcsolatban:
> [!div class="checklist"]
> * Teljesítmény 
> * Rugalmasság másolása
> * Hálózati biztonság
> * Magas szintű megoldás-architektúra 
> * Gyakorlati tanácsok a megvalósításhoz  

## <a name="performance"></a>Teljesítmény

Data Factory DistCp módban az átviteli sebesség ugyanaz, mint ha a DistCp eszközt egymástól függetlenül használja. Data Factory DistCp mód maximalizálja a meglévő Hadoop-fürt kapacitását. A DistCp nagyméretű fürtök közötti vagy fürtön belüli másoláshoz is használható. 

A DistCp az MapReduce-t használja az eloszlás, a hibakezelés és a helyreállítás, valamint a jelentéskészítés hatására. Kibontja a fájlok és a könyvtárak listáját a tevékenységek leképezése bemenetbe. Mindegyik feladat egy, a forrás listában megadott partíciót másol. A DistCp-mel integrált Data Factory használatával olyan folyamatokat építhet ki, amelyekkel teljes mértékben kihasználhatja a hálózati sávszélességet, a tárolási IOPS és a sávszélességet, hogy maximalizálja a környezet adatáthelyezési sebességét.  

Data Factory natív integrációs modul mód lehetővé teszi a párhuzamosságot a különböző szinteken. A párhuzamosságok segítségével teljes mértékben kihasználhatja a hálózati sávszélességet, a tárolási IOPS és a sávszélességet az adatátviteli teljesítmény maximalizálása érdekében:

- Egyetlen másolási tevékenység kihasználhatja a méretezhető számítási erőforrások előnyeit. A saját üzemeltetésű integrációs modul segítségével manuálisan méretezheti a gépet, vagy akár több gépre is kibővítheti ([legfeljebb négy csomóponttal](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)). Egyetlen másolási tevékenység particionálja az összes csomóponton beállított fájlját. 
- Egy másolási tevékenység több szál használatával olvas be és ír az adattárba. 
- Data Factory a vezérlés folyamata egyszerre több másolási tevékenységet is elindíthat. Használhat például egy-t [minden hurokhoz](./control-flow-for-each-activity.md). 

További információ: [másolási tevékenység teljesítményének útmutatója](./copy-activity-performance.md).

## <a name="resilience"></a>Rugalmasság

Data Factory DistCp módban különböző DistCp parancssori paramétereket használhat (például a `-i` hibák figyelmen kívül hagyása vagy az `-update` adatok írása, ha a forrásfájl és a célfájl mérete különbözik) a rugalmasság különböző szintjein.

A Data Factory natív integrációs modulban egyetlen másolási tevékenység futtatásakor Data Factory beépített újrapróbálkozási mechanizmussal rendelkezik. Képes az adattárakban vagy a mögöttes hálózaton lévő átmeneti hibák bizonyos szintjének kezelésére. 

Ha a helyszíni HDFS a blob Storage-ba, a helyszíni Data Lake Store HDFS pedig a Gen2-be végez bináris másolást, Data Factory automatikusan nagy mértékben végrehajtja az ellenőrzőpontot. Ha egy másolási tevékenység futása sikertelen vagy időtúllépés miatt megszakad, egy későbbi újrapróbálkozáskor (Győződjön meg róla, hogy az újrapróbálkozások száma > 1), a másolás az utolsó meghibásodási pontról folytatódik az elejétől kezdődően.

## <a name="network-security"></a>Hálózati biztonság 

Alapértelmezés szerint a Data Factory az adatok átvitelét a helyszíni HDFS a blob Storage-ba vagy Azure Data Lake Storage Gen2 a HTTPS protokollon keresztüli titkosított kapcsolat használatával. A HTTPS adattitkosítást biztosít az átvitel során, és megakadályozza a lehallgatást és a belső támadásokat. 

Ha nem szeretné, hogy az adatok a nyilvános interneten keresztül legyenek áthelyezve, a magasabb szintű biztonság érdekében a ExpressRoute keresztül átviheti az adatátvitelt privát kapcsolaton keresztül. 

## <a name="solution-architecture"></a>Megoldásarchitektúra

Ez a rendszerkép az adatok áttelepítését mutatja be a nyilvános interneten keresztül:

![Az adatáttelepítés nyilvános hálózaton keresztüli áttelepítésére szolgáló megoldási architektúrát bemutató diagram](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- Ebben az architektúrában a rendszer biztonságosan továbbítja az adatátvitelt a HTTPS protokollal a nyilvános interneten keresztül. 
- Azt javasoljuk, hogy Data Factory DistCp módot használjon nyilvános hálózati környezetben. Kihasználhatja egy hatékony meglévő fürt előnyeit a legjobb másolási sebesség eléréséhez. A Data Factory a rugalmas ütemezés és az egységes monitorozási élmény előnyeit is élvezheti.
- Ehhez az architektúrához telepítenie kell a Data Factory saját üzemeltetésű integrációs modulját egy vállalati tűzfal mögötti Windows rendszerű gépre, hogy a DistCp-parancsot a Hadoop-fürtön küldje el, és figyelje a másolási állapotot. Mivel a gép nem az a motor, amely az adatok áthelyezését végzi (csak ellenőrzési célra), a gép kapacitása nem befolyásolja az adatátviteli sebességet.
- A DistCp parancs meglévő paraméterei támogatottak.

Ez a rendszerkép az adatok áttelepítését mutatja be privát kapcsolaton keresztül: 

![A magánhálózati hálózaton keresztül áttelepítési megoldás architektúráját bemutató ábra](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- Ebben az architektúrában a rendszer az Azure ExpressRoute keresztül áttelepíti az adatátvitelt egy privát kapcsolaton keresztül. Az adat soha nem halad át a nyilvános interneten.
- A DistCp eszköz nem támogatja az Azure Storage-beli virtuális hálózati végponttal rendelkező ExpressRoute privát társítását. Javasoljuk, hogy az adatáttelepítéshez az integrációs modulon keresztül használja a Data Factory natív képességeit.
- Ebben az architektúrában telepítenie kell a Data Factory saját üzemeltetésű integrációs modult egy Windows rendszerű virtuális GÉPRE az Azure Virtual Networkben. Manuálisan is méretezheti a virtuális gépet, vagy akár több virtuális gépre is kibővítheti a hálózat és a tárhely IOPS vagy sávszélességének teljes kihasználását.
- Az egyes Azure-beli virtuális gépekhez javasolt konfiguráció (a Data Factory saját üzemeltetésű integrációs modul telepítése után) Standard_D32s_v3 32 vCPU és 128 GB memóriával rendelkezik. Az adatok áttelepítése során a virtuális gép processzor-és memóriahasználat figyelésével ellenőrizheti, hogy a virtuális gép vertikális felskálázása a jobb teljesítmény érdekében, vagy a virtuális gép skálázása a költség csökkentése érdekében.
- Akár négy virtuálisgép-csomópont társításával is kibővítheti a saját üzemeltetésű integrációs modult. A saját üzemeltetésű integrációs modulon futó egyetlen másolási feladatot a rendszer automatikusan particionálja a készletet, és az összes virtuálisgép-csomópontot használja a fájlok párhuzamos másolásához. A magas rendelkezésre állás érdekében javasoljuk, hogy kezdjen el két virtuálisgép-csomóponttal, hogy elkerülje az egypontos meghibásodási forgatókönyvet az adatáttelepítés során.
- Ha ezt az architektúrát használja, a kezdeti pillanatkép-adatok áttelepítése és a különbözeti adatok áttelepítése elérhetővé válik.

## <a name="implementation-best-practices"></a>Gyakorlati tanácsok a megvalósításhoz

Javasoljuk, hogy az adatáttelepítés megvalósításakor kövesse az ajánlott eljárásokat.

### <a name="authentication-and-credential-management"></a>Hitelesítés és hitelesítőadat-kezelés 

- A HDFS való hitelesítéshez használhatja a [Windows (Kerberos) vagy a névtelen](./connector-hdfs.md#linked-service-properties)hitelesítést. 
- Több hitelesítési típus is támogatott az Azure Blob Storage-hoz való csatlakozáshoz.  Kifejezetten ajánlott [felügyelt identitásokat használni az Azure-erőforrásokhoz](./connector-azure-blob-storage.md#managed-identity). A Azure Active Directory (Azure AD) automatikusan felügyelt Data Factory identitására épülő felügyelt identitások lehetővé teszik a folyamatok konfigurálását anélkül, hogy hitelesítő adatokat kellene megadnia a társított szolgáltatás definíciójában. Azt is megteheti, hogy egy [egyszerű szolgáltatásnév](./connector-azure-blob-storage.md#service-principal-authentication), egy [közös hozzáférési aláírás](./connector-azure-blob-storage.md#shared-access-signature-authentication)vagy egy [Storage-fiók kulcsa](./connector-azure-blob-storage.md#account-key-authentication)segítségével hitelesíti a blob Storage-tárolót. 
- Több hitelesítési típus is támogatott a Data Lake Storage Gen2hoz való csatlakozáshoz.  Javasoljuk, hogy [felügyelt identitásokat használjon az Azure-erőforrásokhoz](./connector-azure-data-lake-storage.md#managed-identity), de használhat egy [egyszerű szolgáltatásnevet](./connector-azure-data-lake-storage.md#service-principal-authentication) vagy egy [Storage-fiókot](./connector-azure-data-lake-storage.md#account-key-authentication)is. 
- Ha nem használ felügyelt identitásokat az Azure-erőforrásokhoz, javasoljuk, hogy [a hitelesítő adatokat a Azure Key Vaultban tárolja](./store-credentials-in-key-vault.md) , hogy könnyebben lehessen központilag felügyelni és elforgatni a kulcsokat a Data Factory társított szolgáltatások módosítása nélkül. Ez a [CI/CD esetében is ajánlott eljárás](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Kezdeti pillanatkép-adatok áttelepítése 

Data Factory DistCp módban létrehozhat egy másolási tevékenységet, amellyel elküldheti a DistCp parancsot, és különböző paramétereket használhat a kezdeti adatáttelepítési viselkedés szabályozására. 

Data Factory natív integrációs modulban az adatpartíció használatát javasoljuk, különösen akkor, ha 10 TB-nál több adat áttelepítésére van szükség. Az adatparticionáláshoz használja a HDFS a mappák nevét. Ezután az egyes Data Factory másolási feladatok egyszerre csak egy mappát másolhatnak. Több Data Factory másolási feladatot is futtathat párhuzamosan a jobb teljesítmény érdekében.

Ha a másolási feladatok hálózati vagy adattárbeli átmeneti problémák miatt meghibásodnak, a sikertelen másolási feladat újbóli futtatásával újratöltheti az adott partíciót a HDFS. Más partíciókat betöltő más másolási feladatok nem érintettek.

### <a name="delta-data-migration"></a>Különbözeti adatáttelepítés 

Data Factory DistCp módban a DistCp parancssori paramétert használhatja `-update` , ha a forrásfájl és a célfájl mérete eltér a különbözeti adatáttelepítés során, akkor az adatírást.

Data Factory natív integrációs módban az új vagy módosított fájlok HDFS való azonosításának leghatékonyabb módja egy időpartíciós elnevezési konvenció használata. Ha a HDFS lévő adatok időszelet-információval lettek particionálva a fájl-vagy mappanév (például */yyyy/mm/dd/file.csv*) esetében, a folyamat könnyen azonosítható, hogy mely fájlok és mappák legyenek növekményes másolásra.

Ha a HDFS lévő adatai nem particionálják az időkorlátot, Data Factory az új vagy módosított fájlokat a **LastModifiedDate** érték használatával azonosíthatja. Data Factory megkeresi az összes fájlt a HDFS-ből, és csak az új és frissített fájlokat másolja, amelyeknek az utolsó módosítási időbélyege nagyobb, mint a megadott érték. 

Ha nagy számú fájl található a HDFS-ben, a kezdeti fájl vizsgálata hosszú időt is igénybe vehet, függetlenül attól, hogy hány fájl felel meg a szűrési feltételnek. Ebben a forgatókönyvben azt javasoljuk, hogy először particionálja az adatokhoz ugyanazt a partíciót, amelyet a kezdeti pillanatkép-áttelepítéshez használ. Ezt követően a fájlok vizsgálata párhuzamosan is megtörténhet.

### <a name="estimate-price"></a>Becsült ár 

Vegye figyelembe a következő folyamatot az adatok HDFS-ről Azure Blob Storage-ba történő áttelepítéséhez: 

![A díjszabási folyamatot bemutató diagram](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Tegyük fel a következő információkat: 

- A teljes adatmennyiség 1 PB.
- Az adatáttelepítés a Data Factory natív Integration Runtime Mode használatával történik.
- 1 a PB 1 000 partícióra van osztva, és mindegyik másolat egy partíciót helyez át.
- Minden másolási tevékenység egy olyan, saját üzemeltetésű integrációs modulval van konfigurálva, amely négy géphez van társítva, és amely 500 – MBps átviteli sebességet érhet el.
- A ForEach Egyidejűség értéke **4** , az összesített átviteli sebesség pedig 2 GB/s.
- Összesen 146 órát vesz igénybe az áttelepítés befejezéséhez.

A feltételezéseken alapuló becsült ár: 

![Árképzési számításokat megjelenítő táblázat](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Ez egy feltételezett árképzési példa. A tényleges díjszabás a környezet tényleges átviteli sebességével függ.
> Az Azure Windows rendszerű virtuális gépek (saját üzemeltetésű integrációs modult tartalmazó) díja nem található.

### <a name="additional-references"></a>További referenciák

- [HDFS-összekötő](./connector-hdfs.md)
- [Azure Blob Storage-összekötő](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2-összekötő](./connector-azure-data-lake-storage.md)
- [Másolási tevékenység teljesítményének hangolási útmutatója](./copy-activity-performance.md)
- [Helyi integrációs modul létrehozása és konfigurálása](./create-self-hosted-integration-runtime.md)
- [Saját üzemeltetésű integrációs modul magas rendelkezésre állása és méretezhetősége](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Az adatáthelyezés biztonsági szempontjai](./data-movement-security-considerations.md)
- [Hitelesítő adatok tárolása az Azure Key Vaultban](./store-credentials-in-key-vault.md)
- [Fájl növekményes másolása egy időpartíciós fájlnév alapján](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [Új és módosított fájlok másolása a LastModifiedDate alapján](./tutorial-incremental-copy-lastmodified-copy-data-tool.md)
- [Data Factory díjszabási oldala](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Következő lépések

- [Több tárolóból származó fájlok másolása Azure Data Factory használatával](solution-template-copy-files-multiple-containers.md)