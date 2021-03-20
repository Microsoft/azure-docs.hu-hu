---
title: Hozzáférési szabályzatok társítása szolgáltatási végpontokhoz
description: Megtudhatja, hogyan rendelhet biztonsági hozzáférési házirendeket a Service Fabric szolgáltatásban lévő HTTP-vagy HTTPS-végpontokhoz.
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: c7d30e85848f045b5724bb8bdc6e5c810102c044
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75614655"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Biztonsági hozzáférési házirend kiosztása HTTP-és HTTPS-végpontokhoz
Ha a futtatási házirendet alkalmazza, és a szolgáltatás jegyzékfájlja deklarálja a HTTP-végpont erőforrásait, meg kell adnia egy **SecurityAccessPolicy**.  A **SecurityAccessPolicy** biztosítja, hogy az ezekhez a végpontokhoz lefoglalt portok megfelelően legyenek korlátozva a szolgáltatás által futtatott felhasználói fiókra. Ellenkező esetben a **http.sys** nem fér hozzá a szolgáltatáshoz, és az ügyféltől érkező hívásokkal kapcsolatos hibákhoz juthat. A következő példa egy **végpontneve** nevű végpontra alkalmazza a Customer1-fiókot, amely teljes körű hozzáférési jogosultságokat biztosít.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

HTTPS-végpont esetén az ügyfélnek visszaadott tanúsítvány nevét is meg kell adni. A tanúsítványt a **EndpointBindingPolicy** használatával hivatkozhat.  A tanúsítvány az alkalmazás jegyzékfájljának **tanúsítványok** szakaszában van definiálva.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> HTTPS használata esetén ne használja ugyanazt a portot és tanúsítványt az azonos csomópontra telepített különböző szolgáltatási példányokhoz (az alkalmazástól függetlenül). Ha két különböző szolgáltatást frissít a különböző alkalmazás-példányok ugyanazon portjával, a frissítés sikertelen lesz. További információ: [több alkalmazás frissítése HTTPS-végpontokkal ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
A következő lépésekhez olvassa el a következő cikkeket:
* [Az alkalmazás modelljének megismerése](service-fabric-application-model.md)
* [Erőforrások meghatározása a szolgáltatás jegyzékfájljában](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
