---
title: Biztonsági szempontok
description: Ismerteti az alapszintű biztonsági infrastruktúrát, amelyet az adatátviteli szolgáltatások Azure Data Factory használnak az adatok biztonságossá tételéhez.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 08a20349d012652fb15dc1818d0201d93175aed8
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550572"
---
# <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>A Azure Data Factory adatáthelyezésének biztonsági szempontjai

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
>
> * [1-es verzió](v1/data-factory-data-movement-security-considerations.md)
> * [Aktuális verzió](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt az alapszintű biztonsági infrastruktúrát ismerteti, amelyet az adatátviteli szolgáltatások Azure Data Factory használnak az adatok biztonságossá tételéhez. Data Factory felügyeleti erőforrások az Azure biztonsági infrastruktúrára épülnek, és az Azure által kínált összes lehetséges biztonsági intézkedést felhasználhatják.

A Data Factory-megoldásokkal egy vagy több [adatfolyamatot](concepts-pipelines-activities.md) is létrehozhat. A folyamatok olyan tevékenységek logikus csoportosításai, amelyek együttesen vesznek részt egy feladat végrehajtásában. Ezek a folyamatok abban a régióban találhatók, ahol az adat-előállítót létrehozták.

Annak ellenére, hogy Data Factory csak néhány régióban érhető el, az adatátviteli szolgáltatás [globálisan elérhető](concepts-integration-runtime.md#integration-runtime-location) az adatmegfelelőség, a hatékonyság és a csökkentett hálózati kimenő költségek biztosításához.

A Azure Data Factory beleértve a Azure Integration Runtime és a saját üzemeltetésű Integration Runtime nem tárol semmilyen ideiglenes adatot, nem gyorsítótáraz adatokat vagy naplókat, kivéve a társított szolgáltatás hitelesítő adatait a Felhőbeli adattárakhoz, amelyek tanúsítványokkal vannak titkosítva. A Data Factory segítségével adatvezérelt munkafolyamatokat hozhat létre, amelyek a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)közötti adatáthelyezést és az adatok feldolgozását végzik a más régiókban vagy helyszíni környezetben lévő [számítási szolgáltatások](compute-linked-services.md) használatával. Az SDK-k és a Azure Monitor használatával is nyomon követheti és kezelheti a munkafolyamatokat.

A Data Factory minősítése a következő:

| **[CSA-csillag minősítése](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](/compliance/regulatory/offering-iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](/compliance/regulatory/offering-hipaa-hitech)** |
| **[HITRUST](/compliance/regulatory/offering-hitrust)** |

Ha érdekli az Azure megfelelősége, és hogyan védi az Azure a saját infrastruktúráját, látogasson el a [Microsoft adatvédelmi központba](https://microsoft.com/trustcenter/default.aspx). Az összes Azure-megfelelőségi ajánlat legújabb listájáért látogasson el a következőre:  https://aka.ms/AzureCompliance .

Ebben a cikkben a következő két adatáthelyezési forgatókönyvben tekintjük át a biztonsági szempontokat:

- **Felhőbeli forgatókönyv**: ebben az esetben a forrás és a cél is nyilvánosan elérhető az interneten keresztül. Ezek közé tartoznak a felügyelt felhőalapú tárolási szolgáltatások, például az Azure Storage, az Azure szinapszis Analytics, a Azure SQL Database, a Azure Data Lake Store, az Amazon S3, az Amazon vöröseltolódás, az SaaS-szolgáltatások, például a Salesforce és a webes protokollok, például az FTP és a OData. A támogatott  [adattárakban és-formátumokban](copy-activity-overview.md#supported-data-stores-and-formats)található támogatott adatforrások teljes listája.
- **Hibrid forgatókönyv**: ebben a forgatókönyvben a forrás vagy a cél egy tűzfal mögött vagy egy helyszíni vállalati hálózaton belül van. Az adattár pedig magánhálózat vagy virtuális hálózat (leggyakrabban a forrás), és nem nyilvánosan elérhető. A virtuális gépeken üzemeltetett adatbázis-kiszolgálók is ebbe a forgatókönyvbe tartoznak.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Felhőbeli forgatókönyvek

### <a name="securing-data-store-credentials"></a>Adattároló hitelesítő adatainak biztonságossá tétele

- **Titkosított hitelesítő adatok tárolása egy Azure Data Factory felügyelt tárolóban**. A Data Factory az adattár hitelesítő adatainak védelme érdekében titkosítja őket a Microsoft által kezelt tanúsítványokkal. Ezeket a tanúsítványokat kétévente elforgatják (ami magában foglalja a tanúsítvány megújítását és a hitelesítő adatok áttelepítését). Az Azure Storage biztonságával kapcsolatos további információkért lásd: az [Azure Storage biztonsági áttekintése](../storage/blobs/security-recommendations.md).
- **Hitelesítő adatok tárolása Azure Key Vaultban**. Az adattár hitelesítő adatait [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)is tárolhatja. Data Factory lekéri a hitelesítő adatot egy tevékenység végrehajtása során. További információ: [a hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Adattitkosítás az átvitel során
Ha a felhőalapú adattár támogatja a HTTPS-t vagy a TLS-t, az adatátviteli Data Factory szolgáltatások és a felhőalapú adattárolók közötti adatforgalom a biztonságos csatorna HTTPS vagy TLS protokollon keresztül történik.

> [!NOTE]
> Az Azure SQL Database és az Azure szinapszis Analytics szolgáltatással létesített összes kapcsolat titkosítást (SSL/TLS) igényel, miközben az adatok átvitele az adatbázisba és az adatbázisból történik. Amikor JSON használatával készít folyamatokat, adja hozzá a titkosítási tulajdonságot, és állítsa **igaz** értékre a kapcsolódási karakterláncban. Az Azure Storage esetében a kapcsolati sztringben **HTTPS protokollt** használhat.

> [!NOTE]
> Ha engedélyezni szeretné a titkosítást az Oracle-adatok áthelyezése közben, kövesse az alábbi lehetőségek egyikét:
>
> 1. Az Oracle-kiszolgálón nyissa meg az Oracle Advanced Security (OAS) parancsot, és konfigurálja a titkosítási beállításokat, amelyek támogatják a Triple DES encryption (3DES) és a Advanced Encryption Standard (AES) használatát, további részletekért tekintse meg a következő témakört [:.](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) Az ADF automatikusan egyezteti a titkosítási módszert, hogy az Oracle-hez való kapcsolódáskor a OAS-ben konfigurált konfigurációt használja.
> 2. Az ADF-ben a EncryptionMethod = 1 a kapcsolati sztringben (a társított szolgáltatásban) adható hozzá. Ez titkosítási módszerként az SSL/TLS protokollt fogja használni. Ennek használatához le kell tiltania a nem SSL titkosítási beállításokat a OAS az Oracle-kiszolgáló oldalán a titkosítási ütközés elkerülése érdekében.

> [!NOTE]
> A használt TLS-verzió 1,2.

### <a name="data-encryption-at-rest"></a>Adat-titkosítás inaktív állapotban

Egyes adattárak támogatják a nyugalmi állapotban lévő adatok titkosítását. Javasoljuk, hogy engedélyezze az adattitkosítási mechanizmust ezekhez az adattárakhoz. 

#### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Az Azure szinapszis Analytics transzparens adattitkosítás (TDE) segít megvédeni a kártékony tevékenységek fenyegetését azáltal, hogy valós idejű titkosítást és visszafejtést végez a nyugalmi állapotban. Ez a viselkedés átlátható az ügyfél számára. További információ: [adatbázis biztonságossá tétele az Azure szinapszis Analyticsben](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

A Azure SQL Database támogatja az transzparens adattitkosítást (TDE), amely segít megvédeni a kártékony tevékenységek fenyegetését azáltal, hogy az adatok valós idejű titkosítását és visszafejtését végzi, anélkül, hogy az alkalmazás módosítására lenne szükség. Ez a viselkedés átlátható az ügyfél számára. További információ: [transzparens adattitkosítás a SQL Database és az adattárházban](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store

A Azure Data Lake Store a fiókban tárolt adatkezeléshez is biztosít titkosítást. Ha engedélyezve van, a Data Lake Store automatikusan titkosítja az adatokkal, mielőtt megőrzi és visszafejti azokat a beolvasás előtt, így transzparensvé válik az adatokhoz hozzáférő ügyfél számára. További információ: [Security in Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage és Azure Table Storage

Az Azure Blob Storage és az Azure Table Storage támogatási Storage Service Encryption (SSE), amely automatikusan titkosítja az adatait, mielőtt megőrzi a tárterületet, és visszafejti a beolvasás előtt. További információ: [Azure Storage Service encryption for](../storage/common/storage-service-encryption.md)inaktív adatok.

#### <a name="amazon-s3"></a>Amazon S3

Az Amazon S3 az inaktív adatok ügyfél-és kiszolgálói titkosítását is támogatja. További információkért lásd: [adatok védelme titkosítás használatával](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift

Az Amazon vöröseltolódás támogatja a fürtök titkosítását a nyugalmi állapotban lévő adatokhoz. További információ: [Amazon vöröseltolódás Database encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce

A Salesforce támogatja a védelmi platform titkosítását, amely lehetővé teszi az összes fájl, melléklet és egyéni mező titkosítását. További információ: [a webkiszolgáló OAuth-hitelesítési folyamatának ismertetése](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Hibrid forgatókönyvek

Hibrid forgatókönyvek esetén a saját üzemeltetésű integrációs modult egy helyszíni hálózaton, egy virtuális hálózaton (Azure) belül vagy egy virtuális magánhálózati felhőben (Amazon) belül kell telepíteni. A saját üzemeltetésű integrációs modulnak képesnek kell lennie a helyi adattárak elérésére. További információ a saját üzemeltetésű integrációs modulról: [saját üzemeltetésű integrációs modul létrehozása és konfigurálása](./create-self-hosted-integration-runtime.md). 

![saját üzemeltetésű Integration Runtime-csatornák](media/data-movement-security-considerations/data-management-gateway-channels.png)

A parancssori csatorna lehetővé teszi az adatátviteli szolgáltatások közötti kommunikációt Data Factory és saját üzemeltetésű integrációs modulban. A kommunikáció a tevékenységgel kapcsolatos információkat tartalmaz. Az adatcsatorna a helyszíni adattárak és a Felhőbeli adattárak közötti adatátvitelhez használatos.    

### <a name="on-premises-data-store-credentials"></a>Helyszíni adattároló hitelesítő adatai

A hitelesítő adatokat a rendszer az adat-előállítóban tárolhatja, vagy az [adat-előállító](store-credentials-in-key-vault.md) az Azure Key Vault-ból futtatott futtatókörnyezetben hivatkozhat rá. Ha a adat-előállítóban tárolja a hitelesítő adatokat, a rendszer mindig titkosítja a saját üzemeltetésű integrációs modulban. 

   - **Hitelesítő adatok helyi tárolása**. Ha közvetlenül a **set-AzDataFactoryV2LinkedService** parancsmagot használja a kapcsolati karakterláncokkal és a JSON-ban beágyazott hitelesítő adatokkal, akkor a társított szolgáltatás titkosítva van, és a saját üzemeltetésű integrációs modulban tárolódik.  Ebben az esetben a hitelesítő adatok az Azure háttér-szolgáltatáson keresztül futnak, amely rendkívül biztonságos a saját üzemeltetésű integrációs géphez, ahol végül titkosítva és tárolva van. A saját üzemeltetésű integrációs modul a Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) használatával titkosítja a bizalmas adatokat és a hitelesítő adatokat.

   - **Hitelesítő adatok tárolása Azure Key Vaultban**. Az adattár hitelesítő adatait [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)is tárolhatja. Data Factory lekéri a hitelesítő adatot egy tevékenység végrehajtása során. További információ: [a hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md).

   - A hitelesítő **adatokat helyileg tárolhatja anélkül, hogy a hitelesítő adatokat a saját üzemeltetésű integrációs modulra kellene átadnia az Azure-háttér** használatával. Ha a hitelesítő adatokat a saját üzemeltetésű integrációs modulban helyileg kívánja titkosítani és tárolni anélkül, hogy a hitelesítő adatokat a adat-előállító háttérrendszer használatával kellene elvégeznie, kövesse a [hitelesítő adatok titkosítása a helyszíni adattárakhoz Azure Data Factory-ben](encrypt-credentials-self-hosted-integration-runtime.md)című témakört. Az összes összekötő támogatja ezt a beállítást. A saját üzemeltetésű integrációs modul a Windows [DPAPI](/previous-versions/ms995355(v=msdn.10)) használatával titkosítja a bizalmas adatokat és a hitelesítő adatokat. 

   - A **New-AzDataFactoryV2LinkedServiceEncryptedCredential** parancsmag használatával Titkosítsa a társított szolgáltatás hitelesítő adatait és a bizalmas adatokat a társított szolgáltatásban. Ezután a visszaadott JSON-t (a kapcsolati karakterlánc **EncryptedCredential** eleme) használva hozzon létre egy társított szolgáltatást a **set-AzDataFactoryV2LinkedService** parancsmag használatával.  

#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>A társított szolgáltatás saját üzemeltetésű integrációs modulban való titkosításához használt portok

Alapértelmezés szerint a PowerShell az 8060-as portot használja a gépen a saját üzemeltetésű integrációs modul használatával a biztonságos kommunikáció érdekében. Ha szükséges, ez a port módosítható.  

![HTTPS-port az átjáróhoz](media/data-movement-security-considerations/https-port-for-gateway.png)


### <a name="encryption-in-transit"></a>Titkosítás az átvitel során

Az adatátvitel a biztonságos csatorna HTTPS protokollon és a TLS protokollon keresztül történik az Azure-szolgáltatásokkal folytatott kommunikáció során.

Az [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) -t vagy az [Azure ExpressRoute](../expressroute/expressroute-introduction.md) -t is használhatja a kommunikációs csatorna további biztonságossá tételéhez a helyszíni hálózat és az Azure között.

Az Azure Virtual Network a hálózat logikai ábrázolása a felhőben. A helyszíni hálózatot a virtuális hálózathoz az IPSec VPN (helyek közötti) vagy a ExpressRoute (privát társ) beállításával lehet összekapcsolni.

A következő táblázat összefoglalja a hálózat és a saját üzemeltetésű integrációs modul konfigurációs javaslatait a hibrid adatáthelyezéshez használt forrás-és célhelyek különböző kombinációi alapján.

| Forrás      | Cél                              | Hálózati konfiguráció                    | Integrációs modul telepítése                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Helyszíni | Virtuális hálózatokban üzembe helyezett virtuális gépek és felhőalapú szolgáltatások | IPSec VPN (pont – hely vagy hely – hely) | A saját üzemeltetésű integrációs modult egy Azure-beli virtuális gépre kell telepíteni a virtuális hálózaton.  |
| Helyszíni | Virtuális hálózatokban üzembe helyezett virtuális gépek és felhőalapú szolgáltatások | ExpressRoute (privát társ)           | A saját üzemeltetésű integrációs modult egy Azure-beli virtuális gépre kell telepíteni a virtuális hálózaton.  |
| Helyszíni | Nyilvános végponttal rendelkező Azure-alapú szolgáltatások | ExpressRoute (Microsoft-társ)            | A saját üzemeltetésű integrációs modult a helyszínen vagy egy Azure-beli virtuális gépen lehet telepíteni. |

Az alábbi képek a saját üzemeltetésű integrációs modul használatát mutatják be a helyszíni adatbázis és az Azure-szolgáltatások közötti adatáthelyezéshez a ExpressRoute és az IPSec VPN használatával (Azure Virtual Network):

#### <a name="express-route"></a>Express Route

![ExpressRoute használata az átjáróval](media/data-movement-security-considerations/express-route-for-gateway.png) 

#### <a name="ipsec-vpn"></a>IPSec VPN

![IPSec VPN átjáróval](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a>Tűzfal-konfigurációk és engedélyezési lista beállítása IP-címekhez

> [!NOTE]
> Előfordulhat, hogy a megfelelő adatforrások által megkövetelt portokat kell kezelnie, vagy a vállalati tűzfal szintjén be kell állítania a tartományok engedélyezési listáját. Ez a táblázat csak a Azure SQL Database, az Azure szinapszis Analytics és a Azure Data Lake Store használatát szemlélteti.

> [!NOTE]
> Az Azure Data Factoryon keresztüli adatelérési stratégiákkal kapcsolatos részletekért tekintse meg [ezt a cikket](./data-access-strategies.md#data-access-strategies-through-azure-data-factory).

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>A tűzfalra vonatkozó követelmények helyszíni/magánhálózati hálózatokhoz

A vállalatokban a vállalati tűzfal a szervezet központi útválasztóján fut. A Windows tűzfal démonként fut azon a helyi gépen, amelyben a saját üzemeltetésű integrációs modul telepítve van. 

A következő táblázat a vállalati tűzfalak kimenő portokra és tartományokra vonatkozó követelményeit tartalmazza:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE]
> Előfordulhat, hogy a megfelelő adatforrások által megkövetelt portokat kell kezelnie, vagy a vállalati tűzfal szintjén be kell állítania a tartományok engedélyezési listáját. Ez a táblázat csak a Azure SQL Database, az Azure szinapszis Analytics és a Azure Data Lake Store használatát szemlélteti.   

A következő táblázat a Windows tűzfal bejövő portokra vonatkozó követelményeit tartalmazza:

| Bejövő portok | Description                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | A PowerShell-titkosítási parancsmag szükséges a [helyi adattárakhoz tartozó hitelesítő adatok titkosítása Azure Data Factoryban](encrypt-credentials-self-hosted-integration-runtime.md), valamint a Hitelesítőadat-kezelő alkalmazás által a helyi adattárakhoz tartozó hitelesítő adatok biztonságos beállítása a saját üzemeltetésű integrációs modulban. |

![Az átjáró portjának követelményei](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>IP-konfigurációk és engedélyezési lista beállítása az adattárakban

A felhőben lévő egyes adattárakhoz az is szükséges, hogy engedélyezze az áruházhoz hozzáférő számítógép IP-címét. Győződjön meg arról, hogy a saját üzemeltetésű integrációs modul számítógépének IP-címe megfelelően engedélyezett vagy konfigurálva van a tűzfalon.

A következő felhőalapú adattárakhoz a saját üzemeltetésű Integration Runtime-gép IP-címének engedélyezése szükséges. Az adattárak némelyike alapértelmezés szerint nem igényel engedélyezési listát.

* [Azure SQL Database](../azure-sql/database/firewall-configure.md)
* [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)
* [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
* [Azure Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
* [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Megoszthatók a saját üzemeltetésű integrációs modul a különböző adatüzemek között?**

Igen. További részletek [.](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/)

**Milyen portokra vonatkozó követelmények vonatkoznak a saját üzemeltetésű Integration Runtime működésére?**

A saját üzemeltetésű integrációs modul lehetővé teszi, hogy a HTTP-alapú kapcsolatok hozzáférjenek az internethez. A kapcsolódáshoz a saját üzemeltetésű integrációs modul 443 kimenő portjait kell megnyitni. Nyissa meg a 8060-es bejövő portot csak a számítógép szintjén (nem a vállalati tűzfal szintjén) a Hitelesítőadat-kezelő alkalmazáshoz. Ha Azure SQL Database vagy az Azure szinapszis Analytics a forrásként vagy a célhelyként van használatban, akkor a 1433-as portot is meg kell nyitnia. További információ: [tűzfal-konfigurációk és engedélyezési lista beállítása az IP-címekhez](#firewall-configurations-and-allow-list-setting-up-for-ip-addresses) szakasz.

## <a name="next-steps"></a>Következő lépések

A másolási tevékenység teljesítményének Azure Data Factory a [másolási tevékenység teljesítményének és hangolásának útmutatója](copy-activity-performance.md)című témakörben talál további információt.
