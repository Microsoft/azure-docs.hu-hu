---
title: Windows Azure Diagnostics-bővítmény (WAD) konfigurációs sémájának korábbi verziói
description: Az Azure Virtual Machines, VM Scale Sets, Service Fabric és Cloud Services teljesítmény-számlálóinak gyűjtéséhez szükséges.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 3b6dc0e19c181ebf4df2778633f981258023e046
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617318"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>Windows Azure Diagnostics-bővítmény (WAD) konfigurációs sémájának verziói és előzményei
Ez a cikk a [Windows (wad)](diagnostics-extension-overview.md) sémájának a Microsoft Azure SDK részeként szállított verziójának Azure Diagnostics korábbi verzióit tartalmazza.  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Az Azure SDK és a diagnosztikai verziók szállítási diagramja  

|Azure SDK-verzió | Diagnosztikai bővítmény verziója | Modellezés|  
|------------------|-------------------------------|------|  
|1. x               |1.0                            |beépülő modul|  
|2,0 – 2,4         |1.0                            |beépülő modul|  
|2.5               |1.2                            |kiterjesztés|  
|2,6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2,8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2,96              |1.7                            |"|
|2,96              |1.8                            |"|
|2,96              |1.8.1                          |"|
|2,96              |1.9                            |"|
|2,96              |1,11                           |"|


 A Azure Diagnostics 1,0-es verziójának első része egy beépülőmodul-modellben – ami azt jelenti, hogy amikor az Azure SDK-t telepítette, az Azure Diagnostics azon verzióját kapta, amelyet a szolgáltatással szállított.  

 Az SDK 2,5-as verziójától kezdve (a diagnosztika 1,2-es verziója) az Azure Diagnostics bővítmény-modellbe került. Az új funkciók felhasználására szolgáló eszközök csak az újabb Azure SDK-k esetében voltak elérhetők, de az Azure Diagnostics szolgáltatást használó bármely szolgáltatás közvetlenül az Azure-ból fogja használni a legújabb szállítási verziót. Előfordulhat például, hogy az SDK 2,5-et használó bárki betölti az előző táblázatban látható legújabb verziót, függetlenül attól, hogy az újabb funkciókat használja-e.  

## <a name="schemas-index"></a>Sémák indexe  
Az Azure Diagnostics különböző verziói eltérő konfigurációs sémákat használnak. A 1,0-es és a 1,2-es séma elavult. További információ a 1,3-es és újabb verziókról: [diagnosztika 1,3 és újabb konfigurációs séma](diagnostics-extension-schema-windows.md)  

## <a name="version-history"></a>Korábbi verziók

### <a name="diagnostics-extension-111"></a>Diagnosztikai bővítmény 1,11
A Azure Monitor-fogadó támogatása. Ez a fogadó csak a teljesítményszámlálók esetében alkalmazható. Lehetővé teszi a virtuális gépen, VMSS vagy Cloud Service-ben gyűjtött teljesítményszámlálók küldését egyéni metrikák Azure Monitor. A Azure Monitor fogadó a következőket támogatja:
* A Azure Monitor eljuttatott teljesítményszámlálók beolvasása a [Azure monitor metrikák API](/rest/api/monitor/metrics/list) -kon keresztül.
* Riasztás a Azure Monitor elküldhető összes teljesítményszámlálók számára az új, [egyesített riasztások](../platform/alerts-overview.md) használatával Azure monitor
* A helyettesítő karakterek kezelése a teljesítményszámlálók esetében a mérőszámban a "példány" dimenzió. Ha például összegyűjtötte a "LogicalDisk ( \* )/DiskWrites/sec" számlálót, akkor az egyes logikai lemezek (C:, D: stb.) esetében a "példány" dimenzióra bontva vagy a riasztást is fel lehet osztani a lemezre, illetve a riasztásra.

Azure Monitor definiálása új fogadóként a diagnosztikai bővítmény konfigurációjában
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> A klasszikus virtuális gépek és a klasszikus CLoud Service Azure Monitor-fogadójának konfigurálása több paramétert igényel a diagnosztikai bővítmény privát konfigurációjában.
>
> További részletekért tekintse meg a [diagnosztikai bővítmények részletes sémájának dokumentációját.](diagnostics-extension-schema-windows.md)

Ezt követően beállíthatja, hogy a teljesítményszámlálók átirányítva legyenek a Azure Monitor fogadóba.
```json
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "AzureMonitorSink",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT1M",
            "unit": "percent"
        }
    ]
},
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>Diagnosztikai bővítmény 1,9
Docker-támogatás hozzáadva.


### <a name="diagnostics-extension-181"></a>Diagnosztikai bővítmény – 1.8.1
A saját konfigurációjában a Storage-fiók kulcsa helyett egy SAS-tokent is megadhat. Ha SAS-token van megadva, a rendszer figyelmen kívül hagyja a Storage-fiók kulcsát.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Diagnosztikai bővítmény 1,8
A tárolási típus hozzáadva a PublicConfig. A StorageType lehet *Table*, *blob* vagy *TableAndBlob*. A *tábla* az alapértelmezett.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Diagnosztikai bővítmény 1,7
Lehetőség van a EventHub való átirányításra.

### <a name="diagnostics-extension-15"></a>Diagnosztikai bővítmény 1,5
Hozzáadta a mosogató elemet, és lehetővé teszi a diagnosztikai adataik küldését [Application Insights](../app/cloudservices.md) így könnyebben diagnosztizálhatja az alkalmazással kapcsolatos problémákat, valamint a rendszer és az infrastruktúra szintjét.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2,6 és diagnosztikai bővítmény 1,3
A Visual Studióban a Cloud Service-projektek esetében a következő módosítások történnek. (Ezek a módosítások az Azure SDK újabb verzióira is érvényesek.)

* A helyi emulátor mostantól támogatja a diagnosztikát. Ez a módosítás azt jelenti, hogy diagnosztikai adatokat gyűjthet, és biztosítja, hogy az alkalmazás a megfelelő nyomkövetést hozza létre a Visual Studióban történő fejlesztés és tesztelés során. A kapcsolati karakterlánc `UseDevelopmentStorage=true` lehetővé teszi a diagnosztikai adatgyűjtést, miközben a Cloud Service-projektet a Visual Studióban futtatja az Azure Storage Emulator használatával. Az összes diagnosztikai adatokat a (fejlesztői tároló) Storage-fiók gyűjti.
* A diagnosztika Storage-fiók kapcsolati karakterláncát (Microsoft. WindowsAzure. plugins. Diagnostics. ConnectionString) a szolgáltatás konfigurációs (. cscfg) fájljában ismét tárolja a rendszer. Az Azure SDK 2,5-as verziójában a Diagnostics Storage-fiók meg lett adva a Diagnostics. wadcfgx fájlban.

A kapcsolati karakterlánc az Azure SDK 2,4-es és korábbi verzióiban, illetve az Azure SDK 2,6-es és újabb verzióiban is működik.

* Az Azure SDK 2,4-es és korábbi verzióiban a diagnosztika beépülő modul a következőhöz használta a kapcsolódási karakterláncot a diagnosztikai naplók átadásához szükséges Storage-fiók adatainak lekéréséhez:.
* Az Azure SDK 2,6-as és újabb verzióiban a Visual Studio a diagnosztika-kapcsolódási karakterlánc használatával konfigurálja a diagnosztika bővítményt a közzététel során a megfelelő Storage-fiók adataival. A kapcsolódási karakterlánc lehetővé teszi különböző tárolási fiókok definiálását különböző szolgáltatási konfigurációkhoz, amelyeket a Visual Studio a közzétételkor használni fog. Mivel azonban a diagnosztikai beépülő modul már nem érhető el (az Azure SDK 2,5 után), a. cscfg fájl önmagában nem tudja engedélyezni a diagnosztikai bővítményt. A bővítményt külön kell engedélyeznie, például a Visual Studióban vagy a PowerShellben.
* A diagnosztikai bővítmény PowerShell-lel való konfigurálásának egyszerűbbé tétele érdekében a Visual studióból származó csomag kimenete tartalmazza az egyes szerepkörökhöz tartozó diagnosztikai bővítmény nyilvános konfigurációs XML-fájlját is. A Visual Studio a diagnosztikai kapcsolódási karakterlánc használatával tölti fel a nyilvános konfigurációban lévő Storage-fiók adatait. A nyilvános konfigurációs fájlok a Extensions mappában jönnek létre, és követik a mintát `PaaSDiagnostics.<RoleName>.PubConfig.xml` . A PowerShell-alapú telepítések ezt a mintát használhatják az egyes konfigurációknak egy szerepkörhöz való leképezéséhez.
* A. cscfg fájlban található kapcsolati karakterláncot a Azure Portal is használja a diagnosztikai információ eléréséhez, hogy az megjelenjen a **figyelés** lapon. A kapcsolati karakterlánc szükséges ahhoz, hogy a szolgáltatás beállítható legyen a portálon a részletes monitorozási adatai megjelenítéséhez.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Projektek migrálása az Azure SDK 2,6-es és újabb verzióiba
Ha az Azure SDK 2,5-ból az Azure SDK 2,6-es vagy újabb verziójára végez áttelepítést, akkor ha a. wadcfgx fájlban megadott diagnosztikai Storage-fiókkal rendelkezik, akkor ott marad. Ahhoz, hogy a különböző Storage-fiókok különböző tárolási konfigurációkhoz való használatának rugalmassága kihasználható legyen, manuálisan hozzá kell adnia a kapcsolódási karakterláncot a projekthez. Ha egy projektet az Azure SDK 2,4-es vagy korábbi verziójából az Azure SDK 2,6-be telepít át, a rendszer megőrzi a diagnosztikai kapcsolódási karakterláncokat. Vegye figyelembe azonban, hogy a rendszer hogyan kezeli a kapcsolatok karakterláncait az Azure SDK 2,6-ben az előző szakaszban leírtak szerint.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Hogyan határozza meg a Visual Studio a diagnosztikai Storage-fiókot
* Ha a. cscfg fájlban egy diagnosztikai kapcsolódási karakterlánc van megadva, a Visual Studio azt használja a diagnosztikai bővítmény konfigurálásához a közzétételkor, valamint a nyilvános konfigurációs XML-fájlok a csomagolás során történő létrehozásakor.
* Ha nem ad meg diagnosztikai kapcsolódási karakterláncot a. cscfg fájlban, akkor a Visual Studio visszavált a. wadcfgx fájlban megadott Storage-fiók használatára a diagnosztika bővítménynek a közzétételkor történő konfigurálásához, valamint a nyilvános konfigurációs XML-fájlok csomagoláskor történő létrehozásához.
* A. cscfg fájl diagnosztika kapcsolati karakterlánca elsőbbséget élvez a. wadcfgx fájl Storage-fiókjával szemben. Ha a. cscfg fájlban a diagnosztikai kapcsolatok karakterlánca van megadva, akkor a Visual Studio ezt használja, és figyelmen kívül hagyja a Storage-fiókot a. wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Mit jelent a "fejlesztési tárolói kapcsolatok frissítése karakterláncok..." jelölőnégyzetet?
A **diagnosztikai és gyorsítótárazási Microsoft Azure a Storage-fiók hitelesítő adataival történő Microsoft Azure közzétételkor a frissítési fejlesztési tárolóhoz való kapcsolódáshoz használt karakterláncok** jelölőnégyzetének megadásával egyszerűen frissítheti a fejlesztői fiók kapcsolódási karakterláncait a közzététel során megadott Azure Storage-fiókkal.

Tegyük fel például, hogy bejelöli ezt a jelölőnégyzetet, és a diagnosztikai kapcsolatok karakterlánca határozza meg `UseDevelopmentStorage=true` . Amikor közzéteszi a projektet az Azure-ban, a Visual Studio automatikusan frissíti a diagnosztikai kapcsolódási karakterláncot a Közzétételi varázslóban megadott Storage-fiókkal. Ha azonban valódi Storage-fiókot adott meg diagnosztikai kapcsolatok karakterláncként, akkor a rendszer ezt a fiókot használja helyette.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Az Azure SDK 2,4-es és korábbi verziói, valamint az Azure SDK 2,5-es és újabb verziói közötti diagnosztikai funkciók
Ha az Azure SDK 2,4-ból az Azure SDK 2,5-es vagy újabb verziójára frissíti a projektet, vegye figyelembe a következő diagnosztikai funkciók különbségeit.

* A **konfigurációs API-k elavultak** – a diagnosztika programozási konfigurációja az azure SDK 2,4-as vagy korábbi verzióiban érhető el, de az azure SDK 2,5-es és újabb verzióiban elavult. Ha a diagnosztikai konfiguráció jelenleg kód alapján van definiálva, akkor a diagnosztika működésének érdekében újra kell konfigurálnia ezeket a beállításokat az áttelepített projektben. Az Azure SDK 2,4 diagnosztikai konfigurációs fájlja a Diagnostics. wadcfg és a Diagnostics. wadcfgx az Azure SDK 2,5-es és újabb verzióihoz.
* **A Cloud Service-alkalmazások diagnosztikát csak a szerepkör szintjén lehet konfigurálni, nem pedig a példány szintjén.**
* **Minden alkalommal, amikor telepíti az alkalmazást, a rendszer frissíti a diagnosztika konfigurációját** – ez paritásos problémákat okozhat, ha módosítja a diagnosztika konfigurációját a Server Explorerben, majd újból üzembe helyezi az alkalmazást.
* **Az Azure sdk 2,5-as és újabb verzióiban az összeomlási memóriaképek a diagnosztika konfigurációs fájljában vannak konfigurálva, nem pedig a kódban** – ha a kódban beállított összeomlási memóriaképek vannak, akkor manuálisan kell átvinnie a konfigurációt a kódból a konfigurációs fájlba, mivel az összeomlási memóriaképek nem kerülnek át az Azure SDK 2,6-es verzióra való Migrálás során.

