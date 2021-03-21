---
title: Adatmegőrzés és szerializálás a Durable Functionsban – Azure
description: Ismerje meg, hogyan őrzi meg Azure Functions Durable Functions bővítménye az adattárolást
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: ea4aaa1cdbe10e2db9cf619452558d104a2293ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449373"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Az adatmegőrzés és a szerializálás Durable Functions (Azure Functions)

Durable Functions automatikusan megőrzi a függvény paramétereit, a visszatérési értékeket és az egyéb állapotot egy tartós háttérrendszer számára a megbízható végrehajtás érdekében. A tartós tároláshoz szükséges adatmennyiség és gyakoriság azonban hatással lehet az alkalmazások teljesítményére és a tárolási tranzakciós költségekre. Az alkalmazás által tárolt adatok típusától függően előfordulhat, hogy az adatmegőrzési és adatvédelmi szabályzatokat is figyelembe kell venni.

## <a name="azure-storage"></a>Azure Storage

Alapértelmezés szerint a Durable Functions az Ön által megadott [Azure Storage](https://azure.microsoft.com/services/storage/) -fiókban lévő várólistákra, táblákra és blobokra vonatkozó adatmegőrzést végzi.

### <a name="queues"></a>Üzenetsorok

A Durable Functions az Azure Storage-várólisták használatával megbízhatóan ütemezhet minden függvény-végrehajtást. Ezek a várólista-üzenetek függvény bemeneteit vagy kimeneteit tartalmazzák, attól függően, hogy az üzenet egy végrehajtás bevezetésére szolgál-e, vagy visszaadja vissza egy értéket egy hívási függvénynek. Ezek az üzenetsor-üzenetek olyan további metaadatokat is tartalmaznak, amelyeket a Durable Functions belső célokra használ, például az útválasztást és a végpontok közötti korrelációt. Miután egy függvény elvégezte a végrehajtást egy fogadott üzenetre adott válaszként, a rendszer törli az üzenetet, és a végrehajtás eredménye az Azure Storage-táblák vagy az Azure Storage-Blobok számára is megtartható.

Egy adott [feladatsoron](durable-functions-task-hubs.md)belül a Durable functions üzeneteket hoz létre és helyez hozzá az ütemezési tevékenység funkcióival nevű *munkaelem-* várólistához, `<taskhub>-workitem` valamint egy vagy több, a Orchestrator és az Entity függvények ütemezésére vagy folytatására szolgáló *vezérlési várólistát* `<taskhub>-control-##` . A vezérlési várólisták száma megegyezik az alkalmazáshoz konfigurált partíciók számával. A várólistákkal és a partíciókkal kapcsolatos további információkért tekintse meg a [teljesítmény-és méretezhetőségi dokumentációt](durable-functions-perf-and-scale.md).

### <a name="tables"></a>Táblázatok

Miután az előkészítési folyamat sikeresen elvégezte az üzenetek feldolgozását, az eredményül kapott műveletekről szóló feljegyzések megmaradnak a nevű *History* táblában `<taskhub>History` . A rendszer megőrzi a megnevezett *példányok* tábla előkészítési bemeneteit, kimeneteit és egyéni állapotinformációkat is `<taskhub>Instances` .

### <a name="blobs"></a>Blobok

A legtöbb esetben a Durable Functions nem használja az Azure Storage-blobokat az adatmegőrzéshez. A várólisták és a táblák azonban [mérete korlátokkal](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits) rendelkeznek, így megakadályozhatja, hogy Durable functions az összes szükséges adatmennyiséget egy tárolási sorba vagy üzenetsor-üzenetbe megőrizni. Ha például egy várólistán megőrzött adatok száma meghaladja a 45 KB-ot szerializált értéknél, Durable Functions tömöríti az adatmennyiséget, és egy blobban tárolja azt. Ha ily módon tartja az adattárolást a blob Storage-ba, a tartós függvény a táblázat soraiban vagy üzenetsor-üzenetében tárolja a blobra mutató hivatkozást. Ha Durable Functions le kell kérnie az adatok lekérését, a rendszer automatikusan lekéri azt a blobból. Ezek a Blobok a blob-tárolóban tárolódnak `<taskhub>-largemessages` .

> [!NOTE]
> A nagyméretű üzenetek további tömörítési és blob-üzemeltetési lépései költségesek lehetnek a CPU-és az I/O-késleltetési költségek tekintetében. Emellett Durable Functions kell betölteni a megőrzött adatmennyiséget a memóriában, és a függvények számos különböző végrehajtása esetén is elvégezhető. Ennek eredményeképpen a nagy mennyiségű adattartalom megőrzése is nagy memória-használatot eredményezhet. A memória terhelésének csökkentése érdekében érdemes lehet a nagyméretű adattartalmakat manuálisan tárolni (például a blob Storage-ban), és inkább átadni az adatokra mutató hivatkozásokat. Így a kód csak akkor tölthető be az adatba, ha a [Orchestrator függvény visszajátszásakor](durable-functions-orchestrations.md#reliability)a redundáns terhelések elkerüléséhez szükséges. A hasznos adatok lemezre való tárolása azonban *nem* ajánlott, mivel a lemezes állapot nem garantált, mert a függvények a különböző virtuális gépeken futhatnak az élettartamuk során.

### <a name="types-of-data-that-is-serialized-and-persisted"></a>Szerializált és megőrzött adattípusok
Az alábbi lista felsorolja azokat a különböző típusú adattípusokat, amelyeket a rendszer a Durable Functions szolgáltatásainak használatakor szerializál, és megőrzi:

- A Orchestrator, a tevékenység és az entitás funkcióinak összes bemenete és kimenete, beleértve az azonosítókat és a nem kezelt kivételeket is
- Orchestrator, tevékenység és entitás-függvények nevei
- Külső események nevei és hasznos adatai
- Egyéni előkészítési állapot adattartalma
- Előkészítési megszakítási üzenetek
- Tartós időzítő hasznos adatai
- Tartós HTTP-kérelem és válasz URL-címek, fejlécek és hasznos adatok
- Az entitások hívására és a jelekre vonatkozó hasznos adatok
- Entitás-állapot hasznos adatai

### <a name="working-with-sensitive-data"></a>Bizalmas adatok használata
Az Azure Storage használatakor az összes adatok automatikusan titkosítva maradnak a nyugalmi állapotban. Azonban bárki, aki hozzáféréssel rendelkezik a Storage-fiókhoz, a titkosítatlan formában is beolvashatja az adatforrást. Ha erősebb védelemre van szüksége a bizalmas adatokhoz, érdemes megfontolnia az adatok titkosítását a saját titkosítási kulcsainak használatával, hogy Durable Functions megmaradjanak az adatok egy előre titkosított formában.

Másik lehetőségként a .NET-felhasználók az automatikus titkosítást biztosító egyéni szerializálási szolgáltatók megvalósítására is lehetőségük van. [Ebben a GitHub-mintában](https://github.com/charleszipp/azure-durable-entities-encryption)a titkosítással rendelkező egyéni szerializálási példa található.

> [!NOTE]
> Ha úgy dönt, hogy implementálja az alkalmazás szintű titkosítást, vegye figyelembe, hogy az összeszerelések és az entitások határozatlan ideig létezhetnek. Ez akkor fontos, ha a titkosítási kulcsok elforgatásához időt vesz igénybe, mert egy adott eljárás vagy entitások hosszabb ideig futhatnak, mint a Key rotációs szabályzat. Ha a kulcs elforgatása történik meg, az adattitkosításhoz használt kulcs már nem lesz elérhető a visszafejtéshez a következő alkalommal, amikor a rendszer végrehajtja a rendszerbeállítást vagy az entitást. Az ügyfél titkosítása ezért csak akkor javasolt, ha a rendszer az előkészítést és az entitásokat viszonylag rövid időtartamra szeretné futtatni.

## <a name="customizing-serialization-and-deserialization"></a>A szerializálás és a deszerializálás testreszabása

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>Alapértelmezett szerializálási logika

A Durable Functions belsőleg a [JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm) -t használja az összehangolás és az ENTITÁSok JSON-beli szerializálásához. A Json.NET alapértelmezett beállításai a következők: Durable Functions.

**Bemenetek, kimenetek és állapot:**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**Kivételek**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

További részletekért tekintse meg a dokumentációt `JsonSerializerSettings` [](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm).

## <a name="customizing-serialization-with-net-attributes"></a>Szerializálás testreszabása .NET-attribútumokkal

Az adatok szerializálásakor a Json.NET az osztályok és tulajdonságok [különböző attribútumait](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm) keresi, amelyek azt szabályozzák, hogy az adatok hogyan szerializálva és deszerializálva legyenek a JSON-ból. Ha a forráskódja a Durable Functions API-khoz átadott adattípushoz tartozik, akkor a szerializálás és a deszerializálás testreszabásához vegye fel ezeket az attribútumokat a típushoz.

## <a name="customizing-serialization-with-dependency-injection"></a>A szerializálás függőségi befecskendezéssel való testreszabása

A .NET-alapú és a functions v3 futtatókörnyezetben futtatott alkalmazások a [függőségi befecskendezés (di)](../functions-dotnet-dependency-injection.md) segítségével szabják testre az adatok és a kivételek szerializált módját. Az alábbi mintakód azt mutatja be, hogyan lehet a DI használatával felülbírálni az alapértelmezett Json.NET szerializálási beállításokat a `IMessageSerializerSettingsFactory` és a szolgáltatás felületének egyéni implementációja segítségével `IErrorSerializerSettingsFactory` .

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Extensions.DependencyInjection;
using Newtonsoft.Json;
using System.Collections.Generic;

[assembly: FunctionsStartup(typeof(MyApplication.Startup))]
namespace MyApplication
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingsFactory>();
            builder.Services.AddSingleton<IErrorSerializerSettingsFactory, CustomErrorSerializerSettingsFactory>();
        }

        /// <summary>
        /// A factory that provides the serialization for all inputs and outputs for activities and
        /// orchestrations, as well as entity state.
        /// </summary>
        internal class CustomMessageSerializerSettingsFactory : IMessageSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }

        /// <summary>
        /// A factory that provides the serialization for all exceptions thrown by activities
        /// and orchestrations
        /// </summary>
        internal class CustomErrorSerializerSettingsFactory : IErrorSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="serialization-and-deserialization-logic"></a>Szerializálási és deszerializálási logika

Azure Functions csomópont-alkalmazások [ `JSON.stringify()` szerializáláshoz](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) és [ `JSON.Parse()` deszerializáláshoz](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)használhatók. A legtöbb típusnak zökkenőmentesen kell szerializálnia és deszerializálnia. Abban az esetben, ha az alapértelmezett logika nem elegendő, a `toJSON()` metódus definiálása az objektumon eltéríti a szerializálási logikát. Az objektumok deszerializálása azonban nem létezik analóg.

A szerializálási/deszerializálási folyamat teljes testreszabásához vegye fontolóra a szerializálás és a deszerializálás kezelését a saját kóddal, és az adatátvitelt karakterláncként.


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>Szerializálási és deszerializálási logika

Javasoljuk, hogy írja be a jegyzetek használatát, hogy Durable Functions szerializálja és deszerializálja az adatait megfelelően. Noha számos beépített típus automatikusan van kezelve, a deszerializálás során bizonyos beépített adattípusokhoz kötelező jegyzeteket kell megadni a típus megőrzése érdekében.

Az egyéni adattípusok esetében meg kell határoznia egy adattípus JSON-szerializálását és deszerializálását úgy, hogy az `to_json` `from_json` osztályból statikus és metódust exportál.

---
