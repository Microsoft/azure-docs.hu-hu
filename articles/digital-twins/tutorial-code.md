---
title: 'Oktatóanyag: ügyfélalkalmazás kódolása'
titleSuffix: Azure Digital Twins
description: Oktatóanyag egy ügyfélalkalmazás minimális kódjának megírásához a .NET (C#) SDK használatával.
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: bd3ba88650161bd11a24697b4ff8575d307120e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102424461"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Oktatóanyag: kódolás az Azure Digital Twins API-kkal

Gyakori, hogy az Azure Digital Twins-szel dolgozó fejlesztők az Azure Digital Twins szolgáltatás példányaival együttműködve írhatnak be egy ügyfélalkalmazás-alkalmazást. Ez a fejlesztői témájú oktatóanyag bevezetést nyújt az Azure Digital Twins szolgáltatással való programozáshoz a [.net-hez készült Azure Digital Twins SDK (C#)](/dotnet/api/overview/azure/digitaltwins/client)használatával. Részletesen ismerteti a C#-konzol ügyfélalkalmazás lépésről lépésre történő írását.

> [!div class="checklist"]
> * Projekt beállítása
> * Első lépések a Project Code-ban   
> * Kód teljes mintája
> * Az erőforrások eltávolítása
> * Következő lépések

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag a telepítő és a projekthez használható parancssort használja. Ezért bármilyen Kódszerkesztő használatával megtekintheti a gyakorlatokat.

A kezdéshez szükséges lépések:
* Bármely Kódszerkesztő
* **.Net Core 3,1** a fejlesztői gépen. A .NET Core SDK ezen verzióját több platformra is letöltheti a [.net Core 3,1 letöltésével](https://dotnet.microsoft.com/download/dotnet-core/3.1).

### <a name="prepare-an-azure-digital-twins-instance"></a>Azure digitális Twins-példány előkészítése

[!INCLUDE [Azure Digital Twins: instance prereq](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

## <a name="set-up-project"></a>Projekt beállítása

Ha készen áll az Azure Digital Twins-példánnyal való használatra, kezdje el beállítani az ügyfélalkalmazás-projektet. 

Nyisson meg egy parancssort vagy egy másik konzolablak-ablakot a gépen, és hozzon létre egy üres Project könyvtárat, ahol az oktatóanyag során szeretné tárolni a munkáját. Nevezze el a könyvtárat, amit szeretne (például *DigitalTwinsCodeTutorial*).

Navigáljon az új könyvtárba.

Egyszer a projekt könyvtárában **hozzon létre egy üres .net-konzol alkalmazás-projektet**. A parancssori ablakban a következő parancs futtatásával hozhat létre egy minimális C#-projektet a-konzolhoz:

```cmd/sh
dotnet new console
```

Ez több fájlt hoz létre a címtárban, például egy *program. cs nevű programot* , ahol a kód nagy részét fogja írni.

Tartsa megnyitva a parancssorablakot, ahogy az oktatóanyag során is használni fogja.

Ezután **vegyen fel két függőséget a projekthez** , amely szükséges lesz az Azure digitális ikrekkel való együttműködéshez. Az első a .NET-hez készült [Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client)csomag, a második pedig az Azure-hoz való hitelesítéshez segítséget nyújtó eszközöket biztosít.

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
```

## <a name="get-started-with-project-code"></a>Első lépések a Project Code-ban

Ebben a szakaszban megkezdi a kód megírását az új alkalmazás-projekthez az Azure digitális Twins szolgáltatással való együttműködéshez. Az érintett műveletek a következők:
* Hitelesítés a szolgáltatáson keresztül
* Modell feltöltése
* Kifogási hibák
* Digitális ikrek létrehozása
* Kapcsolatok létrehozása
* Digitális ikrek lekérdezése

Létezik egy szakasz is, amely az oktatóanyag végén található teljes kódot mutatja. Ez a hivatkozásként használható a program elindításához.

A kezdéshez nyissa meg a *program. cs* fájlt bármilyen szerkesztőprogramban. Ehhez a következőhöz hasonló kódot kell megnéznie:

:::row:::
    :::column:::
        :::image type="content" source="media/tutorial-code/starter-template.png" alt-text="A mintakód egy kódrészlete. Létezik egy &quot;System;&quot; utasítás, egy DigitalTwinsCodeTutorial nevű névtér. a program nevű névtér egyik osztálya; a osztály egyik fő metódusa a &quot;statikus Void Main (string [] ARG)&quot; szabványos aláírása. A Main metódus egy Hello World Print utasítást tartalmaz." lightbox="media/tutorial-code/starter-template.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Először vegyen fel néhány `using` sort a kód elejére a szükséges függőségek lekéréséhez.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Azure_Digital_Twins_dependencies":::

Ezután adjon hozzá kódot ehhez a fájlhoz, hogy kitöltsön néhány funkciót. 

### <a name="authenticate-against-the-service"></a>Hitelesítés a szolgáltatással

Az alkalmazás első lépéseként hitelesítenie kell magát az Azure Digital Twins szolgáltatásban. Ezután létrehozhat egy szolgáltatás-ügyfél osztályt az SDK-függvények eléréséhez.

A hitelesítéshez szüksége lesz az Azure Digital Twins-példány *állomásneve* .

A *program. cs programban* illessze be a következő kódot a "Hello, World!" alá. a metódus nyomtatási sora `Main` . Állítsa be az értékét az `adtInstanceUrl` Azure Digital Twins-példány *állomásneve* értékre.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Authentication_code":::

Mentse a fájlt. 

A következő paranccsal futtassa a kódot a parancsablakban: 

```cmd/sh
dotnet run
```

Ezzel visszaállítja a függőségeket az első futtatás után, majd végrehajtja a programot. 
* Ha nem fordul elő hiba, a program kinyomtatja a *szolgáltatáshoz létrehozott ügyfelet, amely készen áll*.
* Mivel ebben a projektben még nincsenek hibák, ha problémák merülnek fel, a kód egy kivételt fog látni.

### <a name="upload-a-model"></a>Modell feltöltése

Az Azure Digital Twins nem rendelkezik belső tartománybeli szókincstel. A környezetben az Azure Digital Twins-ban ábrázolt elemek típusai a **modellek** használatával definiálhatók. A [modellek](concepts-models.md) hasonlók az objektumorientált programozási nyelvekben található osztályokhoz. felhasználói sablonokat biztosítanak a [digitális ikrek](concepts-twins-graph.md) számára a későbbi követéshez és létrehozáshoz. A rendszer a **digitális Twins Definition Language (DTDL)** nevű JSON-szerű nyelven íródott.

Az Azure digitális Twins-megoldások létrehozásának első lépéseként legalább egy modellt DTDL-fájlban kell meghatározni.

Hozzon létre egy *SampleModel.js* nevű új *. JSON* fájlt a címtárban, ahol létrehozta a projektet. Illessze be a következő fájlt a szövegtörzsbe: 

:::code language="json" source="~/digital-twins-docs-samples/models/SampleModel.json":::

> [!TIP]
> Ha a Visual studiót használja ehhez az oktatóanyaghoz, érdemes kijelölni az újonnan létrehozott JSON-fájlt, és a tulajdonság-ellenőrben a *Másolás a kimeneti könyvtárba* tulajdonságot úgy kell beállítani, hogy az *újabb* vagy a *Másolás Always*. Ez lehetővé teszi a Visual Studio számára, hogy megkeresse a JSON-fájlt az alapértelmezett elérési úttal, amikor az oktatóanyag többi részében futtatja a programot az **F5 billentyűvel** .

> [!TIP] 
> A DTDL érvényességének ellenőrzéséhez használhatja a Language-agnosztikus [DTDL-érvényesítő mintát](/samples/azure-samples/dtdl-validator/dtdl-validator) , amellyel ellenőrizhetők a modell dokumentumai. A szolgáltatás a DTDL-elemző könyvtárra épül, amelyről további információt a modellek elemzése [*és ellenőrzése*](how-to-parse-models.md)című témakörben olvashat.

Ezután adjon hozzá még egy kódot a *programhoz. cs* , és töltse fel az imént létrehozott modellt az Azure Digital Twins-példányba.

Először adjon hozzá néhány `using` utasítást a fájl elejéhez:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_dependencies":::

Következő lépésként készítse elő az aszinkron metódusok használatát a C# Service SDK-ban úgy, hogy módosítja a `Main` metódus aláírását, hogy engedélyezze az aszinkron végrehajtást. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Async_signature":::

> [!NOTE]
> `async`A használata nem feltétlenül szükséges, mivel az SDK az összes hívás szinkron verzióját is tartalmazza. Az oktatóanyag gyakorlata a használatával `async` .

A következő lépés az Azure Digital Twins szolgáltatással kommunikáló kód első kis része. Ez a kód betölti a lemezről létrehozott DTDL-fájlt, majd feltölti az Azure Digital Twins Service-példányba. 

Illessze be a következő kódot a korábban hozzáadott engedélyezési kód alá.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp_excerpt_model.cs":::

A parancsablakban futtassa a programot a következő paranccsal: 

```cmd/sh
dotnet run
```
A "modell feltöltése" a kimenetben jelenik meg, amely jelzi, hogy a rendszer elérte ezt a kódot, de még nincs kimenet, amely jelzi, hogy a feltöltés sikeres volt-e.

Ha olyan Print utasítást szeretne hozzáadni, amely az összes olyan modellt tartalmazza, amely sikeresen fel lett töltve a példányba, adja hozzá a következő kódot közvetlenül az előző szakasz után:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Print_model":::

**Mielőtt újra futtatja a programot az új kód teszteléséhez**, emlékezteti arra, hogy a program legutóbbi futtatásakor már feltöltötte a modellt. Az Azure Digital Twins nem teszi lehetővé kétszer ugyanazt a modellt, így ha újra megpróbálja feltölteni ugyanazt a modellt, a program kivételt jelez.

Ezt szem előtt tartva futtassa újra a programot ezzel a paranccsal a parancssori ablakban:

```cmd/sh
dotnet run
```

A program kivételt jelez. Ha olyan modellt próbál meg feltölteni, amely már fel van töltve, a szolgáltatás "hibás kérés" hibát ad vissza a REST APIon keresztül. Ennek eredményeképpen az Azure-beli digitális Twins ügyféloldali SDK kivételt jelez minden szolgáltatáshoz, amely nem a sikertől függ. 

A következő szakasz a kivételeket tárgyalja, és azt, hogyan kezelheti őket a kódban.

### <a name="catch-errors"></a>Fogási hibák

Ahhoz, hogy a program összeomlik, a modell feltöltési kódjában adhat hozzá kivételi kódot. A meglévő ügyfél hívásának becsomagolása `await client.CreateModelsAsync(typeList)` egy try/catch kezelőben, például:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_try_catch":::

Ha most futtatja a programot a `dotnet run` parancssori ablakban, látni fogja, hogy hibakódot kap vissza. A modell létrehozási kódjának kimenete a következő hibaüzenetet jeleníti meg:

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="A program kimenete, amely a &quot;409: Service kérelem sikertelen&quot; üzenetet mutatja. Állapot: 409 (ütközés). ', majd egy hibaüzenet jelenik meg, amely azt jelzi, hogy a dtmi: példa: SampleModel; 1 már létezik":::

Ettől a ponttól kezdve az oktatóanyag a kipróbálási és a fogási kezelőben a szolgáltatási metódusok összes hívását lezárja.

### <a name="create-digital-twins"></a>Digitális ikerpéldányok létrehozása

Most, hogy feltöltött egy modellt az Azure digitális Twins-ba, a modell definíciójában **digitális ikreket** hozhat létre. A [digitális ikrek](concepts-twins-graph.md) egy modell példányai, és az üzleti környezetben található entitásokat képviselik, például a farmon lévő érzékelők, a helyiségek egy épületben vagy egy autóban található fények. Ez a szakasz néhány digitális ikreket hoz létre a korábban feltöltött modell alapján.

Adja hozzá a következő kódot a metódus végéhez a `Main` modell alapján három digitális ikrek létrehozásához és inicializálásához.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Initialize_twins":::

A parancsablakban futtassa a programot a paranccsal `dotnet run` . A kimenetben keresse meg azokat a nyomtatási üzeneteket, amelyeket a *sampleTwin-0*, a *sampleTwin-1* és a *sampleTwin-2* hoztak létre. 

Ezután futtassa újra a programot. 

Figyelje meg, hogy az ikrek létrehozásakor a második alkalommal nem fordul elő hiba, még akkor is, ha az ikrek már léteznek az első futtatása után. A modell-létrehozástól eltérően a kettős létrehozás, a REST szinten, egy *put* hívás *upsert* szemantikai feladatokkal. Ez azt jelenti, hogy ha egy iker már létezik, akkor a létrehozási kísérlet során a rendszer csak az eredeti IKeret váltja fel. Nem történt hiba.

### <a name="create-relationships"></a>Kapcsolatok létrehozása

Ezután létrehozhat **kapcsolatokat** a létrehozott ikrek között, és összekapcsolhatja őket egy **különálló gráfban**. A [két gráf](concepts-twins-graph.md) a teljes környezet ábrázolására szolgál.

Adjon hozzá egy **új statikus metódust** a `Program` osztályhoz a `Main` metódus alatt (a kód mostantól két módszerrel rendelkezik):

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Create_relationship":::

Ezután adja hozzá a következő kódot a metódus végéhez `Main` , hogy meghívja a `CreateRelationship` metódust, és használja az imént írt kódot:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_create_relationship":::

A parancsablakban futtassa a programot a paranccsal `dotnet run` . A kimenetben keresse meg a nyomtatási utasításokat, mondván, hogy a két kapcsolat sikeresen létrejött.

Vegye figyelembe, hogy az Azure Digital Twins nem teszi lehetővé, hogy kapcsolatot hozzon létre, ha már létezik egy másik kapcsolat ugyanazzal az AZONOSÍTÓval, tehát ha többször futtatja a programot, akkor a kapcsolatok létrehozásával kapcsolatos kivételek jelennek meg. Ez a kód elveszi a kivételeket, és figyelmen kívül hagyja őket. 

### <a name="list-relationships"></a>Kapcsolatok listázása

A hozzáadni kívánt következő kód lehetővé teszi, hogy megtekintse a létrehozott kapcsolatok listáját.

Adja hozzá a következő **új metódust** a `Program` osztályhoz:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="List_relationships":::

Ezután adja hozzá a következő kódot a metódus végéhez a `Main` kód meghívásához `ListRelationships` :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_list_relationships":::

A parancsablakban futtassa a programot a paranccsal `dotnet run` . A következőhöz hasonló kimeneti utasításban létrehozott összes kapcsolat listáját meg kell tekinteni:

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="A program kimenete egy üzenet, amely azt jelzi, hogy a &quot;Twin sampleTwin-0 csatlakozik a következőhöz: tartalmaz->sampleTwin-1,-tartalmazza->sampleTwin-2&quot;" lightbox="media/tutorial-code/list-relationships.png":::

### <a name="query-digital-twins"></a>Digitális ikrek lekérdezése

Az Azure Digital Twins egyik fő funkciója, hogy könnyen és hatékonyan [lekérdezheti](concepts-query-language.md) a Twin Graphot a környezettel kapcsolatos kérdések megválaszolásához.

Az oktatóanyagban felvenni kívánt kód utolsó szakasza egy lekérdezést futtat az Azure Digital Twins-példányon. Az ebben a példában használt lekérdezés az összes digitális ikreket adja vissza a példányban.

Adja hozzá ezt az `using` utasítást az osztály használatának engedélyezéséhez a `JsonSerializer` digitális Twin-információk megjelenítéséhez:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_dependencies":::

Ezután adja hozzá a következő kódot a metódus végéhez `Main` :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_twins":::

A parancsablakban futtassa a programot a paranccsal `dotnet run` . Ebben a példányban az összes digitális Twins megjelenik a kimenetben.

## <a name="complete-code-example"></a>Példa teljes kódra

Az oktatóanyag ezen pontján egy teljes ügyfélalkalmazás van, amely képes az Azure digitális Twins alapszintű műveleteinek elvégzésére. A program *. cs* programban a program teljes kódját az alábbi lista tartalmazza:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag elvégzése után kiválaszthatja, hogy mely erőforrásokat szeretné eltávolítani, attól függően, hogy mit szeretne tenni.

* **Ha azt tervezi, hogy folytatja a következő oktatóanyagot**, az oktatóanyagban használt példány újra felhasználható a következőben. Megtarthatja az itt beállított Azure digitális Twins-erőforrásokat, és kihagyhatja a szakasz további részeit.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Érdemes törölni a Project mappát is a helyi gépről.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozta a .NET-konzol ügyfélprogramját a semmiből. Az ügyfélalkalmazás kódját az Azure Digital Twins-példányon végzett alapszintű műveletek végrehajtásához írta.

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan teheti meg az ilyen minta ügyfélprogramot: 

> [!div class="nextstepaction"]
> [*Oktatóanyag: az alapok megismerése egy minta ügyfélalkalmazás alkalmazásával*](tutorial-command-line-app.md)
