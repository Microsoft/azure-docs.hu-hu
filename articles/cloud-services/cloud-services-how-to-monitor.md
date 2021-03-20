---
title: Azure Cloud Service (klasszikus) figyelése | Microsoft Docs
description: Leírja, hogy az Azure Cloud Service milyen figyelési funkciót tartalmaz, és milyen lehetőségek közül választhat.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 60f320f86860cca482cdf25c7d93f84dae8c4e5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743372"
---
# <a name="introduction-to-cloud-service-classic-monitoring"></a>Bevezetés a Cloud Service (klasszikus) Monitorozásba

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Bármely felhőalapú szolgáltatás fő teljesítménymutatóit nyomon követheti. Minden Cloud Service-szerepkör minimális adatmennyiséget gyűjt: a CPU-használatot, a hálózati használatot és a lemez kihasználtságát. Ha a felhőalapú szolgáltatás rendelkezik `Microsoft.Azure.Diagnostics` egy szerepkörre alkalmazott bővítménnyel, akkor a szerepkör további adatpontokat gyűjthet. Ez a cikk a Cloud Services Azure Diagnostics bevezetését ismerteti.

Alapszintű figyeléssel a szerepkör példányaiból származó teljesítményszámláló-adatok mintavétele és összegyűjtése 3 percenként történik. Ezt az alapszintű figyelési adat nem tárolja a Storage-fiókban, és nem rendelkezik további díjszabással.

A speciális monitorozással a további mérőszámok mintavételezése és gyűjtése 5 perc, 1 óra és 12 óra időközönként történik. Az összesített adatokat egy Storage-fiókban tárolja a rendszer, és 10 nap után törlődik. A használt Storage-fiókot a szerepkör konfigurálja; a különböző szerepkörökhöz különböző tárolási fiókokat használhat. Ez a [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) és a [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fájlokban lévő kapcsolatok karakterláncával van konfigurálva.


## <a name="basic-monitoring"></a>Alapszintű figyelés

Ahogy az a bevezetésben is szerepel, a Cloud Service automatikusan gyűjti az alapszintű figyelési adatokat a gazdagép virtuális gépről. Ezek az információk a CPU százalékos arányát, a hálózat be-és kijelentkezését, valamint a lemezek olvasását és írását tartalmazzák. A begyűjtött figyelési adatok automatikusan megjelennek a Cloud Service áttekintés és metrikák lapjain, a Azure Portalban. 

Az alapszintű figyeléshez nincs szükség Storage-fiókra. 

![alapszintű Cloud Service-figyelési csempék](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Speciális figyelés

A speciális figyelés a figyelni kívánt szerepkör **Azure Diagnostics** -bővítményét (és opcionálisan a Application Insights SDK-t) használja. A diagnosztikai bővítmény egy **Diagnostics. wadcfgx** nevű konfigurációs fájlt használ a megfigyelt diagnosztikai metrikák konfigurálásához. Az Azure diagnosztikai bővítmény egy Azure Storage-fiókban gyűjti és tárolja az adatokat. Ezek a beállítások a **. wadcfgx**, a [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)és a [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) fájlokban vannak konfigurálva. Ez azt jelenti, hogy a speciális figyeléshez külön díj jár.

Az egyes szerepkörök létrehozásakor a Visual Studio hozzáadja a Azure Diagnostics bővítményt. Ez a diagnosztikai bővítmény a következő típusú információkat tudja gyűjteni:

* Egyéni teljesítményszámlálók
* Alkalmazás-naplók
* Windows-eseménynaplók
* .NET-esemény forrása
* IIS-naplók
* Jegyzékalapú ETW
* Összeomlási memóriaképek
* Felhasználói hibák naplói

> [!IMPORTANT]
> Noha az összes ilyen adatokat összesíti a rendszer a Storage-fiókba, a portál **nem** biztosít natív módot az adatdiagramhoz. Erősen ajánlott egy másik szolgáltatás, például a Application Insights integrálása az alkalmazásba.

## <a name="setup-diagnostics-extension"></a>Beállítási diagnosztikai bővítmény

Először is, ha nem rendelkezik **klasszikus** Storage-fiókkal, [hozzon létre egyet](../storage/common/storage-account-create.md). Győződjön meg arról, hogy a Storage-fiók a megadott **klasszikus üzembehelyezési modellel** lett létrehozva.

Ezután navigáljon a **Storage-fiók (klasszikus)** erőforráshoz. Válassza a **Beállítások**  >  **hozzáférési kulcsok** lehetőséget, és másolja az **elsődleges kapcsolati sztring** értéket. Erre az értékre szüksége lesz a Cloud Service-hez. 

A speciális diagnosztika engedélyezéséhez két konfigurációs fájlra van szükség, a **ServiceDefinition. csdef** és a **ServiceConfiguration. cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition. csdef

A **ServiceDefinition. csdef** fájlban adjon hozzá egy nevű új beállítást `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` minden olyan szerepkörhöz, amely speciális diagnosztikát használ. Ha új projektet hoz létre, a Visual Studio hozzáadja ezt az értéket a fájlhoz. Ha hiányzik, most hozzáadhatja. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Ez egy új beállítást határoz meg, amelyet hozzá kell adni minden **ServiceConfiguration. cscfg** fájlhoz. 

Legvalószínűbb, hogy két **. cscfg** -fájlt, egy **ServiceConfiguration. Cloud. cscfg** -t használ az Azure-ba való üzembe helyezéshez, valamint egy elnevezett **ServiceConfiguration. local. cscfg** , amely az emulált környezetben való helyi központi telepítésekhez használatos. Nyissa meg és módosítsa az egyes **. cscfg** fájlokat. Adjon hozzá egy nevű beállítást `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` . Állítsa az értéket a klasszikus Storage-fiók **elsődleges kapcsolódási karakterláncára** . Ha a fejlesztői gépen a helyi tárterületet szeretné használni, használja a következőt: `UseDevelopmentStorage=true` .

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Application Insights használata

Amikor közzéteszi a Cloud Service-t a Visual studióból, lehetősége van a diagnosztikai adatok küldésére Application Insights. Ekkor létrehozhatja az Application Insights Azure-erőforrást, vagy elküldheti azokat egy meglévő Azure-erőforrásnak. A felhőalapú szolgáltatást Application Insights a rendelkezésre állás, a teljesítmény, a hibák és a használat szempontjából is megfigyelheti. Az egyéni diagramok hozzáadhatók a Application Insightshoz, így a leginkább fontos információk láthatók. A szerepkör-példány adatait a Cloud Service-projektben található Application Insights SDK-val lehet gyűjteni. A Application Insights integrálásával kapcsolatos további információkért lásd: [Application Insights a Cloud Services](../azure-monitor/app/cloudservices.md).

Vegye figyelembe, hogy habár a Application Insights használatával jelenítheti meg a Windows Azure Diagnostics-bővítményen keresztül megadott teljesítményszámlálók (és egyéb beállítások), az Application Insights SDK-nak a munkavégző és webes szerepköreibe való integrálásával gazdagabb élményt nyújt.


## <a name="next-steps"></a>Következő lépések

- [További információ a Application Insightsról Cloud Services](../azure-monitor/app/cloudservices.md)
- [Teljesítményszámlálók beállítása](diagnostics-performance-counters.md)




