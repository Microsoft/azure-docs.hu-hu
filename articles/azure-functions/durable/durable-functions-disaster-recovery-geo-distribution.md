---
title: Vész-helyreállítási és földrajzi eloszlású Azure Durable Functions
description: Ismerje meg a vész-helyreállítást és a Geo-eloszlást Durable Functionsban.
author: MS-Santi
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: azfuncdf
ms.openlocfilehash: 01c400f51cce85ef39e9d39bcad1221253c6942d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89071210"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Vész-helyreállítás és geo-eloszlás az Azure-ban Durable Functions

A Microsoft igyekszik biztosítani, hogy az Azure-szolgáltatások mindig elérhetők legyenek. A nem tervezett szolgáltatások azonban előfordulhatnak. Ha az alkalmazása rugalmasságot igényel, a Microsoft javasolja az alkalmazás konfigurálását a Geo-redundancia érdekében. Emellett az ügyfeleknek vészhelyzeti helyreállítási tervvel kell rendelkezniük a regionális szolgáltatások kimaradásának kezelésére. A vész-helyreállítási terv fontos része arra készül, hogy átadja a feladatátvételt az alkalmazás és a tároló másodlagos replikáján abban az esetben, ha az elsődleges replika elérhetetlenné válik.

A Durable Functionsban az összes állapot alapértelmezés szerint az Azure Storage-ban marad. A [Task hub](durable-functions-task-hubs.md) olyan Azure Storage-erőforrások logikai tárolója, [amelyeket a rendszer](durable-functions-types-features-overview.md#orchestrator-functions) az előkészítésekhez és az [entitásokhoz](durable-functions-types-features-overview.md#entity-functions)használ. A Orchestrator, a tevékenység és az entitás funkciói csak akkor kommunikálhatnak egymással, ha ugyanahhoz a feladathoz tartozó hubhoz tartoznak. Ez a dokumentum a feladatok hubokra vonatkozik, amikor leírja az Azure Storage-erőforrások nagyfokú rendelkezésre állását.

Az Összehangolók és az entitások a HTTP-n [keresztül, vagy](durable-functions-types-features-overview.md#client-functions) a más támogatott Azure functions trigger-típusok egyikével aktiválva lehetnek. A [beépített http API](durable-functions-http-features.md#built-in-http-apis)-k használatával is elindíthatók. Az egyszerűség kedvéért ez a cikk az Azure Storage-t és a HTTP-alapú függvény-eseményindítókat érintő forgatókönyvekre összpontosít, valamint a rendelkezésre állás növelését és a vész-helyreállítási tevékenységek során a leállás minimalizálását szolgáló lehetőségeket. Más eseményindító-típusok, például Service Bus vagy Cosmos DB eseményindítók nem lesznek explicit módon lefedettek.

A következő forgatókönyvek Active-Passive konfigurációkon alapulnak, mivel ezeket az Azure Storage-használat vezérli. Ez a minta egy biztonsági mentési (passzív) függvény alkalmazásának egy másik régióba való telepítését jelenti. A Traffic Manager figyeli az elsődleges (aktív) function alkalmazást a HTTP rendelkezésre állásához. Ha az elsődleges művelet meghiúsul, a rendszer átadja a feladatokat a Backup Function alkalmazásnak. További információ: [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) [priority Traffic-Routing metódusa.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

> [!NOTE]
> - A javasolt Active-Passive konfiguráció gondoskodik arról, hogy az ügyfél mindig képes legyen új, HTTP-n keresztül kiváltani a folyamatokat. Azonban annak következményeként, hogy két Function-alkalmazás ugyanazt a feladatot osztja meg a tárolóban, a rendszer néhány háttérbeli tárolási tranzakciót továbbít egymás között. Ez a konfiguráció ezért a másodlagos Function alkalmazáshoz tartozó további kimenő költségekkel jár.
> - Az alapul szolgáló Storage-fiók és a Task hub az elsődleges régióban jön létre, és mindkét Function-alkalmazásban meg van osztva.
> - A redundánsan telepített functions-alkalmazásoknak ugyanazokat a függvény-hozzáférési kulcsokat kell megosztaniuk, ha HTTP-n keresztül aktiválják őket. A functions futtatókörnyezet olyan [felügyeleti API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) -t tesz elérhetővé, amely lehetővé teszi a felhasználóknak a funkcióbillentyűk programozott hozzáadását, törlését és frissítését. A kulcskezelő [Azure Resource Manager API](https://www.markheath.net/post/managing-azure-functions-keys-2)-k használatával is lehetséges.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>1. eset – elosztott terhelésű számítás megosztott tárolóval

Ha az Azure-beli számítási infrastruktúra meghibásodik, előfordulhat, hogy a Function alkalmazás elérhetetlenné válik. Ha csökkenteni szeretné az ilyen állásidők lehetőségét, ez a forgatókönyv két, különböző régiókban üzembe helyezett függvény alkalmazást használ.
Traffic Manager úgy van konfigurálva, hogy észlelje a problémákat az elsődleges Function alkalmazásban, és automatikusan átirányítsa a forgalmat a másodlagos régióban található Function alkalmazásba. Ez a Function-alkalmazás ugyanazt az Azure Storage-fiókot és-feladatot osztja meg. Ezért a Function apps állapota nem vész el, és a munka a szokásos módon folytatódik. Ha az állapotot visszaállítja az elsődleges régióba, az Azure Traffic Manager automatikusan elindítja az útválasztási kérelmeket az adott függvény alkalmazásnak.

![Az 1. forgatókönyvet ábrázoló diagram](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Ennek a telepítési forgatókönyvnek a használata számos előnnyel jár:

- Ha a számítási infrastruktúra meghibásodik, a munka adatvesztés nélkül folytatható a feladatátvételi régióban.
- A Traffic Manager automatikusan gondoskodik az automatikus feladatátvételről az egészséges Function alkalmazásra.
- Traffic Manager automatikusan újra létrehozza a forgalmat az elsődleges Function alkalmazásnak a leállás kijavítása után.

Ebben a forgatókönyvben azonban a következőket érdemes figyelembe venni:

- Ha a Function alkalmazás dedikált App Service csomaggal van telepítve, a számítási infrastruktúra replikálása a feladatátvételi adatközpontban növeli a költségeket.
- Ez a forgatókönyv a számítási infrastruktúra leállásait fedi le, de a Storage-fiók továbbra is a Function alkalmazás egyetlen meghibásodási pontja. Ha tárolási leállás történik, az alkalmazás leállást szenved.
- Ha a Function alkalmazás feladatátvételt végez, a rendszer megnöveli a késést, mivel a több régióban is hozzáfér a Storage-fiókjához.
- A Storage szolgáltatást egy másik régióból érheti el, ahol a hálózati forgalom miatt magasabb költségekkel jár.
- Ez a forgatókönyv a Traffic Managertól függ. Tekintettel arra, [hogy Traffic Manager működik](../../traffic-manager/traffic-manager-how-it-works.md)-e, lehet, hogy egy olyan ügyfélalkalmazás, amely nem használ tartós függvényt, újra le kell kérdezni a Function app-címeket a Traffic Managerról.

> [!NOTE]
> A Durable Functions bővítmény **v 2.3.0** kezdődően két Function apps is biztonságosan futtatható egyszerre ugyanazzal a Storage-fiókkal és a Task hub-konfigurációval. Az első alkalmazás az alkalmazás-szintű blob bérletét fogja beszerezni, amely megakadályozza, hogy más alkalmazások ne lopják el az üzeneteket a Task hub-várólistákból. Ha az első alkalmazás futása leáll, a bérlete lejár, és egy második alkalmazás szerzi be, amely ezután a feladat hub-üzeneteinek feldolgozását végzi.
> 
> A v 2.3.0 előtt az azonos Storage-fiók használatára konfigurált functions-alkalmazások feldolgozzák az üzeneteket és frissítik a tárolási összetevőket, ami sokkal nagyobb általános késési és kimenő költségekkel jár. Ha az elsődleges és a replika alkalmazásnak már van olyan programkódja, amely még átmenetileg is telepítve van, akkor is előfordulhat, hogy a rendszer nem tudja megfelelően végrehajtani az előkészítést, mert a Orchestrator függvény inkonzisztencia a két alkalmazás között. Ezért javasoljuk, hogy minden olyan alkalmazásnál, amelynél a földrajzi eloszlás szükséges a vész-helyreállítási célokra, használja a tartós bővítmény v 2.3.0 vagy újabb verzióját.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>2. forgatókönyv – elosztott terhelésű számítás regionális tárterülettel

Az előző forgatókönyv csak a számítási infrastruktúra meghibásodásának esetét tárgyalja. Ha a tárolási szolgáltatás meghibásodik, akkor a függvény alkalmazás leállás miatt leáll.
A tartós függvények folyamatos működésének biztosítása érdekében ez a forgatókönyv egy helyi Storage-fiókot használ minden olyan régióban, amelyhez a Function apps telepítve van.

![A 2. forgatókönyvet bemutató diagram](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Ez a megközelítés javítja az előző forgatókönyvet:

- Ha a Function alkalmazás meghibásodik, Traffic Manager gondoskodik a másodlagos régióba való feladatátvételről. Mivel azonban a Function alkalmazás a saját Storage-fiókjára támaszkodik, a tartós függvények továbbra is működőképesek maradnak.
- A feladatátvétel során nincs további késés a feladatátvételi régióban, mivel a Function alkalmazás és a Storage-fiók közös helyen található.
- A tárolási réteg meghibásodása hibát okoz a tartós függvényeknél, ami viszont elindítja a feladatátvételi régióba való átirányítást. Mivel a Function app és a Storage régiónként elkülönített, a tartós függvények továbbra is működni fognak.

A forgatókönyvhöz kapcsolódó fontos szempontok:

- Ha a Function alkalmazás dedikált App Service csomaggal van telepítve, a számítási infrastruktúra replikálása a feladatátvételi adatközpontban növeli a költségeket.
- A jelenlegi állapot nem halad át, ami azt jelenti, hogy a meglévő Összehangolók és entitások gyakorlatilag szünetelnek, és elérhetetlenné válnak, amíg az elsődleges régió helyre nem áll.

Összefoglalva, az első és második forgatókönyv közötti kompromisszum azt mutatja, hogy a késés megmarad, és a kimenő forgalomra vonatkozó költségek kisebbek, de a meglévő összeszerelések és entitások az állásidő alatt elérhetetlenné válnak. Az alkalmazás követelményeitől függ, hogy elfogadható-e a kompromisszumok.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>3. forgatókönyv – elosztott terhelésű számítás GRS megosztott tárolóval

Ez a forgatókönyv az első forgatókönyv módosítása, egy megosztott Storage-fiók implementálása. A fő különbség az, hogy a Storage-fiók engedélyezve van a Geo-replikációval.
Funkcionálisan ez a forgatókönyv ugyanazokat az előnyöket biztosítja, mint az 1. forgatókönyv, de lehetővé teszi a további adat-helyreállítási előnyöket:

- A Geo-redundáns tárolás (GRS) és az olvasási hozzáférésű GRS (RA-GRS) teljes körű rendelkezésre állást biztosít a Storage-fiók számára.
- Ha a tárolási szolgáltatás regionális leállás miatt leáll, a [feladatátvételt manuálisan is kezdeményezheti a másodlagos replikára](../../storage/common/storage-initiate-account-failover.md). Szélsőséges körülmények között, amikor egy régiót súlyos katasztrófa okoz, a Microsoft regionális feladatátvételt kezdeményezhet. Ebben az esetben nincs szükség beavatkozásra a részen.
- Feladatátvétel esetén a tartós függvények állapotát a rendszer a Storage-fiók utolsó replikálásával fogja megőrizni, ami általában néhány percenként történik.

A többi forgatókönyvhöz hasonlóan fontos szempontokat is figyelembe kell venni:

- A replika feladatátvétele hosszabb időt is igénybe vehet. Amíg a feladatátvétel befejeződik, és az Azure Storage DNS-rekordjai frissítve lettek, a Function alkalmazás áramkimaradás miatt leáll.
- A földrajzilag replikált Storage-fiókok használatának megnövelt díja.
- A GRS replikáció aszinkron módon másolja az adatait. A replikálási folyamat késése miatt előfordulhat, hogy a legújabb tranzakciók némelyike elvész.

![A 3. forgatókönyvet bemutató diagram.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

> [!NOTE]
> Az 1. forgatókönyvben leírtak szerint erősen ajánlott, hogy az ezzel a stratégiával telepített alkalmazások a Durable Functions bővítmény **v 2.3.0** vagy újabb verzióját használják.

További információkért lásd az [Azure Storage vész-helyreállítási és a Storage-fiók feladatátvételi](../../storage/common/storage-disaster-recovery-guidance.md) dokumentációját.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a magasan elérhető alkalmazások tervezéséről az Azure Storage-ban](../../storage/common/geo-redundant-design.md)
