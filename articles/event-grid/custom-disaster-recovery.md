---
title: A Azure Event Grid egyéni témaköreinek vész-helyreállítási következményei
description: Ez az oktatóanyag végigvezeti az eseményvezérelt architektúra beállításának lépésein, ha a Event Grid szolgáltatás nem Kifogástalan állapotba kerül egy régióban.
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e37cb6a0679ee2e249de4ed8fa31c40d5082ea4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020143"
---
# <a name="build-your-own-disaster-recovery-for-custom-topics-in-event-grid"></a>Hozzon létre saját vész-helyreállítást a Event Grid egyéni témaköreihez
A vész-helyreállítási funkció az alkalmazások jelentős elvesztése miatti helyreállításra összpontosít. Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be az eseményvezérelt architektúrát, ha a Event Grid szolgáltatás nem Kifogástalan állapotba kerül egy adott régióban.

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre aktív-passzív feladatátvételi architektúrát a Event Grid egyéni témaköreihez. A feladatátvételt a témakörök és az előfizetések két régióban való tükrözésével, majd a feladatátvétel kezelésével végezheti el, ha egy témakör nem megfelelő állapotba kerül. Az oktatóanyag architektúrája minden új forgalom esetében meghiúsul. Fontos tisztában lenni azzal, hogy ezzel a beállítással a már a repülés során bekövetkezett események nem lesznek helyreállítva, amíg a feltört régió Kifogástalan állapotba nem kerül.

> [!NOTE]
> Event Grid támogatja az automatikus geo vész-helyreállítást (GeoDR) a kiszolgálóoldali oldalon. Ha nagyobb mértékű vezérlést szeretne végrehajtani a feladatátvételi folyamaton, továbbra is megvalósíthatja az ügyféloldali vész-helyreállítási logikát. Az automatikus GeoDR kapcsolatos részletekért lásd: [kiszolgálóoldali földrajzi katasztrófa-helyreállítás Azure Event Gridban](geo-disaster-recovery.md).

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A feladatátvételi konfiguráció teszteléséhez szüksége lesz egy végpontra, hogy megkapja az eseményeket a következő helyen:. A végpont nem része a feladatátvételi infrastruktúrának, de az eseménykezelővel fog működni, hogy könnyebb legyen a tesztelés.

A tesztelés egyszerűsítése érdekében helyezzen üzembe egy [előre elkészített webalkalmazást](https://github.com/Azure-Samples/azure-event-grid-viewer) , amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

1. A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Aquent." /></a>

1. Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.
Jegyezze fel ezt az URL-címet, mert később szüksége lesz rá.

1. A hely látható, de még nem lett közzétéve esemény.

   ![Új hely megtekintése](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]


## <a name="create-your-primary-and-secondary-topics"></a>Az elsődleges és másodlagos témakörök létrehozása

Először hozzon létre két Event Grid témakört. Ezek a témakörök elsődlegesként és másodlagosként is működni fognak. Alapértelmezés szerint az események az elsődleges témakörön keresztül fognak folyni. Ha az elsődleges régióban van szolgáltatási leállás, a másodlagos átveszi a feladatokat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

1. Az Azure fő menüjének bal felső sarkában válassza a **minden szolgáltatás** lehetőséget, > keressen rá **Event Grid** > válassza a **Event Grid témakörök** lehetőséget.

   ![Event Grid témakörök menü](./media/custom-disaster-recovery/select-topics-menu.png)

    Válassza ki a Event Grid témakörök melletti csillagot, és adja hozzá az erőforrás menühöz a későbbi könnyebb hozzáférés érdekében.

1. A Event Grid témakörök menüben válassza a **+ Hozzáadás** elemet az elsődleges témakör létrehozásához.

   * Adjon egy logikai nevet a témakörnek, és adja hozzá az "-Primary" utótagot, hogy könnyen nyomon követhető legyen.
   * Ennek a témakörnek a régiója az elsődleges régió lesz.

     ![Event Grid témakör elsődleges létrehozási párbeszéde](./media/custom-disaster-recovery/create-primary-topic.png)

1. A témakör létrehozása után navigáljon hozzá, és másolja ki a **témakör-végpontot**. később szüksége lesz az URI-ra.

    ![Event Grid elsődleges témakör](./media/custom-disaster-recovery/get-primary-topic-endpoint.png)

1. A témakörhöz tartozó hozzáférési kulcs beszerzése, amelyre később is szüksége lesz. Kattintson a **hozzáférési kulcsok** elemre az erőforrás menüben, és másolja az 1. kulcsot.

    ![Elsődleges témakör kulcsának beolvasása](./media/custom-disaster-recovery/get-primary-access-key.png)

1. A témakör panelen kattintson az **+ esemény-előfizetés** lehetőségre egy előfizetés létrehozásához, amely az előfeltételekben az oktatóanyaghoz kapcsolódó esemény-fogadó webhelyhez csatlakozik.

   * Adja meg az esemény-előfizetés logikai nevét, és adja hozzá az "-Primary" utótagot, hogy könnyen nyomon követhető legyen.
   * Válassza ki a végpont típusa webhook elemet.
   * Állítsa a végpontot az Event fogadó esemény-URL-címére, amelynek a következőképpen kell kinéznie: `https://<your-event-reciever>.azurewebsites.net/api/updates`

     ![Képernyőkép, amely a "név", a "végpont típusa" és a "végpont" értékek kiemelésével jeleníti meg az "esemény-előfizetés – alapszintű" lapot.](./media/custom-disaster-recovery/create-primary-es.png)

1. Ismételje meg ugyanezt a folyamatot a másodlagos témakör és előfizetés létrehozásához. Ezúttal cserélje le az "-Primary" utótagot "-másodlagos" értékre a könnyebb nyomon követés érdekében. Végül ügyeljen arra, hogy egy másik Azure-régióban helyezze el. Amíg a kívánt helyre helyezi, azt javasoljuk, hogy az [Azure párosított régiókat](../best-practices-availability-paired-regions.md)használja. A másodlagos témakör és az előfizetés egy másik régióban való elhelyezése biztosítja, hogy az új események akkor is folynak, ha az elsődleges régió leáll.

Ekkor a következőket kell tennie:

   * Egy Event fogadó webhelye teszteléshez.
   * Elsődleges témakör az elsődleges régióban.
   * Egy elsődleges esemény-előfizetés, amely összekapcsolja az elsődleges témakört az Event fogadó webhellyel.
   * Másodlagos témakör a másodlagos régióban.
   * Egy másodlagos esemény-előfizetés, amely az elsődleges témakört csatlakoztatja az Event fogadó webhelyéhez.

## <a name="implement-client-side-failover"></a>Ügyféloldali feladatátvétel implementálása

Most, hogy már van egy regionálisan redundáns pár témakör és előfizetések beállítása, készen áll az ügyféloldali feladatátvétel megvalósítására. Több módon is elvégezhető, de az összes feladatátvételi implementáció közös szolgáltatással fog rendelkezni: Ha egy témakör már nem kifogástalan állapotú, akkor a forgalom átirányítja a másik témakörre.

### <a name="basic-client-side-implementation"></a>Alapszintű ügyféloldali implementáció

Az alábbi mintakód egy egyszerű .NET-közzétevő, amely először az elsődleges témakörre próbálja meg közzétenni a közzétételt. Ha ez nem sikerül, a rendszer feladatátvételt hajt végre a másodlagos témakörben. Mindkét esetben a beolvasás során a másik témakör Health API-ját is ellenőrzi `https://<topic-name>.<topic-region>.eventgrid.azure.net/api/health` . Az egészséges témakörnek mindig a **200 OK** értékűre kell válaszolnia, ha az **/API/Health** -végponton beolvasás történik.

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;
using Newtonsoft.Json;

namespace EventGridFailoverPublisher
{
    // This captures the "Data" portion of an EventGridEvent on a custom topic
    class FailoverEventData
    {
        [JsonProperty(PropertyName = "teststatus")]
        public string TestStatus { get; set; }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // TODO: Enter the endpoint each topic. You can find this topic endpoint value
            // in the "Overview" section in the "Event Grid Topics" blade in Azure Portal..
            string primaryTopic = "https://<primary-topic-name>.<primary-topic-region>.eventgrid.azure.net/api/events";
            string secondaryTopic = "https://<secondary-topic-name>.<secondary-topic-region>.eventgrid.azure.net/api/events";

            // TODO: Enter topic key for each topic. You can find this in the "Access Keys" section in the
            // "Event Grid Topics" blade in Azure Portal.
            string primaryTopicKey = "<your-primary-topic-key>";
            string secondaryTopicKey = "<your-secondary-topic-key>";

            string primaryTopicHostname = new Uri( primaryTopic).Host;
            string secondaryTopicHostname = new Uri(secondaryTopic).Host;

            Uri primaryTopicHealthProbe = new Uri("https://" + primaryTopicHostname + "/api/health");
            Uri secondaryTopicHealthProbe = new Uri("https://" + secondaryTopicHostname + "/api/health");

            var httpClient = new HttpClient();

            try
            {
                TopicCredentials topicCredentials = new TopicCredentials(primaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(primaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to primary Event Grid topic.");

                HttpResponseMessage health = httpClient.GetAsync(secondaryTopicHealthProbe).Result;
                Console.Write("\n\nSecondary Topic health " + health);
            }
            catch (Microsoft.Rest.Azure.CloudException e)
            {
                TopicCredentials topicCredentials = new TopicCredentials(secondaryTopicKey);
                EventGridClient client = new EventGridClient(topicCredentials);

                client.PublishEventsAsync(secondaryTopicHostname, GetEventsList()).GetAwaiter().GetResult();
                Console.Write("Published events to secondary Event Grid topic. Reason for primary topic failure:\n\n" + e);

                HttpResponseMessage health = httpClient.GetAsync(primaryTopicHealthProbe).Result;
                Console.Write("\n\nPrimary Topic health " + health);
            }

            Console.ReadLine();
        }

        static IList<EventGridEvent> GetEventsList()
        {
            List<EventGridEvent> eventsList = new List<EventGridEvent>();

            for (int i = 0; i < 5; i++)
            {
                eventsList.Add(new EventGridEvent()
                {
                    Id = Guid.NewGuid().ToString(),
                    EventType = "Contoso.Failover.Test",
                    Data = new FailoverEventData()
                    {
                        TestStatus = "success"
                    },
                    EventTime = DateTime.Now,
                    Subject = "test" + i,
                    DataVersion = "2.0"
                });
            }

            return eventsList;
        }
    }
}
```

### <a name="try-it-out"></a>Próbálja ki

Most, hogy már rendelkezik az összes összetevővel, tesztelheti a feladatátvétel megvalósítását. Futtassa a fenti mintát a Visual Studio Code-ban vagy a kedvenc környezetében. Cserélje le a következő négy értéket a végpontokkal és kulcsokkal a témakörökből:

   * primaryTopic – az elsődleges témakör végpontja.
   * secondaryTopic – a másodlagos témakör végpontja.
   * primaryTopicKey – az elsődleges témakör kulcsa.
   * secondaryTopicKey – a másodlagos témakör kulcsa.

Próbálja meg futtatni az esemény közzétevőjét. Az alábbihoz hasonló Event Grid-megjelenítőben láthatja a tesztelési eseményeket.

![Elsődleges esemény-előfizetés Event Grid](./media/custom-disaster-recovery/event-grid-viewer.png)

A feladatátvétel működésének biztosításához módosíthatja az elsődleges témakör kulcsának néhány karakterét, hogy az ne legyen érvényes. Próbálja meg újból futtatni a közzétevőt. Az új események továbbra is megjelennek a Event Grid-megjelenítőben, de ha megtekinti a konzolt, láthatja, hogy most már a másodlagos témakörben jelennek meg.

### <a name="possible-extensions"></a>Lehetséges bővítmények

A mintát többféleképpen is kiterjesztheti igényei alapján. Nagy mennyiségű forgatókönyv esetén érdemes lehet rendszeresen ellenőriznie a témakör Health API-ját egymástól függetlenül. Így ha egy témakör leállt, nem kell minden egyes közzétételsel ellenőriznie. Ha már tudja, hogy egy témakör állapota nem megfelelő, a rendszer alapértelmezés szerint a másodlagos témakörben teheti közzé.

Hasonlóképpen érdemes lehet megvalósítani a feladat-visszavétel logikáját az adott igények alapján. Ha a legközelebbi adatközpontba való közzététel kritikus fontosságú a késés csökkentése érdekében, rendszeres időközönként lekérdezheti egy olyan témakör Health API-ját, amely feladatátvételt végez. Ha ismét Kifogástalan állapotba kerül, biztos lehet benne, hogy a szorosabb adatközpontba történő feladat-visszavétel biztonságos.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [fogadhat eseményeket egy http-végponton](./receive-events.md)
- Ismerje meg, hogyan [irányíthatja az eseményeket hibrid kapcsolatok](./custom-event-to-hybrid-connection.md)
- Tudnivalók a vész [-helyreállításról Azure DNS és Traffic Manager használatával](../networking/disaster-recovery-dns-traffic-manager.md)
