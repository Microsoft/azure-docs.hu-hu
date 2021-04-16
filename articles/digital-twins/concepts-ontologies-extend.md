---
title: Az ralogik kiterjesztése
titleSuffix: Azure Digital Twins
description: Az ontológia kibővítését indokokkal és stratégiákkal kapcsolatos tudnivalók
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b38b4910773c433ed63fd2082c5cbefce81e0e9e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480230"
---
# <a name="extending-ontologies"></a>Az ralogik kiterjesztése 

Egy iparági szabványnak megfelelő [ontológia,](concepts-ontologies.md)például az intelligens épületek [DTDL-alapú RealEstateCore ontológiája,](https://github.com/Azure/opendigitaltwins-building)remek módja az IoT-megoldás építésének. Az iparági ralogikák számos olyan alapillesztőt biztosítanak, amelyek a tartományhoz vannak kialakítva, és úgy vannak kialakítva, hogy használatra készen is működjön az Azure IoT-szolgáltatásokban, például Azure Digital Twins. 

Előfordulhat azonban, hogy a megoldásnak olyan speciális igényei vannak, amelyekre az iparági ontológia nem terjed ki. Előfordulhat például, hogy a digitális ikereket egy külön rendszerben tárolt 3D-modellekkel szeretné összekapcsolni. Ebben az esetben kiterjesztheti az egyik ilyen onlogikát, hogy saját képességeket adjon hozzá, miközben megtartja az eredeti ontológia összes előnyét.

Ez a cikk a DTDL-alapú [RealEstateCore-ontológiát](https://www.realestatecore.io/) használja az új DTDL-tulajdonságokkal bővítő ontológiák példáihoz. Az itt ismertetett technikák azonban általánosak, és a DTDL-alapú ontológia bármely részére alkalmazhatók bármilyen DTDL-képességgel (telemetria, tulajdonság, kapcsolat, összetevő vagy parancs). 

## <a name="realestatecore-space-hierarchy"></a>RealEstateCore térhierarchia 

A DTDL-alapú RealEstateCore ontológiában a Térhierarchia különböző terek meghatározására szolgál: helyiségek, épületek, zóna stb. A hierarchia ezen modellek mindegyikét kiterjedve különféle helyiségeket, épületeket és zónákat definiál. 

A hierarchia egy része az alábbi diagramhoz hasonló. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-original.png" alt-text="A RealEstateCore térhierarchia egy részét ábrázoló folyamatábra. A legfelső szinten található egy Space (Tér) nevű elem; a helyiségig egy szinttel lefelé mutató &quot;kiterül&quot; nyíllal van csatlakoztatva; A helyiség két &quot;kiterjesztési&quot; nyíllal van összekapcsolva egy szinttel a ConferenceRoom és az Office között."::: 

A RealEstateCore ontológiával kapcsolatos további információkért [*lásd: Alapfogalmak: Iparági szabványnak megfelelő ontológiák elfogadása.*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology)

## <a name="extending-the-realestatecore-space-hierarchy"></a>A RealEstateCore térhierarchia kiterjesztése 

Előfordulhat, hogy a megoldásnak olyan speciális igényei vannak, amelyekre az iparági ontológia nem terjed ki. Ebben az esetben a hierarchia kiterjesztése lehetővé teszi, hogy továbbra is használja az iparági ontológiát, miközben testre szabja az igényeinek megfelelően. 

Ebben a cikkben két különböző esetet tárgyalunk, amelyekben hasznos az ontológia hierarchiájának kiterjesztése: 

* Új interfészek hozzáadása olyan fogalmakhoz, amelyek nem az iparági ontológiában vannak. 
* További tulajdonságok (vagy kapcsolatok, összetevők, telemetria vagy parancsok) hozzáadása a meglévő felületekhez.

### <a name="add-new-interfaces-for-new-concepts"></a>Új felületek hozzáadása az új fogalmakhoz 

Ebben az esetben a megoldáshoz szükséges, de az iparági ontológiában nem jelen bíró fogalmakhoz szeretne interfészeket hozzáadni. Ha például a megoldás más típusú helyiségeket is használ, amelyek nem létezikak a DTDL-alapú RealEstateCore ontológiában, akkor hozzáadhatja őket közvetlenül a RealEstateCore felületekről való kiterjesztésekkel. 

Az alábbi példa egy olyan megoldást mutat be, amelynek "fókusztermeket" kell képviselnie, amelyek nincsenek jelen a RealEstateCore ontológiában. A fókuszterem egy kis terület, amelyet arra terveztek, hogy az emberek egyszerre néhány óráig összpontosítsak egy feladatra. 

Az iparági ontológia új fogalommal való kiterjesztéséhez hozzon létre egy új felületet, amely az iparági ontológia interfészeiből is kiterjed. [](concepts-models.md#model-inheritance) 

A fókuszterem-felület hozzáadása után a kiterjesztett hierarchia megjeleníti az új helyiség típusát. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-1.png" alt-text="A fenti RealEstateCore térhierarchiát ábrázoló folyamatábra egy új összeadással. A ConferenceRoom és az Office legalsó szintjén található egy FocusRoom nevű új elem (amely a Roomból egy &quot;extends&quot; nyíllal is csatlakoztatva van)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>További képességek hozzáadása meglévő felületekhez 

Ebben az esetben további tulajdonságokat (vagy kapcsolatokat, összetevőket, telemetriát vagy parancsokat) szeretne hozzáadni az iparági ontológiában.

Ebben a szakaszban két példát láthat: 
* Ha olyan megoldást hoz létre, amely egy meglévő rendszerben már meglévő térközök 3D-s rajzait jeleníti meg, érdemes lehet minden digitális ikereszközt a 3D-s rajzhoz társítani (azonosító alapján), hogy amikor a megoldás a térre vonatkozó információkat jelenít meg, a meglévő rendszerből is lekérheti a 3D rajzot. 
* Ha a megoldásnak nyomon kell követnie a konferenciatermek online/offline állapotát, akkor érdemes lehet nyomon követni a konferenciaterem állapotát a megjelenítéshez vagy a lekérdezésekhez. 

Mindkét példa új tulajdonságokkal valósítható meg: egy tulajdonsággal, amely a 3D rajzot társítja a digitális ikerhez, és egy "online" tulajdonság, amely jelzi, hogy a konferenciaterem online állapotban `drawingId` van-e vagy sem. 

Általában nem szeretné közvetlenül módosítani az iparági ontológiát, mert a jövőben szeretné beépíteni a frissítéseket a megoldásba (ami felülírná a kiegészítéseket). Ehelyett az ilyen típusú kiegészítések a DTDL-alapú RealEstateCore ontológiától való kiterjesztésű saját felületi hierarchiában is elérhetőek. Minden létrehozott felület több felületöröklést használ a szülő RealEstateCore interfész és a szülőfelület kiterjesztéséhez a kiterjesztett felületi hierarchiából. Ez a megközelítés lehetővé teszi az iparági ontológia és az összeadások együttes használatát. 

Az iparági ontológia kibővítéséhez saját felületeket kell létrehoznia, amelyek az iparági ontológia felületeitől is kiterjednek, és az új képességeket bővítik a kiterjesztett felületeken. Minden kiterjeszteni kívánt felülethez új felületet kell létrehoznia. A bővített interfészek DTDL-ként vannak megírva (lásd a DTDL for Extended Interfaces című szakaszt a dokumentum későbbi részében). 

A fent látható hierarchia részének kibővítését követően a kiterjesztett hierarchia az alábbi ábrához hasonló lesz. Itt a kiterjesztett térfelület hozzáadja a tulajdonságot, amely tartalmazni fog egy azonosítót, amely a digitális ikereszközt a `drawingId` 3D rajzhoz társítja. Emellett a ConferenceRoom interfész hozzáad egy "online" tulajdonságot, amely a konferenciaterem online állapotát fogja tartalmazni. Az öröklés révén a ConferenceRoom felület a RealEstateCore ConferenceRoom felület összes képességét tartalmazza, valamint a kiterjesztett space felület összes képességét. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-2.png" alt-text="A fenti kiterjesztett RealEstateCore térhierarchiát ábrázoló folyamatábra további új kiegészítésekkel. A helyiség szintje most egy Szóköz elemmel rendelkezik, amely egy szinttel egy szinttel lefelé mutató &quot;extends&quot; nyíllal csatlakozik egy új Room elemhez a ConferenceRoom és az Office mellett.  Az új elemek a meglévő ontológiához csatlakoznak, és több &quot;bővítő&quot; kapcsolat van közöttünk."::: 

## <a name="using-the-extended-space-hierarchy"></a>A kiterjesztett terület hierarchiájának használata 

Amikor a kiterjesztett tér-hierarchiával hoz létre digitális ikereket, mindegyik digitális ikermodell egy lesz a kiterjesztett térhierarchiából (nem az eredeti iparági ontológiából), és az iparági ontológia és a kiterjesztett interfészek összes képességét tartalmazza a interfészek öröklése ellenére.

Minden digitális ikermodell egy interfész lesz a kiterjesztett hierarchiából, az alábbi ábrán látható módon. 
 
:::image type="content" source="media/concepts-ontologies-extend/ontology-with-models.png" alt-text="A kiterjesztett RealEstateCore térhierarchia egyik kivonata, amely tartalmazza a tér (legfelső szint), egy helyiség (középső szint) és a ConferenceRoom, az Office és a FocusRoom (alsó szintű) hierarchiáját. A modellek neve minden elemhez kapcsolódik (például a Room egy Room101 nevű modellhez csatlakozik)."::: 

Amikor a modellazonosító (a operátor) használatával lekérdezi a digitális ikereket, a kiterjesztett hierarchiából származó modellazonosítókat `IS_OF_MODEL` kell használnia. Például: `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Hozzájárulás az eredeti ontológiához 

Bizonyos esetekben olyan módon bővítheti az iparági ontológiát, amely széles körben hasznos az ontológia legtöbb felhasználója számára. Ebben az esetben érdemes megfontolni a bővítmények eredeti ontológiához való hozzájárulását. Mindegyik ontológia más-más közreműködői folyamattal rendelkezik, ezért a közreműködés részleteiért tekintse meg az ontológia GitHub-adattárát. 

## <a name="dtdl-for-new-interfaces"></a>DTDL az új felületekhez 

A DTDL az iparági ontológiából közvetlenül kiható új felületekhez így nézne ki. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DTDL bővített felületekhez 

A kiterjesztett felületek DTDL-e a fent tárgyalt részre korlátozva így nézne ki. 

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

Folytassa az ontológián alapuló modellek fejlesztésének útvonalával: [*Ontológiastratégiák használata modellfejlesztési útvonalon.*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)