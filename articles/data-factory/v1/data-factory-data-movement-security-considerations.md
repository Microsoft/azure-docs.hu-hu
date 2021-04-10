---
title: A Azure Data Factory adatáthelyezésének biztonsági szempontjai
description: További információ az adatáthelyezés biztonságossá tételéről Azure Data Factoryban.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 33b1ad381b3f7865768f9e39295a2985f8aa5234
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375102"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory – az adatáthelyezés biztonsági szempontjai

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a Data Factory adatáthelyezési biztonsági szempontjait](../data-movement-security-considerations.md).

## <a name="introduction"></a>Bevezetés
Ez a cikk azt az alapszintű biztonsági infrastruktúrát ismerteti, amelyet az adatátviteli szolgáltatások Azure Data Factory használnak az adatok biztonságossá tételéhez. Azure Data Factory felügyeleti erőforrások az Azure biztonsági infrastruktúrára épülnek, és az Azure által kínált összes lehetséges biztonsági intézkedést felhasználhatják.

A Data Factory-megoldásokkal egy vagy több [adatfolyamatot](data-factory-create-pipelines.md) is létrehozhat. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. Ezek a folyamatok abban a régióban találhatók, ahol az adat-előállítót létrehozták. 

Bár a Data Factory csak az **USA nyugati** régiójában, az **USA keleti** régiójában és az **észak-európai** régióban érhető el, az adatáthelyezési szolgáltatás [globálisan számos régióban](data-factory-data-movement-activities.md#global)elérhető. Data Factory szolgáltatás biztosítja, hogy az adatforgalom ne maradjon földrajzi terület/régió, kivéve, ha kifejezetten arra utasítja a szolgáltatást, hogy alternatív régiót használjon, ha az adatátviteli szolgáltatás még nincs telepítve az adott régióban. 

A Azure Data Factory maga nem tárol adatokat, kivéve a társított szolgáltatás hitelesítő adatait a felhőalapú adattárakhoz, amelyek tanúsítványokkal vannak titkosítva. Lehetővé teszi, hogy adatvezérelt munkafolyamatokat hozzon létre a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) közötti adatmozgás előkészítéséhez és az adatok feldolgozásához más régiókban, illetve helyszíni környezetben lévő [számítási szolgáltatások](data-factory-compute-linked-services.md) használatával. Lehetővé teszi továbbá a [munkafolyamatok monitorozását és kezelését](data-factory-monitor-manage-pipelines.md) a programozott és a felhasználói felületi mechanizmusokkal.

A Azure Data Factory használatával történő adatáthelyezés a következőhöz lett **hitelesítve** :
-    [HIPAA/HITECH](/compliance/regulatory/offering-hipaa-hitech)  
-    [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-    [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-    [CSA-CSILLAG](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Ha érdekli az Azure megfelelősége, és hogy az Azure Hogyan védi a saját infrastruktúráját, látogasson el a [Microsoft adatvédelmi központba](https://microsoft.com/en-us/trustcenter/default.aspx). 

Ebben a cikkben a következő két adatáthelyezési forgatókönyvben tekintjük át a biztonsági szempontokat: 

- **Felhőalapú forgatókönyv**– ebben a forgatókönyvben a forrás és a cél is nyilvánosan elérhető az interneten keresztül. Ezek közé tartoznak a felügyelt felhőalapú tárolási szolgáltatások, például az Azure Storage, az Azure szinapszis Analytics, a Azure SQL Database, a Azure Data Lake Store, az Amazon S3, az Amazon vöröseltolódás, az SaaS-szolgáltatások, például a Salesforce és a webes protokollok, például az FTP és a OData. A támogatott adatforrások teljes listáját [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats)találja.
- **Hibrid forgatókönyv**– ebben a forgatókönyvben a forrás vagy a cél tűzfal mögött van, vagy egy helyszíni vállalati hálózaton belül, vagy az adattár egy magánhálózat/virtuális hálózatban (leggyakrabban a forrás) található, és nem nyilvánosan elérhető. A virtuális gépeken üzemeltetett adatbázis-kiszolgálók is ebbe a forgatókönyvbe tartoznak.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Felhőbeli forgatókönyvek
### <a name="securing-data-store-credentials"></a>Adattároló hitelesítő adatainak biztonságossá tétele
Azure Data Factory védi az adattár hitelesítő **adatait a** **Microsoft által kezelt tanúsítványok** használatával. Ezeket a tanúsítványokat **kétévente elforgatják (ami** magában foglalja a tanúsítvány megújítását és a hitelesítő adatok áttelepítését). Ezeket a titkosított hitelesítő adatokat a rendszer biztonságosan tárolja **Azure Data Factory felügyeleti szolgáltatások által felügyelt Azure-tárolóban**. Az Azure Storage biztonságával kapcsolatos további információkért tekintse meg az [Azure Storage biztonsági áttekintése](../../storage/blobs/security-recommendations.md)című témakört.

### <a name="data-encryption-in-transit"></a>Adattitkosítás az átvitel során
Ha a felhőalapú adattár támogatja a HTTPS-t vagy a TLS-t, az adatátviteli Data Factory szolgáltatások és a felhőalapú adattárolók közötti adatforgalom a biztonságos csatorna HTTPS vagy TLS protokollon keresztül történik.

> [!NOTE]
> A **Azure SQL Database** és az **Azure szinapszis Analytics** összes kapcsolata mindig titkosítást (SSL/TLS) igényel, miközben az adatok átvitele az adatbázisból és az adatbázisból történik. Egy folyamat JSON-szerkesztővel való készítése közben adja hozzá a **titkosítási** tulajdonságot, és állítsa **igaz** értékre a **kapcsolódási karakterláncban**. A [Másolás varázsló](data-factory-azure-copy-wizard.md)használatakor a varázsló alapértelmezés szerint beállítja ezt a tulajdonságot. Az **Azure Storage** esetében a kapcsolati sztringben **HTTPS protokollt** használhat.

### <a name="data-encryption-at-rest"></a>Adat-titkosítás inaktív állapotban
Egyes adattárak támogatják a nyugalmi állapotban lévő adatok titkosítását. Javasoljuk, hogy ezekhez az adattárakhoz engedélyezze az adattitkosítási mechanizmust. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics
Az Azure szinapszis Analytics transzparens adattitkosítás (TDE) szolgáltatásával megvédheti a kártékony tevékenységek fenyegetését azáltal, hogy valós idejű titkosítást és visszafejtést végez a nyugalmi állapotban. Ez a viselkedés átlátható az ügyfél számára. További információ: [adatbázis biztonságossá tétele az Azure szinapszis Analyticsben](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
A Azure SQL Database támogatja az transzparens adattitkosítást (TDE), amely segít megvédeni a kártékony tevékenységek fenyegetését azáltal, hogy az alkalmazás módosítása nélkül valós idejű titkosítást és visszafejtést végez. Ez a viselkedés átlátható az ügyfél számára. További információ: [transzparens adattitkosítás Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
A Azure Data Lake tároló a fiókban tárolt adathalmazok titkosítását is biztosítja. Ha engedélyezve van, a Data Lake tároló automatikusan titkosítja az adatok titkosítását, mielőtt megőrzi és visszafejti azokat a lekérés előtt, így az adatokhoz hozzáférő ügyfél átlátszóvá válik. További információ: [Security in Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage és Azure Table Storage
Az Azure Blob Storage és az Azure Table Storage támogatja a Storage Service Encryption (SSE), amely automatikusan titkosítja az adatait, mielőtt megőrzi a tárterületet, és visszafejti azokat a beolvasás előtt. További információ: [Azure Storage Service encryption for](../../storage/common/storage-service-encryption.md)inaktív adatok.

#### <a name="amazon-s3"></a>Amazon S3
Az Amazon S3 az inaktív adatok ügyfél-és kiszolgálói titkosítását is támogatja. További információkért lásd: [adatok védelme titkosítás használatával](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). A Data Factory jelenleg nem támogatja az Amazon S3-t egy virtuális magánhálózati felhőben (VPC) belül.

#### <a name="amazon-redshift"></a>Amazon Redshift
Az Amazon vöröseltolódás támogatja a fürtök titkosítását a nyugalmi állapotban lévő adatokhoz. További információ: [Amazon vöröseltolódás Database encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). A Data Factory jelenleg nem támogatja az Amazon vöröseltolódását egy VPC-ban. 

#### <a name="salesforce"></a>Salesforce
A Salesforce támogatja a védelmi platform titkosítását, amely lehetővé teszi az összes fájl, melléklet és egyéni mező titkosítását. További információ: [a webkiszolgáló OAuth-hitelesítési folyamatának ismertetése](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hibrid forgatókönyvek (adatkezelés átjáró használatával)
Hibrid forgatókönyvek esetén adatkezelés átjárót egy helyszíni hálózaton vagy egy virtuális hálózaton (Azure) vagy egy virtuális magánhálózati felhőben (Amazon) belül kell telepíteni. Az átjárónak képesnek kell lennie a helyi adattárak elérésére. Az átjáróval kapcsolatos további információkért lásd: [adatkezelés átjáró](data-factory-data-management-gateway.md). 

![adatkezelés átjáró csatornák](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

A **parancssori csatorna** lehetővé teszi az adatátviteli szolgáltatások közötti kommunikációt Data Factory és adatkezelés átjárón. A kommunikáció a tevékenységgel kapcsolatos információkat tartalmaz. Az adatcsatorna a helyszíni adattárak és a Felhőbeli adattárak közötti adatátvitelhez használatos.    

### <a name="on-premises-data-store-credentials"></a>Helyszíni adattároló hitelesítő adatai
A helyszíni adattárakhoz tartozó hitelesítő adatokat helyileg (nem a felhőben) tárolja a rendszer. Három különböző módon is megadhatók. 

- **Egyszerű szöveges** (kevésbé biztonságos) használata a HTTPS-en keresztül az Azure Portal/másolás varázslójával. A hitelesítő adatokat a rendszer egyszerű szövegként adja át a helyszíni átjárónak.
- **JavaScript titkosítási függvénytár használata a másolási varázslóval**.
- A **Click-Once-alapú hitelesítő adatok kezelő alkalmazásának** használata. A Click-Once alkalmazás a helyszíni gépen fut, amely hozzáféréssel rendelkezik az átjáróhoz, és beállítja a hitelesítő adatokat az adattárhoz. Ez a lehetőség és a következő a legbiztonságosabb lehetőség. A Hitelesítőadat-kezelő alkalmazás alapértelmezés szerint a 8050-as portot használja a gépen a biztonságos kommunikáció érdekében.  
- A hitelesítő adatok titkosításához használja a [New-AzDataFactoryEncryptValue PowerShell-](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) parancsmagot. A parancsmag azt a tanúsítványt használja, amelyet az átjáró a hitelesítő adatok titkosítására való használatra konfigurált. Használhatja a parancsmag által visszaadott titkosított hitelesítő adatokat, és hozzáadhatja a **ConnectionString** **EncryptedCredential** eleméhez a [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) parancsmaggal vagy a portálon a Data Factory-szerkesztőben használt JSON-kódrészletben. Ez a lehetőség és a Click-Once alkalmazás a legbiztonságosabb lehetőség. 

#### <a name="javascript-cryptography-library-based-encryption"></a>JavaScript titkosítási függvénytár-alapú titkosítás
A [Másolás varázslóval](data-factory-copy-wizard.md)titkosíthatja az adattár hitelesítő adatait a [JavaScript titkosítási kódtár](https://www.microsoft.com/download/details.aspx?id=52439) használatával. Ha ezt a beállítást választja, a másolási varázsló lekéri az átjáró nyilvános kulcsát, és a használatával titkosítja az adattár hitelesítő adatait. A hitelesítő adatokat az átjáró számítógépe visszafejti, és a Windows [DPAPI](/previous-versions/ms995355(v=msdn.10))védi.

**Támogatott böngészők:** IE8, IE9, IE10, IE11, Microsoft Edge, és a legújabb Firefox, Chrome, Opera, Safari böngészők. 

#### <a name="click-once-credentials-manager-app"></a>Kattintson egyszeres hitelesítő adatok kezelő alkalmazás
A folyamatok létrehozásakor a Azure Portal/másolás varázslóból is elindíthatja a Click-Once-alapú Hitelesítőadat-kezelő alkalmazást. Ez az alkalmazás biztosítja, hogy a hitelesítő adatok ne legyenek áthelyezve egyszerű szövegre a vezetéken keresztül. Alapértelmezés szerint a **8050** -as portot használja a gépen a biztonságos kommunikáció érdekében. Ha szükséges, ez a port módosítható.  
  
![HTTPS-port az átjáróhoz](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Az adatkezelés-átjáró jelenleg egyetlen **tanúsítványt** használ. Ez a tanúsítvány az átjáró telepítésekor jön létre (a adatkezelés-átjáróra vonatkozik, amely a 2016. novemberi és a 2.4. xxxx. x vagy újabb verzió után jött létre). Ezt a tanúsítványt a saját SSL/TLS-tanúsítványával is lecserélheti. Ezt a tanúsítványt a Click-Once Hitelesítőadat-kezelő alkalmazás használja az adattároló hitelesítő adatainak beállításához az átjáró géphez való biztonságos kapcsolódáshoz. Az adattároló hitelesítő adatait biztonságosan a helyszínen tárolja a számítógép Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) a Gateway használatával. 

> [!NOTE]
> A régebbi, a 2016. és a 2.3. xxxx. x verzió előtt telepített átjárók továbbra is használni szeretnék a felhőben tárolt hitelesítő adatokat. Akkor is, ha az átjárót a legújabb verzióra frissíti, a hitelesítő adatokat a rendszer nem telepíti át egy helyszíni gépre.    
  
| Átjáró verziója (a létrehozás során) | Tárolt hitelesítő adatok | Hitelesítő adatok titkosítása/biztonság | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3. xxxx. x | Felhőben | Titkosított tanúsítvány használatával (a Hitelesítőadat-kezelő alkalmazás által használttól eltérő) | 
| > = 2.4. xxxx. x | A helyszínen | Védett DPAPI-n keresztül | 
  

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Az adatátvitel a biztonságos csatorna **https** protokollon és a **TLS** protokollon keresztül történik az Azure-szolgáltatásokkal folytatott kommunikáció során.
 
A helyszíni hálózat és az Azure közötti kommunikációs csatorna további biztonságossá tételéhez [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) vagy [Express Route](../../expressroute/expressroute-introduction.md) is használható.

A virtuális hálózat a hálózat logikai ábrázolása a felhőben. Helyszíni hálózatot az Azure Virtual Network (VNet) szolgáltatáshoz is összekapcsolhat az IPSec VPN (helyek közötti) vagy az expressz útvonal (privát társ) beállításával        

A következő táblázat összefoglalja a hálózati és átjárók konfigurációs javaslatait a hibrid adatáthelyezéshez használt forrás-és célhelyek különböző kombinációi alapján.

| Forrás | Cél | Hálózati konfiguráció | Átjáró beállítása |
| ------ | ----------- | --------------------- | ------------- | 
| Helyszíni | Virtuális hálózatokban üzembe helyezett virtuális gépek és felhőalapú szolgáltatások | IPSec VPN (pont – hely vagy hely – hely) | Az átjáró a VNet-ben a helyszínen vagy egy Azure-beli virtuális gépen (VM) is telepíthető. | 
| Helyszíni | Virtuális hálózatokban üzembe helyezett virtuális gépek és felhőalapú szolgáltatások | ExpressRoute (privát társ) | Az átjáró a VNet-ben a helyszínen vagy egy Azure-beli virtuális gépen is telepíthető. | 
| Helyszíni | Nyilvános végponttal rendelkező Azure-alapú szolgáltatások | ExpressRoute (nyilvános peering) | Az átjárót a helyszínen kell telepíteni | 

Az alábbi képek a adatkezelés Gateway használatát mutatják be a helyszíni adatbázis és az Azure-szolgáltatások közötti adatáthelyezéshez az expressz útvonal és az IPSec VPN használatával (Virtual Network):

**Expressz útvonal:**
 
![Express Route használata az átjáróval](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN átjáróval](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-filtering-ip-address-of-gateway"></a>Tűzfal-konfigurációk és az átjáró IP-címének szűrése

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Helyszíni/privát hálózatok tűzfalkövetelményei    
A vállalatokban a **vállalati tűzfal** a szervezet központi útválasztóján fut. A **Windows tűzfal** démonként fut azon a helyi gépen, amelyen az átjáró telepítve van. 

A következő táblázat a **vállalati tűzfal** **kimenő portokra** és tartományokra vonatkozó követelményeit tartalmazza.

| Tartománynevek | Kimenő portok | Leírás |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Az átjáró számára szükséges az adatátviteli szolgáltatásokhoz való kapcsolódáshoz Data Factory |
| `*.core.windows.net` | 443 | Az átjáró az Azure Storage-fiókhoz való csatlakozáshoz használja az [előkészített másolási](data-factory-copy-activity-performance.md#staged-copy) szolgáltatás használatakor. | 
| `*.frontend.clouddatahub.net` | 443 | Ahhoz szükséges, hogy az átjáró csatlakozhasson a Azure Data Factory szolgáltatáshoz. | 
| `*.database.windows.net` | 1433    | (Nem kötelező) akkor szükséges, ha a cél Azure SQL Database/Azure szinapszis Analytics. Az előkészített másolási szolgáltatással az 1433-as port megnyitása nélkül másolhat Adatmásolást Azure SQL Database/Azure szinapszis Analytics szolgáltatásba. | 
| `*.azuredatalakestore.net` | 443 | (Nem kötelező) akkor szükséges, ha a célhelye Azure Data Lake Store | 

> [!NOTE] 
> Előfordulhat, hogy a megfelelő adatforrások által megkövetelt módon kell kezelnie a portok/szűrési tartományokat a vállalati tűzfal szintjén. Ez a táblázat csak az Azure SQL Database, az Azure szinapszis Analytics, Azure Data Lake Store példaként használja.   

A következő táblázat a **Windows tűzfal** **bejövő portokra** vonatkozó követelményeit tartalmazza.

| Bejövő portok | Leírás | 
| ------------- | ----------- | 
| 8050 (TCP) | A Hitelesítőadat-kezelő alkalmazása megköveteli a helyszíni adattárakhoz tartozó hitelesítő adatok biztonságos beállítását az átjárón. | 

![Az átjáró portjának követelményei](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurationsfiltering-in-data-store"></a>IP-konfigurációk/szűrés az adattárban
A felhőben lévő egyes adattárakhoz szükség van a számítógép IP-címének jóváhagyására is. Győződjön meg arról, hogy az átjáró számítógépének IP-címe megfelelően van jóváhagyva/konfigurálva a tűzfalon.

A következő felhőalapú adattárakhoz az átjáró-számítógép IP-címének jóváhagyása szükséges. Az adattárak némelyike alapértelmezés szerint nem igényli az IP-cím jóváhagyását. 

- [Azure SQL Database](../../azure-sql/database/firewall-configure.md) 
- [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/how-to-configure-firewall.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Kérdés:** Megoszthatók az átjárók a különböző adatüzemek között?
**Válasz:** Ez a funkció még nem támogatott. Már dolgozunk rajta.

**Kérdés:** Milyen portokra vonatkozó követelmények vonatkoznak az átjáró működéséhez?
**Válasz:** Az átjáró HTTP-alapú kapcsolatokat tesz elérhetővé az Internet megnyitásához. A kapcsolódáshoz a **443-es és a 80-as kimenő portnak** kell megnyitnia az átjáró számára. Nyissa meg a 8050-es **bejövő portot** a Hitelesítőadat-kezelő alkalmazáshoz csak a számítógép szintjén (a vállalati tűzfal szintjén). Ha Azure SQL Database vagy az Azure szinapszis Analytics forrásként vagy célhelyként van használatban, akkor a **1433** -as portot is meg kell nyitnia. További információ: [tűzfal-konfigurációk és IP-címek szűrése](#firewall-configurations-and-filtering-ip-address-of gateway) szakasz. 

**Kérdés:** Mik a tanúsítványokra vonatkozó követelmények az átjáróhoz?
**Válasz:** Az aktuális átjáróhoz szükség van egy tanúsítványra, amelyet a Hitelesítőadat-kezelő alkalmazás az adattár hitelesítő adatainak biztonságos beállításához használ. Ez a tanúsítvány az átjáró telepítője által létrehozott és konfigurált önaláírt tanúsítvány. Ehelyett saját TLS/SSL-tanúsítványt is használhat. További információ: [Click-Once Hitelesítőadat-kezelő alkalmazás](#click-once-credentials-manager-app) szakasz. 

## <a name="next-steps"></a>Következő lépések
A másolási tevékenység teljesítményével kapcsolatos további információkért lásd: a [másolási tevékenység teljesítményének és finomhangolásának útmutatója](data-factory-copy-activity-performance.md).