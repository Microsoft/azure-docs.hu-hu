---
title: Ontológiákat konvertálása
titleSuffix: Azure Digital Twins
description: Ismerje meg az iparági szabványnak megfelelő modellek átalakításának folyamatát az Azure Digital Twins DTDL
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: aa4dde51c077152dd5c8a938ad64ad0a051f89ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100561751"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Az iparági szabványnak megfelelő ontológiákat átalakítása az Azure Digital Twins DTDL

A legtöbb [ontológiákat](concepts-ontologies.md) szemantikai webes szabványokon alapulnak, mint például a [bagoly](https://www.w3.org/OWL/), az [RDF](https://www.w3.org/2001/sw/wiki/RDF)és a [kemence](https://www.w3.org/2001/sw/wiki/RDFS). 

Az Azure Digital Twins-modell használatához DTDL formátumban kell megadni. Ez a cikk az RDF-alapú modellek DTDL való átalakítására szolgáló **átalakítási minta** formájában történő általános tervezési útmutatót ismerteti, hogy az Azure Digital Twins használatával is használható legyen. 

A cikk az RDF-hez és a bagoly átalakítóhoz is tartalmaz [**minta-átalakító kódot**](#converter-samples) , amely az építőipar más sémái esetében bővíthető.

## <a name="conversion-pattern"></a>Átalakítási minta

Az RDF-alapú modellek DTDL való konvertálásakor több külső gyártótól származó kódtár is használható. Ezen könyvtárak némelyike lehetővé teszi a modell fájljának betöltését egy gráfba. A diagramon az adott kemence-és bagoly-szerkezeteket keresi, és a DTDL alakíthatja át őket.   

A következő táblázat egy példát mutat be arra, hogy a KEMENCÉk és a bagoly szerkezetek hogyan képezhetők le a DTDL. 

| KEMENCE/bagoly koncepció | KEMENCE/bagoly-összeállítás | DTDL koncepció | DTDL-összeállítás |
| --- | --- | --- | --- |
| Osztályok | `owl:Class`<br>IRI utótag<br>``rdfs:label``<br>``rdfs:comment`` | Interfész | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Alosztályok | `owl:Class`<br>IRI utótag<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interfész | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Adattípus tulajdonságai | `owl:DatatypeProperty`<br>`rdfs:label` vagy `INode`<br>`rdfs:label`<br>`rdfs:range` | Interfész tulajdonságai | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Objektum tulajdonságai | `owl:ObjectProperty`<br>`rdfs:label` vagy `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Kapcsolat | `type:Relationship`<br>`name`<br>`target` (vagy nincs megadva `rdfs:range` )<br>`comment`<br>`displayName`<br>

A következő C# kódrészlet bemutatja, hogyan tölthető be egy RDF-modell egy gráfba, és hogyan konvertálható DTDL a [**dotNetRDF**](https://www.dotnetrdf.org/) -könyvtár használatával. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Átalakító minták

### <a name="rdf-converter-application"></a>RDF átalakító alkalmazás 

Rendelkezésre áll egy minta alkalmazás, amely egy RDF-alapú modellt konvertál a [DTDL (2. verzió)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) az Azure Digital Twins szolgáltatás általi használatra. A rendszer ellenőrizte a [tégla](https://brickschema.org/ontology/) sémát, és kiterjeszthető az építőipar más sémái számára (például a [topológiai ontológia (bot)](https://w3c-lbd-cg.github.io/bot/), a [szemantikai érzékelő hálózat](https://www.w3.org/TR/vocab-ssn/)vagy a [buildingSmart Industry Foundation classs (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

A minta egy **RdfToDtdlConverter** nevű .net Core parancssori alkalmazás.

A mintát itt érheti el: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

A kód a gépre való letöltéséhez nyomja le a *zip letöltése* gombot a minta kezdőlapján a cím alatt. Ezzel letölt egy *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* nevű *zip* -fájlt, amelyet később kibonthat és megvizsgálhat.

Ezzel a mintával megtekintheti az átalakítási mintákat a kontextusban, és létrehozhat építőelemeket a saját igényeinek megfelelő modell-konverziókat végző saját alkalmazások számára.

### <a name="owl2dtdl-converter"></a>OWL2DTDL-átalakító 

A [**OWL2DTDL Converter**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) egy olyan minta, amely a bagoly ontológia-t egy DTDL-felületi deklarációra fordítja, amely az Azure Digital Twins szolgáltatással használható. Emellett az ontológia-hálózatok esetében is működik, amely egy gyökérszintű ontológia-t használ, és más ontológiákat is alkalmaz a `owl:imports` deklarációk segítségével.

Ezt a konvertert használták a [Real Estate Core ONTOLÓGIA](https://doc.realestatecore.io/3.1/full.html) DTDL való lefordítására, és bármely bagoly-alapú ontológia esetében használható.

## <a name="next-steps"></a>Következő lépések 

* További információ az iparági szabványnak megfelelő ontológiákat kibővítéséről a specifikációk teljesítése érdekében: [*fogalmak: az iparági ontológiákat kiterjesztése*](concepts-ontologies-extend.md).

* Vagy folytassa a modellek fejlesztésének elérési útját a ontológiákat alapján: az [*ontológia-stratégiák használatával a modell fejlesztési útvonalán*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).