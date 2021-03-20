---
title: Tanúsítványok importálása tárolóba
description: Ismerkedjen meg a tanúsítványfájl Service Fabric Container Service-be történő importálásával.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 219882a3f7f6db665f1ec311098ef53464773b71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92313683"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Tanúsítványfájl importálása Service Fabricon futó tárolóba

> [!NOTE]
> Az Azure-on futó Service Fabric-fürtök esetében ajánlott [Service Fabric alkalmazás által felügyelt identitást](./concepts-managed-identity.md) használni, hogy kiépítse az alkalmazás tanúsítványait egy tárolóból. A felügyelt identitás biztosítja a titkok és a tanúsítványok elkülönítését a szolgáltatási szinten, és lehetővé teszi az alkalmazás-tanúsítvány kiépítését az alkalmazás munkafolyamatának részeként, nem pedig az infrastruktúra munkafolyamatát. A CertificateRef mechanizmus egy későbbi kiadásban elavulttá válik.

Egy tanúsítvány megadásával biztonságossá teheti a tároló szolgáltatásokat. A Service Fabric a tárolón belüli szolgáltatások számára biztosít olyan tanúsítványt, amely a Windows vagy Linux rendszerű fürt csomópontjain telepített tanúsítványokhoz fér hozzá (5,7-es vagy újabb verzió). A tanúsítványt a fürt összes csomópontján lévő LocalMachine területen lévő tanúsítványtárolóba kell telepíteni. A tanúsítványnak megfelelő titkos kulcsnak elérhetőnek, elérhetőnek és Windows-exportálható kell lennie. A tanúsítvány információit az alkalmazás jegyzékfájljában, a címke alatt, a `ContainerHostPolicies` következő kódrészlet mutatja:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Windows-fürtök esetén az alkalmazás indításakor a futtatókörnyezet minden hivatkozott tanúsítványt és a hozzá tartozó titkos kulcsot exportál egy PFX-fájlba, amelyet véletlenszerűen generált jelszóval kell védeni. A PFX-és jelszavas fájlok a tárolón belül elérhetők a következő környezeti változók használatával: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Linux-fürtök esetében a tanúsítványok (PEM) át lesznek másolva a X509StoreName által megadott tárolóból a tárolóba. A Linuxon a megfelelő környezeti változók a következők:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Vegye figyelembe, hogy mind a, mind a `PEM` `PrivateKey` fájl tartalmazza a tanúsítványt és a titkosítatlan titkos kulcsot.

Ha már rendelkezik a tanúsítványokkal a szükséges űrlapon, és a tárolón belül szeretné elérni azt, létrehozhat egy adatcsomagot az alkalmazáscsomag belül, és a következőket adhatja meg az alkalmazási jegyzékfájlban:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

A tároló szolgáltatás vagy folyamat feladata a tanúsítványfájl tárolóba való importálása. A tanúsítvány importálásához parancsfájlokat használhat, `setupentrypoint.sh` vagy egyéni kódot futtathat a tároló folyamatán belül. Az alábbi mintakód a PFX-fájl importálásához használható C# nyelven:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Ez a PFX-tanúsítvány használható az alkalmazás vagy szolgáltatás hitelesítésére vagy más szolgáltatásokkal való biztonságos kommunikációra. Alapértelmezés szerint a fájlok csak a rendszer ACLed. A szolgáltatás által igénybe veheti az ACL-t más fiókokba.

Következő lépésként olvassa el a következő cikkeket:

* [Windows-tároló üzembe helyezése Service Fabric Windows Server 2016 rendszeren](service-fabric-get-started-containers.md)
* [Docker-tároló üzembe helyezése Linuxon Service Fabric](service-fabric-get-started-containers-linux.md)