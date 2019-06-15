---
title: Biztonságos WCF-alapú kommunikációt az Azure Service Fabricben |} A Microsoft Docs
description: Útmutató a biztonságos kommunikáció a WCF-alapú reliable Services, Azure Service Fabric-fürtben futó.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: 26d34f0473dec5e0767041df400b84887a0d1778
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725584"
---
# <a name="secure-wcf-based-communications-for-a-service"></a>Biztonságos kommunikáció a WCF-alapú szolgáltatás
Biztonsági az egyik legfontosabb szempontja kommunikáció. A Reliable Services alkalmazási keretrendszer tartalmaz néhány előre létrehozott kommunikációs implementációt, eszközök, amelyek a biztonság növelése érdekében használhatja. Ez a cikk ismerteti hogyan szolgáltatás távelérésének lehetővé tétele használatakor a biztonság növelése érdekében.

Egy meglévő használjuk [példa](service-fabric-reliable-services-communication-wcf.md) , amely azt ismerteti, hogyan állítható be egy WCF-alapú kommunikációs verem reliable Services. Biztonságos egy szolgáltatás, amikor egy WCF-alapú kommunikációs verem használata érdekében kövesse az alábbi lépéseket:

1. A szolgáltatást, hogy kell biztonságossá tétele a WCF-kommunikáció figyelője (`WcfCommunicationListener`), amely hoz létre. Ehhez módosítsa a `CreateServiceReplicaListeners` metódust.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
2. Az ügyfél a `WcfCommunicationClient` osztály, amely az előzőleg létrehozott [példa](service-fabric-reliable-services-communication-wcf.md) változatlan marad. De felül kell bírálnia az `CreateClientAsync` metódusa `WcfCommunicationClientFactory`:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Használat `SecureWcfCommunicationClientFactory` hozhat létre egy WCF-kommunikáció ügyfél (`WcfCommunicationClient`). Az ügyfél segítségével szolgáltatás metódusokat hívhat meg.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

A következő lépésben, olvassa el a [webes API-hoz a Reliable Services OWIN](service-fabric-reliable-services-communication-webapi.md).
