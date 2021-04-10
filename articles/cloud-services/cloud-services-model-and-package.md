---
title: Mi a Cloud Service (klasszikus) modell és csomag | Microsoft Docs
description: Ismerteti a Cloud Service-modellt (. csdef,. cscfg) és a csomagot (. cspkg) az Azure-ban
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 1cf8e966b80e005a0cb2cf7ea46f355e38cb0011
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741536"
---
# <a name="what-is-the-cloud-service-classic-model-and-how-do-i-package-it"></a>Mi a Cloud Service (klasszikus) modell, és hogyan lehet becsomagolni?

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A felhőalapú szolgáltatás három összetevőből, a szolgáltatás-definícióból ( *. csdef)*, a szolgáltatás-konfigurációból *(. cscfg)* és egy szolgáltatáscsomag *(. cspkg)* jön létre. A **ServiceDefinition. csdef** és a **ServiceConfig. CSCFG** fájl is XML-alapú, és leírja a Cloud Service szerkezetét és konfigurálását. együttesen nevezik a modellt. A **szervizcsomag. cspkg** egy zip-fájl, amely a **ServiceDefinition. csdef** és egyebek között jön létre, és tartalmazza az összes szükséges bináris-alapú függőséget. Az Azure létrehoz egy felhőalapú szolgáltatást mind a **szervizcsomaggal. cspkg** , mind a **ServiceConfig. cscfg**.

Miután a Cloud Service fut az Azure-ban, újrakonfigurálhatja azt a **ServiceConfig. cscfg** fájlon keresztül, de a definíció nem módosítható.

## <a name="what-would-you-like-to-know-more-about"></a>Mit szeretne megtudni?
* Többet szeretnék megtudni a [ServiceDefinition. csdef](#csdef) és a [ServiceConfig. cscfg](#cscfg) fájlokról.
* Már tudom, hogy van [néhány példa](#next-steps) arra, hogy mit tudok konfigurálni.
* Szeretném létrehozni a [szervizcsomagot. cspkg](#cspkg).
* Visual studiót használok, és szeretnék...
  * [Felhőalapú szolgáltatás létrehozása][vs_create]
  * [Meglévő felhőalapú szolgáltatás újrakonfigurálása][vs_reconfigure]
  * [Cloud Service-projekt üzembe helyezése][vs_deploy]
  * [Távoli asztal felhőalapú szolgáltatásbeli példányba][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition. csdef
A **ServiceDefinition. csdef** fájl azokat a beállításokat adja meg, amelyeket az Azure a Cloud Service konfigurálásához használ. Az [Azure-szolgáltatás definíciós sémája (. Csdef fájl)](/previous-versions/azure/reference/ee758711(v=azure.100)) egy szolgáltatás-definíciós fájl számára engedélyezhető formátumot biztosít. A következő példa a webes és feldolgozói szerepkörökhöz definiálható beállításokat mutatja be:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Az itt használt XML-séma jobb megismeréséhez tekintse meg a [szolgáltatás definíciós sémáját](/previous-versions/azure/reference/ee758711(v=azure.100)) , de itt talál néhány elemet:

**Webhelyek**  
A IIS7 szolgáltatásban üzemeltetett webhelyek vagy webalkalmazások definícióit tartalmazza.

**InputEndpoints**  
A felhőalapú szolgáltatáshoz való kapcsolódáshoz használt végpontok definícióit tartalmazza.

**InternalEndpoints**  
A szerepkör-példányok által az egymással való kommunikációhoz használt végpontok definícióit tartalmazza.

**ConfigurationSettings**  
Egy adott szerepkör funkcióinak beállítási definícióit tartalmazza.

**Tanúsítványok**  
A szerepkörhöz szükséges tanúsítványok definícióit tartalmazza. Az előző kód példa az Azure-kapcsolat konfigurálásához használt tanúsítványt mutatja be.

**LocalResources**  
A helyi tárolási erőforrások definícióit tartalmazza. A helyi tárolási erőforrás a virtuális gép fájlrendszerén található fenntartott könyvtár, amelyben a szerepkör egy példánya fut.

**Importálja**  
Az importált modulok definícióit tartalmazza. Az előző példában a Távoli asztali kapcsolat és az Azure-kapcsolat moduljai láthatók.

**Indítás**  
A szerepkör indításakor futtatott feladatokat tartalmazza. A feladatok egy. cmd vagy végrehajtható fájlban vannak meghatározva.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration. cscfg
A felhőalapú szolgáltatás beállításainak konfigurációját a **ServiceConfiguration. cscfg** fájl értékei határozzák meg. Itt adhatja meg, hogy hány példányt kíván telepíteni a fájl egyes szerepköreihez. A szolgáltatás-definíciós fájlban megadott konfigurációs beállítások értékeit a rendszer hozzáadja a szolgáltatás konfigurációs fájljához. A rendszer a felhőalapú szolgáltatáshoz társított felügyeleti tanúsítványok ujjlenyomatai megfelelnek is hozzáadja a fájlhoz. Az [Azure szolgáltatás konfigurációs sémája (. Cscfg fájl)](/previous-versions/azure/reference/ee758710(v=azure.100)) a szolgáltatás konfigurációs fájljának engedélyezett formátumát adja meg.

A szolgáltatás konfigurációs fájlja nincs becsomagolva az alkalmazásba, de az Azure-ba feltöltött külön fájlként, és a felhőalapú szolgáltatás konfigurálására szolgál. A felhőalapú szolgáltatás újbóli üzembe helyezése nélkül feltöltheti az új szolgáltatás konfigurációs fájlját. A felhőalapú szolgáltatás konfigurációs értékei megváltoztathatók a Cloud Service futása közben. A következő példa a webes és feldolgozói szerepkörökhöz definiálható konfigurációs beállításokat mutatja be:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Az itt használt XML-séma jobb megismeréséhez tekintse meg a [szolgáltatás konfigurációs sémáját](/previous-versions/azure/reference/ee758710(v=azure.100)) , de itt találja az elemek gyors magyarázatát:

**példányszám**  
A szerepkörhöz tartozó futó példányok számát konfigurálja. Annak megakadályozása érdekében, hogy a felhőalapú szolgáltatás esetleg elérhetetlenné váljon a frissítések során, javasoljuk, hogy a webes szerepkörök egynél több példányát telepítse. Több példány üzembe helyezésével betartja az [Azure számítási szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/)irányelveit, ami 99,95%-os külső kapcsolatot garantál az internetes szerepkörökhöz, ha két vagy több szerepkör-példány van telepítve egy szolgáltatáshoz.

**ConfigurationSettings**  
A szerepkörhöz tartozó futó példányok beállításainak konfigurálása. Az `<Setting>` elemek nevének meg kell egyeznie a szolgáltatás-definíciós fájlban megadott beállításokkal.

**Tanúsítványok**  
A szolgáltatás által használt tanúsítványok konfigurálása. Az előző kód példa bemutatja, hogyan határozható meg a RemoteAccess modul tanúsítványa. Az *ujjlenyomat* -attribútum értékét a használni kívánt tanúsítvány ujjlenyomatára kell beállítani.

<p/>

> [!NOTE]
> A Tanúsítvány ujjlenyomata egy szövegszerkesztő használatával adható hozzá a konfigurációs fájlhoz. Az értéket a Visual Studióban a szerepkör **tulajdonságlapján** is hozzáadhatja a **tanúsítványok** lapon.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Portok definiálása a szerepkör példányaihoz
Az Azure csak egy belépési pontot engedélyez egy webes szerepkör számára. Azt jelenti, hogy minden forgalom egy IP-címen keresztül történik. A webhelyeket úgy konfigurálhatja, hogy a gazdagép fejlécének konfigurálásával megossza a kérést a megfelelő helyre. Az alkalmazásokat úgy is konfigurálhatja, hogy az IP-címen jól ismert portokat hallgasson.

A következő minta egy webhelyhez és webalkalmazáshoz tartozó webes szerepkör konfigurációját mutatja be. A webhely a 80-es porton alapértelmezett belépési helyként van konfigurálva, és a webalkalmazások úgy vannak konfigurálva, hogy a "mail.mysite.cloudapp.net" nevű másik állomásfejléc-fejléctől fogadják a kérelmeket.

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Szerepkör konfigurációjának módosítása
Ha az Azure-ban fut, akkor a szolgáltatás offline állapotba helyezése nélkül frissítheti a felhőalapú szolgáltatás konfigurációját. A konfigurációs adatok módosításához feltölthet egy új konfigurációs fájlt, vagy szerkesztheti a konfigurációs fájlt, és alkalmazhatja azt a futó szolgáltatásra. Egy szolgáltatás konfigurációjában a következő módosítások hajthatók végre:

* **Konfigurációs beállítások értékének módosítása**  
  A konfigurációs beállítások megváltozásakor a szerepkör-példány dönthet úgy, hogy a példány online állapotba kerül, vagy a példány újrahasznosítására, valamint a példány offline állapotba állítására alkalmazza a módosítást.
* **A szerepkör-példányok szolgáltatási topológiájának módosítása**  
  A topológia módosításai nem érintik a futó példányokat, kivéve, ha egy példányt eltávolítanak. A többi példányt általában nem kell újrahasznosítani; lehetősége van azonban újrahasznosítani a szerepkör-példányokat a topológia változásakor.
* **A tanúsítvány ujjlenyomatának módosítása**  
  A tanúsítvány csak akkor frissíthető, ha a szerepkör-példány offline állapotban van. Ha egy tanúsítvány hozzáadva, törölve vagy módosítva lett, miközben egy szerepkör-példány online állapotú, az Azure szabályosan leállítja a példányt a tanúsítvány frissítéséhez, és online állapotba helyezi azt a módosítás befejeződése után.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>A konfigurációs változások a szolgáltatás futásidejű eseményeivel való kezelésére
Az [Azure runtime library](/previous-versions/azure/reference/mt419365(v=azure.100)) tartalmazza a [Microsoft. WindowsAzure. ServiceRuntime](/previous-versions/azure/reference/ee741722(v=azure.100)) névteret, amely osztályokat biztosít az Azure-környezettel való interakcióhoz a szerepkörből. A [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) osztály a következő, a konfiguráció módosítása előtt és után kiváltott eseményeket definiálja:

* **Esemény [módosítása](/previous-versions/azure/reference/ee758134(v=azure.100))**  
  Ez akkor fordul elő, ha a konfigurációs változást a szerepkör egy adott példányára alkalmazza, így ha szükséges, lehetősége van a szerepkör-példányok lefolytatására.
* **[Módosított](/previous-versions/azure/reference/ee758129(v=azure.100)) esemény**  
  Akkor következik be, amikor a konfiguráció módosítása a szerepkör egy adott példányára lett alkalmazva.

> [!NOTE]
> Mivel a tanúsítvány módosítása mindig offline állapotba helyezi a szerepkörök példányait, nem növelik a RoleEnvironment. Change vagy RoleEnvironment. changed eseményeket.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>Szervizcsomag. cspkg
> [!NOTE]
> Az üzembe helyezhető csomagok maximális mérete 600MB

Egy alkalmazás Azure-beli felhőalapú szolgáltatásként történő üzembe helyezéséhez először a megfelelő formátumban kell becsomagolni az alkalmazást. A **CSPack** parancssori eszközt (az [Azure SDK](https://azure.microsoft.com/downloads/)-val együtt) a Visual Studio alternatívájaként a csomagfájl létrehozásához használhatja.

A **CSPack** a Service definition fájl és a szolgáltatás konfigurációs fájljának tartalmát használja a csomag tartalmának meghatározásához. A **CSPack** létrehoz egy alkalmazáscsomag-fájlt (. cspkg), amelyet feltölt az Azure-ba a [Azure Portal](cloud-services-how-to-create-deploy-portal.md#create-and-deploy)használatával. Alapértelmezés szerint a csomag neve `[ServiceDefinitionFileName].cspkg` , de más nevet is megadhat a `/out` **CSPack** lehetőség használatával.

A **CSPack** a következő helyen található:  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (Windows rendszeren) az SDK-val telepített **Microsoft Azure parancssori** parancsikon futtatásával érhető el.  
> 
> Az CSPack.exe programot saját maga futtatva tekintheti meg az összes lehetséges kapcsolóról és parancsról szóló dokumentációt.
> 
> 

<p />

> [!TIP]
> Futtassa helyileg a Cloud Service-t a **Microsoft Azure számítási emulátorban**, használja a **/copyonly** kapcsolót. Ezzel a beállítással az alkalmazás bináris fájljait átmásolja egy olyan könyvtár-elrendezésbe, amelyről futtathatók a Compute Emulator.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Példa a Cloud Service csomagolására szolgáló parancsra
Az alábbi példa egy olyan alkalmazáscsomag létrehozását mutatja be, amely egy webes szerepkör információit tartalmazza. A parancs meghatározza a használni kívánt szolgáltatás-definíciós fájlt, a könyvtárat, ahol a bináris fájlok találhatók, valamint a csomagfájl neve.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Ha az alkalmazás webes szerepkört és feldolgozói szerepkört is tartalmaz, a rendszer a következő parancsot használja:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Ahol a változók a következőképpen vannak meghatározva:

| Változó | Érték |
| --- | --- |
| \[Könyvtárnév\] |Az Azure-projekt. csdef fájlját tartalmazó legfelső szintű projekt könyvtárában található alkönyvtár. |
| \[ServiceDefinition\] |A szolgáltatás definíciós fájljának neve. Alapértelmezés szerint a fájl neve ServiceDefinition. csdef. |
| \[OutputFileName\] |A létrehozott csomagfájl neve. Ez általában az alkalmazás nevére van beállítva. Ha nincs megadva fájlnév, az alkalmazáscsomag az \[ ApplicationName. cspkg néven jön létre \] . |
| \[RoleName\] |A szolgáltatás definíciós fájljában definiált szerepkör neve. |
| \[RoleBinariesDirectory] |A szerepkör bináris fájljainak helye. |
| \[VirtuálisElérésiÚt\] |A szolgáltatás definíciójának helyek szakaszában meghatározott virtuális elérési utak fizikai könyvtárai. |
| \[PhysicalPath\] |A szolgáltatás definíciójának hely csomópontjában meghatározott virtuális elérési utak tartalmának fizikai könyvtára. |
| \[RoleAssemblyName\] |A szerepkör bináris fájljának neve. |

## <a name="next-steps"></a>Következő lépések
Létrehozok egy Cloud Service-csomagot, és szeretnék...

* [Távoli asztal beállítása Cloud Service-példányhoz][remotedesktop]
* [Cloud Service-projekt üzembe helyezése][deploy]

Visual studiót használok, és szeretnék...

* [Új felhőalapú szolgáltatás létrehozása][vs_create]
* [Meglévő felhőalapú szolgáltatás újrakonfigurálása][vs_reconfigure]
* [Cloud Service-projekt üzembe helyezése][vs_deploy]
* [Távoli asztal beállítása Cloud Service-példányhoz][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: /visualstudio/azure/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio
[vs_reconfigure]: /visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service
[vs_create]: /visualstudio/azure/vs-azure-tools-azure-project-create