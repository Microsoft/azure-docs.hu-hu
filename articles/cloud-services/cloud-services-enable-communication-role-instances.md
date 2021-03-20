---
title: Kommunikáció a Cloud Services (klasszikus) szerepkörökhöz | Microsoft Docs
description: A Cloud Servicesban lévő szerepkör-példányok rendelkezhetnek olyan végpontokkal (http, HTTPS, TCP, UDP), amelyek a külső vagy más szerepkör-példányok közötti kommunikációhoz vannak meghatározva.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 82aa1579a1f7feb36732153341e1eacf266a7218
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743032"
---
# <a name="enable-communication-for-role-instances-in-azure-cloud-services-classic"></a>Szerepkör-példányok kommunikációjának engedélyezése az Azure Cloud Services (klasszikus)

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A felhőalapú szolgáltatás szerepkörei belső és külső kapcsolatokon keresztül kommunikálnak. A külső kapcsolatokat **bemeneti végpontoknak** nevezzük, a belső kapcsolatokat pedig **belső végpontoknak** nevezzük. Ez a témakör azt ismerteti, hogyan módosíthatja a [szolgáltatás definícióját](cloud-services-model-and-package.md#csdef) végpontok létrehozásához.

## <a name="input-endpoint"></a>Bemeneti végpont
A bemeneti végpontot akkor kell használni, ha a portot kívülre kívánja tenni. Adja meg a protokoll típusát és a végponthoz tartozó portot, amely ezután a végpont külső és belső portjaira is érvényes. Ha szeretné, megadhat egy másik belső portot a végponthoz a [localPort](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) attribútummal.

A bemeneti végpont a következő protokollokat használhatja: **http, HTTPS, TCP, UDP**.

Bemeneti végpont létrehozásához adja hozzá a **InputEndpoint** gyermek elemet a webes vagy feldolgozói szerepkör **végpontok** eleméhez.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Példány bemeneti végpontja
A példány bemeneti végpontja hasonló a bemeneti végpontokhoz, de lehetővé teszi, hogy az egyes szerepkör-példányok számára a port továbbítása szolgáltatással leképezse a megadott nyilvános portokat a terheléselosztó használatával. Megadhat egyetlen nyilvános portot, vagy a portok egy tartományát is.

A példány bemeneti végpontja csak a **TCP** vagy az **UDP** protokollt használhatja protokollként.

Egy példány bemeneti végpontjának létrehozásához adja hozzá a **InstanceInputEndpoint** gyermek elemet a webes vagy feldolgozói szerepkör **végpontok** eleméhez.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Belső végpont
A belső végpontok például a példányok közötti kommunikációhoz érhetők el. A port nem kötelező, és ha nincs megadva, a rendszer egy dinamikus portot rendel hozzá a végponthoz. Egy porttartomány is használható. Legfeljebb öt belső végpont adható meg.

A belső végpont a következő protokollokat használhatja: **http, TCP, UDP, any**.

Belső bemeneti végpont létrehozásához adja hozzá a **InternalEndpoint** gyermek elemet a webes vagy feldolgozói szerepkör **végpontok** eleméhez.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

A portok tartományát is használhatja.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8999" min="8995" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Feldolgozói szerepkörök és webes szerepkörök
A munkavégző és a webes szerepkörök használata esetén a végpontokkal kapcsolatban egyetlen kisebb különbség van. A webes szerepkörnek legalább egyetlen bemeneti végponttal kell rendelkeznie a **http** protokoll használatával.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>A .NET SDK használata végpont eléréséhez
Az Azure Managed Library olyan metódusokat biztosít a szerepkör-példányok számára, amelyek futásidőben kommunikálhatnak. A szerepkör-példányon belül futó programkódból lekérheti a többi szerepkör-példány és a végpontok létezéséről, valamint az aktuális szerepkör-példányra vonatkozó információkat.

> [!NOTE]
> Csak a felhőalapú szolgáltatásban futó szerepkör-példányokra vonatkozó információkat kérhet le, és amelyek legalább egy belső végpontot határoznak meg. Egy másik szolgáltatásban futó szerepkör-példányok adatait nem lehet beolvasni.
> 
> 

A [példányok](/previous-versions/azure/reference/ee741904(v=azure.100)) tulajdonságot a szerepkörök példányainak lekérésére használhatja. Először használja a [CurrentRoleInstance](/previous-versions/azure/reference/ee741907(v=azure.100)) az aktuális szerepkör-példányra mutató hivatkozás visszaküldéséhez, majd használja a [role](/previous-versions/azure/reference/ee741918(v=azure.100)) tulajdonságot a szerepkörre mutató hivatkozás visszaküldéséhez.

Ha a .NET SDK-val programozott módon csatlakozik a szerepkör-példányhoz, viszonylag könnyen elérheti a végpont adatait. Ha például egy adott szerepkör-környezethez már kapcsolódott, akkor egy adott végpont portját a következő kóddal szerezheti be:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

A **instances** tulajdonság **RoleInstance** objektumok gyűjteményét adja vissza. Ez a gyűjtemény mindig az aktuális példányt tartalmazza. Ha a szerepkör nem határoz meg belső végpontot, a gyűjtemény tartalmazza az aktuális példányt, de más példányokat sem. A gyűjteményben található szerepkör-példányok száma mindig 1 lesz abban az esetben, ha nincs megadva belső végpont a szerepkörhöz. Ha a szerepkör egy belső végpontot határoz meg, a példányok futásidőben felderíthetők, és a gyűjtemény példányainak száma a szolgáltatás konfigurációs fájljában a szerepkörhöz megadott számú példánynak felel meg.

> [!NOTE]
> Az Azure Managed Library nem biztosít más szerepkör-példányok állapotának meghatározására szolgáló módszert, de ha a szolgáltatásnak szüksége van erre a funkcióra, Ön is megvalósíthatja az ilyen állapot-értékeléseket. A [Azure Diagnostics](cloud-services-dotnet-diagnostics.md) használatával információkat szerezhet be a futó szerepkör példányairól.
> 
> 

Egy szerepkör-példány belső végpontjának portszámának meghatározásához a [`InstanceEndpoints`](/previous-versions/azure/reference/ee741917(v=azure.100)) tulajdonságot használhatja a végpontok nevét, valamint a hozzájuk tartozó IP-címeket és portokat tartalmazó szótár objektum visszaküldéséhez. A [`IPEndpoint`](/previous-versions/azure/reference/ee741919(v=azure.100)) tulajdonság egy megadott végpont IP-címét és portját adja vissza. A `PublicIPEndpoint` tulajdonság egy elosztott terhelésű végpont portját adja vissza. A tulajdonság IP-címének része `PublicIPEndpoint` nincs használatban.

Íme egy példa, amely megismétli a szerepkör-példányokat.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Íme egy példa arra a feldolgozói szerepkörre, amely a szolgáltatás definícióján keresztül teszi elérhetővé a végpontot, és megkezdi a kapcsolatok figyelését.

> [!WARNING]
> Ez a kód csak központilag telepített szolgáltatás esetén működik. Az Azure számítási emulátorban való futtatáskor a rendszer figyelmen kívül hagyja a közvetlen portok végpontját (**InstanceInputEndpoint** -elemeket) létrehozó szolgáltatás-konfigurációs elemeket.
> 
> 

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;

        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;

        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);

        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
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

## <a name="network-traffic-rules-to-control-role-communication"></a>Hálózati forgalmi szabályok a szerepkör-kommunikáció vezérléséhez
A belső végpontok definiálását követően hálózati forgalmi szabályokat adhat hozzá (a létrehozott végpontok alapján) annak vezérléséhez, hogy a szerepkör-példányok hogyan kommunikáljanak egymással. Az alábbi ábrán néhány gyakori forgatókönyv látható a szerepkör-kommunikáció szabályozásához:

![Hálózati forgalomra vonatkozó szabályok forgatókönyvei](./media/cloud-services-enable-communication-role-instances/scenarios.png "Hálózati forgalomra vonatkozó szabályok forgatókönyvei")

A következő kódrészlet az előző ábrán látható szerepkörökhöz tartozó szerepkör-definíciókat mutatja be. Minden szerepkör-definíció legalább egy belső végpontot definiál:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
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
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [!NOTE]
> A szerepkörök közötti kommunikáció korlátozása a rögzített és automatikusan hozzárendelt portok belső végpontjának használatával történhet.
> 
> 

Alapértelmezés szerint a belső végpontok meghatározása után a kommunikáció bármely szerepkörről a szerepkör belső végpontja számára korlátozás nélkül elvégezhető. A kommunikáció korlátozásához hozzá kell adnia egy **NetworkTrafficRules** elemet a **ServiceDefinition** elemhez a szolgáltatás definíciós fájljában.

### <a name="scenario-1"></a>1\. példa
Csak a **webrole1 webes** és a **WorkerRole1** közötti hálózati forgalom engedélyezése.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>2\. példa
Csak a **webrole1 webes** és a **WorkerRole1** , illetve a **WorkerRole2** közötti hálózati forgalmat engedélyezi.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>3\. példa
A csak a **webrole1 webes** és a **WorkerRole1** közötti hálózati forgalmat engedélyezi, és **WorkerRole1** a **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>4\. példa
A csak a **Webrole1 webes** **WorkerRole1**, a **webrole1 webes** **és a** **WorkerRole1** közötti hálózati forgalmat engedélyezi a **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

[Itt](/previous-versions/azure/reference/gg557551(v=azure.100))talál egy XML-séma-referenciát a fent használt elemekhez.

## <a name="next-steps"></a>Következő lépések
Tudjon meg többet a Cloud Service- [modellről](cloud-services-model-and-package.md).




