---
title: A naplók konvertálása
titleSuffix: Azure Digital Twins
description: Az iparági szabványnak megfelelő modellek DTDL-ként való konvertálásának Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 22b41fce59bf7dbe9db1186036c5ed44f07a4aad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484479"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Iparági szabványú ralogik konvertálása DTDL-ként Azure Digital Twins

A [legtöbb onlogies](concepts-ontologies.md) olyan szemantikai webes szabványokon alapul, mint [például az AZ,](https://www.w3.org/OWL/) [RDF](https://www.w3.org/2001/sw/wiki/RDF)és [RDFS.](https://www.w3.org/2001/sw/wiki/RDFS) 

A modellnek DTDL Azure Digital Twins kell lennie. Ez a cikk általános tervezési útmutatót ír **le** az RDF-alapú modellek DTDL-ről DTDL-be konvertálására szolgáló konverziós minta formájában, hogy azok használhatók Azure Digital Twins. 

A cikk egy [**RDF-**](#converter-samples) és EGYRED-átalakító mintakódját is tartalmazza, amely kiterjeszthető az épületipar más sémáira is.

## <a name="conversion-pattern"></a>Konverziós minta

Számos külső kódtár használható az RDF-alapú modellek DTDL-be konvertálásához. Ezen kódtárak némelyike lehetővé teszi a modellfájl gráfba való betöltését. A gráfon végighurkolva konkrét RDFS- és EGYRED-szerkezeteket keres, és átalakíthatja őket DTDL-ként.   

Az alábbi táblázat egy példát mutat be arra, hogyan lehet RDFS- és EGYRED-szerkezeteket leképezni a DTDL-be. 

| RDFS/FOGALMAZÁS | RDFS/EGYENSÚDB-szerkezet | A DTDL fogalma | DTDL-szerkezet |
| --- | --- | --- | --- |
| Osztályok | `owl:Class`<br>IRI-utótag<br>``rdfs:label``<br>``rdfs:comment`` | Interfész | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Alosztályok | `owl:Class`<br>IRI-utótag<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interfész | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Adattípus tulajdonságai | `owl:DatatypeProperty`<br>`rdfs:label` vagy `INode`<br>`rdfs:label`<br>`rdfs:range` | Csatoló tulajdonságai | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Objektum tulajdonságai | `owl:ObjectProperty`<br>`rdfs:label` vagy `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Kapcsolat | `type:Relationship`<br>`name`<br>`target` (vagy kihagyva, ha nem `rdfs:range` )<br>`comment`<br>`displayName`<br>

Az alábbi C#-kódrészlet bemutatja, hogyan töltődik be egy RDF-modellfájl egy gráfba, és hogyan konvertálható DTDL-re a [**dotNetRDF kódtár**](https://www.dotnetrdf.org/) használatával. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Átalakító minták

### <a name="rdf-converter-application"></a>RDF-átalakító alkalmazás 

Elérhető egy mintaalkalmazás, amely egy RDF-alapú modellfájlt alakít át [DTDL-re (2-es verzió)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) a Azure Digital Twins számára. Érvényesítve lett a [Brick](https://brickschema.org/ontology/) sémára, és kiterjeszthető az építőipar egyéb sémáira is (például [Topológia-topológia (BOT),](https://w3c-lbd-cg.github.io/bot/) [Szemantikus](https://www.w3.org/TR/vocab-ssn/)érzékelő hálózat vagy [buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

A minta egy **RdfToDtdlConverter** nevű .NET Core parancssori alkalmazás.

A mintát itt kaphatja meg: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

A kód gépre való letöltéséhez válassza a mintaoldal cím alatti Kód tallózása gombot, amellyel a minta GitHub-adattárába érhetők el.  A minta **letöltéséhez** válassza a Kód gombot és a **ZIP** letöltése *lehetőséget. ZIP-fájl* néven *RdfToDtdlConverter-main.zip.* Ezután csomagolja ki a fájlt, és vizsgálja meg a kódot.

:::image type="content" source="media/concepts-ontologies-convert/download-repo-zip.png" alt-text="Képernyőkép az RdfToDtdlConverter adattárról a GitHubon. A Kód gomb van kiválasztva, amely egy kis párbeszédpanelt hoz, ahol a ZIP letöltése gomb ki van emelve." lightbox="media/concepts-ontologies-convert/download-repo-zip.png":::

Ezzel a mintával kontextusban is láthatja a konverziós mintákat, és építőelemként használhatja a saját alkalmazásai számára a modellátalakításokat a saját igényeinek megfelelően.

### <a name="owl2dtdl-converter"></a>EGYREDTDL-átalakító 

Az [**EGYEK2DTDL-átalakító**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) egy olyan minta, amely EGY OLYAN ONTTológiát fordít le DTDL interfészdeklarációk készletére, amelyek az Azure Digital Twins szolgáltatással használhatók. Deklarációkon keresztül más onlogiákat is újrahasználó, egy gyökér ontológiából készült ontológiahálózatok `owl:imports` esetében is működik.

Ezzel az átalakítóval lefordítható a [Real Estate Core Ontology](https://doc.realestatecore.io/3.1/full.html) DTDL-re, és bármely, AZ-alapú ontológiához használható.

## <a name="next-steps"></a>Következő lépések 

* További információ az iparági szabványoknak a specifikációknak való megfelelő kiterjesztéséről: [*Fogalmak: Az iparági ralogizációk kiterjesztése.*](concepts-ontologies-extend.md)

* Vagy folytassa az ontológián alapuló modellek fejlesztésének útvonalával: [*Ontológiastratégiák használata modellfejlesztési útvonalon.*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)