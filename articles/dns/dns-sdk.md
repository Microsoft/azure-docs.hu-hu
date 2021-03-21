---
title: DNS-zónák és-rekordhalmazok létrehozása a .NET SDK használatával
titleSuffix: Azure DNS
description: Ebben a képzési tervben a .NET SDK használatával kezdheti meg a DNS-zónák és-rekordhalmazok létrehozását a Azure DNSban.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: rohink
ms.custom: devx-track-csharp
ms.openlocfilehash: 8e116096afbd01af4914be49d5675881724d5069
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015060"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>DNS-zónák és-rekordhalmazok létrehozása a .NET SDK használatával

A DNS-zónák,-rekordhalmazok és-rekordok létrehozásához, törléséhez vagy frissítéséhez a .NET DNS felügyeleti könyvtár használatával automatizálhatja a műveleteket. A teljes Visual Studio-projekt itt érhető el [.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Egyszerű szolgáltatásnév fiók létrehozása

Az Azure-erőforrásokhoz való programozott hozzáférést általában a saját felhasználói hitelesítő adatai helyett egy dedikált fiókon keresztül biztosítjuk. Ezeket a dedikált fiókokat "egyszerű szolgáltatásnév" fiókoknak nevezzük. Az Azure DNS SDK-minta projekt használatához először létre kell hoznia egy egyszerű szolgáltatásnevet, és hozzá kell rendelnie a megfelelő engedélyeket.

1. Az [alábbi útmutatást](../active-directory/develop/howto-authenticate-service-principal-powershell.md) követve hozzon létre egy egyszerű szolgáltatásnevet (a Azure DNS SDK Sample Project jelszó alapú hitelesítést feltételez.)
2. Hozzon létre egy erőforráscsoportot (a[következőképpen](../azure-resource-manager/templates/deploy-portal.md)).
3. Az Azure RBAC használatával adja meg a "DNS-zóna közreműködői" jogosultságokat az erőforráscsoporthoz (a következő[módon](../role-based-access-control/role-assignments-portal.md):.)
4. Ha a Azure DNS SDK-minta projektet használja, szerkessze a "program. cs" fájlt a következőképpen:

   * Szúrja be a ( `tenantId` `clientId` más néven fiókazonosító) helyes értékeit `secret` (szolgáltatásnév), és az `subscriptionId` 1. lépésben használt módon.
   * Adja meg a 2. lépésben kiválasztott erőforráscsoport-nevet.
   * Adja meg a választott DNS-zóna nevét.

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet-csomagok és névtér-deklarációk

A Azure DNS .NET SDK használatához telepítenie kell a **Azure DNS Management Library** NuGet csomagot és az egyéb szükséges Azure-csomagokat.

1. Nyisson meg egy projektet vagy egy új projektet a **Visual Studióban**.
2. Válassza a **Tools** **>** **NuGet csomagkezelő** **>** **NuGet-csomagok kezelése a megoldáshoz...** lehetőséget.
3. Kattintson a **Tallózás** gombra, engedélyezze az **előzetes kiadás belefoglalása** jelölőnégyzetet, és írja be a **Microsoft. Azure. Management. DNS** kifejezést a keresőmezőbe.
4. Válassza ki a csomagot, majd a **telepítés** gombra kattintva adja hozzá a Visual Studio-projekthez.
5. A fenti folyamat megismétlésével telepítse a következő csomagokat is: **Microsoft. Rest. ClientRuntime. Azure. Authentication** és **Microsoft. Azure. Management. erőforráskezelő**.

## <a name="add-namespace-declarations"></a>Névtér-deklarációk hozzáadása

Adja hozzá a következő névtér-deklarációkat

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>A DNS-kezelő ügyfél inicializálása

A a `DnsManagementClient` DNS-zónák és-rekordhalmazok kezeléséhez szükséges metódusokat és tulajdonságokat tartalmazza.  A következő kód bejelentkezik a egyszerű szolgáltatásnév fiókjába, és létrehoz egy `DnsManagementClient` objektumot.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>DNS-zóna létrehozása vagy frissítése

DNS-zóna létrehozásához először a "Zone" objektum jön létre, amely tartalmazza a DNS-zóna paramétereit. Mivel a DNS-zónák nincsenek egy adott régióhoz kapcsolva, a hely a "Global" értékre van állítva. Ebben a példában egy [Azure Resource Manager "tag"](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) is hozzá van adva a zónához.

Ha a zónát ténylegesen szeretné létrehozni vagy frissíteni Azure DNSban, a rendszer a zóna paramétereit tartalmazó zónát adja át a `DnsManagementClient.Zones.CreateOrUpdateAsyc` metódusnak.

> [!NOTE]
> A DnsManagementClient három működési módot támogat: szinkron ("CreateOrUpdate"), aszinkron ("CreateOrUpdateAsync"), vagy aszinkron módon a HTTP-válaszhoz való hozzáféréssel ("CreateOrUpdateWithHttpMessagesAsync").  Az alkalmazás igényeitől függően bármelyik mód közül választhat.

Azure DNS támogatja a [etagek](./dns-getstarted-powershell.md)nevű optimista egyidejűséget. Ebben a példában a "Ha-None-Match" fejlécnél a "*" értéket kell megadnia, amely azt jelzi, hogy az Azure DNS DNS-zónát hoz létre, ha még nem létezik ilyen.  A hívás sikertelen, ha a megadott nevű zóna már létezik a megadott erőforráscsoporthoz.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>DNS-rekordhalmazok és-rekordok létrehozása

A DNS-rekordok rekordhalmazként vannak kezelve. A rekordhalmaz a zónán belüli azonos nevű és bejegyzéstípusú rekordok halmaza.  A rekordazonosító neve a zóna nevéhez képest nem a teljesen minősített DNS-név.

Rekordhalmaz létrehozásához vagy frissítéséhez létre kell hozni és át kell adni egy "RecordSet" paraméter-objektumot `DnsManagementClient.RecordSets.CreateOrUpdateAsync` . A DNS-zónákhoz hasonlóan három működési mód van: szinkron ("CreateOrUpdate"), aszinkron ("CreateOrUpdateAsync") vagy aszinkron, a HTTP-válaszhoz való hozzáféréssel ("CreateOrUpdateWithHttpMessagesAsync").

A DNS-zónákhoz hasonlóan a rekordhalmazok műveletei támogatják az optimista párhuzamosságot is.  Ebben a példában, mivel nincs megadva a "If-Match" és a "If-None-Match" érték, a rendszer mindig létrehozza a rekordot.  Ez a hívás felülírja a DNS-zónában található azonos nevű és bejegyzéstípusú meglévő rekordot.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Zónák és rekordhalmazok beolvasása

Az `DnsManagementClient.Zones.Get` és `DnsManagementClient.RecordSets.Get` metódusok egyéni zónákat és rekordhalmazokat kérdeznek le. A rekordhalmazok a típusuk, a neveik, valamint a zóna és az erőforráscsoport alapján vannak azonosítva. A zónákat a nevük és az abban található erőforráscsoport azonosítja.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Meglévő rekordazonosító frissítése

Egy meglévő DNS-rekord frissítéséhez először le kell kérnie a készletet, majd frissítenie kell a rekord-készlet tartalmát, majd el kell küldenie a módosítást.  Ebben a példában a "ETAG" a "If-Match" paraméterben megadott beolvasott rekorddal van megadva. A hívás sikertelen, ha egy egyidejű művelet időközben módosította a rekordot.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Zónák és rekordhalmazok listázása

A zónák listázásához használja a *DnsManagementClient. Zones. list...* metódusokat, amelyek támogatják az adott erőforráscsoport összes zónáját vagy az adott Azure-előfizetés összes zónáját (erőforráscsoportok között). A rekordhalmazok listázásához használja az *DnsManagementClient. recordsets. list...* metódusokat, amelyek támogatják az adott zónában lévő összes rekordhalmaz listázását, vagy csak egy adott típusú rekordhalmazt.

Vegye figyelembe, hogy ha olyan zónákat és rekordhalmazokat ad meg, amelyeket az eredmények oldalszámozásba foglalhatnak.  Az alábbi példa azt szemlélteti, hogyan lehet megismételni az eredményeket az eredmények oldalain. (A "2" mesterségesen kisméretű mérete a lapozás kényszerítésére szolgál. a gyakorlatban ezt a paramétert ki kell hagyni, és az alapértelmezett oldalméret van használatban.)

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Következő lépések

Töltse le a [Azure DNS .net SDK-minta projektet](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), amely további példákat tartalmaz a Azure DNS .net SDK használatáról, beleértve a más DNS-bejegyzéstípusokra vonatkozó példákat is.