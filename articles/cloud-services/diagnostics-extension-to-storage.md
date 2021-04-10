---
title: Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban
description: Ismerje meg, hogyan gyűjthet Azure-diagnosztikai adatokat egy Azure Storage-fiókban, így megtekintheti azt a számos elérhető eszköz egyikével.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 4962d5f048cf41eca50a77a0dedad3cef48ac1f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98740074"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A diagnosztikai adatok nem tárolódnak véglegesen, hacsak nem továbbítja azt a Microsoft Azure Storage Emulatorba vagy az Azure Storage-ba. A tárolás után a több elérhető eszköz egyikével is megtekinthető.

## <a name="specify-a-storage-account"></a>Válasszon Storage-fiókot
A ServiceConfiguration. cscfg fájlban használni kívánt Storage-fiókot kell megadnia. A fiókadatok a konfigurációs beállításokban kapcsolatok karakterláncként vannak megadva. Az alábbi példa a Visual Studióban egy új Cloud Service-projekthez létrehozott alapértelmezett kapcsolatok karakterláncát mutatja be:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Ez a kapcsolódási sztring módosítható egy Azure Storage-fiókhoz tartozó fiókadatok megadásához.

A gyűjtött diagnosztikai adatok típusától függően Azure Diagnostics a Blob service vagy a Table service használja. A következő táblázat a megőrzött adatforrásokat és azok formátumát mutatja be.

| Adatforrás | Tárolási formátum |
| --- | --- |
| Azure-naplók |Tábla |
| IIS 7,0-naplók |Blob |
| Azure Diagnostics-infrastruktúranaplók |Tábla |
| Sikertelen kérelmek nyomkövetési naplói |Blob |
| Windows-eseménynaplók |Tábla |
| Teljesítményszámlálók |Tábla |
| Összeomlási memóriaképek |Blob |
| Egyéni hibanaplók |Blob |

## <a name="transfer-diagnostic-data"></a>Diagnosztikai adatok átvitele
Az SDK 2,5-es és újabb verzióiban a diagnosztikai adatok átvitelére irányuló kérelem a konfigurációs fájlon keresztül történhet. A diagnosztikai adatok átvihetők ütemezett időközönként a konfigurációban megadott módon.

Az SDK 2,4 és az előző csomag esetében a diagnosztikai adatoknak a konfigurációs fájlon keresztül történő átvitelét, valamint programozott módon történő továbbítását kérheti. A programozott megközelítés lehetővé teszi az igény szerinti átvitelt is.

> [!IMPORTANT]
> A diagnosztikai adatok Azure Storage-fiókba való átvitele során a diagnosztikai adatok által használt tárolási erőforrások költségei is felmerülnek.
> 
> 

## <a name="store-diagnostic-data"></a>Diagnosztikai adattárolók tárolása
A naplófájlok tárolása a blob vagy a Table Storage szolgáltatásban történik a következő nevekkel:

**Táblák**

* **WadLogsTable** – a nyomkövetési figyelő használatával kódban írt naplók.
* **WADDiagnosticInfrastructureLogsTable** – diagnosztikai figyelő és konfigurációs változások.
* **WADDirectoriesTable** – a diagnosztikai figyelő által figyelt címtárak.  Ez magában foglalja az IIS-naplókat, az IIS sikertelen kérelmek naplóit és az egyéni címtárakat.  A blob naplófájljának helye a tároló mezőben van megadva, és a blob neve a RelativePath mezőben található.  A AbsolutePath mező a fájl helyét és nevét jelzi, ahogy az Azure-beli virtuális gépen létezett.
* **WADPerformanceCountersTable** – teljesítményszámlálók.
* **WADWindowsEventLogsTable** – Windows-eseménynaplók.

**Blobok**

* **wad-Control-Container** – (csak az SDK 2,4-es és korábbi esetében) tartalmazza az Azure Diagnostics-t vezérlő XML-konfigurációs fájlokat.
* **wad-IIS-failedreqlogfiles** – az IIS sikertelen kérelmek naplóiból származó információkat tartalmaz.
* **wad-IIS-LogFiles** – az IIS-naplókról tartalmaz információkat.
* **"Custom"** – egyéni tároló, amely a diagnosztikai figyelő által figyelt könyvtárak konfigurálásán alapul.  A blob-tároló neve a WADDirectoriesTable-ben lesz meghatározva.

## <a name="tools-to-view-diagnostic-data"></a>Eszközök a diagnosztikai adatgyűjtés megtekintéséhez
Több eszköz is elérhető az adattárolásra a Storage szolgáltatásba való átvitelük után. Például:

* Kiszolgálókezelő a Visual Studióban – ha telepítette a Microsoft Visual studióhoz készült Azure-eszközöket, a Server Explorerben az Azure Storage csomópont használatával megtekintheti az Azure Storage-fiókok írásvédett blob-és táblázat-adatait. A helyi Storage Emulator-fiókból és az Azure-hoz létrehozott Storage-fiókoktól származó adatok is megjeleníthetők. További információ: [Storage-erőforrások tallózása és kezelése a Server Explorerben](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy önálló alkalmazás, amely lehetővé teszi az Azure Storage-alapú adattárolást Windows, OSX és Linux rendszeren.
* Az [azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) tartalmaz Azure Diagnostics kezelőt, amely lehetővé teszi az Azure-on futó alkalmazások által gyűjtött diagnosztikai adatok megtekintését, letöltését és kezelését.

## <a name="next-steps"></a>Következő lépések
[A folyamat nyomon követése egy Cloud Services alkalmazásban Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


