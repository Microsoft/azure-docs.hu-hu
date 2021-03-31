---
title: Skálázás több példányban – Azure Signaler szolgáltatás
description: Számos skálázási helyzetben a vásárlónak gyakran több példányt kell kiépítenie, és úgy kell konfigurálnia, hogy együtt használja őket a nagyméretű központi telepítés létrehozásához. A horizontális skálázás például több példány támogatását igényli.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: fd6ac8c4d4fc4c3fec4f549f8ef4f955e2b1c637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89439214"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>Hogyan méretezheti a Signaler szolgáltatást több példányon?
A legújabb Signaler Service SDK támogatja a Signaler szolgáltatás példányainak több végpontját. Ezzel a szolgáltatással méretezheti az egyidejű kapcsolatokat, vagy felhasználhatja a régiók közötti üzenetkezeléshez.

## <a name="for-aspnet-core"></a>ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hogyan lehet több végpontot felvenni a konfigurációból?

A config `Azure:SignalR:ConnectionString` vagy `Azure:SignalR:ConnectionString:` a Signal Service-kapcsolatok karakterlánca.

Ha a kulcs a-vel kezdődik `Azure:SignalR:ConnectionString:` , a formátumnak kell lennie, `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` ahol `Name` `EndpointType` az objektum tulajdonságai és a `ServiceEndpoint` kód elérhetők.

A következő parancsokkal adhat hozzá több példányos kapcsolatok karakterláncot `dotnet` :

```cmd
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hogyan lehet több végpontot felvenni a kódból?

A `ServicEndpoint` rendszer egy olyan osztályt vezet be, amely leírja az Azure signaler szolgáltatás végpontjának tulajdonságait.
Ha az Azure Signaler Service SDK-t használja a használatával, több példány-végpontot is beállíthat:
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>Az Endpoint router testreszabása

Alapértelmezés szerint az SDK a [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) használja a végpontok felvételéhez.

#### <a name="default-behavior"></a>Alapértelmezett viselkedés 
1. Ügyfél-kérelem útválasztása

    Az ügyfél `/negotiate` és az alkalmazás-kiszolgáló között. Az SDK alapértelmezés szerint **véletlenszerűen kiválaszt** egy végpontot az elérhető szolgáltatási végpontok készletéről.

2. Kiszolgálói üzenet útválasztása

    Ha * üzenet küldése egy adott * * kapcsolat * * * felé, és a célként megadott kapcsolat az aktuális kiszolgálóra van irányítva, az üzenet közvetlenül erre a csatlakoztatott végpontra kerül. Ellenkező esetben az üzenetek az összes Azure Signaler-végpontra továbbítva lesznek.

#### <a name="customize-routing-algorithm"></a>Útválasztási algoritmus testreszabása
Saját útválasztót is létrehozhat, ha speciális ismeretekkel rendelkezik annak azonosításához, hogy mely végpontoknak kell megjelenniük.

Az alábbi példa egy egyéni útválasztót mutat be, amikor a-től kezdődő csoportok `east-` mindig a nevű végpontra lépnek `east` :

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

Egy másik példa alább, amely felülbírálja az alapértelmezett egyeztetési viselkedést, a végpontok kiválasztásához attól függ, hogy hol található az App Server.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Ne felejtse el regisztrálni az útválasztót a DI Container használatával:

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>Hogyan lehet több végpontot felvenni a konfigurációból?

A config `Azure:SignalR:ConnectionString` vagy `Azure:SignalR:ConnectionString:` a Signal Service-kapcsolatok karakterlánca.

Ha a kulcs a-vel kezdődik `Azure:SignalR:ConnectionString:` , a formátumnak kell lennie, `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` ahol `Name` `EndpointType` az objektum tulajdonságai és a `ServiceEndpoint` kód elérhetők.

Több példányra vonatkozó kapcsolódási karakterláncot is hozzáadhat a következőhöz `web.config` :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>Hogyan lehet több végpontot felvenni a kódból?

A `ServicEndpoint` rendszer egy olyan osztályt vezet be, amely leírja az Azure signaler szolgáltatás végpontjának tulajdonságait.
Ha az Azure Signaler Service SDK-t használja a használatával, több példány-végpontot is beállíthat:

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>Az útválasztó testreszabása

Az egyetlen különbség a ASP.NET-jelző és a ASP.NET Core-jelző között a http-környezet típusa a következőhöz: `GetNegotiateEndpoint` . A ASP.NET-jelző esetében [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) típusú.

Alább látható a ASP.NET-jelzőhöz tartozó egyéni egyeztetési példa:

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

Ne felejtse el regisztrálni az útválasztót a DI Container használatával:

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>Konfigurálás régiók közötti forgatókönyvekben

Az `ServiceEndpoint` objektum `EndpointType` tulajdonsága értéke `primary` vagy `secondary` .

`primary` a végpontok előnyben részesített végpontok az ügyfél forgalmának fogadására, és megbízhatóbb hálózati kapcsolatnak tekintendők. a `secondary` végpontoknak kevesebb megbízható hálózati kapcsolattal kell rendelkezniük, és csak azért használják a kiszolgálót az ügyfelek forgalmára, mint például a szórási üzenetek, nem pedig az ügyfél és a kiszolgáló közötti adatforgalom.

Régiók közötti esetekben a hálózat instabil lehet. Az *USA keleti* régiójában található egyik app Server esetében az UGYANABBAN az USA-beli *keleti* régióban található Signal Service-végpont konfigurálható `primary` más régiókban, illetve végpontként is `secondary` . Ebben a konfigurációban a más régiókban lévő szolgáltatási végpontok **fogadhatnak** e-maileket erről az USA-beli *East* app Server-kiszolgálóról, de nem lesznek átirányítva **régiók közötti** ügyfelek az adott app Serverhez. Az architektúra az alábbi ábrán látható:

![Földrajzilag átnyúló infra](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

Ha az ügyfél az `/negotiate` alapértelmezett útválasztóval próbálkozik az App Serverrel, az SDK **véletlenszerűen kiválaszt** egy végpontot az elérhető `primary` végpontok készletéről. Ha az elsődleges végpont nem érhető el, az SDK **véletlenszerűen kiválasztja** az összes elérhető `secondary` végpontot. A végpont **elérhetőként** van megjelölve, ha a kiszolgáló és a szolgáltatási végpont közötti kapcsolat él.

Régiók közötti forgatókönyv esetén, amikor `/negotiate` az ügyfél az *USA keleti* régiójában üzemeltetett app Serverrel próbálkozik, alapértelmezés szerint mindig az `primary` ugyanabban a régióban található végpontot adja vissza. Ha az összes *kelet-amerikai* végpont nem érhető el, a rendszer átirányítja az ügyfelet más régiókban lévő végpontokra. Az alábbi feladatátvételi szakasz részletesen ismerteti a forgatókönyvet.

![Normál egyeztetés](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>Feladatátvétel

Ha `primary` az összes végpont nem érhető el, az ügyfél `/negotiate` az elérhető végpontokból származó csákányt `secondary` . Ez a feladatátvételi mechanizmus megköveteli, hogy az egyes végpontok végpontként legyenek kiszolgálva `primary` legalább egy alkalmazás-kiszolgálóként.

![Feladatátvétel](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>Következő lépések

Ebből az útmutatóból megtudhatta, hogyan konfigurálhat több példányt ugyanabban az alkalmazásban a méretezés, a horizontális skálázás és a régiók közötti helyzetekhez.

Több végpont is támogatott magas rendelkezésre állású és vész-helyreállítási helyzetekben.

> [!div class="nextstepaction"]
> [A riasztási szolgáltatás beállítása a vész-helyreállításhoz és a magas rendelkezésre álláshoz](./signalr-concept-disaster-recovery.md)
