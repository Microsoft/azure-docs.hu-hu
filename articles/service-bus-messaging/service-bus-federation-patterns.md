---
title: Üzenet-replikációs feladat mintái – Azure Service Bus | Microsoft Docs
description: Ez a cikk részletes útmutatást nyújt az egyes üzenet-replikációs feladatok mintáinak megvalósításához
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d823ee7ccd4f53bfc3e10211a4f44908273a110d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97657502"
---
# <a name="message-replication-tasks-patterns"></a>Az üzenet-replikációs feladatok mintái

Az [összevonás áttekintése](service-bus-federation-overview.md) és a [replikátor függvények áttekintése](service-bus-federation-replicator-functions.md) ismerteti a replikációs feladatok indoklását és alapvető elemeit, és azt javasoljuk, hogy a jelen cikk folytatása előtt Ismerkedjen meg velük.

Ebben a cikkben részletesen ismertetjük a megvalósítási útmutatót az Áttekintés szakaszban Kiemelt minták közül. 

## <a name="replication"></a>Replikáció 

A replikációs minta üzeneteket másol egy várólistából vagy témakörből a következőre, vagy egy várólistából vagy témakörből egy másik célhelyre, például egy Event hub-ra. Az üzenetek továbbítása az üzenet tartalmának módosítása nélkül történik. 

Ennek a mintának a megvalósítását az Azure Service Bus mintából [és onnan érkező üzenet-replikáció](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy) szabályozza.

### <a name="sequences-and-order-preservation"></a>A sorrendek és a sorrend megőrzése

A replikációs modell nem célja a forrás-üzenetsor vagy-témakör üzeneteinek abszolút sorrendjének megtartása a célvárólista vagy a témakörben, de szükség esetén arra összpontosít, hogy megőrizze az üzenetek relatív sorrendjét, amelyre az alkalmazásnak szüksége van. Az alkalmazás lehetővé teszi, hogy engedélyezze a munkamenet-támogatást a forrás entitáshoz, és hogy a kapcsolódó üzeneteket ugyanazzal a [munkamenet-kulccsal](message-sessions.md)csoportosítsa.

A munkamenet-kompatibilis, előre elkészített replikációs függvények biztosítják, hogy a forrás entitásból beolvasott, azonos munkamenet-azonosítóval rendelkező üzenetek a célként megadott várólistába vagy témakörbe kötegként legyenek beküldve az eredeti sorozatba és ugyanazzal a munkamenet-azonosítóval. 

### <a name="service-assigned-metadata"></a>Szolgáltatáshoz rendelt metaadatok 

A forrás-és a témakörből származó üzenet szolgáltatáshoz hozzárendelt metaadatait, az eredeti sorba helyezni időt és a sorszámot a rendszer új szolgáltatás által hozzárendelt értékekre cseréli a célvárólista vagy a témakörben, de a mintákban található alapértelmezett replikációs feladatokkal az eredeti értékek megmaradnak a felhasználó tulajdonságaiban: `repl-enqueue-time` (ISO8601 karakterlánc) és `repl-sequence` .

Ezek a tulajdonságok karakterlánc típusúak, és tartalmazzák a megfelelő eredeti tulajdonságok sztringesített értékét.  Ha az üzenet többször is továbbítva van, az azonnali forrás szolgáltatáshoz hozzárendelt metaadatait a rendszer hozzáfűzi a már meglévő tulajdonságokhoz, és a pontosvesszővel elválasztott értékeket tartalmaz. 

### <a name="failover"></a>Feladatátvétel

Ha a replikálást vész-helyreállítási célokra használja, akkor a Service Bus szolgáltatásban vagy a hálózati megszakításokkal szembeni védelem érdekében az ilyen meghibásodási forgatókönyvek esetében az egyik várólistáról vagy témakörből feladatátvételt kell végeznie a másodlagos végpont használatára.

Az összes feladatátvételi forgatókönyv esetében feltételezhető, hogy a névterek szükséges elemei szerkezetileg megegyeznek, ami azt jelenti, hogy a várólisták és a témakörök azonos módon vannak elnevezve, és a közös hozzáférésű aláírási szabályok és/vagy szerepköralapú hozzáférés-vezérlési szabályok ugyanúgy vannak beállítva. A másodlagos névterek létrehozásához és frissítéséhez kövesse a [névterek áthelyezésére](move-across-regions.md) és a karbantartási lépés kihagyása című témakör útmutatását.

Ha szeretné kényszeríteni a termelőket és a fogyasztókat a váltásra, meg kell adnia, hogy mely névtér használható a kereséshez egy könnyen elérhető és frissíthető helyen. Ha a termelők vagy a fogyasztók gyakori vagy állandó hibákba ütköznek, akkor a helyükön kell megjelenniük, és módosítaniuk kell a konfigurációt. A konfiguráció megosztása számos módon megosztható, de a következő két lehetőség van: DNS-és fájlmegosztás.

#### <a name="dns-based-failover-configuration"></a>DNS-alapú feladatátvételi konfiguráció

Az egyik jelölt módszer a DNS SRV-rekordokban lévő információk megtartása egy Ön által felügyelt DNS-ben, és a megfelelő üzenetsor-vagy témakör-végpontokra mutat. Vegye figyelembe, hogy az üzenetsor-hubok nem engedélyezik a végpontok számára a CNAME-rekordokkal való közvetlen aliasok használatát, ami azt jelenti, hogy a DNS-t rugalmas keresési mechanizmusként fogja használni a végponti címekhez, és nem az IP-cím adatainak közvetlen feloldásához. 

Tegyük fel, hogy Ön a tartomány tulajdonosa, `example.com` és az alkalmazása esetében egy zóna `test.example.com` . Két alternatív Service Bus esetén két további beágyazott zónát, valamint egy SRV-rekordot fog létrehozni. 

Az SRV-rekordok a Common Convention (két végponttal ellátott) előtagot követve, a (z) és a (z) 5671-as porton, az egyik az 443-es porton található AMQP-en, a másik pedig a `_azure_servicebus._amqp` zónához tartozó névtér Service Bus végpontján mutatnak.

| Zóna                 | SRV-rekord
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

Az alkalmazás zónájában létre fog hozni egy CNAME-bejegyzést, amely az elsődleges üzenetsor vagy témakörhöz tartozó alárendelt zónára mutat:

| CNAME-rekord                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `test1.test.example.com`

Ha olyan DNS-ügyfelet használ, amely lehetővé teszi a CNAME és SRV rekordok explicit lekérdezését (a Java és a .NET beépített ügyfelei csak a nevek egyszerű feloldását teszik lehetővé IP-címekre), akkor a kívánt végpontot fel lehet oldani. Ha például a [DnsClient.net](https://dnsclient.michaco.net/), a lookup függvény a következő:

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

A függvény a zóna 5671-as portján regisztrált cél gazdagép nevét adja vissza, amely jelenleg a CNAME néven van alias. 

A feladatátvétel végrehajtásához szerkesztenie kell a CNAME rekordot, és azt a másodlagos zónára kell mutatnia. 

A DNS használatának előnye, és különösen [Azure DNS](../dns/dns-overview.md), hogy az Azure DNS információ globálisan replikálódik, ezért az egyrégiós kimaradások elleni ellenálló képesség.

Ez az eljárás hasonlít a [Service Bus geo-Dr](service-bus-geo-dr.md) működéséhez, de teljes mértékben a saját irányítása alá tartozik, és aktív/aktív forgatókönyvekkel is működik.

#### <a name="file-share-based-failover-configuration"></a>Fájlmegosztás alapú feladatátvételi konfiguráció

A DNS a végponti adatok megosztására való használatának legegyszerűbb alternatívája az elsődleges végpont neve egy egyszerű szövegfájlba, és a fájl kiszolgálása egy olyan infrastruktúrából, amely robusztus az kimaradások ellen, és továbbra is lehetővé teszi a frissítések használatát. 

Ha már futtatta a globális rendelkezésre állással és a tartalom-replikálással rendelkező, magasan elérhető webhelyes infrastruktúrát, adjon hozzá egy ilyen fájlt, és tegye közzé újra a fájlt, ha kapcsolóra van szükség.

## <a name="merge"></a>Egyesítés

Az egyesítési minta egy vagy több olyan replikációs feladattal rendelkezik, amely egy adott célpontra mutat, valószínűleg párhuzamosan a normál termelőktől, ugyanakkor üzeneteket is küld ugyanarra a célra. 

A minta variációi:
- Két vagy több replikációs funkció egyidejűleg több különböző forrásból származó üzeneteket is beszerezzen, és ugyanarra a célra küldi őket.
- Egy újabb replikálási függvény, amely egy forrásból származó üzeneteket szerez be, miközben a célt közvetlenül a gyártók is használják. 
- Az előző minta, de a két vagy több téma között tükrözött üzenetek, amelyek ugyanazt az üzenetet tartalmazó témaköröket tartalmazzák, függetlenül attól, hogy az üzenetek mikor jönnek létre.

Az első két minta variációja triviális, és nem különbözik az egyszerű replikálási feladatoktól.

Az utolsó forgatókönyvhöz a már replikált üzenetek replikálásának ismételt kizárása szükséges. A technikát az aktív/aktív minta mutatja be és ismerteti.

## <a name="editor"></a>Szerkesztő

A szerkesztő minta a [replikálási](#replication) mintára épül, de a továbbított üzenetek a továbbítás előtt módosulnak. Ilyen módosítások például a következők:

- ***Átkódolás*** – ha az üzenet tartalma (más néven "szövegtörzs" vagy "hasznos adat") az *Apache Avro* -formátummal vagy valamilyen saját szerializálási formátummal van kódolva, azonban a cél tulajdonosának az a célja, hogy a tartalom *JSON* -kódolású legyen, egy átkódoló replikációs feladat először deszerializálja az *Apache Avro* adattartalmát a memóriában lévő Object gráfba, majd Szerializálja a gráfot a továbbított üzenet *JSON* -formátumára. A transcoding a **tartalom tömörítési** és kibontási feladatait is tartalmazza.
- ***Átalakítás*** – a strukturált adatmennyiséget tartalmazó üzenetek esetében szükség lehet arra, hogy az alsóbb rétegbeli fogyasztók könnyebben átformálják ezeket az adatmennyiségeket. Ez magában foglalhat olyan munkát, mint a beágyazott struktúrák összeolvasztása, a felesleges adatelemek metszése vagy a hasznos adatok átalakítása, hogy pontosan illeszkedjenek az adott sémához.
- ***Kötegelt feldolgozás*** – előfordulhat, hogy az üzenetek kötegekben (több üzenet egyetlen átvitelben) érkeznek a forrásokból, de egy célhoz kell továbbítani őket, vagy fordítva. Egy feladat ezért több üzenetet is továbbíthat egy bemeneti üzenet átvitele vagy egy olyan üzenet összevonása alapján, amelyet aztán együtt továbbítanak. 
- ***Ellenőrzés*** – a külső forrásokból származó üzenetek adatait gyakran ellenőrizni kell, hogy azok megfelelnek-e a szabályoknak, mielőtt azok továbbítva lesznek. A szabályok sémák vagy kódok használatával adhatók meg. azok az üzenetek, amelyek nem felelnek meg a megfelelőségnek, eldobásra kerülhetnek, a naplókban feljegyzett problémával, vagy egy speciális célhelyre továbbítva a további kezelés érdekében.   
- A ***dúsítás*** – egyes forrásokból érkező üzenet-adatok esetében a további kontextushoz is szükség lehet, ha a cél rendszerekben használható. Ez magában foglalhatja a hivatkozási adatok megkeresését és az adatok beágyazását az üzenettel, illetve a replikálási feladat által ismert forrással kapcsolatos adatok hozzáadását, az üzenetekben nem szereplő adatokat. 
- ***Szűrés*** – előfordulhat, hogy néhány szabály alapján a forrástól érkező üzeneteket vissza kell tartani a célhelyről. A szűrő ellenőrzi az üzenetet egy szabályhoz, és eldobja az üzenetet, ha az üzenet nem felel meg a szabálynak. Az ismétlődő üzenetek kiszűrése bizonyos feltételek betartásával és a további, azonos értékekkel rendelkező üzenetek eldobásával a szűrés formája.
- ***Útválasztás és particionálás*** – néhány replikációs feladat engedélyezheti két vagy több alternatív célpont használatát, és meghatározhatja azokat a szabályokat, amelyek esetében a replikációs cél az üzenet metaadatai vagy tartalma alapján van kiválasztva egy adott üzenethez. Az Útválasztás egy speciális formája a particionálás, ahol a feladat explicit módon hozzárendeli a partíciókat egy replikációs célhoz a szabályok alapján.
- ***Titkosítás*** – előfordulhat, hogy a replikációs feladatnak vissza kell fejtenie a forrásból érkező tartalmat, és/vagy titkosítania kell a tartalmat a célhelyre, és/vagy a tartalom és a metaadatok integritását ellenőrizni kell az üzenetben végzett aláíráshoz képest, vagy csatolnia kell az aláírást. 
- ***Igazolás*** – a replikálási feladat a digitális aláírással potenciálisan védett metaadatokat is csatolhat egy olyan üzenethez, amely tanúsítja, hogy az üzenet egy adott csatornán vagy adott időpontban érkezett.     
- ***Láncolás*** – egy replikációs feladat aláírásokat alkalmazhat az üzenetek sorozatából, hogy a sorozat integritása védve legyen, és a rendszer hiányzó üzeneteket észlel.

Ezek a minták a Azure Functions használatával valósíthatók meg, az [üzenetsor-trigger](../azure-functions/functions-bindings-service-bus-trigger.md) használatával az üzenetek beszerzéséhez, valamint az üzenetsor [vagy a témakör kimeneti kötéseinek](../azure-functions/functions-bindings-service-bus-output.md) kézbesítéséhez. 

## <a name="routing"></a>Útválasztás

Az útválasztási minta a [replikálási](#replication) mintára épül, de ahelyett, hogy egy forrást és egy célt használ, a replikálási feladat több célként is szerepel a C# nyelven:

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

Az útválasztási függvény figyelembe veszi az üzenet metaadatait és/vagy az üzenet tartalmát, majd kiválaszthatja az egyik elérhető célhelyet a küldéshez.

## <a name="next-steps"></a>Következő lépések

- [Message replikátor-alkalmazások Azure Functions][1]
- [Üzenetek replikálása Service Bus között][2]
- [Üzenetek replikálása az Azure Event Hubsba][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub