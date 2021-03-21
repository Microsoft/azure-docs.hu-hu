---
title: Ellenőrzőpontok és újrajátszás helyreállítási fogalmak a Azure Stream Analytics
description: Ez a cikk az ellenőrzőpontok és a Replay feladatok helyreállítási fogalmait ismerteti Azure Stream Analyticsban.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: df31f8538bb9eabeca37fe4c52c4443fd447e415
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015318"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Ellenőrzőpont-és visszajátszás-fogalmak a Azure Stream Analytics-feladatokban
Ez a cikk ismerteti a Azure Stream Analytics belső ellenőrzőpont-és újralejátszási fogalmait, valamint a feladatokra gyakorolt hatást. Minden alkalommal, amikor egy Stream Analytics feladatot futtat, az állapotadatok belsőleg maradnak. Az állapotadatok rendszeres időközönként kerülnek mentésre. Bizonyos esetekben az ellenőrzőpont-információk a feladatok helyreállítására szolgálnak, ha a feladattal kapcsolatos hiba vagy frissítés történik. Más esetekben az ellenőrzőpont nem használható a helyreállításhoz, és szükség van egy újrajátszásra.

## <a name="stateful-query-logic-in-temporal-elements"></a>Állapot-nyilvántartó lekérdezési logika az időbeli elemekben
Azure Stream Analytics feladatok egyik egyedi funkciója az állapot-nyilvántartó feldolgozás végrehajtása, például ablakos összesítések, időbeli illesztések és időbeli analitikai függvények. Ezek az operátorok a feladatok futtatásakor megőrzik az állapotadatok állapotát. A lekérdezési elemek maximális ablakméret hét nap. 

Az időszakos ablak fogalma számos Stream Analytics lekérdezési elemben jelenik meg:
1. Ablakos összesítések (felhúzási, átugró és csúszó ablakok CSOPORTOSÍTÁSa)

2. Időbeli illesztések (csatlakozás DATEDIFF)

3. Időbeli elemzési függvények (ISFIRST, LAST és LAG a korlát IDŐTARTAMával)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Sikertelen volt a feladatok helyreállítása a csomópont meghibásodása esetén, beleértve az operációs rendszer frissítését
Minden alkalommal, amikor egy Stream Analytics-feladatot futtat, belsőleg, hogy több munkavégző csomóponton végezze a munkát. Az egyes munkavégző csomópontok állapota néhány percenként ellenőrzőpontra kerül, ami segít a rendszer helyreállításában, ha hiba történik.

Időnként előfordulhat, hogy egy adott feldolgozó csomópont meghibásodik, vagy az adott munkavégző csomópont operációs rendszerének frissítése is előfordulhat. Az automatikus helyreállításhoz Stream Analytics új, kifogástalan állapotú csomópontot szerezhet be, és a rendszer visszaállítja az előző feldolgozói csomópont állapotát a legújabb elérhető ellenőrzőponton. A munka folytatásához kis mennyiségű újrajátszás szükséges ahhoz, hogy az állapotot az ellenőrzőpont elvégzésének időpontjában vissza lehessen állítani. A visszaállítási hézag általában néhány percet vesz igénybe. Ha a feladatokhoz elegendő folyamatos átviteli egység van kiválasztva, a visszajátszás gyorsan elvégezhető. 

Egy teljes mértékben párhuzamos lekérdezésben a munkavégző csomópont meghibásodása utáni felzárkózás ideje a következőhöz arányos:

[a bemeneti esemény sebessége] x [a térköz hossza]/[feldolgozási partíciók száma]

Ha a csomópont meghibásodása és az operációs rendszer frissítése miatt már jelentős feldolgozási késleltetés figyelhető meg, vegye fontolóra a lekérdezés teljes párhuzamos kiosztását, és méretezze át a feladatot több folyamatos átviteli egység foglalásához. További információ: [Azure stream Analytics-feladatok méretezése az átviteli sebesség növelése érdekében](stream-analytics-scale-jobs.md).

A jelenlegi Stream Analytics nem jeleníti meg a jelentést, ha az ilyen típusú helyreállítási folyamat zajlik.

## <a name="job-recovery-from-a-service-upgrade"></a>A feladatok helyreállítása a szolgáltatás verziófrissítése után 
A Microsoft alkalmanként frissíti a Stream Analytics feladatokat futtató bináris fájlokat az Azure szolgáltatásban. Ezen időpontokban a felhasználók futó feladatok újabb verzióra frissülnek, és a feladat automatikusan újraindul. 

Jelenleg a helyreállítási ellenőrzőpont formátuma nem marad meg a frissítések között. Ennek eredményeképpen az adatfolyam-lekérdezés állapotát teljes egészében vissza kell állítani a Replay Technique használatával. Ha engedélyezni szeretné, hogy Stream Analytics feladatok a korábban megadott adatokat újra visszajátszják, fontos, hogy a forrásadatok adatmegőrzési szabályát legalább a lekérdezésben szereplő ablakméret értékre állítsa. Ha ezt elmulasztja, a szolgáltatás frissítése során helytelen vagy részleges eredményeket eredményezhet, mivel előfordulhat, hogy a forrásadatok nem maradnak meg elég messzire, hogy belefoglalják a teljes ablak méretét.

Általánosságban elmondható, hogy az újraindításhoz szükséges mennyiség arányos az ablak méretével, szorozva az esemény átlagos értékével. Példaként egy olyan feladathoz, amely másodpercenként 1000 eseményt tartalmaz, az egy óránál hosszabb ablak mérete nagy újrajátszás méretének minősül. Előfordulhat, hogy akár egy órányi adat újrafeldolgozására is szükség lehet az állapot inicializálásához, hogy teljes és helyes eredményeket lehessen létrehozni, ami késleltetett kimenetet eredményezhet (nincs kimenet) egy hosszabb ideig. A Windows vagy más időszakos operátorokkal (például vagy) nem rendelkező lekérdezések esetében `JOIN` `LAG` nulla visszajátszás lenne.

## <a name="estimate-replay-catch-up-time"></a>Visszajátszási Felskálázási idő becslése
A szolgáltatás verziófrissítése miatti késés hosszának becsléséhez kövesse ezt a technikát:

1. Töltse be a bemeneti esemény központját, és adjon meg elegendő adatot a lekérdezés legnagyobb méretének, a várt esemény díjszabása alapján. Az események időbélyegének közel kell lennie a fal órájához az adott időszak alatt, ahogy az élő bemeneti adatcsatorna. Ha például 3 napos ablaka van a lekérdezésben, az eseményeket három napig küldje el az Event hub számára, és folytassa az események küldését. 

2. Indítsa el a feladatot **most** a kezdési időpont használatával. 

3. A kezdési időpont és az első kimenet létrehozása közötti idő mérése. Az idő durva, hogy a szolgáltatás frissítése során mennyi késleltetéssel jár a feladatok.

4. Ha a késés túl hosszú, próbálja meg particionálni a feladatot, és növelje a SUs számát, így a terhelést több csomópontra is kiterjesztheti. Azt is megteheti, hogy csökkenti a lekérdezésben szereplő ablakméret méretét, és további összesítést vagy más állapot-nyilvántartó feldolgozást hajt végre az alsóbb rétegbeli fogadó Stream Analytics feladata által létrehozott kimeneten (például Azure SQL Database használatával).

A kritikus fontosságú feladatok frissítése során felmerülő általános szolgáltatási stabilitás érdekében érdemes lehet ismétlődő feladatokat futtatni a párosított Azure-régiókban. További információ: a [szolgáltatás frissítéseinek stream Analytics a feladatok megbízhatóságának garantálása](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>A feladatok helyreállítása a felhasználó által kezdeményezett leállítás és indítás után
A lekérdezési szintaxis egy folyamatos átviteli feladaton való szerkesztéséhez, illetve a bemenetek és kimenetek módosításához a feladatot le kell állítani a módosítások végrehajtásához és a feladatok kialakításának frissítéséhez. Ilyen esetekben, amikor egy felhasználó leállítja a folyamatos átviteli feladatot, és újra elindítja, a helyreállítási forgatókönyv hasonló a szolgáltatás verziófrissítéséhez. 

Ellenőrzőpont-adatok nem használhatók a felhasználó által kezdeményezett feladatok újraindításához. Az újraindításkor a kimenet késleltetésének megbecsléséhez használja ugyanazt az eljárást, mint az előző szakaszban leírtak szerint, és alkalmazzon hasonló mérséklést, ha a késés túl hosszú.

## <a name="next-steps"></a>Következő lépések
A megbízhatósággal és a méretezhetőséggel kapcsolatos további információkért tekintse meg a következő cikkeket:
- [Oktatóanyag: riasztások beállítása Azure Stream Analytics feladatokhoz](stream-analytics-set-up-alerts.md)
- [Azure Stream Analytics feladatok méretezése az átviteli sebesség növelése érdekében](stream-analytics-scale-jobs.md)
- [A szolgáltatás frissítéseinek Stream Analytics a feladatok megbízhatóságának garantálása](stream-analytics-job-reliability.md)
