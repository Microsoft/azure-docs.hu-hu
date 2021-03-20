---
title: A ontológiákat kiterjesztése
titleSuffix: Azure Digital Twins
description: Ismerje meg az ontológia kibővítése mögötti okokat és stratégiákat
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e5973f58887b212919ad739232faafddcf9e735c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100561398"
---
# <a name="extending-ontologies"></a>A ontológiákat kiterjesztése 

Az iparági szabványnak megfelelő [ontológia](concepts-ontologies.md), mint például a [DTDL-alapú RealEstateCore ontológia intelligens épületekhez](https://github.com/Azure/opendigitaltwins-building), nagyszerű módszer a IoT-megoldás kialakításának megkezdéséhez. Az iparági ontológiákat az alapszintű felületek gazdag készletét biztosítják, amelyek a tartományhoz lettek tervezve, és az Azure IoT Services, például az Azure Digital Twins szolgáltatásban működő dobozból is dolgozhatnak. 

Előfordulhat azonban, hogy a megoldás olyan konkrét igényekkel is rendelkezhet, amelyeket nem érint az iparági ontológia. Előfordulhat például, hogy össze szeretné kapcsolni a digitális ikreket egy különálló rendszeren tárolt 3D-modellekhez. Ebben az esetben kiterjesztheti az egyik ontológiákat a saját képességeinek hozzáadására, miközben megőrizheti az eredeti ontológia összes előnyét.

Ez a cikk a DTDL-alapú [RealEstateCore](https://www.realestatecore.io/) ontológia-t használja a ONTOLÓGIÁKAT új DTDL tulajdonságokkal való kiterjesztésére szolgáló példák alapjául. Az itt ismertetett módszerek általánosak, és a DTDL-alapú ontológia bármely részén alkalmazhatók bármilyen DTDL képességgel (telemetria, tulajdonság, kapcsolat, összetevő vagy parancs). 

## <a name="realestatecore-space-hierarchy"></a>RealEstateCore 

A DTDL-alapú RealEstateCore ontológia a Space hierarchia különböző típusú szóközök meghatározására szolgál: szobák, épületek, zóna stb. A hierarchia az egyes modellektől különböző típusú helyiségek, épületek és zónák meghatározására szolgál. 

A hierarchia egy része az alábbi diagramhoz hasonlít. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-original.png" alt-text="A flow diagram a RealEstateCore terület hierarchiájának részét szemlélteti. A legfelső szinten van egy Space nevű elem; egy &quot;kiterjesztő&quot; nyíllal van összekötve egy szintről a helyiségbe; A helyiség két &quot;kiterjesztő&quot; nyíllal van összekötve egy szintről a ConferenceRoom és az Office-ra."::: 

További információ a RealEstateCore ontológia-ról: az [*iparági szabványnak megfelelő ontológiákat bevezetése*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology).

## <a name="extending-the-realestatecore-space-hierarchy"></a>A RealEstateCore terület hierarchiájának kiterjesztése 

Előfordulhat, hogy a megoldásnak konkrét igényei vannak, amelyeket nem érint az iparági ontológia. Ebben az esetben a hierarchia kibővítése lehetővé teszi, hogy továbbra is használhassa az iparági ontológia-t, miközben az igényeinek megfelelően testre szabhatja. 

Ebben a cikkben két különböző esetet tárgyalunk, amelyekben az ontológia hierarchiájának kiterjesztése hasznos: 

* Új felületek hozzáadása az iparági ontológia-ben nem szereplő fogalmakhoz. 
* További tulajdonságok (vagy kapcsolatok, összetevők, telemetria vagy parancsok) hozzáadása a meglévő interfészekhez.

### <a name="add-new-interfaces-for-new-concepts"></a>Új felületek hozzáadása új fogalmakhoz 

Ebben az esetben a megoldáshoz szükséges, de az iparági ontológia-ben nem szereplő fogalmakat kell hozzáadnia. Ha például a megoldás más típusú szobákat tartalmaz, amelyek nem szerepelnek a DTDL-alapú RealEstateCore ontológia-ben, akkor a RealEstateCore-felületek közvetlen kibővítésével adhat hozzájuk. 

Az alábbi példa olyan megoldást mutat be, amelynek a "Focus Rooms" kifejezésnek kell megfelelnie, amelyek nem szerepelnek a RealEstateCore ontológia-ben. A fókuszban lévő helyiség egy kis méretű terület, amely arra szolgál, hogy a felhasználók egy adott tevékenységre összpontosítsanak egy adott időpontban. 

Az iparági ontológia ezen új koncepcióval való kibővítéséhez hozzon létre egy új felületet, amely az iparági ontológia-ben található felületekből is [kiterjeszthető.](concepts-models.md#model-inheritance) 

A fókuszos helyiség felületének hozzáadása után a kiterjesztett hierarchia az új szobatípus típusát jeleníti meg. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-1.png" alt-text="A flow diagram a RealEstateCore tér hierarchiáját mutatja be, új hozzáadással. A ConferenceRoom és az Office alsó szintjén található egy új, FocusRoom nevű elem (amely szintén egy &quot;kibővíthető&quot; nyíllal van összekötve a helyiségből)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>További funkciók hozzáadása meglévő interfészekhez 

Ebben az esetben további tulajdonságokat (vagy kapcsolatokat, összetevőket, telemetria vagy parancsokat) szeretne hozzáadni az iparági ontológia-ben található felületekhez.

Ebben a szakaszban két példát láthat: 
* Ha olyan megoldást hoz létre, amely egy már meglévő rendszerbeli szóközökből álló 3D-rajzokat jelenít meg, érdemes lehet minden digitális Twin-t hozzárendelni a 3D-rajzhoz (azonosító alapján), hogy amikor a megoldás információkat jelenít meg a területről, a meglévő rendszerből is lekérheti a 3D-rajzot. 
* Ha a megoldásnak követnie kell a konferenciatermek online/offline állapotát, akkor érdemes lehet nyomon követni a konferenciaterem állapotát a megjelenített és a lekérdezésekben való használatra. 

Mindkét példa az új tulajdonságokkal valósítható meg: egy olyan `drawingId` tulajdonság, amely a 3D-rajzot a digitális Twin és egy "online" tulajdonsággal társítja, amely jelzi, hogy a konferenciaterem online állapotban van-e. 

Általában nem kívánja közvetlenül módosítani az iparági ontológia-t, mert szeretné a jövőben is beépíteni azt a megoldásba (amely felülírja a hozzáadásokat). Ehelyett az ilyen típusú kiegészítések a saját interfész-hierarchiában hozhatók létre, amely kiterjeszthető a DTDL-alapú RealEstateCore ontológia-ból. Minden egyes létrehozott felület több illesztőfelület-öröklést használ a fölérendelt RealEstateCore felületének és a fölérendelt felületének a kiterjesztett felületi hierarchiából való kibővítéséhez. Ez a megközelítés lehetővé teszi az iparági ontológia és a hozzájuk tartozó kiegészítések használatát. 

Az iparági ontológia kibővítéséhez létre kell hoznia egy saját felületet, amely az iparági ontológia felületeit bővíti, és hozzáadja az új képességeket a kiterjesztett interfészekhez. Minden egyes kiterjeszteni kívánt csatolóhoz létre kell hoznia egy új felületet. A kiterjesztett felületek a DTDL-ben íródnak (lásd a jelen dokumentum későbbi, a kiterjesztett felületek DTDL című szakaszát). 

A fent bemutatott hierarchia részének kibővítését követően a kiterjesztett hierarchia az alábbi ábrához hasonlóan néz ki. Itt a kiterjesztett terület felülete hozzáadja azt a `drawingId` tulajdonságot, amely tartalmaz egy azonosítót, amely a digitális Twin-et a 3D-rajzzal társítja. Emellett a ConferenceRoom felület egy "online" tulajdonságot is tartalmaz, amely tartalmazza a konferenciaterem online állapotát. Az öröklés révén a ConferenceRoom felület a RealEstateCore ConferenceRoom felület összes funkcióját, valamint a kiterjesztett terület felületének összes funkcióját tartalmazza. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-2.png" alt-text="A flow diagram a fentiekből álló kiterjesztett RealEstateCore-hierarchiát mutatja be, és további új kiegészítéseket is nyújt. A Room most megosztja a szintjét egy szóköz elemmel, amely a ConferenceRoom és az Office mellett egy új, &quot;kiterjeszt&quot; nyílra mutat.  Az új elemek a már meglévő ontológia-kapcsolatokhoz kapcsolódnak."::: 

## <a name="using-the-extended-space-hierarchy"></a>A kibővített terület hierarchiájának használata 

Amikor digitális ikreket hoz létre a kibővített lemezterület-hierarchiával, minden egyes digitális Twin modell a kibővített hely hierarchiájának egyike lesz (nem az eredeti iparági ontológia), és az iparági ontológia és a kiterjesztett felületek összes funkcióját tartalmazza, bár az illesztőfelület öröklődése.

Mindegyik digitális Twin modell a kiterjesztett hierarchia egy felülete lesz, amely az alábbi ábrán látható. 
 
:::image type="content" source="media/concepts-extending-ontologies/ontology-with-models.png" alt-text="Egy részlet a kiterjesztett RealEstateCore, beleértve a terület (legfelső szint), egy helyiség (középső szint) és a ConferenceRoom, az Office és a FocusRoom (alacsonyabb szint). A modellek nevei az egyes elemekhez vannak csatlakoztatva (például a helyiség egy Room101 nevű modellhez csatlakozik)."::: 

A digitális ikreknek a modell-AZONOSÍTÓval (a `IS_OF_MODEL` kezelővel) való lekérdezésekor a kibővített hierarchiából származó modell-azonosítókat kell használni. Például: `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Hozzájárulás az eredeti ontológia-hoz 

Bizonyos esetekben az iparági ontológia-t olyan módon kell kiterjeszteni, amely széles körben hasznos az ontológia legtöbb felhasználója számára. Ebben az esetben érdemes megfontolni a bővítmények visszaadását az eredeti ontológia számára. Minden ontológia más folyamattal járul hozzá, ezért az ontológia GitHub-tárházában tájékozódhat a hozzájárulás részleteiről. 

## <a name="dtdl-for-new-interfaces"></a>Új felületek DTDL 

A közvetlenül az iparági ontológia-ből kiterjesztő új felületek DTDL így néz ki. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DTDL kiterjesztett illesztőfelületekhez 

A kibővített felületek DTDL, amely a fent tárgyalt részre korlátozódik, a következőképpen néz ki:. 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>Következő lépések

Folytassa a modellek fejlesztésének elérési útját a ontológiákat alapján: az [*ontológia-stratégiák használatával a modell fejlesztési útvonalán*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).