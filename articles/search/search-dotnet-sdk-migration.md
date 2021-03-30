---
title: Frissítés a Azure Search .NET SDK 3-as verziójára
titleSuffix: Azure Cognitive Search
description: Telepítse át a kódot a Azure Search .NET SDK 3-as verziójára régebbi verzióról. Ismerje meg az újdonságokat és a szükséges programkód-módosításokat.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e9f4602582a8c24f74d6603b732e7b91d97fa8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89002691"
---
# <a name="upgrade-to-azure-search-net-sdk-version-3"></a>Frissítés a Azure Search .NET SDK 3-as verziójára

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Ha a [Azure Search .net SDK](/dotnet/api/overview/azure/search)-hoz készült 2,0-es vagy régebbi verzióját használja, ez a cikk segítséget nyújt az alkalmazás 3. verzióra való frissítéséhez.

Az SDK-val kapcsolatos általános áttekintést a példákat lásd: [Azure Search használata .NET-alkalmazásokból](search-howto-dotnet-sdk.md).

A Azure Search .NET SDK 3. verziója a korábbi verziók néhány módosítását tartalmazza. Ezek többnyire kisebbek, ezért a kód módosítása csak minimális erőfeszítést igényelhet. Az új SDK-verzió használatára vonatkozó utasításokért lásd: a [verziófrissítés lépései](#UpgradeSteps) .

> [!NOTE]
> Ha a 1.0.2-Preview vagy régebbi verziót használja, először frissítsen az 1,1-es verzióra, majd frissítsen a 3-as verzióra. Útmutatásért lásd: [a Azure Search .net SDK 1,1-es verziójának frissítése](search-dotnet-sdk-migration-version-1.md) .
>
> Az Azure Search Service-példány számos REST API verziót támogat, beleértve a legújabbat is. Továbbra is használhatja a verziót, ha már nem a legújabb, de javasoljuk, hogy a legújabb verzió használatára telepítse át a kódot. A REST API használatakor az API-verziót minden kérelemben meg kell adnia az API-Version paraméter használatával. A .NET SDK használatakor a használt SDK verziója meghatározza a REST API megfelelő verzióját. Ha régebbi SDK-t használ, továbbra is futtathatja ezt a kódot, még akkor sem, ha a szolgáltatás frissítve van egy újabb API-verzió támogatására.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>A 3. verzió újdonságai
A Azure Search .NET SDK 3. verziója a Azure Search REST API legújabb általánosan elérhető verzióját célozza meg, pontosabban 2016-09-01. Ez lehetővé teszi a Azure Search számos új funkciójának használatát egy .NET-alkalmazásból, beleértve a következőket:

* [Egyéni elemzők](index-add-custom-analyzers.md)
* [Azure blob Storage](search-howto-indexing-azure-blob-storage.md) és [Azure Table Storage](search-howto-indexing-azure-tables.md) indexelő támogatása
* Indexelő testreszabása [mező-hozzárendelések](search-indexer-field-mappings.md) használatával
* Etagek-támogatás az index-definíciók, az indexelő és az adatforrások biztonságos párhuzamos frissítésének engedélyezéséhez
* Az index mező-definíciók deklaratív kialakításának támogatása a modell osztályának díszítésével és az új `FieldBuilder` osztály használatával.
* A .NET Core és a .NET hordozható profil 111 támogatása

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései
Először frissítse a NuGet-referenciát a `Microsoft.Azure.Search` NuGet csomagkezelő konzoljának használatára, vagy kattintson a jobb gombbal a projekt hivatkozásaira, és válassza a "NuGet-csomagok kezelése..." lehetőséget. a Visual Studióban.

Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. A kód szerkezetének módjától függően előfordulhat, hogy az Újraépítés sikeresen megtörtént. Ha igen, készen állsz!

Ha a Build sikertelen, a következőhöz hasonló fordítási hibaüzenetnek kell megjelennie:

```output
Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)
```

A következő lépés a Build-hiba kijavítása. A hiba okaival és megoldásával kapcsolatos részletekért tekintse meg a [3. verzióban](#ListOfChanges) megjelenő változásokat.

Előfordulhat, hogy az elavult metódusokkal vagy tulajdonságokkal kapcsolatos további felépítési figyelmeztetések jelennek meg. A figyelmeztetések tartalmazzák az elavult funkció helyett a használatra vonatkozó utasításokat is. Ha például az alkalmazás a `IndexingParameters.Base64EncodeKeys` tulajdonságot használja, a következő figyelmeztetést kell kérnie: `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

A felépítési hibák kijavítása után módosíthatja az alkalmazást, és igény szerint kihasználhatja az új funkciókat. Az SDK új funkciói részletesen ismertetik a [3. verzió újdonságait](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>A 3. verzióban feltört változások
A 3. verzióban kevés a feltörési változás, amely az alkalmazás újraépítése mellett kód módosítását is szükségessé teheti.

### <a name="indexesgetclient-return-type"></a>Indexek. GetClient visszatérési típusa
A `Indexes.GetClient` metódus új visszatérési típussal rendelkezik. Korábban visszatért `SearchIndexClient` , de ez a `ISearchIndexClient` 2,0-es verzióra módosult, és ez a változás a 3. verziót veszi át. Ez az olyan ügyfelek támogatása, akik az egységbeli tesztek módszerét szeretnék kipróbálni, ha a `GetClient` modelljét a következő módon implementálják: `ISearchIndexClient` .

#### <a name="example"></a>Példa
Ha a kód így néz ki:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

A fordítási hibák kijavításához módosíthatja a következőt:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>A AnalyzerName, az adattípus és mások már nincsenek implicit módon konvertálva karakterlánccá
A-ból származó Azure Search .NET SDK számos típussal rendelkezik `ExtensibleEnum` . Korábban ezek a típusok implicit módon lettek konvertálva a típusra `string` . Azonban a rendszer hibát észlelt `Object.Equals` ezen osztályok megvalósításában, és az implicit konverzió letiltásához szükséges hibát rögzíti. A explicit átalakítás `string` továbbra is engedélyezett.

#### <a name="example"></a>Példa
Ha a kód így néz ki:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

A fordítási hibák kijavításához módosíthatja a következőt:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Elavult tagok eltávolítva

Előfordulhat, hogy az 2,0-es verzióban elavultként megjelölt metódusokkal vagy tulajdonságokkal kapcsolatos fordítási hibák jelennek meg, amelyek a 3. verzióban már el lettek távolítva. Ha ilyen hibák merülnek fel, az alábbi módon oldható meg:

- Ha ezt a konstruktort használta: `ScoringParameter(string name, string value)` , használja ezt helyette: `ScoringParameter(string name, IEnumerable<string> values)`
- Ha a `ScoringParameter.Value` tulajdonságot használta, használja `ScoringParameter.Values` helyette a tulajdonságot vagy a `ToString` metódust.
- Ha a `SearchRequestOptions.RequestId` tulajdonságot használta, használja `ClientRequestId` helyette a tulajdonságot.

### <a name="removed-preview-features"></a>Előzetes verziójú funkciók eltávolítva

Ha a 2,0-es verzióról frissít a 3. verzióra, vegye figyelembe, hogy a JSON-és CSV-elemzési támogatás a blob indexekhez szolgáltatás el lett távolítva, mivel ezek a funkciók még előzetes verziójúak. Az osztály következő módszerei `IndexingParametersExtensions` el lettek távolítva:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Ha az alkalmazás ezen funkcióktól függ, nem fog tudni frissíteni a Azure Search .NET SDK 3. verziójára. Továbbra is használhatja a 2,0-es verziót – előzetes verzió. Ne feledje azonban, hogy az **előnézeti SDK-k éles alkalmazásokban való használatát nem javasoljuk**. Az előzetes verziójú funkciók csak értékelésre használhatók, és változhatnak.

## <a name="conclusion"></a>Összegzés
Ha további részletekre van szüksége a Azure Search .NET SDK használatával kapcsolatban, tekintse meg a [.net útmutató](search-howto-dotnet-sdk.md)című témakört.

Üdvözöljük az SDK-val kapcsolatos visszajelzéseit. Ha problémákba ütközik, kérjen segítséget a [stack overflow](https://stackoverflow.com/questions/tagged/azure-search). Ha hibát talál, a probléma az [Azure .net SDK GitHub-tárházában](https://github.com/Azure/azure-sdk-for-net/issues)is megadható. Ügyeljen arra, hogy a probléma címét "[Azure Search]" előtaggal adja meg.

Köszönjük Azure Search használatát!