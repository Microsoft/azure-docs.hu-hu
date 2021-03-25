---
title: A partner bevezetésének áttekintése (Azure Event Grid)
description: Áttekintést nyújt arról, hogyan lehet Event Grid partnerként bevezetni.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: d16337eada829a817110abf7842f763972d0718f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109339"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>A partner bevezetésének áttekintése (Azure Event Grid)

Ez a cikk azt ismerteti, hogyan használható a Azure Event Grid partner erőforrásainak magánjellegű használata, és hogyan válhat nyilvánosan elérhető partneri témakör típusa.

Nincs szükség különleges engedélyre a közzétételi eseményekhez Event Grid-partnerként társított Event Grid-erőforrástípusok használatának megkezdéséhez. Valójában a mai napon közzéteheti az eseményeket a saját Azure-előfizetései számára, és tesztelheti az erőforrás-modellt, ha fontolóra veszi a partnert.

> [!NOTE]
> A Event Grid-partnernek a Azure Portal használatával történő bevezetésének részletes ismertetését a következő témakörben tekintheti meg: [Event Grid partner (Azure Portal)](onboard-partner.md). 

## <a name="how-partner-events-work"></a>A partnerek eseményeinek működése
A partner Events szolgáltatással megtekintheti a meglévő architektúrát, amelyet Event Grid már használ az Azure-erőforrások, például az Azure Storage és az Azure IoT Hub eseményeinek közzétételéhez, és nyilvánosan elérhetővé teszi ezeket az eszközöket mindenki számára. Ezek az eszközök alapértelmezés szerint csak a saját Azure-előfizetésére vonatkoznak. Az események nyilvánosan elérhetővé tételéhez töltse ki az űrlapot, és [lépjen kapcsolatba a Event Grid csapatával](mailto:gridpartner@microsoft.com).

A partner Events szolgáltatás lehetővé teszi események közzétételét Azure Event Grid a több-bérlős felhasználásra.

## <a name="onboarding-and-event-publishing-overview"></a>Bevezetési és esemény-közzététel áttekintése

### <a name="partner-flow"></a>Partneri folyamat

1. Hozzon létre egy Azure-bérlőt, ha még nem rendelkezik ilyennel.
1. Új Event Grid létrehozásához használja az Azure CLI-t `partnerRegistration` . Ez az erőforrás olyan adatokat tartalmaz, mint a megjelenítendő név, a leírás, a beállítási URI stb.

    ![Partneri témakör létrehozása](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Hozzon létre egy vagy több partneri névteret minden olyan régióban, ahol eseményeket szeretne közzétenni. A Event Grid szolgáltatás egy közzétételi végpontot (például `https://contoso.westus-1.eventgrid.azure.net/api/events` ) és a hozzáférési kulcsokat foglal le.

    ![Partneri névtér létrehozása](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Adja meg, hogy az ügyfelek hogyan regisztrálhatnak a rendszeren, hogy egy partneri témakört szeretnének használni.
1. Vegye fel a kapcsolatot a Event Grid csapatával, és tájékoztassa őket arról, hogy szeretné, hogy a partneri témakör típusa nyilvános legyen.

### <a name="customer-flow"></a>Felhasználói folyamat

1. Az ügyfél meglátogatja a Azure Portalt, hogy jegyezze fel az Azure-előfizetés AZONOSÍTÓját és az erőforráscsoportot, amelyre a partneri témakört létre kívánja hozni.
1. Az ügyfél a rendszeren keresztül kér partneri témakört. Válaszként hozzon létre egy esemény-alagutat a partneri névtérhez.
1. Event Grid létrehoz egy **függőben lévő** partneri témakört az ügyfél Azure-előfizetésében és erőforráscsoporthoz.

    ![Esemény-csatorna létrehozása](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Az ügyfél a Azure Portalon keresztül aktiválja a partner témakört. Az események mostantól a szolgáltatástól az ügyfél Azure-előfizetéséhez is tartozhatnak.

    ![Partneri témakör aktiválása](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Erőforrásmodell
A következő erőforrás-modell a partneri eseményekhez használható.

### <a name="partner-registrations"></a>Partneri regisztrációk
* Erőforrás `partnerRegistrations`
* Használja: partnerek
* Leírás: rögzíti a szolgáltatott szoftver (SaaS) globális metaadatait (például név, megjelenítendő név, leírás, beállítási URI).
    
    A partnerek regisztrációjának létrehozása vagy frissítése a partnereknek szóló önkiszolgáló művelet. Ez az önkiszolgáló képesség lehetővé teszi a partnerek számára a teljes körű folyamat kiépítését és tesztelését.
    
    Az ügyfelek csak a Microsoft által jóváhagyott partneri regisztrációkat vehetik igénybe.
* Hatókör: létrehozás a partner Azure-előfizetésében. A metaadatok láthatók az ügyfelek számára, miután nyilvánosságra került.

### <a name="partner-namespaces"></a>Partneri névterek
* Erőforrás `partnerNamespaces`
* Használja: partnerek
* Leírás: regionális erőforrást biztosít az ügyfelek eseményeinek közzétételéhez. Minden partner-névtér közzétételi végpontot és hitelesítési kulcsokat tartalmaz. A névtér azt is ismerteti, hogy a partner hogyan kér partneri témakört egy adott ügyfél számára, és felsorolja az aktív ügyfeleket.
* Hatókör: a partner előfizetésében él.

### <a name="event-channel"></a>Esemény-csatorna
* Erőforrás `partnerNamespaces/eventChannels`
* Használja: partnerek
* Leírás: az Event channels az ügyfél partneri témakörének tükrözése. Egy Event Channel létrehozásával és a metaadatokban az ügyfél Azure-előfizetésének és erőforráscsoportének megadásával Event Grid, hogy partneri témakört hozzon létre az ügyfél számára. Event Grid kiadja a Azure Resource Manager hívást egy kapcsolódó partneri témakör létrehozásához az ügyfél előfizetésében. A partneri témakör egy függőben lévő állapotban jön létre. Egy-az-egyhez kapcsolat áll fenn az egyes esemény-csatornák és a partneri témák között.
* Hatókör: a partner előfizetésében él.

### <a name="partner-topics"></a>Partnertémakörök
* Erőforrás `partnerTopics`
* Használja: ügyfelek
* Leírás: a partneri témakörök hasonlóak az egyéni témakörökhöz és a Event Grid rendszerbeli témaköreihez. Minden partneri témakör egy adott forráshoz (például `Contoso:myaccount` ) és egy adott partneri témakörhöz (például contoso) van társítva. Az ügyfelek a partneri témakörben esemény-előfizetéseket hozhatnak létre az események különböző eseménykezelők számára történő átirányításához.

    Az ügyfelek nem tudják közvetlenül létrehozni ezt az erőforrást. A partneri témakör létrehozásának egyetlen módja egy esemény-csatornát létrehozó partneri művelet.
* Hatókör: az ügyfél előfizetésében él.

### <a name="partner-topic-types"></a>Partneri témák típusai
* Erőforrás `partnerTopicTypes`
* Használja: ügyfelek
* Leírás: a partneri témák a bérlői szintű erőforrástípusok, amelyek lehetővé teszik, hogy az ügyfelek felfedezzék a jóváhagyott partneri témakörök listáját. Az URL-cím így néz ki: https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Hatókör: globális

## <a name="publish-events-to-event-grid"></a>Események közzététele Event Grid
Amikor létrehoz egy partneri névteret egy Azure-régióban, egy regionális végpontot és a hozzá tartozó hitelesítési kulcsokat kap. Események kötegeit teheti közzé ehhez a végponthoz az adott névtérben lévő összes ügyfél-esemény csatornája számára. Az esemény forrás mezője alapján Azure Event Grid az egyes eseményeket a kapcsolódó partneri témakörökkel együtt leképezi.

### <a name="event-schema-cloudevents-v10"></a>Event Schema: CloudEvents 1.0-s verzió
A CloudEvents 1,0 séma használatával teheti közzé az eseményeket a Azure Event Grid. Event Grid támogatja a strukturált módot és a kötegelt üzemmódot is. A CloudEvents 1,0 az egyetlen támogatott esemény sémája a partneri névterekhez.

### <a name="example-flow"></a>Példa folyamatra

1.  A közzétételi szolgáltatás HTTP-BEJEGYZÉST végez a következőhöz: `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .
1.  A kérelemben adjon meg egy AEG-sas-Key nevű fejlécet, amely a hitelesítés kulcsát tartalmazza. Ez a kulcs a partneri névtér létrehozásakor lett kiépítve. Egy érvényes fejléc-érték például az AEG-sas-Key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg = =.
1.  Állítsa be a Content-type fejlécet az "Application/cloudevents-batch + JSON;" értékre. charset = UTF-8a ".
1.  Futtasson egy HTTP POST lekérdezést a közzétételi URL-címen az adott régiónak megfelelő események kötegével. Például:

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

A partneri névtér végpontján való közzététel után választ kap. A válasz egy szabványos HTTP-válasz kódja. Néhány gyakori Válasz:

| Eredmény                             | Reagálás              |
|------------------------------------|-----------------------|
| Siker                            | 200 OK                |
| Az esemény adatformátuma helytelen formátumú    | 400 Hibás kérés       |
| Érvénytelen hozzáférési kulcs                 | 401 Nem engedélyezett      |
| Helytelen végpont                 | 404 Nem található         |
| Tömb vagy esemény meghaladja a méretkorlátot | 413 túl nagy a hasznos adat |

## <a name="references"></a>Hivatkozások

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM-sablon](/azure/templates/microsoft.eventgrid/allversions)
  * [ARM-sablon sémája](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST API-k](/azure/templates/microsoft.eventgrid/2020-04-01-preview/partnernamespaces)
  * [CLI-bővítmény](/cli/azure/ext/eventgrid/)

### <a name="sdks"></a>SDK-k
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Ugrás](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Következő lépések
- [A partneri témakörök áttekintése](partner-events-overview.md)
- [Partneri témakörök bevezetésének űrlapja](https://aka.ms/gridpartnerform)
- [Auth0-partneri témakör](auth0-overview.md)
- [Az Auth0-partneri témakör használata](auth0-how-to.md)
