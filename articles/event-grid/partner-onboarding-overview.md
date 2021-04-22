---
title: Partnerekre való beiratás áttekintése (Azure Event Grid)
description: Áttekintést nyújt arról, hogyan lehet Event Grid partnerként.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 40d0afe0aaeb40412948eb304a36a3627566551b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869688"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>Partnerekre való beiratás áttekintése (Azure Event Grid)

Ez a cikk azt ismerteti, hogyan használhatók Azure Event Grid partnererőforrások, és hogyan válhatnak nyilvánosan elérhető partnertémakör-típussá.

Az események partnerként való közzétételéhez társított Event Grid erőforrástípusok használatának megkezdéséhez nincs szükség különleges Event Grid. Sőt, ma már saját Azure-előfizetésében is közzétehet eseményeket, és tesztelheti az erőforrásmodellt, ha partnerré válást fontolgat.

> [!NOTE]
> Az Event Grid-partnerként való Azure Portal-partnerként való Event Grid [(Azure Portal)](onboard-partner.md)használatával való Event Grid lépésenként. 

## <a name="how-partner-events-work"></a>A partneresemények működése
A Partneresemények funkció az Azure-erőforrások Event Grid például az Azure Storage és az Azure IoT Hub eseményeinek közzétételéhez már meglévő architektúrát használja, és ezeket az eszközöket bárki számára nyilvánosan elérhetővé teszi. Ezeknek az eszközöknek a használata alapértelmezés szerint csak az Azure-előfizetés számára privát. Ahhoz, hogy az események nyilvánosan elérhetők legyen, töltse ki az űrlapot, és lépjen [kapcsolatba a Event Grid csapatával.](mailto:gridpartner@microsoft.com)

A Partneresemények funkcióval eseményeket tehet közzé Azure Event Grid több-bérlős használathoz.

## <a name="onboarding-and-event-publishing-overview"></a>A be- és esemény-közzététel áttekintése

### <a name="partner-flow"></a>Partnerfolyam

1. Ha még nem rendelkezik Azure-bérlővel, hozzon létre egyet.
1. Az Azure CLI használatával hozzon létre egy új `partnerRegistration` Event Grid. Ez az erőforrás olyan információkat tartalmaz, mint például a megjelenítendő név, a leírás, a telepítési URI és így tovább.

    ![Partnertémakör létrehozása](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Hozzon létre egy vagy több partnernévteret minden régióban, ahol közzé szeretné tenni az eseményeket. A Event Grid szolgáltatás ki hoz egy közzétételi végpontot (például `https://contoso.westus-1.eventgrid.azure.net/api/events` ) és hozzáférési kulcsokat.

    ![Partnernévtér létrehozása](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Lehetővé kell adnia, hogy az ügyfelek regisztrálni tudják a rendszert, hogy partnertémakört keresnek.
1. Lépjen kapcsolatba az Event Grid csapatával, és tudatossa velük, hogy szeretné, ha a partnertémakör-típus nyilvánossá váljon.

### <a name="customer-flow"></a>Ügyfélfolyam

1. Az ügyfél felkeresi a Azure Portal, hogy feljegyze az Azure-előfizetés azonosítóját és erőforráscsoportját, amelyekben létre szeretné hozatni a partnertémakört.
1. Az ügyfél az Ön rendszerében kér partnertémát. Válaszul létrehoz egy eseményalagutat a partnernévtérhez.
1. Event Grid létrehoz egy **Függőben** lévő partnertémakört az ügyfél Azure-előfizetésében és -erőforráscsoportban.

    ![Eseménycsatorna létrehozása](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Az ügyfél a partnertémakört a következő Azure Portal. Az események a szolgáltatásból az ügyfél Azure-előfizetésére áramlhatnak.

    ![Partnertémaktéma aktiválása](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Erőforrásmodell
Az alábbi erőforrásmodell a partneresemények esetében szükséges.

### <a name="partner-registrations"></a>Partnerregisztrációk
* Erőforrás: `partnerRegistrations`
* A partnerek használják
* Leírás: A szolgáltatott szoftver (SaaS) partner globális metaadatait rögzíti (például név, megjelenített név, leírás, beállítási URI).
    
    A partnerregisztrációk létrehozása vagy frissítése önkiszolgáló művelet a partnerek számára. Ez az önkiszolgáló képesség lehetővé teszi a partnerek számára a teljes folyamat felépítését és tesztelését.
    
    Az ügyfelek csak a Microsoft által jóváhagyott partnerregisztrációkat deríthetik fel.
* Hatókör: A partner Azure-előfizetésében jön létre. A metaadatok a nyilvános adatokat követően láthatók az ügyfelek számára.

### <a name="partner-namespaces"></a>Partnernévterek
* Erőforrás: `partnerNamespaces`
* A partnerek használják
* Leírás: Regionális erőforrást biztosít az ügyfélesemények közzétételéhez. Minden partnernévtér rendelkezik közzétételi végponttal és hitelesítési kulcsokkal. A névtér azt is bemutatja, hogyan kér a partner egy partnertémát egy adott ügyfélhez, és listázza az aktív ügyfeleket.
* Hatókör: A partner előfizetésében van.

### <a name="event-channel"></a>Eseménycsatorna
* Erőforrás: `partnerNamespaces/eventChannels`
* A partnerek használják
* Leírás: Az eseménycsatornák az ügyfél partnertémakörének tükrözései. Ha létrehoz egy eseménycsatornát, és megadja az ügyfél Azure-előfizetését és -erőforráscsoportját a metaadatok között, jelez a Event Grid, hogy hozzon létre egy partnertémakört az ügyfél számára. Event Grid ad ki Azure Resource Manager hívást egy megfelelő partnertémakör létrehozásához az ügyfél előfizetésében. A partnertémakör függő állapotban jön létre. Minden eseménycsatorna és partnertémakör között egy-az-egyhez kapcsolat található.
* Hatókör: A partner előfizetésében van.

### <a name="partner-topics"></a>Partnertémakörök
* Erőforrás: `partnerTopics`
* Felhasználók által használt: Ügyfelek
* Leírás: A partnertémaktémák hasonlóak a témakörben található egyéni és rendszer-Event Grid. Minden partnertémakör egy adott forráshoz (például ) és egy adott partnertémakör-típushoz `Contoso:myaccount` (például Contoso) van társítva. Az ügyfelek esemény-előfizetéseket hoznak létre a partnertémakörben az események különböző eseménykezelőkhöz való útválasztásához.

    Az ügyfelek nem hozhatják létre közvetlenül ezt az erőforrást. Partnertémakört csak egy eseménycsatornát létrehozására használt partnerművelettel hozhat létre.
* Hatókör: Az ügyfél előfizetésében van.

### <a name="partner-topic-types"></a>Partnertémakör-típusok
* Erőforrás: `partnerTopicTypes`
* Felhasználók által használt: Ügyfelek
* Leírás: A partnertémakör-típusok bérlői szintű erőforrástípusok, amelyek lehetővé teszik az ügyfelek számára a jóváhagyott partnertémakör-típusok listájának felderítését. Az URL-cím a következő: https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Hatókör: Globális

## <a name="publish-events-to-event-grid"></a>Események közzététele Event Grid
Amikor létrehoz egy partnernévteret egy Azure-régióban, egy regionális végpontot és a hozzá tartozó hitelesítési kulcsokat kap. Események kötegét teszi közzé ebben a végpontban a névtér összes ügyfélesemény-csatornájára. Az esemény forrásmezője alapján a Azure Event Grid leképezi az egyes eseményeket a megfelelő partnertémakémákra.

### <a name="event-schema-cloudevents-v10"></a>Eseményséma: CloudEvents v1.0
Események közzététele Azure Event Grid CloudEvents 1.0 sémával. Event Grid a strukturált és a kötegelt módot is támogatja. A CloudEvents 1.0 az egyetlen támogatott eseményséma a partnernévterek számára.

### <a name="example-flow"></a>Példa folyamat

1.  A közzétételi szolgáltatás http POST-kérést tesz a `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` címre.
1.  A kérelembe foglalhat egy aeg-sas-key nevű fejlécértéket, amely egy hitelesítési kulcsot tartalmaz. Ez a kulcs a partnernévtér létrehozása során lesz kiépítve. Érvényes fejlécérték például az aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnnm5OFg==.
1.  Állítsa a Content-Type fejlécet az "application/cloudevents-batch+json; charset=UTF-8a".
1.  Futtason egy HTTP POST-lekérdezést a közzétételi URL-címre az adott régiónak megfelelő események kötegével. Például:

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

A partner névtér-végpontjának való közzététel után választ kap. A válasz egy szabványos HTTP-válaszkód. Néhány gyakori válasz:

| Eredmény                             | Reagálás              |
|------------------------------------|-----------------------|
| Siker                            | 200 OK                |
| Az eseményadatok formátuma helytelen    | 400 Hibás kérés       |
| Érvénytelen hozzáférési kulcs                 | 401 Nem engedélyezett      |
| Helytelen végpont                 | 404 Nem található         |
| A tömb vagy esemény túllépi a méretkorlátokat | 413 Túl nagy hasznos adat |

## <a name="references"></a>Hivatkozások

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM-sablon](/azure/templates/microsoft.eventgrid/allversions)
  * [ARM-sablonséma](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST API-k](/azure/templates/microsoft.eventgrid/2020-04-01-preview/partnernamespaces)
  * [CLI-bővítmény](/cli/azure/)

### <a name="sdks"></a>SDK-k
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [Js](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Ugrás](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Következő lépések
- [Partnertémaktémák áttekintése](partner-events-overview.md)
- [Partnertémaktémák – beiratás űrlap](https://aka.ms/gridpartnerform)
- [Auth0-partner témaköre](auth0-overview.md)
- [Az Auth0-partner témakör használata](auth0-how-to.md)
