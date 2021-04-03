---
title: Azure Service Fabric-szolgáltatás futtatása gMSA-fiókkal
description: Megtudhatja, hogyan futtathat egy szolgáltatást csoportosan felügyelt szolgáltatásfiók (gMSA) használatával egy Service Fabric Windows önálló fürtön.
ms.topic: how-to
ms.date: 03/29/2018
ms.openlocfilehash: 9750042764306c5df7a391429cc6926704db05ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91838908"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Szolgáltatás futtatása csoportosan felügyelt szolgáltatásfiókként

Egy Windows Server önálló fürtön futtathatja a szolgáltatást egy *csoportosan felügyelt szolgáltatásfiók* (gMSA) használatával *futtató házirenddel.*  Alapértelmezés szerint Service Fabric alkalmazások a folyamat alatt futó fiók alatt futnak `Fabric.exe` . Az alkalmazások különböző fiókokban való futtatása, még egy megosztott környezetben is, biztonságosabbá teszi őket egymástól. GMSA használatával nincs jelszó vagy titkosított jelszó az alkalmazás jegyzékfájljában.  A szolgáltatásokat [Active Directory felhasználóként vagy csoportként](service-fabric-run-service-as-ad-user-or-group.md)is futtathatja.

Az alábbi példa bemutatja, hogyan hozhat létre egy *SVC-test $* nevű gMSA-fiókot, hogyan helyezheti üzembe a felügyelt szolgáltatásfiókot a fürtcsomópontok között, és hogyan konfigurálhatja a rendszerbiztonsági tag konfigurációját.

> [!NOTE]
> Ha önálló Service Fabric-fürttel rendelkező gMSA használ, a tartományon belül Active Directory kell lennie (nem Azure Active Directory (Azure AD)).

Előfeltételek:

- A tartománynak szüksége van egy KDS-gyökér kulcsra.
- A tartományban legalább egy Windows Server 2012 (vagy R2) tartományvezérlőnek kell lennie.

1. Active Directory tartományi rendszergazdának létre kell hoznia egy csoportosan felügyelt szolgáltatásfiókot a `New-ADServiceAccount` parancsmag használatával, és gondoskodnia kell arról, hogy a `PrincipalsAllowedToRetrieveManagedPassword` tartalmazza az összes Service Fabric fürtcsomópont. `AccountName`, `DnsHostName` és `ServicePrincipalName` egyedinek kell lennie.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. A Service Fabric fürtcsomópontokon (például `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$` ) telepítse és tesztelje a gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Konfigurálja az egyszerű felhasználónevet, és konfigurálja a `RunAsPolicy` hivatkozást a [felhasználóra](./service-fabric-cluster-fabric-settings.md#runas).
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE]
> Ha futtató házirendet alkalmaz egy szolgáltatásra, és a szolgáltatás jegyzékfájlja deklarálja a végponti erőforrásokat a HTTP protokollal, meg kell adnia egy **SecurityAccessPolicy**.  További információ: [biztonsági hozzáférési házirend társítása http-és HTTPS-végpontokhoz](service-fabric-assign-policy-to-endpoint.md).
>

A következő cikkek végigvezetik a következő lépéseken:

- [Az alkalmazás modelljének megismerése](service-fabric-application-model.md)
- [Erőforrások meghatározása a szolgáltatás jegyzékfájljában](service-fabric-service-manifest-resources.md)
- [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
