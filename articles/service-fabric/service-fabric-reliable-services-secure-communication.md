---
title: 'Biztonságos szolgáltatás távelérési kommunikációja C-vel #'
description: Ismerje meg, hogyan védheti meg az Azure Service Fabric-fürtön futó C# megbízható szolgáltatásokat a szolgáltatás távelérés-alapú kommunikációjában.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ba68df53f1f21b9ff360772fe1a60c93c8df74d3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86252215"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Biztonságos szolgáltatás – távelérési kommunikáció C#-szolgáltatásban
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-secure-communication.md)
> * [Java Linuxon](service-fabric-reliable-services-secure-communication-java.md)
>
>

A biztonság a kommunikáció egyik legfontosabb aspektusa. A Reliable Services alkalmazás-keretrendszer néhány előre elkészített kommunikációs veremet és eszközt biztosít, amelyek segítségével javíthatja a biztonságot. Ez a cikk azt ismerteti, hogyan javíthatja a biztonságot, ha C#-szolgáltatásban használja a szolgáltatás távelérését. Egy meglévő [példára](service-fabric-reliable-services-communication-remoting.md) épül, amely elmagyarázza, hogyan állíthatja be a távoli eljáráshívás szolgáltatást a C# nyelven írt megbízható szolgáltatásokhoz. 

Az alábbi lépéseket követve biztonságossá teheti a szolgáltatást, ha a szolgáltatás távelérését C#-szolgáltatásokkal használja:

1. Hozzon létre egy felületet, `IHelloWorldStateful` amely meghatározza azokat a metódusokat, amelyek elérhetők lesznek a szolgáltatás távoli eljáráshívási hívásához. A szolgáltatás fogja használni `FabricTransportServiceRemotingListener` , amely deklarálva van a `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` névtérben. Ez egy olyan `ICommunicationListener` implementáció, amely távelérési funkciókat biztosít.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Figyelő-beállítások és biztonsági hitelesítő adatok hozzáadása.

    Győződjön meg arról, hogy a szolgáltatás kommunikációjának biztonságossá tételéhez használni kívánt tanúsítvány telepítve van a fürt összes csomópontján. 
    
    > [!NOTE]
    > Linux-csomópontokon a tanúsítványnak PEM formátumú fájlként kell lennie a */var/lib/sfcerts* könyvtárban. További információért lásd: [X. 509 tanúsítványok helye és formátuma Linux-csomópontokon](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Kétféle módon biztosíthat figyelő-beállításokat és biztonsági hitelesítő adatokat:

   1. Adja meg azokat közvetlenül a szolgáltatás kódjában:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Adja meg őket egy [konfigurációs csomag](service-fabric-application-and-service-manifests.md)használatával:

       Adjon hozzá egy nevesített `TransportSettings` szakaszt a settings.xml fájlban.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       Ebben az esetben a `CreateServiceReplicaListeners` metódus a következőhöz hasonlóan fog kinézni:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        Ha hozzáad egy `TransportSettings` szakaszt a settings.xml fájlban, akkor a `FabricTransportRemotingListenerSettings` rendszer alapértelmezés szerint betölti a szakasz összes beállítását.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        Ebben az esetben a `CreateServiceReplicaListeners` metódus a következőhöz hasonlóan fog kinézni:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. Ha metódusokat hív meg egy biztonságos szolgáltatáson a távelérési verem használatával, nem az `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` osztályt használja a szolgáltatás-proxy létrehozásához, használja a parancsot `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` . Beadás `FabricTransportRemotingSettings` , amely tartalmazza `SecurityCredentials` .

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Ha az ügyfél kódja egy szolgáltatás részeként fut, akkor `FabricTransportRemotingSettings` a settings.xml fájlból tölthető be. Hozzon létre egy olyan HelloWorldClientTransportSettings szakaszt, amely hasonló a szolgáltatás kódjához, ahogy az a korábban látható. Hajtsa végre a következő módosításokat az ügyfél kódjában:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Ha az ügyfél nem a szolgáltatás részeként fut, létrehozhat egy client_name.settings.xml fájlt ugyanazon a helyen, ahol a client_name.exe. Ezután hozzon létre egy TransportSettings szakaszt a fájlban.

    A szolgáltatáshoz hasonlóan, ha az `TransportSettings` ügyfél settings.xml/client_name.settings.xml szakaszát adja hozzá, a rendszer `FabricTransportRemotingSettings` alapértelmezés szerint betölti a szakasz összes beállítását.

    Ebben az esetben a korábbi kód még egyszerűbbé válik:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


A következő lépésként olvassa el a [webes API-t a OWIN Reliable Services-ben](./service-fabric-reliable-services-communication-aspnetcore.md)című témakörben.
