---
title: Saját üzemeltetésű integrációs modul konfigurálása SSIS proxyként
description: Megtudhatja, hogyan konfigurálhat egy saját üzemeltetésű integrációs modult proxyként egy Azure-SSIS Integration Runtime számára.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/19/2020
ms.openlocfilehash: 5f393e95a7d468ea2c6130077e42b25b038e839d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376224"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Saját üzemeltetésű IR konfigurálása proxyként egy Azure-SSIS IRhoz Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogyan futtathat SQL Server Integration Services (SSIS) csomagokat egy Azure-SSIS Integration Runtime (Azure-SSIS IR) Azure Data Factory egy saját üzemeltetésű integrációs modul (helyi IR) használatával, amely proxyként van konfigurálva. 

Ezzel a szolgáltatással a helyszíni adataihoz anélkül férhet hozzá, hogy a [virtuális hálózathoz kellene csatlakoztatnia a Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md). A funkció akkor hasznos, ha a vállalati hálózat konfigurációja túl összetett, vagy a házirend túlságosan korlátozó ahhoz, hogy beinjektálja a Azure-SSIS IR.

Ez a szolgáltatás a SSIS adatáramlási feladatot két átmeneti feladatba bontja, amikor alkalmazható: 
* Helyszíni **előkészítési feladat**: Ez a feladat futtatja az adatfolyam-összetevőt, amely egy helyszíni adattárolóhoz csatlakozik a saját üzemeltetésű integrációs modulban. Áthelyezi az adatok a helyszíni adattárból az Azure Blob Storage egy átmeneti területére, vagy fordítva.
* **Felhő-előkészítési feladat**: Ez a feladat olyan adatfolyam-összetevőt futtat, amely nem kapcsolódik a helyszíni adattárakhoz a Azure-SSIS IR. Az adatok áthelyezése az Azure Blob Storage átmeneti területéről egy felhőalapú adattárba, vagy fordítva.

Ha az adatáramlási feladat a helyszínről a felhőbe helyezi át az adatait, akkor az első és a második előkészítési feladat a helyszíni és a Felhőbeli előkészítési feladatok lesznek. Ha az adatfolyam-tevékenység a felhőből a helyszíni környezetbe helyezi át az adatait, akkor az első és a második előkészítési feladat a Felhőbeli és a helyszíni előkészítési feladatok lesznek. Ha az adatfolyam-feladatnak a helyszíni helyről a helyszíni környezetbe helyezi át az adatait, akkor az első és a második előkészítési feladat is a helyszíni előkészítési feladatok között lesz. Ha az adatfolyam-feladat felhőből a felhőbe helyezi át az adatforgalmat, akkor ez a funkció nem alkalmazható.

A szolgáltatás egyéb előnyei és képességei lehetővé teszik például a saját üzemeltetésű integrációs modul beállítását olyan régiókban, amelyeket egy Azure-SSIS IR még nem támogat, és lehetővé teszi a saját üzemeltetésű IR nyilvános statikus IP-címének használatát az adatforrások tűzfalán.

## <a name="prepare-the-self-hosted-ir"></a>A saját üzemeltetésű integrációs modul előkészítése

A szolgáltatás használatához először hozzon létre egy adatelőállítót, és állítson be egy Azure-SSIS IR. Ha még nem tette meg, kövesse az [Azure-SSIS IR beállítása](./tutorial-deploy-ssis-packages-azure.md)című témakör utasításait.

Ezután állítsa be a saját üzemeltetésű integrációs modult ugyanabban az adatgyárban, ahol a Azure-SSIS IR be van állítva. Ehhez tekintse meg [a saját üzemeltetésű IR létrehozása](./create-self-hosted-integration-runtime.md)című témakört.

Végül letölti és telepíti a saját üzemeltetésű integrációs modul legújabb verzióját, valamint a további illesztőprogramokat és futtatókörnyezetet a helyszíni gépen vagy az Azure-beli virtuális gépen (VM), a következőképpen:
- Töltse le és telepítse a [saját](https://www.microsoft.com/download/details.aspx?id=39717)üzemeltetésű integrációs modul legújabb verzióját.
- Ha az objektum-csatolási és beágyazási adatbázist (OLEDB), az Open Database Connectivity (ODBC) vagy a ADO.NET-összekötőket használja a csomagokban, töltse le és telepítse a megfelelő illesztőprogramokat ugyanarra a gépre, amelyen a saját üzemeltetésű integrációs modul telepítve van, ha még nem tette volna meg.  

  Ha az OLEDB-illesztőprogram korábbi verzióját használja SQL Server (SQL Server Native Client [SQLNCLI]), [töltse le a 64 bites verziót](https://www.microsoft.com/download/details.aspx?id=50402).  

  Ha az OLEDB-illesztőprogram legújabb verzióját használja a SQL Serverhoz (MSOLEDBSQL), [töltse le a 64 bites verziót](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Ha az OLEDB/ODBC/ADO. NET-illesztőprogramokat használja más adatbázis-rendszerekhez, például a PostgreSQL-hez, a MySQL-hez, az Oracle-hoz és így tovább, akkor letöltheti a 64 bites verzióit a webhelyükről.
- Ha még nem tette meg, [töltse le és telepítse a Visual C++ (VC) futtatókörnyezet 64 bites verzióját](https://www.microsoft.com/download/details.aspx?id=40784) ugyanarra a gépre, amelyen a saját üzemeltetésű IR telepítve van.

### <a name="enable-windows-authentication-for-on-premises-staging-tasks"></a>Windows-hitelesítés engedélyezése helyszíni előkészítési feladatokhoz

Ha a saját üzemeltetésű integrációs modul helyszíni előkészítési feladatainak Windows-hitelesítésre van szükségük, [konfigurálja a SSIS-csomagokat, hogy ugyanazt a Windows-hitelesítést használják](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). 

A helyszíni előkészítési feladatok a saját üzemeltetésű IR-szolgáltatásfiók (alapértelmezés szerint *NT SERVICE\DIAHostService*) szerint lesznek megnyitva, és az adattárak a Windows-hitelesítési fiókkal lesznek elérhetők. Mindkét fiókhoz szükség van bizonyos biztonsági házirendek hozzárendelésére. A saját üzemeltetésű IR-gépen lépjen a **helyi biztonsági házirend**  >  **helyi házirendek**  >  **felhasználói jogok kiosztása** elemre, majd tegye a következőket:

1. Rendeljen hozzá *egy folyamathoz tartozó memória-kvótát* , és *cserélje le a folyamat szintű jogkivonat* -szabályzatokat a saját üzemeltetésű IR-szolgáltatásfiók. Ez automatikusan megtörténik, amikor telepíti a saját üzemeltetésű integrációs modult az alapértelmezett szolgáltatásfiók-fiókkal. Ha nem, akkor manuálisan rendelje hozzá ezeket a házirendeket. Ha más szolgáltatásfiókot használ, ugyanazt a szabályzatot rendeli hozzá.

1. Rendelje hozzá a *bejelentkezést szolgáltatási* házirendként a Windows-hitelesítési fiókhoz.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Az Azure Blob Storage társított szolgáltatás előkészítése átmeneti használatra

Ha még nem tette meg, hozzon létre egy Azure Blob Storage társított szolgáltatást ugyanabban az adatgyárban, ahol a Azure-SSIS IR be van állítva. Ehhez tekintse meg [Az Azure-beli adatfeldolgozó-társított szolgáltatás létrehozása](./quickstart-create-data-factory-portal.md#create-a-linked-service)című témakört. Ügyeljen arra, hogy tegye a következőket:
- Az **adattár** lapon válassza az **Azure Blob Storage** lehetőséget.  
- Az **integrációs modulon keresztüli csatlakozáshoz** válassza a **AutoResolveIntegrationRuntime** (nem a Azure-SSIS IR, sem a saját üzemeltetésű IR) beállítást, mert az alapértelmezett Azure IR használatával beolvassa az Azure-Blob Storage hozzáférési hitelesítő adatait.
- A **hitelesítési módszer** beállításnál válassza a **fiók kulcsa**, **sas URI-ja**, **szolgáltatásnév** vagy **felügyelt identitás** lehetőséget.  

>[!TIP]
>Ha az **egyszerű szolgáltatásnév** módszert választja, adja meg az egyszerű szolgáltatásnév számára a *tárolási blob adatközreműködői* szerepkört. További információ: [Azure Blob Storage-összekötő](connector-azure-blob-storage.md#linked-service-properties). Ha a **felügyelt identitás** módszerét választja, a megfelelő szerepköröket adja meg az ADF felügyelt identitásának az Azure Blob Storage eléréséhez. További információkért lásd: az [Azure Blob Storage elérése a Azure Active Directory hitelesítéssel az ADF által felügyelt identitással](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication).

![Az Azure Blob Storage-hoz társított szolgáltatás előkészítése előkészítéshez](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Azure-SSIS IR konfigurálása saját üzemeltetésű IR proxyként

A saját üzemeltetésű IR-és Azure Blob Storage-hez társított szolgáltatás előkészítésének előkészítéséhez mostantól konfigurálhatja az új vagy meglévő Azure-SSIS IR a saját üzemeltetésű integrációs modult a saját adatfeldolgozó portál vagy alkalmazás használatával. Mielőtt ezt megtenné, azonban ha a meglévő Azure-SSIS IR már fut, állítsa le, majd indítsa újra.

1. Az **integrációs modul telepítése** ablaktáblán a **Tovább gombra** kattintva ugorjon az **általános beállítások** és az **SQL-beállítások** szakaszban. 

1. A **Speciális beállítások** szakaszban tegye a következőket:

   1. Jelölje be a **Self-Hosted Integration Runtime beállítása proxyként a Azure-SSIS Integration Runtime** jelölőnégyzetet. 

   1. A saját üzemeltetésű **Integration Runtime** legördülő listában válassza ki a meglévő, saját üzemeltetésű IR-t a Azure-SSIS IRhoz.

   1. Az **átmeneti tárolóhoz társított szolgáltatás** legördülő listában válassza ki a meglévő Azure Blob Storage-társított szolgáltatást, vagy hozzon létre egy újat az előkészítéshez.

   1. Az **átmeneti útvonal** mezőben adja meg a BLOB-tárolót a kiválasztott Azure Blob Storage-fiókban, vagy hagyja üresen, hogy az alapértelmezett beállítást használja az átmeneti tároláshoz.

   1. Válassza a **Folytatás** lehetőséget.

   ![Speciális beállítások önkiszolgáló IR-vel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Az új vagy meglévő Azure-SSIS IR a PowerShell használatával is konfigurálhatja proxyként a saját üzemeltetésű IR-ként.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>SSIS-csomagok engedélyezése proxy alapján való csatlakozásra

Ha a legújabb SSDT használja a Visual studióhoz készült SSIS projects bővítménynek vagy egy önálló telepítőnek, egy új `ConnectByProxy` tulajdonságot talál, amely a támogatott adatfolyam-összetevőkhöz lett hozzáadva a Csatlakozáskezelő-kezelőhöz.
* [A SSIS projects bővítmény letöltése a Visual studióhoz](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Az önálló telepítő letöltése](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)   

Ha olyan új csomagokat tervez meg, amelyek adatáramlási feladatokat tartalmaznak a helyszíni adatokhoz hozzáférő összetevőkkel, ezt a tulajdonságot úgy engedélyezheti, hogy az *igaz* értékre van állítva a megfelelő Csatlakozáskezelő **Tulajdonságok** ablaktábláján.

![ConnectByProxy tulajdonság engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Ezt a tulajdonságot akkor is engedélyezheti, ha meglévő csomagokat futtat, anélkül, hogy ezeket manuálisan módosítania kellene.  Két lehetőség érhető el:
- **A. lehetőség**: Nyissa meg, építse újra, majd telepítse újra a csomagokat tartalmazó projektet a legújabb SSDT, hogy az a Azure-SSIS IR fusson. Ezt követően engedélyezheti a tulajdonságot úgy, hogy az *igaz* értéket adja meg a megfelelő ügyfélkapcsolat-kezelők számára. Ha csomagokat futtat a SSMS-ból, akkor ezek a kapcsolatok a **csomag** előugró ablakának **kapcsolatkezelő** lapján jelennek meg.

  ![ConnectByProxy property2 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  A tulajdonságot úgy is engedélyezheti, hogy az *igaz* értékre van ÁLLÍTVA a [SSIS-csomag végrehajtásakor](./how-to-invoke-ssis-package-ssis-activity.md) a **kapcsolatkezelő** lapon megjelenő megfelelő kapcsolatkezelő esetében, ha Data Factory folyamatokban lévő csomagokat futtat.
  
  ![ConnectByProxy property3 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **B. lehetőség:** Telepítse újra a csomagokat tartalmazó projektet a SSIS IR-ben való futtatáshoz. Ezután engedélyezheti a tulajdonságot a tulajdonság elérési útjának megadásával, és beállíthatja, hogy a tulajdonság `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` felülbírálja a **csomag** előugró ablakának **speciális** lapján a csomagok SSMS *való* futtatásakor.

  ![ConnectByProxy property4 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  A tulajdonságot úgy is engedélyezheti, ha a tulajdonság elérési útját `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` adja meg, és az *igaz* értéket adja meg  a tulajdonság felülbírálásához a [SSIS-csomag végrehajtása tevékenységben](./how-to-invoke-ssis-package-ssis-activity.md) , amikor csomagokat futtat Data Factory folyamatokban.
  
  ![ConnectByProxy property5 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-on-premises-and-cloud-staging-tasks"></a>Helyszíni és Felhőbeli előkészítési feladatok hibakeresése

A saját üzemeltetésű integrációs modulban megtalálhatja a futásidejű naplókat a *C:\ProgramData\SSISTelemetry* mappában, valamint a helyszíni előkészítési feladatok végrehajtási naplóit a *C:\ProgramData\SSISTelemetry\ExecutionLog* mappában.  Megtalálhatja a felhő-előkészítési feladatok végrehajtási naplóit a SSISDB, a naplófájlok megadott elérési útját vagy Azure Monitor attól függően, hogy a csomagokat a SSISDB-ben tárolja, engedélyezi [Azure monitor integrációt](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor)stb.). A helyszíni előkészítési feladatok egyedi azonosítói is megtalálhatók a felhőalapú átmeneti feladatok végrehajtási naplóiban. 

![Az első előkészítési feladat egyedi azonosítója](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

Ha felváltotta az ügyfélszolgálati jegyeket, válassza a **naplók küldése** gombot a **diagnosztika** lapon **Microsoft Integration Runtime Configuration Manager** , amely a saját üzemeltetésű IR-re van telepítve, hogy a legutóbbi művelet-végrehajtási naplókat küldjön nekünk a vizsgálathoz.

## <a name="billing-for-the-on-premises-and-cloud-staging-tasks"></a>A helyszíni és a Felhőbeli előkészítési feladatok számlázása

A saját üzemeltetésű integrációs modulon futó helyszíni átmeneti feladatok számlázása külön történik, ugyanúgy, mint a saját üzemeltetésű integrációs modulon futó adatáthelyezési tevékenységek számlázása. Ez a [Azure Data Factory adatcsatorna-díjszabási](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) cikkben van megadva.

A Azure-SSIS IR futó felhő-előkészítési feladatokat nem külön számlázjuk, de a futó Azure-SSIS IR a [Azure-SSIS IR árképzési](https://azure.microsoft.com/pricing/details/data-factory/ssis/) cikkben megadott módon kell fizetni.

## <a name="enable-custom3rd-party-components"></a>Egyéni/harmadik féltől származó összetevők engedélyezése 

Az alábbi utasításokat követve engedélyezheti az egyéni/harmadik féltől származó összetevők számára, hogy a saját üzemeltetésű integrációs modul használatával hozzáférjenek a helyszíni adatközponthoz Azure-SSIS IR számára:

1. Telepítse az egyéni/külső gyártótól származó összetevőket SQL Server 2017 Azure-SSIS IR [standard/expressz egyéni telepítéssel](./how-to-configure-azure-ssis-ir-custom-setup.md).

1. Hozza létre a következő DTSPath-beállításkulcsokat a saját üzemeltetésű integrációs modulban, ha még nem léteznek:
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` beállításban a `C:\Program Files\Microsoft SQL Server\140\DTS\`
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` beállításban a `C:\Program Files (x86)\Microsoft SQL Server\140\DTS\`
   
1. Telepítse az egyéni/külső gyártótól származó összetevőket SQL Server 2017 a saját üzemeltetésű IR-re a fenti DTSPath, és győződjön meg arról, hogy a telepítési folyamat:

   1. A,, `<DTSPath>` és mappákat hozza létre, `<DTSPath>/Connections` `<DTSPath>/PipelineComponents` `<DTSPath>/UpgradeMappings` ha azok még nem léteznek.
   
   1. Létrehoz egy saját XML-fájlt a bővítmény-hozzárendelésekhez a `<DTSPath>/UpgradeMappings` mappában.
   
   1. A globális szerelvény-gyorsítótárban (GAC) az egyéni/harmadik féltől származó összetevő-szerelvények által hivatkozott összes szerelvényt telepíti.

Íme néhány példa a partnereinktől, a [Theobald szoftver](https://kb.theobald-software.com/xtract-is/XIS-for-Azure-SHIR) -és [Aecorsoft](https://www.aecorsoft.com/blog/2020/11/8/using-azure-data-factory-to-bring-sap-data-to-azure-via-self-hosted-ir-and-ssis-ir), akik kiigazították az összetevőket, hogy az expressz egyéni telepítőt és a saját üzemeltetésű integrációs modult használják a Azure-SSIS IR számára.

## <a name="enforce-tls-12"></a>A TLS 1.2 kényszerítése

Ha erős titkosítást/biztonságosabb hálózati protokollt (TLS 1,2) kell használnia, és le kell tiltania a régebbi SSL/TLS-verziókat a saját üzemeltetésű integrációs modulban, letöltheti és futtathatja a nyilvános előzetes tároló *CustomSetupScript/UserScenarios/TLS 1,2* mappájában található *Main. cmd* parancsfájlt.  A [Azure Storage Explorer](https://storageexplorer.com/)használatával a következő sas URI azonosító megadásával csatlakozhat a nyilvános előzetes tárolóhoz:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Aktuális korlátozások

- A (z) Hadoop/HDFS/DQS-összetevők kivételével csak a beépített/előre Azure-SSIS IR telepített adatfolyam-összetevők támogatottak, [Azure-SSIS IR a beépített/előre telepített összetevők](./built-in-preinstalled-components-ssis-integration-runtime.md):.
- Jelenleg csak a felügyelt kódban (.NET-keretrendszer) írt egyéni/harmadik féltől származó adatfolyam-összetevők támogatottak – a natív kóddal (C++) írt elemek jelenleg nem támogatottak.
- A változó értékek módosítása a helyszíni és a Felhőbeli előkészítési feladatok esetében jelenleg nem támogatott.
- Az objektum típusú változó értékének módosítása a helyszíni előkészítési feladatokban nem tükröződik más feladatokban.
- Az OLEDB-forrás *ParameterMapping* jelenleg nem támogatott. Áthidaló megoldásként használja az *SQL parancsot a változóból* *AccessMode* , és használja a *kifejezést* a változók/paraméterek egy SQL-parancsba való beszúrásához. Példaként tekintse meg a *ParameterMappingSample. dtsx* csomagot, amely a nyilvános előzetes verziójú tároló *SelfHostedIRProxy/korlátozások* mappájában található. A Azure Storage Explorer használatával a fenti SAS URI beírásával csatlakozhat a nyilvános előzetes verziójú tárolóhoz.

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta a saját üzemeltetésű integrációs modult proxyként a Azure-SSIS IR számára, a csomagokat üzembe helyezheti és futtathatja a helyszíni adateléréshez a SSIS-csomag tevékenységének végrehajtása Data Factory folyamatokban. Ebből a cikkből megtudhatja, hogyan [FUTTATHAT SSIS-CSOMAGOKAT SSIS-csomagok végrehajtásaként Data Factory-folyamatokban](./how-to-invoke-ssis-package-ssis-activity.md).