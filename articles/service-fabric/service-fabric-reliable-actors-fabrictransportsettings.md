---
title: FabricTransport beállításainak módosítása
description: Ismerje meg, hogyan konfigurálhatja az Azure Service Fabric Actors kommunikációs beállításait a különböző Actor-konfigurációkhoz.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.custom: devx-track-csharp
ms.openlocfilehash: e3c20d86bd29d60eca328a44ab5d5d600bbf4da4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010936"
---
# <a name="configure-fabrictransport-settings-for-reliable-actors"></a>Reliable Actors FabricTransport beállításainak konfigurálása

Itt láthatók a konfigurálható beállítások:
- C#: [FabricTransportRemotingSettings](/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)
- Java: [FabricTransportRemotingSettings](/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)

A FabricTransport alapértelmezett konfigurációját a következő módokon módosíthatja.

## <a name="assembly-attribute"></a>Szerelvény attribútuma

A [FabricTransportActorRemotingProvider](/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute) attribútumot az Actors ügyfél és a Actors szolgáltatás szerelvényei esetében kell alkalmazni.

Az alábbi példa bemutatja, hogyan módosíthatja a FabricTransport OperationTimeout-beállítások alapértelmezett értékét:

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

   A második példa a FabricTransport MaxMessageSize és a OperationTimeoutInSeconds alapértelmezett értékeit módosítja.

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
   ```

## <a name="config-package"></a>Konfigurációs csomag

Az alapértelmezett konfiguráció módosításához használhat egy [konfigurációs csomagot](service-fabric-application-and-service-manifests.md) .

> [!IMPORTANT]
> Linux-csomópontokon a tanúsítványoknak PEM formátumúnak kell lenniük. Ha többet szeretne megtudni a Linux-tanúsítványok kereséséről és konfigurálásáról, tekintse meg [a tanúsítványok konfigurálása Linuxon](./service-fabric-configure-certificates-linux.md)című témakört. 
> 

### <a name="configure-fabrictransport-settings-for-the-actor-service"></a>A Actor szolgáltatás FabricTransport beállításainak konfigurálása

Adjon hozzá egy TransportSettings szakaszt a settings.xml fájlban.

Alapértelmezés szerint a színész kódja a " &lt; ActorName &gt; TransportSettings" SectionName keresi. Ha ez nem található, akkor a SectionName "TransportSettings"-ként ellenőrzi.

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

### <a name="configure-fabrictransport-settings-for-the-actor-client-assembly"></a>FabricTransport-beállítások konfigurálása a színészi ügyfél szerelvényéhez

Ha az ügyfél nem a szolgáltatás részeként fut, létrehozhat egy " &lt; Client exe Name &gt;.settings.xml" fájlt a Client. exe fájllal megegyező helyen. Ezután adjon hozzá egy TransportSettings szakaszt a fájlban. A SectionName "TransportSettings" értéknek kell lennie.

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
      <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```

* A FabricTransport beállításainak konfigurálása a biztonságos Actor szolgáltatáshoz/ügyfélhez másodlagos tanúsítvánnyal.
  A másodlagos tanúsítvány adatai hozzáadhatók a CertificateFindValuebySecondary paraméter hozzáadásával.
  Alább látható a figyelő TransportSettings példája.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
  <Parameter Name="CertificateFindValuebySecondary" Value="h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C,a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
   Az alábbi példa az ügyfél TransportSettings mutatja be.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
  <Parameter Name="CertificateFindValuebySecondary" Value="a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662,h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
  * A FabricTransport beállításainak konfigurálása a tulajdonosi szolgáltatás/ügyfél biztonságossá tételéhez a tulajdonos neve alapján.
    A felhasználónak meg kell adnia a Findtype – FindBySubjectName, CertificateIssuerThumbprints és CertificateRemoteCommonNames értékeket kell hozzáadnia.
    Alább látható a figyelő TransportSettings példája.

    ```xml
    <Section Name="TransportSettings">
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
    <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Alice" />
    <Parameter Name="CertificateIssuerThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
    <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Bob" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    </Section>
    ```
    Az alábbi példa az ügyfél TransportSettings mutatja be.

  ```xml
   <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
  <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Bob" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
