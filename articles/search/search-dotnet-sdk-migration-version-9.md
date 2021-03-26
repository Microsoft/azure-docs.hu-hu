---
title: Frissítés a Azure Search .NET SDK 9-es verziójára
titleSuffix: Azure Cognitive Search
description: Telepítse át a kódot a Azure Search .NET SDK 9-es verziójára régebbi verzióról. Ismerje meg, hogy mi az új, és milyen kód módosítása szükséges.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 76cb2f94c949217c36fdccb8c64a53555bab227c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606334"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Frissítés a Azure Search .NET SDK 9-es verziójára

Ha a [Azure Search .net SDK](/dotnet/api/overview/azure/search)-hoz készült 7,0-es vagy régebbi verziót használja, ez a cikk segítséget nyújt az alkalmazás a 9-es verzió használatára való frissítéséhez.

> [!NOTE]
> Ha az 8,0-es verzió előzetes verzióját szeretné használni a még nem általánosan elérhető funkciók kiértékeléséhez, a cikk utasításait követve frissítsen a 8,0-Preview verzióra a korábbi verziókról.

Az SDK-val kapcsolatos általános áttekintést a példákat lásd: [Azure Search használata .NET-alkalmazásokból](search-howto-dotnet-sdk.md).

A Azure Search .NET SDK 9-es verziója számos változást tartalmaz a korábbi verziókból. Ezek némelyike megszakítja a módosításokat, de csak viszonylag kisebb módosításokat igényelnek a kódban. Az új SDK-verzió használatára vonatkozó utasításokért lásd: a [verziófrissítés lépései](#UpgradeSteps) .

> [!NOTE]
> Ha a 4,0-es vagy régebbi verziót használja, először az 5-ös verzióra kell frissítenie, majd a 9-es verzióra kell frissítenie. Útmutatásért lásd: [a Azure Search .net SDK 5-ös verziójának frissítése](search-dotnet-sdk-migration-version-5.md) .
>
> Az Azure Search Service-példány számos REST API verziót támogat, beleértve a legújabbat is. Továbbra is használhatja a verziót, ha már nem a legújabb, de javasoljuk, hogy a legújabb verzió használatára telepítse át a kódot. A REST API használatakor az API-verziót minden kérelemben meg kell adnia az API-Version paraméter használatával. A .NET SDK használatakor a használt SDK verziója meghatározza a REST API megfelelő verzióját. Ha régebbi SDK-t használ, továbbra is futtathatja ezt a kódot, még akkor sem, ha a szolgáltatás frissítve van egy újabb API-verzió támogatására.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>A 9-es verzió újdonságai
A Azure Search .NET SDK 9-es verziója a Azure Search REST API 2019-05-06 verziójának a következő funkciókkal rendelkezik:

* Az [AI](cognitive-search-concept-intro.md) -bővítés lehetővé teszi a képek, blobok és egyéb strukturálatlan adatforrások szövegének kinyerését – a tartalom gazdagítása, hogy egy Azure Search indexben jobban kereshető legyen.
* Az [összetett típusok](search-howto-complex-data-types.md) támogatása lehetővé teszi szinte bármilyen beágyazott JSON-struktúra modellezését egy Azure Search indexben.
* Az [automatikus kiegészítés](search-add-autocomplete-suggestions.md) lehetővé teszi a **javasolt** API alternatíváját a keresési típus működésének megvalósításához. Az automatikus kiegészítés "befejezi" a felhasználó által jelenleg begépelt szót vagy kifejezést.
* A [JsonLines-elemzési mód](search-howto-index-json-blobs.md), amely az Azure Blob-indexelés részét képezi, egyetlen keresési dokumentumot hoz létre, amely egy sortöréssel elválasztott JSON-entitáson alapul.

### <a name="new-preview-features-in-version-80-preview"></a>Új előzetes verziójú funkciók az 8,0-es verzióban – előzetes verzió
8,0-es verzió – az Azure Search .NET SDK Targets API 2017-11-11-es verziójának előzetes verziója. Ez a verzió tartalmazza a 9-es verzió összes azonos funkcióját, valamint a következőket:

* Az [ügyfél által felügyelt titkosítási kulcsok](search-security-manage-encryption-keys.md) a szolgáltatás oldali titkosításhoz – a REST-nél új előzetes verziójú funkció. A Microsoft által felügyelt beépített titkosítás mellett további titkosítási réteget is alkalmazhat, amelyben Ön a kulcsok egyetlen tulajdonosa...

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései
Először frissítse a NuGet-referenciát a `Microsoft.Azure.Search` NuGet csomagkezelő konzoljának használatára, vagy kattintson a jobb gombbal a projekt hivatkozásaira, és válassza a "NuGet-csomagok kezelése..." lehetőséget. a Visual Studióban.

Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. A kód szerkezetének módjától függően előfordulhat, hogy az Újraépítés sikeresen megtörtént. Ha igen, készen állsz!

Ha a Build sikertelen, ki kell javítania az egyes Build-hibákat. A lehetséges Build-hibák megoldásával kapcsolatos részletekért tekintse meg a 9. verzióban megjelenő [változások feltörését ismertető részt](#ListOfChanges) .

Előfordulhat, hogy az elavult metódusokkal vagy tulajdonságokkal kapcsolatos további felépítési figyelmeztetések jelennek meg. A figyelmeztetések tartalmazzák az elavult funkció helyett a használatra vonatkozó utasításokat is. Ha például az alkalmazás a `DataSourceType.DocumentDb` tulajdonságot használja, egy figyelmeztetést kap, amely szerint a következő üzenet jelenik meg: "Ez a tag elavult. Használja helyette a következőt: CosmosDb.

A felépítési hibák vagy figyelmeztetések kijavítása után módosíthatja az alkalmazást, hogy igénybe vehesse az új funkciókat. Az SDK új funkciói részletesen ismertetik a [9. verzió újdonságait](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>A 9. verzióban feltört változások

Több, a 9-es verzióban felmerülő változás miatt a kód módosítására is szükség lehet az alkalmazás újraépítése mellett.

> [!NOTE]
> Az alábbi módosítások listája nem teljes. Bizonyos változások valószínűleg nem eredményeznek fordítási hibákat, de technikailag megszakadnak, mivel a bináris kompatibilitást a Azure Search .NET SDK-szerelvények korábbi verzióitól függő szerelvényekkel bontják le. Az alábbi módosítások nem szerepelnek az alábbiakban. A bináris kompatibilitási problémák elkerülése érdekében hozza létre újra az alkalmazást a 9-es verzióra való frissítéskor.

### <a name="immutable-properties"></a>Megváltoztathatatlan tulajdonságok

Számos modell osztály nyilvános tulajdonságai mostantól nem változtathatók meg. Ha az osztályok teszteléshez egyéni példányait kell létrehoznia, használhatja az új paraméteres konstruktorokat:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Mező módosításai

Az `Field` osztály most már megváltozott, és összetett mezőket is jelenthet.

A következő `bool` Tulajdonságok mostantól üresek:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Ennek az az oka, hogy ezeknek a tulajdonságoknak `null` a komplex mezők esetében most kell lenniük. Ha van olyan kód, amely beolvassa ezeket a tulajdonságokat, fel kell készülnie a kezelésére `null` . Vegye figyelembe, hogy az összes többi tulajdonsága `Field` mindig és továbbra is üres, és ezek némelyike `null` összetett mezők esetén is előfordulhat – konkrétan a következők:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

A paraméter nélküli konstruktor lett elvégezve `Field` `internal` . Mostantól minden `Field` esetben explicit névvel és adattípussal kell rendelkeznie az építőiparban.

### <a name="simplified-batch-and-results-types"></a>Egyszerűsített köteg-és eredmények típusai

Az 7,0-es verzióban – előzetes és korábbi verziók esetén a dokumentumok csoportjaiba ágyazott különböző osztályok párhuzamos osztály-hierarchiába vannak strukturálva:

  -  `DocumentSearchResult` és `DocumentSearchResult<T>` öröklés forrása `DocumentSearchResultBase`
  -  `DocumentSuggestResult` és `DocumentSuggestResult<T>` öröklés forrása `DocumentSuggestResultBase`
  -  `IndexAction` és `IndexAction<T>` öröklés forrása `IndexActionBase`
  -  `IndexBatch` és `IndexBatch<T>` öröklés forrása `IndexBatchBase`
  -  `SearchResult` és `SearchResult<T>` öröklés forrása `SearchResultBase`
  -  `SuggestResult` és `SuggestResult<T>` öröklés forrása `SuggestResultBase`

Az általános típusparaméter nélküli származtatott típusok a "dinamikusan gépelt" forgatókönyvekben és a típus feltételezett használatában használhatók `Document` .

Az 8,0-es verziótól kezdődően az alaposztályok és a nem általános származtatott osztályok mind el lettek távolítva. A dinamikusan beírt forgatókönyvek esetében használhatja a, `IndexBatch<Document>` `DocumentSearchResult<Document>` és így tovább.
 
### <a name="removed-extensibleenum"></a>ExtensibleEnum eltávolítva

Az `ExtensibleEnum` alaposztály el lett távolítva. A rendszerből származtatott összes osztály most már struct, például, `AnalyzerName` `DataType` és `DataSourceType` . A `Create` metódusok is el lettek távolítva. Egyszerűen eltávolíthat hívásokat, `Create` mivel ezek a típusok implicit módon vannak konvertálva a karakterláncokból. Ha ez a fordítási hibákat eredményezi, explicit módon meghívhatja az átalakítási operátort a egyértelműsítse-típusok használatával. Például a következőhöz hasonló kódot lehet módosítani:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

módosítsa a következőre:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

Az ilyen típusú opcionális értékeket tartalmazó tulajdonságok mostantól nullára írhatók, így továbbra is opcionálisak lesznek.

### <a name="removed-facetresults-and-hithighlights"></a>FacetResults és HitHighlights eltávolítva

A `FacetResults` és az `HitHighlights` osztályok el lettek távolítva. A dimenziók eredményei mostantól a `IDictionary<string, IList<FacetResult>>` és a találatok szerint vannak beírva `IDictionary<string, IList<string>>` . A módosítás által bevezetett felépítési hibák elhárításának gyors módja, ha az `using` eltávolított típusokat használó fájlok tetején lévő aliasokat ad hozzá. Például:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Váltás a SynonymMap 

A `SynonymMap` konstruktor már nem rendelkezik `enum` paraméterrel a következőhöz: `SynonymMapFormat` . Ez a felsorolás csak egy értéket tartalmazott, ezért redundáns volt. Ha a felépítési hibákat látja ennek eredményeképpen, egyszerűen távolítsa el a paraméterre mutató hivatkozásokat `SynonymMapFormat` .

### <a name="miscellaneous-model-class-changes"></a>Egyéb modell-osztályok módosításai

A `AutocompleteMode` tulajdonsága már `AutocompleteParameters` nem null értékű. Ha olyan kóddal rendelkezik, amely ezt a tulajdonságot hozzárendeli a `null` szolgáltatáshoz, egyszerűen távolítsa el, és a tulajdonság automatikusan inicializálva lesz az alapértelmezett értékre.

A konstruktor paramétereinek sorrendje `IndexAction` most már megváltoztak, hogy ez a konstruktor automatikusan létrejön. A konstruktor használata helyett javasoljuk a gyári metódusok használatát, `IndexAction.Upload` `IndexAction.Merge` stb.

### <a name="removed-preview-features"></a>Előzetes verziójú funkciók eltávolítva

Ha a 8,0-es verzióról a 9-es verzióra frissít, vegye figyelembe, hogy az ügyfél által felügyelt kulcsokkal való titkosítás el lett távolítva, mivel ez a szolgáltatás még előzetes verzióban érhető el. Pontosabban a `EncryptionKey` és a `Index` tulajdonságait `SynonymMap` eltávolították.

Ha az alkalmazás nem rendelkezik a szolgáltatáshoz szükséges függőséggel, akkor nem fog tudni frissíteni a Azure Search .NET SDK 9-es verziójára. Továbbra is használhatja a 8,0-es verziót – előzetes verzió. Ne feledje azonban, hogy az **előnézeti SDK-k éles alkalmazásokban való használatát nem javasoljuk**. Az előzetes verziójú funkciók csak értékelésre használhatók, és változhatnak.

> [!NOTE]
> Ha titkosított indexeket vagy szinonima-térképeket hozott létre a 8,0-es verzióval az SDK előzetes verziójával, akkor továbbra is használhatja őket, és a definícióját az SDK 9-es verziójának használatával módosíthatja, anélkül, hogy ez hátrányosan befolyásolná a titkosítási állapotukat. Az SDK 9-es verziója nem küldi el a `encryptionKey` tulajdonságot a REST APInak, ezért a REST API nem módosítja az erőforrás titkosítási állapotát. 

### <a name="behavioral-change-in-data-retrieval"></a>Az adatok lekérésének viselkedési változása

Ha a "dinamikusan beírt" `Search` , `Suggest` vagy "type" `Get` típusú példányokat visszaadó API-kat használ `Document` , vegye figyelembe, hogy most már deszerializálja az üres JSON-tömböket a `object[]` helyett `string[]` .

## <a name="conclusion"></a>Összegzés
Ha további részletekre van szüksége a Azure Search .NET SDK használatával kapcsolatban, tekintse meg a [.net útmutató](search-howto-dotnet-sdk.md)című témakört.

Üdvözöljük az SDK-val kapcsolatos visszajelzéseit. Ha problémákba ütközik, kérjen segítséget a [stack overflow](https://stackoverflow.com/questions/tagged/azure-search). Ha hibát talál, a probléma az [Azure .net SDK GitHub-tárházában](https://github.com/Azure/azure-sdk-for-net/issues)is megadható. Ügyeljen arra, hogy a probléma címét "[Azure Search]" előtaggal adja meg.

Köszönjük Azure Search használatát!