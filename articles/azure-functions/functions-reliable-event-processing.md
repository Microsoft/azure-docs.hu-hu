---
title: Azure Functions megbízható események feldolgozása
description: Kerülje az Event hub-üzenetek hiányzó Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: fd784bb184ff9432efc569ac9fd40de93eec0b53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93379587"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure Functions megbízható események feldolgozása

Az események feldolgozása a kiszolgáló nélküli architektúrához társított leggyakoribb forgatókönyvek egyike. Ez a cikk azt ismerteti, hogyan lehet az üzenetek elvesztésének elkerüléséhez Azure Functions használatával megbízható üzenetet létrehozni.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Az esemény-adatfolyamok kihívásai az elosztott rendszerekben

Vegyünk egy olyan rendszernek, amely másodpercenként 100 eseményt küld az eseményeknek. Ezen a sebességgel néhány percen belül több párhuzamos functions-példány képes a bejövő 100-események másodpercenkénti felhasználására.

Azonban a következő kevésbé optimális feltételek bármelyike lehetséges:

- Mi a teendő, ha az esemény kiadója sérült eseményt küld?
- Mi a teendő, ha a függvények példánya nem kezelt kivételeket észlel?
- Mi a teendő, ha egy alsóbb rétegbeli rendszer offline állapotba kerül?

Hogyan kezelheti ezeket a helyzeteket, miközben megőrizheti az alkalmazás átviteli sebességét?

A várólistákkal a megbízható üzenetküldés természetesen magától értetődő. Functions triggerrel párosítva a függvény zárolást hoz létre az üzenetsor-üzenetben. Ha a feldolgozás sikertelen, a zárolás felszabadítva lesz, hogy egy másik példány újra feldolgozza a feldolgozást. A feldolgozás ezután folytatódik, amíg meg nem történik az üzenet sikeres kiértékelése, vagy hozzá van adva egy méreg-várólistához.

Még ha egy üzenetsor-üzenet is maradhat egy újrapróbálkozási ciklusban, a többi párhuzamos végrehajtás továbbra is továbbra is megmarad a fennmaradó üzenetek várólistára helyezése során. Ennek az az oka, hogy a teljes átviteli sebesség nagy mértékben nem lesz hatással egy hibás üzenetre. A tárolási várólisták azonban nem garantálják a sorrendet, és nem a Event Hubs által igényelt nagy átviteli sebességre vannak optimalizálva.

Ezzel szemben az Azure Event Hubs nem tartalmaz zárolási koncepciót. A nagy átviteli sebességű, több fogyasztói csoport és az újrajátszás képességgel rendelkező funkciók engedélyezéséhez Event Hubs az események többek között egy videolejátszóként viselkednek. Az eseményeket a rendszer az adatfolyamban lévő egy pontról olvassa be. A mutatóból az adott helyről továbbíthatja a továbbításokat vagy visszafelé, de a mutatót úgy kell áthelyeznie, hogy feldolgozza az eseményeket.

Ha hiba lép fel egy adatfolyamban, ha úgy dönt, hogy ugyanazon a helyen tartja a mutatót, a rendszer letiltja az események feldolgozását, amíg a mutató nem lesz speciális. Más szóval, ha a mutató le van állítva egy adott esemény feldolgozásával kapcsolatos problémák kezelésére, a feldolgozatlan események megkezdik a felhalmozás folyamatát.

A Azure Functions elkerüli a holtpontokat azáltal, hogy az adatfolyam mutatóját a sikertől vagy a meghibásodástól függetlenül meghaladják. Mivel a mutató folyamatosan halad, a függvényeknek megfelelően kell foglalkoznia a hibákkal.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Hogyan használják a Azure Functions Event Hubs eseményeket

A Azure Functions az Event hub-eseményeket az alábbi lépések során használja:

1. A rendszer létrehoz egy mutatót, és megőrzi az Azure Storage-ban az Event hub minden partíciója számára.
2. Amikor új üzenet érkezik (alapértelmezés szerint egy kötegben), a gazdagép megpróbálja elindítani a függvényt az üzenetek kötegében.
3. Ha a függvény befejezi a végrehajtást (kivétel nélkül vagy anélkül), a mutató továbblép, és egy ellenőrzőpontot ment a Storage-fiókba.
4. Ha a feltételek megakadályozzák, hogy a függvény végrehajtása befejeződik, a gazdagép nem tudja végrehajtani a mutatót. Ha a mutató nem speciális, akkor a későbbi ellenőrzések során ugyanazok az üzenetek kerülnek feldolgozásra.
5. Ismételje meg a 2 – 4. lépést

Ez a viselkedés a következő fontos pontokat mutatja be:

- *A nem kezelt kivételek miatt elveszítheti az üzeneteket.* A kivételt okozó végrehajtások továbbra is a mutató előrehaladását eredményezik.  Az [újrapróbálkozási szabályzat](./functions-bindings-error-pages.md#retry-policies-preview) beállítása késlelteti a mutatót a teljes újrapróbálkozási házirend kiértékelése után.
- *A függvények legalább egyszeri kézbesítést garantálnak.* Előfordulhat, hogy a kódnak és a függő rendszereknek is [figyelembe kell venniük azt a tényt, hogy ugyanazt az üzenetet kétszer is el lehet fogadni](./functions-idempotent.md).

## <a name="handling-exceptions"></a>Kivételek kezelése

Általános szabályként minden függvénynek tartalmaznia kell egy [try/catch blokkot](./functions-bindings-error-pages.md) a legmagasabb szintű kóddal. Pontosabban, az Event Hubs eseményeket használó összes függvénynek `catch` blokkmal kell rendelkeznie. Így ha kivétel keletkezik, a Catch blokk kezeli a hibát a mutató előrehaladása előtt.

### <a name="retry-mechanisms-and-policies"></a>Újrapróbálkozási mechanizmusok és szabályzatok

Bizonyos kivételek átmeneti jellegűek, és nem jelennek meg újra, amikor egy művelet később próbálkozik újra. Ezért az első lépés az, hogy mindig újra kell próbálkoznia a művelettel.  A függvény végrehajtásán belül kihasználhatja a Function app [újrapróbálkozási szabályzatait](./functions-bindings-error-pages.md#retry-policies-preview) vagy a szerzői újrapróbálkozási logikát.

A függvények hibatűrési viselkedésének bemutatása lehetővé teszi az alapszintű és a speciális újrapróbálkozási házirendek definiálását. Létrehozhat például egy olyan szabályzatot, amely a következő szabályok által illusztrált munkafolyamatot követi:

- Próbáljon meg háromszor beszúrni egy üzenetet (esetleg az újrapróbálkozások közötti késleltetéssel).
- Ha az újrapróbálkozások végeredménye hibát jelez, adjon hozzá egy üzenetet egy várólistához, hogy a feldolgozás folytatható legyen az adatfolyamban.
- A rendszer később kezeli a sérült vagy feldolgozatlan üzeneteket.

> [!NOTE]
> A [Polly](https://github.com/App-vNext/Polly) egy példa arra, hogy hogyan használható a rugalmasság és az átmeneti hibák kezelésére szolgáló függvénytár C#-alkalmazásokhoz.

## <a name="non-exception-errors"></a>Kivételt nem okozó hibák

Bizonyos problémák akkor is felmerülhetnek, ha nem jelennek meg hiba. Vegyünk például egy olyan hibát, amely egy végrehajtás közepén fordul elő. Ebben az esetben, ha egy függvény nem hajtja végre a végrehajtást, az eltolási mutató soha nem halad előre. Ha a mutató nem halad előre, akkor a sikertelen végrehajtást követően futó példányok továbbra is ugyanazokat az üzeneteket olvassák. Ez a helyzet "legalább egyszeri" garanciát biztosít.

Arról, hogy minden üzenet feldolgozása legalább egyszer megtörténik, azt feltételezi, hogy néhány üzenet többször is feldolgozható. A Function apps-alkalmazásoknak ismerniük kell ezt a lehetőséget, és a [idempotencia alapelvei](./functions-idempotent.md)köré kell felépíteni őket.

## <a name="stop-and-restart-execution"></a>Végrehajtás leállítása és újraindítása

Néhány hiba is elfogadható lehet, mi a teendő, ha az alkalmazás jelentős hibákat tapasztal? Előfordulhat, hogy az események aktiválását le szeretné állítani, amíg a rendszer Kifogástalan állapotba nem ér. A feldolgozás szüneteltetésének lehetősége gyakran egy áramkör-megszakító mintával érhető el. Az áramkör-megszakító minta lehetővé teszi, hogy az alkalmazás "megtörje" az esemény folyamatát, és később folytassa a műveletet.

Az áramkör-megszakítót egy eseményvezérelt folyamat megvalósításához két darab szükséges:

- Minden példány közös állapota az áramkör állapotának nyomon követéséhez és figyeléséhez
- Az áramköri állapot kezelésére képes fő folyamat (nyitott vagy lezárt)

A megvalósítás részletei eltérőek lehetnek, de a példányok közötti megosztáshoz szükség van egy tárolási mechanizmusra. Dönthet úgy, hogy az állapotot az Azure Storage-ban, egy Redis-gyorsítótárban vagy bármely más, a függvények gyűjteménye által elérhető fiókban tárolja.

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) -vagy [tartós függvények](./durable/durable-functions-overview.md) természetes illeszkedést biztosítanak a munkafolyamatok és az áramköri állapot kezeléséhez. Más szolgáltatások is ugyanúgy működhetnek, de ehhez a példához a Logic apps is használható. A Logic Apps használatával szüneteltetheti és újraindíthatja a függvények végrehajtását, így az áramkör-megszakító minta megvalósításához szükséges vezérlőt is megadhatja.

### <a name="define-a-failure-threshold-across-instances"></a>Hiba küszöbértékének meghatározása példányok között

Az események egyidejű feldolgozásához egyszerre több példányban kell megőrizni az áramkör állapotának figyeléséhez szükséges megosztott külső állapotot.

A megvalósítani kívánt szabály a következő esetekben kényszeríthető:

- Ha több mint 100, az összes példányon 30 másodpercnél több lehetséges hiba történik, akkor szüntesse meg az áramkört, és állítsa le az indítást az új üzeneteken.

A megvalósítás részletei az Ön igényeinek megfelelően változhatnak, de általánosságban létrehozhat egy olyan rendszerrendszer, amely a következőket teszi:

1. Hibák naplózása egy Storage-fiókba (Azure Storage, Redis stb.)
1. Ha új hiba van naplózva, ellenőrizze a működés közbeni korlátot, és ellenőrizze, hogy teljesülnek-e a küszöbérték (például több mint 100 az elmúlt 30 másodpercben).
1. Ha a küszöbérték teljesül, kibocsát egy eseményt, hogy Azure Event Grid mondja el, hogy a rendszer megszakítja az áramkört.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Áramkör állapotának kezelése Azure Logic Apps

Az alábbi leírás kiemeli, hogyan hozhat létre egy Azure Logic apps-alkalmazást a függvények feldolgozásának leállításához.

Azure Logic Apps a különböző szolgáltatásokhoz beépített összekötőket tartalmaz, a funkciók állapot-nyilvántartó felépítését, és természetes választás az áramköri állapot kezeléséhez. Miután észlelte, hogy az áramkörnek meg kell szakítania a folyamatot, létrehozhat egy logikai alkalmazást a következő munkafolyamat megvalósításához:

1. Event Grid munkafolyamat elindítása és az Azure-függvény leállítása (az Azure Resource connectorral)
1. Értesítési e-mail küldése, amely tartalmaz egy lehetőséget a munkafolyamat újraindítására.

Az e-mail-címzett megvizsgálhatja az áramkör állapotát, és ha szükséges, újraindíthatja az áramkört az értesítő e-mailben található hivatkozás használatával. Ahogy a munkafolyamat újraindítja a függvényt, az üzenetek a legutóbbi Event hub ellenőrzőpontról lesznek feldolgozva.

Ennek a módszernek a használatával egyetlen üzenet sem vész el, az összes üzenet feldolgozása sorrendben történik, és szükség esetén az áramkört is megszüntetheti.

## <a name="resources"></a>Erőforrások

- [Megbízható esemény-feldolgozási minták](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Azure tartós entitás áramkör-megszakítója](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Következő lépések

További információkat találhat az alábbi forrásokban:

- [Azure Functions hibakezelés](./functions-bindings-error-pages.md)
- [Feltöltött képek átméretezésének automatizálása az Event Grid használatával](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása](./functions-twitter-email.md)
