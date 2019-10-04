---
title: Az Azure Diagnostics (.NET) használata Cloud Services használatával | Microsoft Docs
description: Az Azure Diagnostics használatával adatokat gyűjthet az Azure Cloud Servicesből a hibakereséshez, a teljesítmény méréséhez, a figyeléshez, a forgalom elemzéséhez és egyebekhez.
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: gwallace
ms.openlocfilehash: 5f2ec77452b90d4270de043955fc0b443f045d5b
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68359682"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Azure Diagnostics engedélyezése az Azure-ban Cloud Services
A Azure Diagnostics hátterének [Azure Diagnostics áttekintését](../azure-diagnostics.md) lásd:.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Diagnosztika engedélyezése feldolgozói szerepkörben
Ez az útmutató azt ismerteti, hogyan valósítható meg egy olyan Azure feldolgozói szerepkör, amely telemetria-információkat bocsát ki a .NET EventSource osztály használatával. Azure Diagnostics a telemetria adatok gyűjtésére és egy Azure Storage-fiókba való tárolására szolgál. Feldolgozói szerepkör létrehozásakor a Visual Studio automatikusan engedélyezi a diagnosztika 1,0-es verzióját a .NET 2,4-es és korábbi verziójú Azure SDK-k megoldásának részeként. Az alábbi utasítások a feldolgozói szerepkör létrehozási folyamatát, a diagnosztika 1,0 a megoldásból való letiltását, valamint a diagnosztikai 1,2 vagy 1,3 üzembe helyezését ismertetik a feldolgozói szerepkörben.

### <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik Azure-előfizetéssel, és a Visual studiót használja az Azure SDK-val. Ha nem rendelkezik Azure-előfizetéssel, regisztrálhat az [ingyenes próbaverzióra][Free Trial]. Győződjön meg arról, hogy a [Azure PowerShell 0.8.7 vagy újabb verziót telepíti és konfigurálja][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>1\. lépés: Feldolgozói szerepkör létrehozása
1. Indítsa el a **Visual Studiót**.
2. Hozzon létre egy **Azure Cloud Service** -projektet a .NET-keretrendszer 4,5-es verzióit tároló **felhőalapú** sablonból.  Nevezze el a "WadExample" projektet, és kattintson az OK gombra.
3. Válassza a feldolgozó **szerepkör** lehetőséget, majd kattintson az OK gombra. Ekkor létrejön a projekt.
4. A **megoldáskezelő**kattintson duplán a **WorkerRole1** tulajdonságok fájlra.
5. A **konfiguráció** lapon szüntesse meg a diagnosztika **engedélyezését** a diagnosztika 1,0 (Azure SDK 2,4 és korábbi verziók) letiltásához.
6. Hozza létre a megoldását annak ellenőrzéséhez, hogy nincsenek-e hibák.

### <a name="step-2-instrument-your-code"></a>2\. lépés: A kód műszere
Cserélje le a WorkerRole.cs tartalmát a következő kódra. A [EventSource osztálytól][EventSource Class]örökölt SampleEventSourceWriter osztály négy naplózási módszert valósít meg: **SendEnums**, **MessageMethod**, **SetOther** és **HighFreq**. A **writeevent metódusnak** metódus első paramétere a megfelelő esemény azonosítóját határozza meg. A Run metódus egy végtelen hurkot valósít meg, amely minden egyes, a **SampleEventSourceWriter** osztályban implementált naplózási módszert meghívja 10 másodpercenként.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>3\. lépés: Feldolgozói szerepkör üzembe helyezése

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Telepítse a feldolgozói szerepkört az Azure-ba a Visual studióból, és válassza ki a **WadExample** projektet a megoldáskezelő majd **tegye közzé** a **Build** menüből.
2. Válassza ki az előfizetését.
3. A **Microsoft Azure közzétételi beállítások** párbeszédpanelen válassza az **új létrehozása**... lehetőséget.
4. A **felhőalapú szolgáltatás és a Storage-fiók létrehozása** párbeszédpanelen adjon meg egy **nevet** (például "WadExample"), és válasszon ki egy régiót vagy affinitási csoportot.
5. Állítsa be a környezetet **átmeneti**állapotba.
6. Módosítsa a megfelelő **beállításokat** , és kattintson a **Közzététel**gombra.
7. Az üzembe helyezés befejezése után ellenőrizze a Azure Portal, hogy a felhőalapú szolgáltatás **fut** -e.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>4\. lépés: A diagnosztika konfigurációs fájljának létrehozása és a bővítmény telepítése
1. Töltse le a nyilvános konfigurációs fájl sémájának definícióját a következő PowerShell-parancs végrehajtásával:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Vegyen fel egy XML-fájlt a **WorkerRole1** -projektbe úgy, hogy a jobb gombbal a **WorkerRole1** projektre kattint, és kiválasztja az**új elem** **hozzáadása** -> lehetőséget. -> **Visual C# Items** -> -**adatok** -> **XML-fájlja**. Nevezze el a "WadExample. xml" fájlt.

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Társítsa a WadConfig. XSD fájlt a konfigurációs fájllal. Győződjön meg arról, hogy az WadExample. XML szerkesztő ablak az aktív ablak. Nyomja le az **F4** billentyűt a **Tulajdonságok** ablak megnyitásához. Kattintson a **sémák** tulajdonságra a **Tulajdonságok** ablakban. Kattintson a **.** . a **sémák** tulajdonságban. Kattintson a **Hozzáadás...** gombra, és navigáljon arra a helyre, ahová az XSD-fájlt mentette, majd válassza ki a WadConfig. XSD fájlt. Kattintson az **OK** gombra.

4. Cserélje le a WadExample. xml konfigurációs fájl tartalmát a következő XML-fájlba, és mentse a fájlt. Ez a konfigurációs fájl egy pár teljesítményszámlálókat határoz meg a gyűjtéshez: egyet a CPU-használathoz, egyet pedig a memória kihasználtságához. Ezután a konfiguráció a SampleEventSourceWriter osztályban található metódusoknak megfelelő négy eseményt határozza meg.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>5\. lépés: Diagnosztika telepítése a feldolgozói szerepkörre
A webes vagy feldolgozói szerepkör diagnosztika-kezelési PowerShell-parancsmagjai a következők: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension, and Remove-AzureServiceDiagnosticsExtension.

1. Nyissa meg Azure PowerShell.
2. Futtassa a parancsfájlt a diagnosztika telepítéséhez a feldolgozói szerepkörön (cserélje le a *StorageAccountKey* -t a wadexample Storage-fiókhoz tartozó Storage-fiók kulcsára, és *config_path* a *wadexample. XML* fájl elérési útját):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>6\. lépés: Tekintse meg a telemetria-adatait
A Visual Studio **Server Explorerben**navigáljon a wadexample Storage-fiókhoz. Miután a Cloud Service öt (5) percet futott, látnia kell a **WADEnumsTable**, a **WADHighFreqTable**, a **WADMessageTable**, a **WADPerformanceCountersTable** és a **WADSetOtherTable**táblákat. Kattintson duplán az egyik táblázatra a gyűjtött telemetria megtekintéséhez.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Konfigurációs fájl sémája
A diagnosztikai konfigurációs fájl határozza meg azokat az értékeket, amelyeket a rendszer a diagnosztika ügynök indításakor a diagnosztikai konfigurációs beállítások inicializálásához használ. Tekintse meg az érvényes értékeket és példákat a [legújabb séma](/azure/azure-monitor/platform/diagnostics-extension-schema) -referenciával.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha problémája van, tekintse meg a gyakori problémákkal kapcsolatos segítségért [Azure Diagnostics hibaelhárítással](../azure-diagnostics-troubleshooting.md) foglalkozó témakört.

## <a name="next-steps"></a>További lépések
Tekintse meg a [kapcsolódó Azure virtuális gépek diagnosztikai cikkeinek listáját](../azure-monitor/platform/diagnostics-extension-overview.md#cloud-services-using-azure-diagnostics) a gyűjtött adatok módosításához, a problémák elhárításához, illetve a diagnosztika általános megismeréséhez.

[EventSource Class]: https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: https://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/
