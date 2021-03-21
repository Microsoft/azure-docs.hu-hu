---
title: Azure-SSIS Integration Runtime beállításainak testreszabása
description: Ez a cikk azt ismerteti, hogyan használhatók a Azure-SSIS Integration Runtime egyéni telepítési felülete további összetevők telepítéséhez vagy a beállítások módosításához
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: b1b3a34ac495936ed92e29353a41efb8c462768f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387733"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime beállításainak testreszabása

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) a Azure Data Factoryban (ADF) egyéni telepítők használatával testreszabható. Lehetővé teszik saját lépések hozzáadását a Azure-SSIS IR üzembe helyezése vagy újrakonfigurálása során. 

Az egyéni beállítások használatával módosíthatja a Azure-SSIS IR alapértelmezett működési konfigurációját vagy környezetét. Ha például további Windows-szolgáltatásokat szeretne elindítani, megőrzi a fájlmegosztás hozzáférési hitelesítő adatait, vagy használjon erős titkosítást/biztonságosabb hálózati protokollt (TLS 1,2). További összetevőket, például szerelvényeket, illesztőprogramokat vagy bővítményeket is telepíthet a Azure-SSIS IR minden egyes csomópontján. Egyéni, nyílt forráskódú vagy harmadik féltől származó összetevőket is használhatnak. A beépített/előre telepített összetevőkkel kapcsolatos további információkért lásd: [beépített/előre telepített összetevők Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md).

A Azure-SSIS IR kétféleképpen végezheti el az egyéni telepítéseket: 
* **Standard egyéni telepítő parancsfájl** használatával: Készítse elő a parancsfájlt és a hozzá tartozó fájlokat, és töltse fel őket az Azure Storage-fiókban található blob-tárolóba. Ezután meg kell adnia egy közös hozzáférési aláírás (SAS) Uniform Resource Identifier (URI) a tárolóhoz a Azure-SSIS IR beállításakor vagy újrakonfigurálásakor. A Azure-SSIS IR mindegyik csomópontja letölti a parancsfájlt és a hozzá tartozó fájlokat a tárolóból, és az Egyéni telepítést emelt szintű engedélyekkel futtatja. Ha az egyéni telepítés elkészült, minden egyes csomópont feltölti a végrehajtás és más naplók szabványos kimenetét a tárolóba.
* **Expressz egyéni beállítás parancsfájl nélkül**: Futtasson néhány gyakori rendszerkonfigurációt és Windows-parancsot, vagy telepítsen néhány népszerű vagy ajánlott további összetevőt a parancsfájlok használata nélkül.

Az ingyenes (licenc nélküli) és a fizetős (licencelt) összetevőket is telepítheti standard és expressz egyéni telepítéssel. Ha Ön független szoftvergyártó (ISV), tekintse meg [a fizetett vagy licencelt összetevők fejlesztése a Azure-SSIS IRhoz](how-to-develop-azure-ssis-ir-licensed-components.md)című témakört.

> [!IMPORTANT]
> A jövőbeli fejlesztések előnyeinek kihasználása érdekében javasoljuk, hogy az egyéni telepítéssel a Azure-SSIS IR v3-as vagy újabb csomópontjait használja.

## <a name="current-limitations"></a>Aktuális korlátozások

A következő korlátozások érvényesek kizárólag a szabványos egyéni telepítésekre:

- Ha a szkriptben lévő *gacutil.exet* kívánja használni a globális szerelvény-gyorsítótárban (GAC) lévő szerelvények telepítéséhez, meg kell adnia *gacutil.exe* az egyéni telepítés részeként. Vagy használhatja a *nyilvános előzetes* tárolóban található *minta* mappában megadott másolatot is, amely az alábbi **szabványos egyéni telepítési minták** szakaszban található.

- Ha a parancsfájl egyik almappájára szeretne hivatkozni, a *msiexec.exe* nem támogatja a `.\` gyökérkönyvtárra hivatkozó jelölést. Használjon olyan parancsot, mint a `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` helyett `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` .

- A Windows által automatikusan létrehozott rendszergazdai megosztások vagy rejtett hálózati megosztások jelenleg nem támogatottak a Azure-SSIS IR.

- Az IBM iSeries Access ODBC-illesztő nem támogatott a Azure-SSIS IR. Előfordulhat, hogy az egyéni telepítés során telepítési hibák léptek fel. Ha ezt teszi, kérjen segítséget az IBM támogatási szolgálatának.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A Azure-SSIS IR testreszabásához a következő elemek szükségesek:

- [Azure-előfizetés](https://azure.microsoft.com/)

- [A Azure-SSIS IR kiépítése](./tutorial-deploy-ssis-packages-azure.md)

- [Egy Azure Storage-fiók](https://azure.microsoft.com/services/storage/). Az expressz egyéni telepítésekhez nem szükséges. A standard szintű egyéni telepítésekhez feltöltheti és tárolhatja az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat egy blob-tárolóban. Az egyéni telepítési folyamat a végrehajtási naplókat is feltölti ugyanarra a blob-tárolóra.

## <a name="instructions"></a>Utasítások

A Azure-SSIS IR az ADF felhasználói felületén egyéni beállításokkal is kiépítheti vagy újrakonfigurálhatja. Ha ugyanezt a PowerShell-lel szeretné elvégezni, töltse le és telepítse a [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Szabványos egyéni telepítés

Az alábbi lépéseket követve kiépítheti vagy újrakonfigurálhatja az Azure-SSIS IR szabványos egyéni telepítésekkel az ADF felhasználói felületén.

1. Készítse elő az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat (például. bat,. cmd,. exe,. dll,. msi vagy. ps1 fájlok).

   * Rendelkeznie kell egy *Main. cmd* nevű parancsfájl-fájllal, amely az egyéni telepítés belépési pontja.  
   * A parancsfájl csendes végrehajtásának biztosításához először tesztelje a helyi gépen.  
   * Ha más eszközök (például *msiexec.exe*) által generált további naplókat szeretne feltölteni a tárolóba, adja meg az előre definiált környezeti változót, `CUSTOM_SETUP_SCRIPT_LOG_DIR` mint a parancsfájlok log mappáját (például *msiexec/i xxx.msi/quiet/lv% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install. log*).

1. [Azure Storage Explorer](https://storageexplorer.com/)letöltése, telepítése és megnyitása.

   a. A **(helyi és csatolt)** területen kattintson a jobb gombbal a **Storage-fiókok** elemre, majd válassza **a Kapcsolódás az Azure Storage-hoz** lehetőséget.

      ![Csatlakozás az Azure Storage-hoz](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Válassza **a Storage-fiók nevének és kulcsának használata** lehetőséget, majd kattintson a **tovább** gombra.

      ![Tárfióknév és -kulcs használata](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Adja meg az Azure Storage-fiók nevét és kulcsát, válassza a **tovább**, majd a **kapcsolat** lehetőséget.

      ![Adja meg a Storage-fiók nevét és kulcsát](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. A csatlakoztatott Azure Storage-fiókban kattintson a jobb gombbal a **blob-tárolók** elemre, válassza a **blob-tároló létrehozása** elemet, és nevezze el az új tárolót.

      ![Blobtároló létrehozása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Válassza ki az új tárolót, és töltse fel az egyéni telepítési parancsfájlt és a hozzá tartozó fájlokat. Ügyeljen arra, hogy a *Main. cmd* fájlt a tároló legfelső szintjén töltse fel, nem pedig egyetlen mappában sem. A tárolónak csak a szükséges egyéni telepítőfájlokat kell tartalmaznia, ezért a Azure-SSIS IR későbbi letöltés nem hosszú időt vehet igénybe. Az egyéni beállítások maximális időtartama jelenleg 45 percen belül megtörténik, mielőtt időtúllépés történik. Ide tartozik az összes fájl letöltésének ideje a tárolóból, és a Azure-SSIS IR telepítése. Ha a telepítőnek több időre van szüksége, egy támogatási jegyet kell felhívnia.

      ![Fájlok feltöltése a blob-tárolóba](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférési aláírás beolvasása** elemet.

      ![A tároló közös hozzáférési aláírásának beolvasása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   : Hozza létre a tároló SAS URI-JÁT elég hosszú lejárati idővel és olvasási/írási/listázási engedéllyel. Az egyéni telepítési parancsfájl és a hozzá tartozó fájlok letöltéséhez és futtatásához szüksége lesz az SAS URI-ra. Ez akkor fordul elő, amikor a Azure-SSIS IR bármelyik csomópontját újra rendszerképbe állítja vagy újraindította. Írási engedéllyel is kell rendelkeznie a telepítési végrehajtási naplók feltöltéséhez.

      > [!IMPORTANT]
      > Győződjön meg arról, hogy a SAS URI-ja nem jár le, és az egyéni beállítási erőforrások mindig elérhetők a Azure-SSIS IR teljes életciklusa során, a létrehozástól a törlésig, különösen akkor, ha az adott időszakban rendszeresen leállítja és elindítja a Azure-SSIS IR.

      ![A tároló közös hozzáférési aláírásának előállítása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Másolja és mentse a tároló SAS URI-JÁT.

      ![A megosztott hozzáférés aláírásának másolása és mentése](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Jelölje be a **Azure-SSIS Integration Runtime testreszabása további rendszerkonfigurációkkal/összetevő-telepítésekkel** jelölőnégyzetet az **Integration Runtime telepítési** paneljének **Speciális beállítások** lapján. Ezután adja meg a tároló SAS URI-JÁT az **egyéni telepítési tároló sas URI** -szövege szövegmezőben.

   ![Speciális beállítások egyéni telepítésekkel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

A normál egyéni telepítés befejezése és a Azure-SSIS IR elindítása után megtalálhatja az összes egyéni telepítési naplót a tároló *fő. cmd. log* mappájába. Ezek tartalmazzák a *Main. cmd* és más végrehajtási naplók szabványos kimenetét.

### <a name="express-custom-setup"></a>Expressz egyéni beállítás

A következő lépések végrehajtásával kiépítheti vagy újrakonfigurálhatja a Azure-SSIS IR Express Custom setups használatával az ADF felhasználói felületén.

1. Jelölje be a **Azure-SSIS Integration Runtime testreszabása további rendszerkonfigurációkkal/összetevő-telepítésekkel** jelölőnégyzetet az **Integration Runtime telepítési** paneljének **Speciális beállítások** lapján. 

1. Az **új** elemre kattintva nyissa meg az **expressz egyéni telepítés hozzáadása** panelt, majd válasszon egy típust az **expressz egyéni telepítés típusa** legördülő listában. Jelenleg a cmdkey parancs futtatására, környezeti változók hozzáadására, Azure PowerShell telepítésére és licencelt összetevők telepítésére vonatkozó expressz egyéni beállításokat kínálunk.

#### <a name="running-cmdkey-command"></a>Cmdkey parancs futtatása

Ha az expressz egyéni telepítéshez a **cmdkey futtatása parancsot** választja, akkor futtathatja a Windows cmdkey parancsot a Azure-SSIS IR. Ehhez adja meg a célként megadott számítógép nevét vagy tartománynevét, felhasználónevét vagy fiókjának nevét, valamint a jelszó vagy a fiók kulcsát az **/Add**, **/User** és **/pass** szövegmezőben. Ez lehetővé teszi az SQL-kiszolgálók,-fájlmegosztás vagy-Azure Files hozzáférési hitelesítő adatainak megőrzését a Azure-SSIS IR. A Azure Fileshoz való hozzáféréshez például megadhatja `YourAzureStorageAccountName.file.core.windows.net` a `azure\YourAzureStorageAccountName` `YourAzureStorageAccountKey` következőt:, és az **/Add**, a **/User** és a **/pass**. Ez hasonló a Windows [cmdkey](/windows-server/administration/windows-commands/cmdkey) parancs helyi gépen való futtatásához. Most már csak egy expressz egyéni telepítő futtatható a cmdkey parancs futtatásához. Ha több cmdkey-parancsot szeretne futtatni, használja helyette a szabványos egyéni telepítést.

#### <a name="adding-environment-variables"></a>Környezeti változók hozzáadása

Ha az expressz egyéni telepítéshez a **környezeti változó hozzáadása** lehetőséget választja, hozzáadhat egy Windows környezeti változót a Azure-SSIS IR. Ehhez adja meg a környezeti változó nevét és értékét a **változó neve** és a **változó értéke** szövegmezőben. Ez lehetővé teszi a környezeti változó használatát Azure-SSIS IRon futó csomagoknál, például parancsfájl-összetevőkben vagy feladatokban. Ez hasonló a Windows [set](/windows-server/administration/windows-commands/set_1) parancs helyi számítógépen való futtatásához.

#### <a name="installing-azure-powershell"></a>Azure PowerShell telepítése

Ha a **telepítés Azure PowerShell** típusát adja meg az expressz egyéni telepítéshez, akkor az az Module of PowerShellt telepítheti a Azure-SSIS IR. Ehhez írja be az az modul verziószámát (x. y. z), amelyet a [támogatottak listájáról](https://www.powershellgallery.com/packages/az)szeretne használni. Ez lehetővé teszi Azure PowerShell parancsmagok/parancsfájlok futtatását a csomagokba az Azure-erőforrások, például a [Azure Analysis Services (AAS)](../analysis-services/analysis-services-powershell.md)kezelésére.

#### <a name="installing-licensed-components"></a>Licencelt összetevők telepítése

Ha bejelöli a **licencelt összetevő** típusának telepítése az expressz egyéni telepítőhöz lehetőséget, akkor az **összetevő neve** legördülő listában kiválaszthatja az ISV-partnereink integrált összetevőjét:

   * Ha kijelöli a **szoftverével Task Factory** összetevőjét, telepítheti az összetevők szoftverével [-csomagját](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) a Azure-SSIS IR. Ehhez adja meg a **licenckulcs** szövegmezőben előre megvásárolt termék-licenckulcs. Az aktuálisan integrált verzió a **2020.1.3**.

   * Ha a **OH22'S HEDDA választja. I/o** -összetevő, telepítheti a [HEDDA. IO](https://github.com/oh22is/HEDDA.IO/tree/master/SSIS-IR) adatminőség/tisztító összetevő a oh22 a Azure-SSIS IR. Ehhez előre kell megvásárolnia a szolgáltatást. Az aktuálisan integrált verzió a **1.0.14**.

   * Ha a **oh22's SQLPhonetics.net** összetevőt választja, telepítheti a [SQLPhonetics.net](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) adatminőség/megfelelő összetevőjét a oh22 a Azure-SSIS IR. Ehhez adja meg a **licenckulcs** szövegmezőben előre megvásárolt termék-licenckulcs. Az aktuálisan integrált verzió a **1.0.45**.

   * Ha a **KINGSWAYSOFT SSIS Integration Toolkit** összetevőjét választja, akkor a [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) -csomagot telepítheti a CRM/ERP/marketing/csoportmunka-alkalmazások, például a Microsoft Dynamics/SharePoint/Project Server, az Oracle/Salesforce marketing Cloud stb. számára a Azure-SSIS IR. Ehhez adja meg a **licenckulcs** szövegmezőben előre megvásárolt termék-licenckulcs. Az aktuálisan integrált verzió a **2020,1**.

   * Ha a **KINGSWAYSOFT SSIS hatékonyságnövelő csomag** összetevőjét választja, akkor a [SSIS hatékonyságnövelő csomagjának](https://www.kingswaysoft.com/products/ssis-productivity-pack) összetevőit telepítheti a KingswaySoft a Azure-SSIS IR. Ehhez adja meg a **licenckulcs** szövegmezőben előre megvásárolt termék-licenckulcs. Az aktuálisan integrált verzió a **20,1**.

   * Ha kijelöli a **Theobald szoftver xtract** összetevőt, a [xtract az](https://theobald-software.com/en/xtract-is/) SAP-rendszerek (ERP, s/4HANA, BW) összekötői is telepíthetők a Theobald szoftverből a Azure-SSIS IR. Ehhez húzza a & eldobás/Feltöltés lehetőségre a **licencet** , amelyet korábban megvásárolt a licencfájl beviteli mezőjébe. Az aktuálisan integrált verzió a **6.1.1.3**.

   * Ha a **AecorSoft integrációs szolgáltatásának** összetevőjét választja, akkor az Salesforce-hez tartozó Azure-SSIS IR AecorSoft-k [integrációs szolgáltatási](https://www.aecorsoft.com/en/products/integrationservice) csomagját telepítheti az SAP és a rendszerekhez. Ehhez adja meg a **licenckulcs** szövegmezőben előre megvásárolt termék-licenckulcs. Az aktuálisan integrált verzió a **3.0.00**.

   * Ha a **CDATA SSIS standard csomag** összetevőjét választja, telepítheti a legnépszerűbb [SSIS](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#standard) (például Microsoft SharePoint-összekötők) a legtöbb népszerű összetevőjét a Azure-SSIS IR. Ehhez adja meg a **licenckulcs** szövegmezőben előre megvásárolt termék-licenckulcs. Az aktuálisan integrált verzió a **19,7354**.

   * Ha a **CDATA SSIS kiterjesztett csomag** összetevőjét választja, akkor a [Azure-SSIS IR a CDATA](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#extended) összes összetevője, például a Microsoft Dynamics 365 Business Central-összekötők és az **SSIS standard csomag** egyéb összetevői is telepíthetők a. Ehhez adja meg a **licenckulcs** szövegmezőben előre megvásárolt termék-licenckulcs. Az aktuálisan integrált verzió a **19,7354**. Nagy mérete miatt a telepítés időtúllépésének elkerülése érdekében győződjön meg arról, hogy a Azure-SSIS IR legalább 4 CPU-magot tartalmaz.

A hozzáadott expressz egyéni telepítések a **Speciális beállítások** lapon jelennek meg. Ha el szeretné távolítani őket, jelölje be a jelölőnégyzeteket, majd válassza a **Törlés** lehetőséget.

### <a name="azure-powershell"></a>Azure PowerShell

Ha az egyéni beállításokkal Azure PowerShell használatával szeretné kiépíteni vagy újrakonfigurálni a Azure-SSIS IR, hajtsa végre az alábbi lépéseket.

1. Ha a Azure-SSIS IR már elindult/fut, állítsa le először.

1. A Azure-SSIS IR elindítása előtt a parancsmag futtatásával adhat hozzá vagy távolíthat el egyéni beállításokat `Set-AzDataFactoryV2IntegrationRuntime` .

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "InstallAzurePowerShell")
       {
           $moduleVersion = "YourAzModuleVersion"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Standard")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Extended")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

### <a name="standard-custom-setup-samples"></a>Standard egyéni telepítési minták

A standard egyéni telepítések egyes mintáinak megtekintéséhez és újrafelhasználásához hajtsa végre az alábbi lépéseket.

1. Kapcsolódjon a nyilvános előzetes verziójú tárolóhoz Azure Storage Explorer használatával.

   a. A **(helyi és csatolt)** területen kattintson a jobb gombbal a **Storage-fiókok** elemre, válassza a **Kapcsolódás az Azure Storage-hoz** lehetőséget, válassza **a kapcsolati karakterlánc vagy a közös hozzáférési aláírás URI-ja** lehetőséget, majd kattintson a **tovább** gombra.

      ![Kapcsolódás az Azure Storage-hoz a közös hozzáférési aláírással](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Válassza a **sas URI használata** lehetőséget, majd az **URI** szövegmezőbe írja be a következő sas URI-t:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![A tároló közös hozzáférési aláírásának megadása](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Válassza a **tovább**, majd a **kapcsolat** lehetőséget.

   d. A bal oldali ablaktáblán válassza ki a csatlakoztatott **publicpreview** -tárolót, majd kattintson duplán a *CustomSetupScript* mappára. Ebben a mappában a következő elemek találhatók:

      * Egy *minta* mappa, amely egy egyéni telepítőt tartalmaz egy alapszintű feladat telepítéséhez a Azure-SSIS IR egyes csomópontjain. A feladat nem csinál semmit, de aludni néhány másodpercig. A mappa egy *Gacutil* mappát is tartalmaz, amelynek teljes tartalma (*gacutil.exe*, *gacutil.exe.config* és *1033\gacutlrc.dll*) a tárolóba másolható.

      * Egy *UserScenarios* mappa, amely a valós felhasználói forgatókönyvekben számos egyéni telepítési mintát tartalmaz. Ha több mintát is szeretne telepíteni a Azure-SSIS IRra, kombinálhatja az egyéni telepítési parancsfájlt (*Main. cmd*) egyetlen fájlba, és feltöltheti az összes társított fájllal a tárolóba.

        ![A nyilvános előzetes verziójú tároló tartalma](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. A következő elemek megkereséséhez kattintson duplán a *UserScenarios* mappára:

      * A *.NET-keretrendszer 3,5* mappája, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz a .NET-keretrendszer egy korábbi verziójának telepítéséhez a Azure-SSIS IR mindegyik csomópontján. Előfordulhat, hogy ezt a verziót egyes egyéni összetevők igénylik.

      * Egy *BCP* -mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz, hogy SQL Server parancssori segédeszközöket (*MsSqlCmdLnUtils.msi*) telepítsen a Azure-SSIS IR egyes csomópontjaira. Ezen segédprogramok egyike a tömeges másolási program (*BCP*).

      * A *DNS-utótag* mappája, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz a saját DNS-utótag (például *test.com*) hozzáfűzéséhez a nem minősített egycímkés tartományhoz, és a teljes tartománynévre (FQDN) kapcsolja azt, mielőtt a Azure-SSIS IR DNS-lekérdezésekben használja azt.

      * Egy *Excel* -mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz a C#-szerelvények és-tárak telepítéséhez a Azure-SSIS IR egyes csomópontjain. A szkriptek feladataiban felhasználhatja az Excel-fájlok dinamikus olvasását és írását. 
      
        Először töltse le [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) és [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/), majd töltse fel őket a *Main. cmd fájllal* együtt a tárolóba. Ha csak a szabványos Excel-összekötőket (a Csatlakozáskezelő, a forrás és a célhelyet) szeretné használni, akkor az azokat tartalmazó hozzáférés-Újraterjeszthető csomag már telepítve van a Azure-SSIS IR, így nincs szükség egyéni telepítésre.
      
      * Egy *MySQL ODBC* -mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz a MySQL ODBC-illesztőprogramok telepítéséhez a Azure-SSIS IR minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy az ODBC-összekötőket (a Csatlakozáskezelő, a forrás és a cél) használja a MySQL-kiszolgálóhoz való csatlakozáshoz. 
     
        Először [töltse le a MySQL ODBC illesztőprogram-telepítők legújabb 64 bites és 32 bites verzióit](https://dev.mysql.com/downloads/connector/odbc/) (például *mysql-connector-odbc-8.0.13-winx64.msi* és *mysql-connector-odbc-8.0.13-win32.msi*), majd töltse fel őket a *Main. cmd fájllal* együtt a tárolóba.

      * Egy *Oracle Enterprise* mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) és csendes telepítési konfigurációs fájlt (*Client. RSP*) tartalmaz az Oracle-összekötők és a OCI-illesztőprogram telepítéséhez a Azure-SSIS IR Enterprise Edition minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy az Oracle-Csatlakozáskezelő, a forrás és a cél használatával kapcsolódjon az Oracle-kiszolgálóhoz. 
      
        Először töltse le a Microsoft Connectors v 5.0 for Oracle (*AttunitySSISOraAdaptersSetup.msi* és *AttunitySSISOraAdaptersSetup64.msi*) [alkalmazást a Microsoft letöltőközpontból](https://www.microsoft.com/en-us/download/details.aspx?id=55179) és a legújabb oracle-ügyfélprogramból (például *winx64_12102_client.zip*) az [Oracle](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html)-ből. Ezután töltse fel őket a *Main. cmd* és az *Client. RSP* együtt a tárolóba. Ha a TNS használatával csatlakozik az Oracle-hez, le kell töltenie a *tnsnames. ora* fájlját, szerkesztenie kell, és fel kell töltenie a tárolóba. Így a telepítés során a rendszer átmásolhatja az Oracle telepítési mappájába.

      * Egy *Oracle STANDARD ADO.net* mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz az Oracle ODP.net-illesztőprogram telepítéséhez a Azure-SSIS IR minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy a ADO.NET Csatlakozáskezelő, forrás és cél használatával kapcsolódjon az Oracle-kiszolgálóhoz. 
      
        Először [töltse le a legújabb Oracle ODP.net-illesztőprogramot](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (például *ODP.NET_Managed_ODAC122cR1.zip*), majd töltse fel a *Main. cmd fájllal* együtt a tárolóba.
       
      * Egy *Oracle standard ODBC* -mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz az Oracle ODBC-illesztőprogram telepítéséhez a Azure-SSIS IR minden egyes csomópontján. A parancsfájl az adatforrás nevét (DSN) is konfigurálja. Ezzel a beállítással az ODBC-kapcsolatkezelő, a forrás és a cél, illetve a Power Query Csatlakozáskezelő és a forrás használható az Oracle-kiszolgálóhoz való kapcsolódáshoz az ODBC-adatforrás típusával. 
      
        Először töltse le a legújabb Oracle Instant-ügyfelet (alapszintű csomag vagy alapszintű Lite-csomag) és az ODBC-csomagot, majd töltse fel őket a *Main. cmd fájllal* együtt a tárolóba:
        * [64 bites csomagok letöltése](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (alapszintű csomag: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Alapszintű Lite-csomag: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; ODBC-csomag: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [32 bites csomagok letöltése](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (alapszintű csomag: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Alapszintű Lite-csomag: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; ODBC-csomag: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Egy *Oracle standard OLEDB* mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz az Oracle OLEDB-illesztőprogram telepítéséhez a Azure-SSIS IR minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy az OLEDB Csatlakozáskezelő, a forrás és a cél használatával kapcsolódjon az Oracle-kiszolgálóhoz. 
     
        Először [töltse le a legújabb Oracle OLEDB-illesztőprogramot](https://www.oracle.com/partners/campaign/index-090165.html) (például *ODAC122010Xcopy_x64.zip*), majd töltse fel a *Main. cmd fájllal* együtt a tárolóba.

      * Egy *POSTGRESQL ODBC* -mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz a PostgreSQL ODBC-illesztőprogramok telepítéséhez a Azure-SSIS IR minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy a PostgreSQL-kiszolgálóhoz való kapcsolódáshoz az ODBC-Csatlakozáskezelő, a forrás és a cél használatával kapcsolódjon. 
     
        Először [töltse le a POSTGRESQL ODBC illesztőprogram-telepítők legújabb 64 bites és 32 bites verzióit](https://www.postgresql.org/ftp/odbc/versions/msi/) (például *psqlodbc_x64.msi* és *psqlodbc_x86.msi*), majd töltse fel őket a *Main. cmd fájllal* együtt a tárolóba.

      * Egy *SAP BW* mappát, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz az SAP .net-összekötő szerelvény (*librfc32.dll*) telepítéséhez az Azure-SSIS IR Enterprise Edition minden egyes csomópontján. Ez a telepítő lehetővé teszi, hogy a SAP BW Csatlakozáskezelő, forrás és cél használatával kapcsolódjon a SAP BW-kiszolgálóhoz. 
      
        Először töltse fel az 64-bites vagy a 32-bites *librfc32.dll* verzióját az SAP telepítési mappájából, a *Main. cmd fájllal* együtt a tárolóba. A szkript ezután átmásolja az SAP-szerelvényt a *%windir%\syswow64 mappában* vagy a *%windir%\System32* mappába a telepítés során.

      * Egy *Storage* -mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz a Azure PowerShell telepítéséhez a Azure-SSIS IR egyes csomópontjain. Ez a telepítő lehetővé teszi, hogy [Azure PowerShell parancsmagokat/parancsfájlokat](../storage/blobs/storage-quickstart-blobs-powershell.md)futtató SSIS-csomagokat helyezzen üzembe és futtasson az Azure Storage kezeléséhez. 
      
        Másolja a *Main. cmd* fájlt, egy mintát *AzurePowerShell.msi* (vagy használja a legújabb verziót), és *storage.ps1* a tárolóba. A *PowerShell. dtsx* sablonként használhatja a csomagokat. A Package sablon egy [Azure Blob letöltési feladatot](/sql/integration-services/control-flow/azure-blob-download-task)egyesít, amely letölt egy módosítható PowerShell-parancsfájlt (*storage.ps1*) és egy [végrehajtási folyamat feladatot](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), amely végrehajtja a parancsfájlt az egyes csomópontokon.

      * Egy *TERADATA* mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*), a hozzá tartozó fájlt (*install. cmd*) és a telepítő csomagokat (*. msi*) tartalmaz. Ezek a fájlok telepítik a Teradata-összekötőket, a Teradata párhuzamos Transporter (TPT) API-t és az ODBC-illesztőt a Azure-SSIS IR Enterprise Edition minden egyes csomópontján. Ez a beállítás lehetővé teszi, hogy a Teradata Csatlakozáskezelő, forrás és cél használatával kapcsolódjon a Teradata-kiszolgálóhoz. 
      
        Először [töltse le a Teradata-eszközök és-segédprogramok 15. x zip-fájlját](http://partnerintelligence.teradata.com) (például  *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*), majd töltse fel a korábban említett *. cmd* és *. msi* fájlokkal együtt a tárolóba.

      * Egy *tls 1,2* mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) tartalmaz, hogy erős titkosítást és biztonságosabb hálózati protokollt (TLS 1,2) használjon a Azure-SSIS IR mindegyik csomópontján. A parancsfájl letiltja a régebbi SSL/TLS-verziókat is.

      * Egy *ZULU OPENJDK* mappa, amely egy egyéni telepítési parancsfájlt (*Main. cmd*) és egy PowerShell-fájlt (*install_openjdk.ps1*) tartalmaz a Zulu-OPENJDK telepítéséhez a Azure-SSIS IR egyes csomópontjain. Ez a beállítás lehetővé teszi, hogy Azure Data Lake Store és rugalmas fájl-összekötőket használjon az ork és a Parquet fájlok feldolgozásához. További információ: [Az Azure Feature Pack az integrációs szolgáltatásokhoz](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#dependency-on-java). 
      
        Először [töltse le a legújabb Zulu-OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (például *zulu8.33.0.1-jdk8.0.192-win_x64.zip*), majd töltse fel a *Main. cmd fájllal* együtt, és *install_openjdk.ps1* a tárolóba.

        ![A felhasználói forgatókönyvek mappában található mappák](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Ha újra szeretné használni ezeket a szabványos egyéni telepítési mintákat, másolja a kiválasztott mappa tartalmát a tárolóba.

1. Ha az ADF felhasználói felületén kiépít vagy újrakonfigurálja a Azure-SSIS IR, jelölje be a **Azure-SSIS Integration Runtime testreszabása további rendszerkonfigurációkkal/összetevő-telepítésekkel** jelölőnégyzetet az **Integration Runtime telepítési** paneljének **Speciális beállítások** lapján. Ezután adja meg a tároló SAS URI-JÁT az **egyéni telepítési tároló sas URI** -szövege szövegmezőben.
   
1. Ha Azure PowerShell használatával állítja be vagy konfigurálja újra a Azure-SSIS IR, állítsa le, ha már elindult/fut, futtassa a `Set-AzDataFactoryV2IntegrationRuntime` parancsmagot a tároló sas URI-jaként a paraméter értékeként `SetupScriptContainerSasUri` , majd indítsa el a Azure-SSIS IR.

1. A normál egyéni telepítés befejezése és a Azure-SSIS IR elindítása után megtalálhatja az összes egyéni telepítési naplót a tároló *fő. cmd. log* mappájába. Ezek tartalmazzák a *Main. cmd* és más végrehajtási naplók szabványos kimenetét.

## <a name="next-steps"></a>Következő lépések

- [Azure-SSIS IR Enterprise kiadásának beállítása](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Fizetett vagy licencelt összetevők fejlesztése a Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
