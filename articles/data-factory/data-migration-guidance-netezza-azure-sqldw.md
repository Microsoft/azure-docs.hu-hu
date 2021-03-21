---
title: Adatok migrálása helyszíni Netezza-kiszolgálóról az Azure-ba
description: A Azure Data Factory használatával telepítheti át a helyszíni Netezza-kiszolgálóról az Azure-ba történő adatátvitelt.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: 36ca7b709e0ec945f1fb4a9a8b745d20e6a58fe1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367775"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>A helyszíni Netezza-kiszolgálóról az Azure-ba történő Migrálás Azure Data Factory használata 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory egy nagy teljesítményű, robusztus és költséghatékony mechanizmust biztosít az adatok nagy léptékű áttelepítéséhez egy helyszíni Netezza-kiszolgálóról az Azure Storage-fiókjába vagy az Azure szinapszis Analytics-adatbázisába. 

Ez a cikk a következő információkat tartalmazza az adatmérnökök és a fejlesztők számára:

> [!div class="checklist"]
> * Teljesítmény 
> * Rugalmasság másolása
> * Hálózati biztonság
> * Magas szintű megoldás-architektúra 
> * Gyakorlati tanácsok a megvalósításhoz  

## <a name="performance"></a>Teljesítmény

Azure Data Factory olyan kiszolgáló nélküli architektúrát kínál, amely különböző szinteken teszi lehetővé a párhuzamosságot. Ha Ön fejlesztő, ez azt jelenti, hogy a hálózat és az adatbázis sávszélességének teljes kihasználásához folyamatokat hozhat létre, hogy maximalizálja a környezet adatátviteli sebességét.

![Teljesítmény diagram](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

Az előző ábrát a következőképpen lehet értelmezni:

- Egyetlen másolási tevékenység kihasználhatja a méretezhető számítási erőforrások előnyeit. Azure Integration Runtime használatakor az egyes másolási tevékenységeknél [akár 256 DIUs-t](./copy-activity-performance.md#data-integration-units) is megadhat kiszolgáló nélküli módon. Ha saját üzemeltetésű integrációs modult (helyi IR) használ, manuálisan méretezheti a gépet, vagy akár több gépre is kiterjesztheti ([legfeljebb négy csomópontot](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)), és egyetlen másolási tevékenység osztja el a partíciókat az összes csomópont között. 

- Egy másolási tevékenység több szál használatával olvas be és ír az adattárba. 

- Azure Data Factory a vezérlés folyamata egyszerre több másolási tevékenységet is elindíthat. Például elindíthatja őket az [egyes hurkok](./control-flow-for-each-activity.md)használatával. 

További információ: [másolási tevékenység teljesítményének és méretezhetőségének útmutatója](./copy-activity-performance.md).

## <a name="resilience"></a>Rugalmasság

Egy másolási tevékenység futtatásakor Azure Data Factory rendelkezik egy beépített újrapróbálkozási mechanizmussal, amely lehetővé teszi, hogy az informatikai központ bizonyos szintű átmeneti hibákat kezeljen az adattárakban vagy a mögöttes hálózaton.

A másolási tevékenység Azure Data Factory a forrás és a fogadó adattárolók közötti adatmásoláskor két módon kezelheti a nem kompatibilis sorokat. Megszakíthatja, és elvégezheti a másolási tevékenységet, vagy folytathatja a többi adat másolását úgy, hogy kihagyja a nem kompatibilis adatsorokat. Emellett a hiba okának megismeréséhez naplózhatja a nem kompatibilis sorokat az Azure Blob Storage-ban vagy a Azure Data Lake Storeban, kijavíthatja az adatforráson lévő adatok mennyiségét, majd újra próbálkozhat a másolási tevékenységgel.

## <a name="network-security"></a>Hálózati biztonság 

Alapértelmezés szerint a Azure Data Factory a helyszíni Netezza-kiszolgálóról egy Azure Storage-fiókba vagy az Azure szinapszis Analytics-adatbázisába továbbít egy titkosított, Hypertext Transfer Protocol Secure (HTTPS) protokollt használó kapcsolaton keresztül. A HTTPS adattitkosítást biztosít az átvitel során, és megakadályozza a lehallgatást és a belső támadásokat.

Ha nem szeretné, hogy a rendszer a nyilvános interneten keresztül továbbítsa az adatátvitelt, nagyobb biztonságot érhet el azáltal, hogy az Azure Express Route használatával áthelyezi az adatátvitelt egy privát kapcsolaton keresztül. 

A következő szakasz azt ismerteti, hogyan lehet magasabb szintű biztonságot elérni.

## <a name="solution-architecture"></a>Megoldásarchitektúra

Ez a szakasz az adatáttelepítés két módját tárgyalja.

### <a name="migrate-data-over-the-public-internet"></a>Az adatáttelepítés a nyilvános interneten keresztül

![Az adatáttelepítés a nyilvános interneten keresztül](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

Az előző ábrát a következőképpen lehet értelmezni:

- Ebben az architektúrában biztonságosan továbbíthatja az adatátvitelt a HTTPS használatával a nyilvános interneten keresztül.

- Ennek az architektúrának a megvalósításához telepítenie kell a Azure Data Factory Integration Runtime (helyi) szolgáltatást egy vállalati tűzfal mögött található Windows rendszerű gépre. Győződjön meg arról, hogy az integrációs modul közvetlenül tud hozzáférni a Netezza-kiszolgálóhoz. A hálózat és az adattárolók adatmásolási sávszélességének teljes kihasználásához manuálisan méretezheti a gépet, vagy akár több gépre is kibővítheti azokat.

- Ennek az architektúrának a használatával áttelepítheti a kezdeti Pillanatképek és a különbözeti adatok eredeti értékeit is.

### <a name="migrate-data-over-a-private-network"></a>Az adatáttelepítés magánhálózati hálózaton keresztül 

![Az adatáttelepítés magánhálózati hálózaton keresztül](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

Az előző ábrát a következőképpen lehet értelmezni:

- Ebben az architektúrában az Azure Express Route használatával áttelepítheti az adatátvitelt privát kapcsolaton keresztül, az adatforgalom pedig soha nem halad át a nyilvános interneten. 

- Az architektúra eléréséhez telepítenie kell a Azure Data Factory Integration Runtime (helyi) szolgáltatást egy Windows rendszerű virtuális gépre (VM) az Azure-beli virtuális hálózaton belül. A hálózat és az adattárolók adatmásolási sávszélességének teljes kihasználásához manuálisan méretezheti a virtuális gépet, vagy akár több virtuális gépre is kibővítheti azokat.

- Ennek az architektúrának a használatával áttelepítheti a kezdeti Pillanatképek és a különbözeti adatok eredeti értékeit is.

## <a name="implement-best-practices"></a>Ajánlott eljárások implementálása 

### <a name="manage-authentication-and-credentials"></a>A hitelesítés és a hitelesítő adatok kezelése 

- A Netezza való hitelesítéshez használhatja az ODBC- [hitelesítést a kapcsolódási karakterláncon keresztül](./connector-netezza.md#linked-service-properties). 

- Hitelesítés az Azure Blob Storage-ban: 

   - Kifejezetten ajánlott [felügyelt identitásokat használni az Azure-erőforrásokhoz](./connector-azure-blob-storage.md#managed-identity). A Azure Active Directory (Azure AD) automatikusan felügyelt Azure Data Factory identitására épülő felügyelt identitások lehetővé teszik a folyamatok konfigurálását anélkül, hogy hitelesítő adatokat kellene megadniuk a társított szolgáltatás definíciójában.  

   - Azt is megteheti, hogy az Azure Blob Storage-ban az [egyszerű szolgáltatásnév](./connector-azure-blob-storage.md#service-principal-authentication), a [közös hozzáférési aláírás](./connector-azure-blob-storage.md#shared-access-signature-authentication)vagy a [Storage-fiók kulcsa](./connector-azure-blob-storage.md#account-key-authentication)segítségével végez hitelesítést. 

- Hitelesítés Azure Data Lake Storage Gen2: 

   - Kifejezetten ajánlott [felügyelt identitásokat használni az Azure-erőforrásokhoz](./connector-azure-data-lake-storage.md#managed-identity).
   
   - Használhatja a [szolgáltatásnevet](./connector-azure-data-lake-storage.md#service-principal-authentication) vagy a [Storage-fiók kulcsát](./connector-azure-data-lake-storage.md#account-key-authentication)is. 

- Hitelesítés az Azure szinapszis Analytics szolgáltatásban:

   - Kifejezetten ajánlott [felügyelt identitásokat használni az Azure-erőforrásokhoz](./connector-azure-sql-data-warehouse.md#managed-identity).
   
   - Használhatja az [egyszerű szolgáltatásnév](./connector-azure-sql-data-warehouse.md#service-principal-authentication) vagy az [SQL-hitelesítés](./connector-azure-sql-data-warehouse.md#sql-authentication)szolgáltatást is.

- Ha nem használ felügyelt identitásokat az Azure-erőforrásokhoz, javasoljuk, hogy [a hitelesítő adatokat a Azure Key Vaultban tárolja](./store-credentials-in-key-vault.md) , hogy egyszerűbb legyen a kulcsok központi kezelése és elforgatása anélkül, hogy módosítani kellene Azure Data Factory társított szolgáltatásokat. Ez a [CI/CD-vel kapcsolatos ajánlott eljárások](./continuous-integration-deployment.md#best-practices-for-cicd)egyike is. 

### <a name="migrate-initial-snapshot-data"></a>Kezdeti pillanatkép-adatok migrálása 

Kis tábláknál (azaz a 100 GB-nál kisebb mennyiségű, illetve az Azure-ba áttelepíthető, két órán belül elérhető) táblákon minden egyes másolási feladatot betöltenek. A nagyobb átviteli sebesség érdekében több Azure Data Factory másolási feladatot is futtathat a különálló táblák egyidejű betöltéséhez. 

Az egyes másolási feladatokon belül a párhuzamos lekérdezések futtatásához és az adatok partíciók szerinti másolásához a [ `parallelCopies` tulajdonság beállítását](./copy-activity-performance.md#parallel-copy) a következő adatpartíciós beállítások egyikével is elérheti:

- A nagyobb hatékonyság érdekében javasoljuk, hogy egy adatszeletből induljon el.  Győződjön meg arról, hogy a `parallelCopies` beállításban szereplő érték kisebb, mint az Netezza-kiszolgálón lévő tábla adatszelet partícióinak teljes száma.  

- Ha az egyes adatszelet-partíciók mennyisége továbbra is nagy (például 10 GB vagy nagyobb), javasoljuk, hogy váltson dinamikus tartományú partícióra. Ez a beállítás nagyobb rugalmasságot biztosít a partíciók számának és az egyes partíciók mennyiségének a partíciós oszlop, a felső határ és az alsó határ alapján történő meghatározásához.

Nagyobb táblák esetén (azaz 100 GB-os vagy annál nagyobb mennyiségű, illetve két órán belül *nem* telepíthető Azure-ba történő Migrálás esetén) ajánlott az adatok egyéni lekérdezéssel történő particionálása, majd minden egyes másolási feladatot egyszerre egy partíció másolása. A jobb teljesítmény érdekében több Azure Data Factory másolási feladatot is futtathat egyszerre. Az egyes partíciók egyéni lekérdezéssel való betöltéséhez szükséges minden egyes másolási feladatsor esetében növelheti az átviteli sebességet, ha az adatszelet vagy a dinamikus tartomány használatával engedélyezi a párhuzamosságot. 

Ha egy hálózati vagy adattár-átmeneti probléma miatt nem sikerül a másolási feladatok végrehajtása, újrafuttathatja a sikertelen másolási feladatot, hogy az adott partíciót a táblából töltse újra. Más partíciókat betöltő más másolási feladatok nem érintettek.

Amikor az Azure szinapszis Analytics-adatbázisba tölti be az adatok betöltését, javasoljuk, hogy a másolási feladatban engedélyezze az Azure Blob Storage-ba történő előkészítést átmenetiként.

### <a name="migrate-delta-data"></a>Különbözeti adatáttelepítés 

A táblából származó új vagy frissített sorok azonosításához használjon időbélyeg-oszlopot vagy egy növekményes kulcsot a sémán belül. Ezt követően a legújabb értéket magas vízjelként is tárolhatja egy külső táblában, majd a következő betöltéskor felhasználhatja a különbözeti értékek szűrésére. 

Minden táblázat egy másik vízjel oszlopot használhat az új vagy frissített sorainak azonosításához. Javasoljuk, hogy hozzon létre egy külső vezérlőelem-táblázatot. A táblázatban minden egyes sor a Netezza-kiszolgáló egy tábláját jelöli az adott vízjel-oszlop nevével és a magas küszöbértékkel. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű integrációs modul konfigurálása

Ha a Netezza-kiszolgálóról az Azure-ba végzi az adatok áttelepítését, függetlenül attól, hogy a kiszolgáló a vállalati tűzfal mögött vagy egy virtuális hálózati környezetben található, akkor a saját üzemeltetésű integrációs modult egy Windows rendszerű gépre vagy virtuális gépre kell telepítenie, amely az adatok áthelyezéséhez használt motor. A saját üzemeltetésű integrációs modul telepítésekor a következő módszert javasoljuk:

- Minden Windows rendszerű gép vagy virtuális gép esetében a 32 vCPU és a 128-GB memória konfigurációját kell kezdeni. Az adatáttelepítés során megtarthatja az IR-gép processzor-és memóriahasználat figyelését, és megtekintheti, hogy szükség van-e a gép jobb teljesítményének növelésére vagy a számítógép méretezésére a költség megtakarítása érdekében.

- Azt is megteheti, hogy legfeljebb négy csomópontot társít, egyetlen saját üzemeltetésű IR-vel. A saját üzemeltetésű integrációs modulon futó egyetlen másolási feladattípus automatikusan alkalmazza az összes virtuálisgép-csomópontot az adatok párhuzamos másolására. A magas rendelkezésre állás érdekében kezdjen el négy virtuálisgép-csomóponttal, hogy elkerülje a meghibásodást az adatáttelepítés során.

### <a name="limit-your-partitions"></a>Partíciók korlátozása

A bevált gyakorlat szerint a megvalósíthatósági koncepciót (POC) egy reprezentatív minta adatkészlettel kell elvégeznie, hogy minden egyes másolási tevékenységhez megfelelő partíciós méretet lehessen meghatározni. Javasoljuk, hogy az egyes partíciókat két órán belül töltse be az Azure-ba.  

Egy tábla másolásához kezdjen egyetlen másolási tevékenységgel egyetlen, saját üzemeltetésű IR-géppel. Fokozatosan növelje a `parallelCopies` beállítást a tábla adatszelet-partícióinak száma alapján. Megtudhatja, hogy a teljes tábla betölthető-e két órán belül az Azure-ba a másolási feladatokból származó átviteli sebességnek megfelelően. 

Ha két órán belül nem tölthető be az Azure-ba, és a saját üzemeltetésű IR csomópont és az adattár kapacitása nem teljes mértékben használatban van, fokozatosan növelje az egyidejű másolási tevékenységek számát, amíg el nem éri a hálózat korlátját, vagy az adattárak sávszélesség-korlátját. 

Tartsa figyelemmel a CPU-és memóriahasználat figyelését a saját üzemeltetésű IR-gépen, és készen áll a gép vertikális felskálázására vagy a több gépre való skálázásra, amikor azt látja, hogy a processzor és a memória teljes mértékben használatban van. 

Ha sávszélesség-szabályozási hibát tapasztal, ahogy azt Azure Data Factory másolási tevékenység jelzi, csökkentse a párhuzamosságot vagy a `parallelCopies` beállítást a Azure Data Factoryban, vagy növelje a hálózat és az adattárak sávszélességének vagy I/O-műveleteinek másodpercenkénti (IOPS) korlátait. 


### <a name="estimate-your-pricing"></a>A díjszabás becslése 

Vegye figyelembe a következő folyamatot, amely az adatok helyszíni Netezza-kiszolgálóról egy Azure szinapszis Analytics-adatbázisba való átköltöztetésére szolgál:

![A díjszabási folyamat](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Tegyük fel, hogy az alábbi utasítások teljesülnek: 

- A teljes adatmennyiség 50 terabájt (TB). 

- Az első megoldási architektúra használatával migráljuk az adatáttelepítést (a Netezza-kiszolgáló a tűzfal mögött található).

- Az 50 TB-os kötet 500 partícióra van osztva, és mindegyik másolási tevékenység egy partíciót helyez el.

- Minden másolási tevékenység egy saját üzemeltetésű, négy gépen üzemelő IR-vel van konfigurálva, és 20 megabájt/másodperc (MB/s) sebesség elérését éri el. (A másolási tevékenységen belül a `parallelCopies` 4 értékre van állítva, és az adatoknak a táblából való betöltéséhez szükséges minden szál 5 MB/s adatátviteli sebességet érhet el.)

- A ForEach Egyidejűség értéke 3, az összesített átviteli sebesség pedig 60 MBps.

- Összesen 243 órát vesz igénybe az áttelepítés befejezéséhez.

Az előző feltételezések alapján itt látható a becsült ár: 

![A díjszabási táblázat](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Az előző táblázatban látható díjszabás feltételezett. A tényleges díjszabás a környezet tényleges átviteli sebességével függ. A Windows rendszerű gép (a saját üzemeltetésű IR-vel telepített) díja nem található. 

### <a name="additional-references"></a>További referenciák

További információt a következő cikkekben és útmutatókban talál:

- [Adatok migrálása helyszíni adattárház-adatbázisból az Azure-ba Azure Data Factory használatával](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Netezza-összekötő](./connector-netezza.md)
- [ODBC-összekötő](./connector-odbc.md)
- [Azure Blob Storage-összekötő](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2-összekötő](./connector-azure-data-lake-storage.md)
- [Azure szinapszis Analytics-összekötő](./connector-azure-sql-data-warehouse.md)
- [Másolási tevékenység teljesítményének hangolási útmutatója](./copy-activity-performance.md)
- [Helyi integrációs modul létrehozása és konfigurálása](./create-self-hosted-integration-runtime.md)
- [Saját üzemeltetésű integrációs modul, HA és méretezhetőség](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Az adatáthelyezés biztonsági szempontjai](./data-movement-security-considerations.md)
- [Hitelesítő adatok tárolása az Azure Key Vaultban](./store-credentials-in-key-vault.md)
- [Adatok növekményes másolása egy táblából](./tutorial-incremental-copy-portal.md)
- [Adatok növekményes másolása több táblából](./tutorial-incremental-copy-multiple-tables-portal.md)
- [Azure Data Factory díjszabási oldala](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Következő lépések

- [Több tárolóból származó fájlok másolása Azure Data Factory használatával](solution-template-copy-files-multiple-containers.md)