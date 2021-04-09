---
title: Számítási csomópontok automatikus méretezése egy Azure Batch-készletben
description: A készletben lévő számítási csomópontok számának dinamikus beállításához engedélyezze a Felhőbeli készlet automatikus méretezését.
ms.topic: how-to
ms.date: 11/23/2020
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 06f717e7c3ab8285b494f89c39838af6b0d96c8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381426"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Automatikus képlet létrehozása a számítási csomópontok méretezéséhez egy batch-készletben

A Azure Batch automatikusan méretezheti a készleteket a definiált paraméterek alapján, így időt és pénzt takaríthat meg. Az automatikus skálázással a Batch dinamikusan felveszi a csomópontokat egy készletbe, ahogy a feladat megköveteli a növekedést, és eltávolítja a számítási csomópontokat, mivel a feladathoz szükséges mennyiség csökken

Ha a számítási csomópontok készletén szeretné engedélyezni az automatikus skálázást, társítsa a készletet egy Ön által meghatározott automatikus *méretezési képlettel* . A Batch szolgáltatás az autoskálázás képlettel határozza meg, hogy hány csomópontra van szükség a számítási feladatok végrehajtásához. Ezek a csomópontok lehetnek dedikált csomópontok vagy [alacsony prioritású csomópontok](batch-low-pri-vms.md). A Batch ezután rendszeresen áttekinti a szolgáltatási metrikák adatait, és azt használja a készletben lévő csomópontok számának a képlet és a definiált intervallum alapján történő beállításához.

Ha készletet hoz létre, vagy egy meglévő készletre alkalmazza, akkor engedélyezheti az automatikus skálázást. A Batch segítségével kiértékelheti a képleteket, mielőtt hozzárendeli őket a készletekhez, és figyelnie kell az automatikus skálázási futtatások állapotát. Miután konfigurálta a készletet automatikus skálázással, később módosíthatja a képletet.

> [!IMPORTANT]
> Batch-fiók létrehozásakor megadhatja a [készlet lefoglalási módját](accounts.md), amely meghatározza, hogy a készleteket a Batch szolgáltatás előfizetésében (az alapértelmezett) vagy a felhasználói előfizetésben kell-e lefoglalni. Ha létrehozta a Batch-fiókot az alapértelmezett batch szolgáltatás konfigurációjában, akkor a fiókja legfeljebb a feldolgozásra felhasználható magok maximális számára korlátozódik. A Batch szolgáltatás csak az adott alapkorlátig méretezi a számítási csomópontokat. Ezért előfordulhat, hogy a Batch szolgáltatás nem éri el az autoscale Formula által meghatározott számítási csomópontok céljának számát. A fiók kvótáinak megtekintésével és növelésével kapcsolatos információkért tekintse meg [a Azure batch szolgáltatás kvótáit és korlátait](batch-quota-limit.md) .
>
>Ha felhasználói előfizetési móddal hozta létre a fiókját, a fiók az előfizetés fő kvótájában osztozik. További információkért lásd [az Azure-előfizetésekre és -szolgáltatásokra vonatkozó korlátozásokat, kvótákat és megkötéseket](../azure-resource-manager/management/azure-subscription-service-limits.md) ismertető témakör [a virtuális gépek korlátaira](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) vonatkozó részét.

## <a name="autoscale-formulas"></a>Képletek autoskálázása

Az autoskálázási képlet egy definiált karakterlánc-érték, amely egy vagy több utasítást tartalmaz. Az autoskálázási képlet a készlet [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) eleméhez (batch REST) vagy a [CloudPool. autoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) tulajdonsághoz (Batch .net) van rendelve. A Batch szolgáltatás a képlet segítségével határozza meg a készletben lévő számítási csomópontok megcélzott számát a feldolgozás következő időszakában. A képlet sztringje nem haladhatja meg a 8 KB-ot, legfeljebb 100, pontosvesszővel elválasztott utasítást tartalmazhat, és tartalmazhat sortöréseket és megjegyzéseket.

A képletek automatikus méretezése kötegelt automatikus méretezési "nyelv" lehet. A képlet-utasítások olyan szabadon formázott kifejezések, amelyek a szolgáltatás által definiált változókat (a Batch szolgáltatás által definiált) és a felhasználó által definiált változókat is tartalmazhatják. A képletek a beépített típusok, operátorok és függvények használatával különböző műveleteket végezhetnek ezeken az értékeken. Egy utasítás például a következő formát veheti fel:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

A képletek általában több olyan utasítást tartalmaznak, amelyek műveleteket hajtanak végre a korábbi utasításokban beszerzett értékeken. Például először kapjuk meg a értéket `variable1` , majd továbbítjuk egy függvénynek a feltöltéshez `variable2` :

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Vegye fel ezeket az utasításokat az autoskálázási képletbe, hogy a számítási csomópontok megérkezése esetén megtörténjen. A dedikált csomópontok és az alacsony prioritású csomópontok rendelkeznek saját célként megadott beállításokkal. Az autoskálázási képletek a dedikált csomópontok, az alacsony prioritású csomópontok vagy mindkettő céljának értékét is tartalmazhatják.

A csomópontok célként megadott száma lehet magasabb, alacsonyabb vagy ugyanaz, mint a készletben lévő csomópontok aktuális száma. A Batch kiértékeli a készlet automatikus méretezési képletét egy adott [automatikus skálázási intervallumban](#automatic-scaling-interval). A Batch úgy állítja be a készletben lévő egyes csomópontok számát, hogy az a szám, amelyet az automatikusan Felskálázási képlet a kiértékeléskor megad.

### <a name="sample-autoscale-formulas"></a>Minta autoscale-képletek

Az alábbiakban két, a legtöbb forgatókönyv esetén a működéshez igazítható képleteket találhat. A változók `startingNumberOfVMs` és a `maxNumberofVMs` példában szereplő képletek igényei szerint módosíthatók.

#### <a name="pending-tasks"></a>Függőben lévő feladatok

Ezzel az autoskálázási képlettel a készletet kezdetben egyetlen virtuális géppel hozza létre a rendszer. A `$PendingTasks` metrika meghatározza a futtatott vagy a várólistára helyezett feladatok számát. A képlet megkeresi a függőben lévő feladatok átlagos számát az utolsó 180 másodpercben, és ennek megfelelően állítja be a `$TargetDedicatedNodes` változót. A képlet biztosítja, hogy a dedikált csomópontok megcélzott száma soha ne haladja meg a 25 virtuális gépet. Az új feladatok elküldésekor a készlet automatikusan növekszik. A feladatok elvégzése után a virtuális gépek szabadon válnak, és az automatikus skálázási képlet csökkenti a készletet.

Ez a képlet a dedikált csomópontokat méretezi, de úgy módosítható, hogy az alacsony prioritású csomópontok méretezésére is érvényes legyen.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Előzik-csomópontok

Ez a példa egy olyan készletet hoz létre, amely 25 alacsony prioritású csomóponttal kezdődik. Minden alkalommal, amikor egy alacsony prioritású csomópont előzik, a rendszer egy dedikált csomóponttal cseréli le. Ahogy az első példában is látható, a `maxNumberofVMs` változó megakadályozza, hogy a készlet meghaladja a 25 virtuális gépet. Ez a példa hasznos lehet az alacsony prioritású virtuális gépek kihasználása mellett, ugyanakkor gondoskodni kell arról, hogy a készlet élettartama csak rögzített számú preemptions legyen.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

További információ az [autoscale-képletek létrehozásáról](#write-an-autoscale-formula) és a jelen témakör további, [példaként szolgáló, autoskálázási képletekről](#example-autoscale-formulas) című szakaszában található.

## <a name="variables"></a>Változók

A **szolgáltatás által definiált** és a **felhasználó által definiált** változókat is használhatja az autoscale-képletekben.

A szolgáltatás által definiált változók a Batch szolgáltatásba vannak beépítve. Néhány szolgáltatás által definiált változó írható és olvasható, néhány pedig csak olvasható.

A felhasználó által definiált változók a definiált változók. A fenti példában látható képlet `$TargetDedicatedNodes` és `$PendingTasks` a szolgáltatás által definiált változók, míg a `startingNumberOfVMs` és `maxNumberofVMs` a felhasználó által definiált változók.

> [!NOTE]
> A szolgáltatás által definiált változókat mindig a dollár ($) jel előzi meg. Felhasználó által definiált változók esetén a dollár aláírása nem kötelező.

A következő táblázatok a Batch szolgáltatás által definiált írható és olvasható változókat mutatják be.

### <a name="read-write-service-defined-variables"></a>Olvasási és írási szolgáltatás által definiált változók

A szolgáltatás által definiált változók értékeit lekérheti és beállíthatja a készletben lévő számítási csomópontok számának kezeléséhez.

| Változó | Leírás |
| --- | --- |
| $TargetDedicatedNodes |A készlet dedikált számítási csomópontjainak megcélzott száma. Ez célként van megadva, mert előfordulhat, hogy a készlet nem mindig éri el a kívánt számú csomópontot. Ha például a dedikált csomópontok megcélzott száma módosítva van egy autoskálázási kiértékeléssel, mielőtt a készlet elérte a kezdeti célt, előfordulhat, hogy a készlet nem éri el a célt. <br /><br /> Előfordulhat, hogy a Batch szolgáltatás módban létrehozott fiókban lévő készlet nem éri el a célját, ha a cél meghaladja a Batch-fiók csomópontját vagy a fő kvótát. Előfordulhat, hogy a felhasználói előfizetési módban létrehozott fiókban lévő készlet nem éri el a célját, ha a cél meghaladja az előfizetés megosztott alapszintű kvótáját.|
| $TargetLowPriorityNodes |A készlet alacsony prioritású számítási csomópontjainak megcélzott száma. Ez úgy van megadva, mert a készlet nem mindig éri el a kívánt számú csomópontot. Ha például az alacsony prioritású csomópontok megcélzott száma módosítva van egy autoskálázási kiértékeléssel, mielőtt a készlet elérte a kezdeti célt, előfordulhat, hogy a készlet nem éri el a célt. Előfordulhat, hogy a készlet nem éri el a célját, ha a cél meghaladja a Batch-fiók csomópontját vagy a fő kvótát. <br /><br /> Az alacsony prioritású számítási csomópontokkal kapcsolatos további információkért lásd: [alacsony prioritású virtuális gépek használata a Batch használatával](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Az a művelet, amely akkor fordul elő, amikor a számítási csomópontok el lesznek távolítva a készletből. Lehetséges értékek:<ul><li>**újravárólista**: az alapértelmezett érték. Azonnal befejezi a feladatokat, és visszahelyezi őket a feladat-várólistába, hogy azok újra legyenek ütemezve. Ez a művelet biztosítja, hogy a csomópontok megcélzott száma a lehető leggyorsabban elérhető legyen. Azonban előfordulhat, hogy kevésbé hatékony, mivel a futó feladatok megszakadnak, és ezt követően teljesen újra kell indítani. <li>**megszakítás**: a feladatok azonnali befejezése és eltávolítása a feladat-várólistából.<li>**taskcompletion**: megvárja, amíg a jelenleg futó feladatok befejeződik, majd eltávolítja a csomópontot a készletből. Ezzel a beállítással elkerülhető a feladatok megszakítása és újravárólistába helyezése, ami a feladat elvégzése után felhasználható.<li>**retaineddata**: megvárja a csomóponton lévő összes helyi feladat által megőrzött adat tisztítását, mielőtt eltávolítja a csomópontot a készletből.</ul> |

> [!NOTE]
> A `$TargetDedicatedNodes` változó az alias használatával is megadható `$TargetDedicated` . Hasonlóképpen, a `$TargetLowPriorityNodes` változót az alias használatával is megadhatja `$TargetLowPriority` . Ha a képlet a teljes névvel ellátott változót és annak aliasát is beállítja, akkor a teljes névvel ellátott változóhoz rendelt érték elsőbbséget élvez.

### <a name="read-only-service-defined-variables"></a>Csak olvasható, szolgáltatás által definiált változók

A szolgáltatás által definiált változók értékének beszerzésével a Batch szolgáltatás mérőszámai alapján végezheti el a módosításokat.

> [!IMPORTANT]
> A feladat kiadásával kapcsolatos feladatok jelenleg nem szerepelnek olyan változóknál, amelyek a feladatok számát biztosítják, például $ActiveTasks és $PendingTasks. Az autoskálázási képlettől függően ez azt eredményezheti, hogy a csomópontok nem lesznek elérhetők a feladat kiadási feladatainak futtatásához.

| Változó | Leírás |
| --- | --- |
| $CPUPercent |A CPU-használat átlagos százalékos aránya. |
| $WallClockSeconds |A felhasznált másodpercek száma. |
| $MemoryBytes |A felhasznált megabájtok átlagos száma. |
| $DiskBytes |A helyi lemezeken használt gigabájtok átlagos száma. |
| $DiskReadBytes |A beolvasott bájtok száma. |
| $DiskWriteBytes |A megírt bájtok száma. |
| $DiskReadOps |A végrehajtott olvasási lemez műveleteinek száma. |
| $DiskWriteOps |A végrehajtott írási lemez műveleteinek száma. |
| $NetworkInBytes |A bejövő bájtok száma. |
| $NetworkOutBytes |A kimenő bájtok száma. |
| $SampleNodeCount |A számítási csomópontok száma. |
| $ActiveTasks |A végrehajtásra kész, de még nem végrehajtó feladatok száma. Ebbe beletartozik az összes olyan tevékenység, amely aktív állapotban van, és amelynek függőségei teljesültek. Az aktív állapotú, de a függőségeket nem kielégítő feladatok ki vannak zárva a $ActiveTasks darabszámból. A többpéldányos feladatok esetében $ActiveTasks a feladatban beállított példányok számát is tartalmazza.|
| $RunningTasks |A futó állapotú feladatok száma. |
| $PendingTasks |$ActiveTasks és $RunningTasks összege. |
| $SucceededTasks |A sikeresen befejeződött feladatok száma. |
| $FailedTasks |A sikertelen feladatok száma. |
| $TaskSlotsPerNode |Azon feladat-tárolóhelyek száma, amelyek használatával egyidejű feladatok futtathatók a készlet egyetlen számítási csomópontján. |
| $CurrentDedicatedNodes |A dedikált számítási csomópontok aktuális száma. |
| $CurrentLowPriorityNodes |Az alacsony prioritású számítási csomópontok aktuális száma, beleértve a előzik összes csomópontját. |
| $PreemptedNodeCount | A készletben lévő azon csomópontok száma, amelyek előzik állapotban vannak. |

> [!TIP]
> Ezek a csak olvasási szolgáltatás által definiált változók olyan *objektumok* , amelyek különböző módszereket biztosítanak az egyes adatbázisokhoz kapcsolódó adateléréshez. További információ: [Mintaadatok beszerzése](#obtain-sample-data) a cikk későbbi részében.

> [!NOTE]
> Akkor használja `$RunningTasks` , ha az adott időpontban futó feladatok száma alapján skálázást végez, és `$ActiveTasks` a méretezést a várólistára helyezett feladatok száma alapján hajtja végre.

## <a name="types"></a>Típusok

Az autoskálázási képletek a következő típusokat támogatják:

- double
- doubleVec
- doubleVecList
- sztring
- időbélyeg – a következő tagokat tartalmazó összetett struktúra:
  - év
  - hónap (1-12)
  - nap (1-31)
  - hétköznap (szám formátumban, például 1 – hétfő)
  - óra (24 órás számformátum; például: 13, 1 óra)
  - perc (00-59)
  - második (00-59)
- timeinterval
  - TimeInterval_Zero
  - TimeInterval_100ns
  - TimeInterval_Microsecond
  - TimeInterval_Millisecond
  - TimeInterval_Second
  - TimeInterval_Minute
  - TimeInterval_Hour
  - TimeInterval_Day
  - TimeInterval_Week
  - TimeInterval_Year

## <a name="operations"></a>Üzemeltetés

Ezek a műveletek az előző szakaszban felsorolt típusoknál engedélyezettek.

| Művelet | Támogatott operátorok | Eredmény típusa |
| --- | --- | --- |
| dupla *operátor* dupla |+, -, *, / |double |
| kettős *operátor* TimeInterval |* |timeinterval |
| doubleVec- *kezelő* dupla |+, -, *, / |doubleVec |
| doubleVec *operátor* doubleVec |+, -, *, / |doubleVec |
| TimeInterval- *kezelő* dupla |*, / |timeinterval |
| TimeInterval *operátor* TimeInterval |+, - |timeinterval |
| TimeInterval *operátor* időbélyege |+ |időbélyeg |
| időbélyeg- *kezelő* TimeInterval |+ |időbélyeg |
| időbélyeg- *operátor* időbélyege |- |timeinterval |
| *operátor* dupla |-, ! |double |
| *operátor* TimeInterval |- |timeinterval |
| dupla *operátor* dupla |<, <=, = =, >=, >,! = |double |
| karakterlánc- *operátor* karakterlánca |<, <=, = =, >=, >,! = |double |
| időbélyeg- *operátor* időbélyege |<, <=, = =, >=, >,! = |double |
| TimeInterval *operátor* TimeInterval |<, <=, = =, >=, >,! = |double |
| dupla *operátor* dupla |&&,  &#124;&#124; |double |

Ternáris operátorral () való dupla tesztelés esetén a nem `double ? statement1 : statement2` nulla érték **igaz**, és a nulla **hamis**.

## <a name="functions"></a>Functions

Ezeket az előre definiált **függvényeket** használhatja az autoscale-képletek definiálásához.

| Függvény | Visszatérési típus | Leírás |
| --- | --- | --- |
| átlag (doubleVecList) |double |A doubleVecList lévő összes érték átlagos értékét adja vissza. |
| Len (doubleVecList) |double |A doubleVecList létrehozott vektor hosszát adja vissza. |
| LG (dupla) |double |A dupla log-alapot adja vissza. |
| LG (doubleVecList) |doubleVec |Az összetevő – a doubleVecList 2. alapszintű log-alapját adja vissza. A paraméternek explicit módon át kell adni egy VEC (Double). Ellenkező esetben a rendszer a kettős LG (Double) verziót feltételezi. |
| ln (dupla) |double |A Double típusú természetes naplót adja vissza. |
| ln (doubleVecList) |doubleVec |A Double típusú természetes naplót adja vissza. |
| napló (dupla) |double |A Double napló alapját adja vissza. |
| napló (doubleVecList) |doubleVec |A doubleVecList összetevő-Wise log Base 10-es értékét adja vissza. Az egyszeres dupla paraméterhez explicit módon át kell adni egy VEC (Double). Ellenkező esetben a rendszer a kettős napló (Double) verzióját feltételezi. |
| Max (doubleVecList) |double |A doubleVecList maximális értékét adja vissza. |
| min (doubleVecList) |double |A doubleVecList minimális értékét adja vissza. |
| norma (doubleVecList) |double |A doubleVecList létrehozott vektor két normáját adja vissza. |
| percentilis (doubleVec v, Double p) |double |A Vector v percentilis elemét adja vissza. |
| rand() |double |0,0 és 1,0 közötti véletlenszerű értéket ad vissza. |
| tartomány (doubleVecList) |double |A doubleVecList minimális és maximális értékei közötti különbséget adja vissza. |
| STD (doubleVecList) |double |A doubleVecList lévő értékek mintájának szórását adja vissza. |
| Leállítás () | |Leállítja az automatikus skálázási kifejezés kiértékelését. |
| Sum (doubleVecList) |double |A doubleVecList összes összetevőjének összegét adja vissza. |
| idő (karakterlánc dateTime = "") |időbélyeg |Az aktuális idő időbélyegzőjét adja vissza, ha a rendszer nem ad át paramétereket, vagy ha a dateTime karakterlánc időbélyegzője sikeres. A támogatott dateTime formátumok a W3C-DTF és az RFC 1123. |
| val (doubleVec v, dupla i) |double |A Vector v helyen található elem értékének visszaadása a nulla kezdő indexével. |

Az előző táblázatban leírt függvények némelyike argumentumként is elfogadhatja a listát. A vesszővel tagolt lista a *kettős* és a *doubleVec* bármely kombinációját tartalmazza. Például:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

A *doubleVecList* érték a kiértékelés előtt egyetlen *doubleVec* lesz konvertálva. Például, ha a `v = [1,2,3]` , a hívás megegyezik `avg(v)` a hívással `avg(1,2,3)` . A hívás megegyezik `avg(v, 7)` a hívással `avg(1,2,3,7)` .

## <a name="metrics"></a>Mérőszámok

Képletek definiálásakor az erőforrás és a tevékenység mérőszámait is használhatja. A készletben lévő dedikált csomópontok számát a beszerzett és kiértékelt metrikai adatok alapján állíthatja be. Az egyes metrikákkal kapcsolatos további információkért tekintse meg a fenti [változók](#variables) szakaszt.

<table>
  <tr>
    <th>Metric</th>
    <th>Leírás</th>
  </tr>
  <tr>
    <td><b>Erőforrás</b></td>
    <td><p>Az erőforrás-metrikák a PROCESSZORon, a sávszélességen, a számítási csomópontok memóriahasználat és a csomópontok számán alapulnak.</p>
        <p> Ezek a szolgáltatás által definiált változók a csomópontok számán alapuló módosítások elvégzéséhez hasznosak:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Ezek a szolgáltatás által definiált változók a csomópont-erőforrás-használaton alapuló módosítások elvégzéséhez hasznosak:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Feladat</b></td>
    <td><p>A tevékenységek mérőszámai a feladatok állapotán alapulnak, például az aktív, a függőben lévő és a befejezett műveletekkel. A következő, szolgáltatás által definiált változók hasznosak lehetnek a készletre vonatkozó méretek végrehajtásához a tevékenységek metrikái alapján:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="obtain-sample-data"></a>Mintaadatok beszerzése

Az automatikusan méretezhető képletek alapvető működése a feladatok és az erőforrás-metrikai adatok (minták) beszerzése, majd a készlet méretének módosítása az adatok alapján. Ezért fontos, hogy tisztában legyen azzal, hogy a képletek hogyan működnek együtt a mintákkal.

### <a name="methods"></a>Metódusok

Az autoskálázási képletek a Batch szolgáltatás által biztosított metrikus adatok mintáit határozzák meg. A képlet a beszerzett értékek alapján növeli vagy csökkenti a készlet méretét. A szolgáltatás által definiált változók olyan objektumok, amelyek az adott objektumhoz társított adatelérési metódusokat biztosítanak. A következő kifejezés például egy kérést mutat be a CPU-használat utolsó öt percének beszerzéséhez:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

A következő módszerek használhatók a szolgáltatás által definiált változókra vonatkozó mintaadatok beszerzéséhez.

| Metódus | Leírás |
| --- | --- |
| GetSample() |A `GetSample()` metódus adatmintákból álló vektort ad vissza.<br/><br/>A minta a metrikák adataihoz tartozó 30 másodperc. Más szóval a mintákat 30 másodpercenként szerzi be a rendszer. De ahogy az alábbiakban is látható, a rendszer a mintavétel begyűjtésének és a képletek számára elérhetővé tételének késleltetését jelzi. Így az adott időszakra vonatkozóan nem minden minta lehet egy képlet alapján kiértékelésre.<ul><li>`doubleVec GetSample(double count)`: Meghatározza, hogy a rendszer hány mintát kapjon a legutóbbi összegyűjtött mintákból. `GetSample(1)` az utolsó elérhető mintát adja vissza. A hasonló mérőszámok esetében `$CPUPercent` azonban `GetSample(1)` nem ajánlott használni, mert a minta gyűjtése nem lehetséges.  Lehet, hogy a közelmúltban vagy a rendszerproblémák miatt sokkal régebbi lehet. Ilyen esetekben jobb, ha az alább látható időintervallumot használja.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: Meghatározza a mintaadatok gyűjtésének időkeretét. Azt is meghatározza, hogy a minták hány százalékát kell elérhetőnek lennie a kért időkeretben. Például a `$CPUPercent.GetSample(TimeInterval_Minute * 10)` 20 mintát kell visszaadnia, ha az elmúlt 10 percben az összes minta megtalálható az `CPUPercent` előzményekben. Ha a korábbi előzmények nem voltak elérhetők, csak 18 mintát ad vissza. Ebben az esetben `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` sikertelen lesz, mert a minták csak 90%-a érhető el, de `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` sikeres volt.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: Az adatgyűjtés időkeretét adja meg a kezdési és befejezési időponttal együtt. A fentiekben leírtaknak megfelelően a rendszer a mintavétel begyűjtése és a képletek elérhetővé válása között eltelt időt vesz igénybe. Ezt a késleltetést a metódus használatakor érdemes figyelembe venni `GetSample` . Lásd `GetSamplePercent` alább. |
| GetSamplePeriod() |Egy korábbi mintaadatok-készletben szereplő minták időszakát adja vissza. |
| Darabszám () |A metrikus előzményekben szereplő minták teljes számát adja vissza. |
| HistoryBeginTime() |A metrika legrégebbi rendelkezésre állási mintájának időbélyegét adja vissza. |
| GetSamplePercent() |Egy adott időintervallumhoz elérhető minták százalékos arányát adja vissza. Például: `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. Mivel a `GetSample` metódus meghiúsul, ha a visszaadott minták aránya kisebb, mint a `samplePercent` megadott érték, a `GetSamplePercent` metódussal megtekintheti az elsőt. Ezt követően másik műveletet is végrehajthat, ha nincs elegendő minta, az automatikus skálázás kiértékelésének megállítása nélkül. |

### <a name="samples"></a>Példák

A Batch szolgáltatás rendszeres időközönként mintákat vesz fel a feladat-és erőforrás-mérőszámokból, és elérhetővé teszi azokat az autoscale-képletek számára. Ezeket a mintákat a Batch szolgáltatás 30 másodpercenként rögzíti. A minták rögzítése és a (és az) az autoscale-képletek esetében azonban általában egy késleltetéssel jár. Emellett a minták nem rögzíthetők egy adott intervallumra a hálózati vagy más infrastrukturális problémák miatt.

### <a name="sample-percentage"></a>Minta százaléka

Ha `samplePercent` a rendszer átadja a `GetSample()` metódust vagy a `GetSamplePercent()` metódust, a _százalék_ a Batch szolgáltatás által rögzített összes lehetséges számú minta és az autoskálázási képlet számára elérhető minták számának összehasonlítására utal.

Tekintsük át például egy 10 perces TimeSpan. Mivel a mintákat 30 másodpercenként rögzítik a 10 perces TimeSpan belül, a Batch által rögzített összes minta maximális száma 20 minta (2 percenként). A jelentéskészítési mechanizmus és az Azure-beli egyéb problémák miatt azonban csak 15 olyan minta lehet, amely az autoskálázási képlet számára elérhető az olvasáshoz. Tehát például az adott 10 perces időszakra vonatkozóan a képletnek csak a rögzített minták teljes számának 75%-a lehet elérhető.

### <a name="getsample-and-sample-ranges"></a>GetSample () és mintavételezési tartományok

Az automatikus skálázási képletek a csomópontok hozzáadásával vagy eltávolításával növekednek, és csökkennek a készletek. Mivel a csomópontok pénzbe kerülnek, ügyeljen arra, hogy a képletek olyan intelligens elemzési módszert használjanak, amely elegendő adatmennyiségen alapul. Javasoljuk, hogy a képletekben egy trend típusú elemzést használjon. Ez a típus egyre növekszik, és az összegyűjtött minták alapján csökkenti a készleteket.

Ehhez használja a következő `GetSample(interval look-back start, interval look-back end)` mintákat:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Ha a fenti sort kiértékeli a Batch, a több mintát ad vissza az értékek vektora. Például:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Miután összegyűjtötte a minták vektorát, használhatja a (z), a, a és a függvényt `min()` `max()` `avg()` a begyűjtött tartományból származó értelmes értékek kinyeréséhez.

A további biztonság érdekében kényszerítheti a képletek kiértékelését, ha az adott időszakra vonatkozóan kevesebb mint egy bizonyos minta százalékaránya érhető el. Ha a képlet kiértékelését kényszeríti, a Batch a képlet további kiértékelését utasítja, ha a minták megadott százaléka nem érhető el. Ebben az esetben a készlet méretének módosítása nem történik meg. Ha meg szeretné adni, hogy a kiértékelés sikeres legyen-e a minták százalékában, akkor azt a harmadik paraméterként kell megadni `GetSample()` . Itt meg kell adni a minták 75 százalékának követelményét:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Mivel előfordulhat, hogy a minta rendelkezésre állása késésben van, mindig olyan időtartományt kell megadnia, amely egy percnél régebbi megtekintő kezdési idővel rendelkezik. Körülbelül egy percet vesz igénybe, hogy a minták propagálják a rendszert, így előfordulhat, hogy a tartományokban lévő minták `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` nem lesznek elérhetők. Újra használhatja a százalék paramétert is `GetSample()` egy adott minta százalékos követelmény kényszerítéséhez.

> [!IMPORTANT]
> Nyomatékosan javasoljuk, hogy **ne *csak* az `GetSample(1)` autoscale-képletekben ne kelljen támaszkodni**. Ennek az az oka, `GetSample(1)` hogy lényegében a Batch szolgáltatásra vonatkozik, "a legutóbbi mintát adja meg, függetlenül attól, hogy milyen régen lekérte." Mivel ez csak egyetlen minta, és lehet egy régebbi minta is, nem lehet reprezentatív a legutóbbi feladat vagy erőforrás állapotának nagyobb képére. Ha használja, győződjön `GetSample(1)` meg arról, hogy az egy nagyobb utasítás része, és nem az egyetlen adatpont, amelyet a képlet használ.

## <a name="write-an-autoscale-formula"></a>Autoscale-képlet írása

A fenti összetevőket használó utasítások alapján hozhat létre egy autoskálázási képletet, majd egy teljes képletbe egyesítheti ezeket az utasításokat. Ebben a szakaszban egy olyan automatikus méretezési képletet hozunk létre, amely valós skálázási döntéseket hozhat, és módosításokat hajthat végre.

Először is adjuk meg az új, az új autoskálázási képletre vonatkozó követelményeket. A képletnek a következőket kell tennie:

- Növelje meg a készlet dedikált számítási csomópontjainak megcélzott számát, ha a CPU-használat magas.
- Csökkentse a készlet dedikált számítási csomópontjainak megcélzott számát, ha a CPU-használat alacsony.
- Mindig korlátozza a dedikált csomópontok maximális számát 400-re.
- A csomópontok számának csökkentése esetén ne távolítsa el a feladatokat futtató csomópontokat; Ha szükséges, várjon, amíg a tevékenységek el nem fejeződött a csomópontok eltávolítása előtt.

A képlet első utasítása a magas CPU-használat során növeli a csomópontok számát. Definiálunk egy olyan utasítást, amely egy felhasználó által definiált változót () tölt fel `$totalDedicatedNodes` , amelynek értéke a dedikált csomópontok jelenlegi számának 110%-a, de csak akkor, ha az elmúlt 10 percben az átlagos CPU-használat a 70 százalék felett volt. Ellenkező esetben a a dedikált csomópontok aktuális számának értékét használja.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Ha csökkenteni szeretné a dedikált csomópontok számát az alacsony CPU-használat során, a képlet következő utasítása ugyanazt a változót állítja be a `$totalDedicatedNodes` dedikált csomópontok jelenlegi számának 90 százalékára, ha az elmúlt 60 percben az átlagos CPU-használat 20 százalék alatti volt. Ellenkező esetben a `$totalDedicatedNodes` fenti nyilatkozatban kitöltött aktuális értéket használja.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Most a dedikált számítási csomópontok megcélzott számát legfeljebb 400-ra korlátozzuk.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Végezetül biztosítjuk, hogy a csomópontok ne legyenek eltávolítva a feladatok befejezéséig.

```
$NodeDeallocationOption = taskcompletion;
```

A teljes képlet:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
$NodeDeallocationOption = taskcompletion;
```

> [!NOTE]
> Ha úgy dönt, hogy a lehetőséget választja, a képletek karakterláncában is szerepelhetnek megjegyzések és sortörések is. Azt is vegye figyelembe, hogy a hiányzó pontosvesszők kiértékelési hibákat okozhatnak.

## <a name="automatic-scaling-interval"></a>Automatikus skálázási időköz

Alapértelmezés szerint a Batch szolgáltatás 15 percenként úgy állítja be a készlet méretét, hogy az automatikusan méretezi a képletet. Ez az intervallum a következő készlet tulajdonságainak használatával konfigurálható:

- [CloudPool. AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Batch .net)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

A minimális időköz öt perc, a maximális érték 168 óra. Ha a tartományon kívüli intervallum van megadva, a Batch szolgáltatás hibás kérést ad vissza (400).

> [!NOTE]
> Az automatikus skálázás jelenleg nem arra szolgál, hogy egy percnél rövidebb ideig válaszoljon a változásokra, ehelyett a számítási feladatok futtatásakor fokozatosan módosítania kell a készlet méretét.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>A Batch SDK-val rendelkező, automéretezést támogató készlet létrehozása

A készlet automatikus skálázása a Batch [SDK](batch-apis-tools.md#azure-accounts-for-batch-development)-k, a Batch [REST API](/rest/api/batchservice/) a Batch [PowerShell-parancsmagok](batch-powershell-cmdlets-get-started.md)és a [Batch CLI](batch-cli-get-started.md)használatával konfigurálható. Ebben a szakaszban a .NET és a Python esetében is láthat példákat.

### <a name="net"></a>.NET

A .NET-ben engedélyezett automatikus skálázással rendelkező készlet létrehozásához kövesse az alábbi lépéseket:

1. Hozza létre a készletet a [BatchClient. PoolOperations. CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Állítsa a [CloudPool. AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) tulajdonságot a következőre: `true` .
1. Állítsa be az [CloudPool. AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) tulajdonságot az autoscale képlettel.
1. Választható A [CloudPool. AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) tulajdonság beállítása (az alapértelmezett érték 15 perc).
1. Véglegesítse a készletet a [CloudPool. commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) vagy a [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Az alábbi példa egy automatikusan méretezhető készletet hoz létre a .NET-ben. A készlet autoskálázási képlete a dedikált csomópontok megcélzott számát 5 – hétfő és 1 értékre állítja be a hét minden második napján. Az [automatikus skálázási időköz](#automatic-scaling-interval) 30 percre van beállítva. Ebben és a cikkben szereplő többi C#-kódrészlet a `myBatchClient` [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) osztály megfelelően inicializált példánya.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Amikor létrehoz egy autoscale-kompatibilis készletet, ne a _targetDedicatedNodes_ paramétert, vagy a _targetLowPriorityNodes_ paramétert a **CreatePool** hívására állítsa be. Ehelyett a **AutoScaleEnabled** és a **AutoScaleFormula** tulajdonságokat kell megadnia a készleten. Ezen tulajdonságok értékei határozzák meg az egyes csomópontok céljának számát.
>
> Az automatikus méretezést engedélyező készlet (például a [BatchClient. PoolOperations. ResizePoolAsync](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)) manuális átméretezéséhez először le kell tiltania az automatikus skálázást a készleten, majd át kell méreteznie.

### <a name="python"></a>Python

Az autoskálázást támogató készlet létrehozása a Python SDK-val:

1. Hozzon létre egy készletet, és határozza meg annak konfigurációját.
1. Adja hozzá a készletet a szolgáltatás ügyfeléhez.
1. Az autoskálázás engedélyezése a készleten egy írásos képlettel.

A következő példa ezeket a lépéseket szemlélteti.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
             $isWorkingWeekdayHour = $workHours && $isWeekday;
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10),
                                            pool_enable_auto_scale_options=None,
                                            custom_headers=None, raw=False)
```

> [!TIP]
> A Python SDK használatával kapcsolatban további példákat talál a [Batch Python](https://github.com/Azure-Samples/batch-python-quickstart) rövid útmutatójában a githubon.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Automatikus skálázás engedélyezése meglévő készleten

Minden batch SDK lehetővé teszi az automatikus skálázást. Például:

- [BatchClient. PoolOperations. EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (Batch .net)
- [Automatikus skálázás engedélyezése egy készleten](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

Ha egy meglévő készleten engedélyezi az automatikus skálázást, vegye figyelembe a következőket:

- Ha az automatikus skálázás jelenleg le van tiltva a készleten, érvényes automatikus méretezési képletet kell megadnia a kérelem kiadásakor. Opcionálisan megadhat egy automatikus skálázási időközt is. Ha nem ad meg intervallumot, a rendszer az alapértelmezett 15 perces értéket használja.
- Ha az automatikus skálázás jelenleg engedélyezve van a készleten, megadhat egy új képletet, egy új intervallumot vagy mindkettőt. Legalább egy ilyen tulajdonságot meg kell adnia.
  - Ha új automatikus skálázási időközt ad meg, a rendszer leállítja a meglévő ütemtervet, és új ütemtervet indít el. Az új ütemezett kezdési idő az az idő, amikor az automatikus skálázás engedélyezésére vonatkozó kérést kiállították.
  - Ha kihagyja az automatikusan méretezhető képletet vagy intervallumot, a Batch szolgáltatás továbbra is a beállítás aktuális értékét fogja használni.

> [!NOTE]
> Ha az **CreatePool** metódus *targetDedicatedNodes* vagy *targetLowPriorityNodes* paraméterei számára megadott értékeket a készlet .net-ben történő létrehozásakor vagy egy másik nyelvben szereplő hasonló paraméterek esetében, akkor a rendszer figyelmen kívül hagyja ezeket az értékeket az autoskálázási képlet kiértékelése során.

Ez a C# példa a [Batch .net](/dotnet/api/microsoft.azure.batch) -függvénytárat használja az automatikus skálázás egy meglévő készleten való engedélyezéséhez.

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Az autoscale-képlet frissítése

Ha egy meglévő automatikus méretezést használó készleten szeretné frissíteni a képletet, hívja meg a műveletet, és engedélyezze újra az automatikus skálázást az új képlettel. Ha például `myexistingpool` a következő .net-kód végrehajtásakor már engedélyezve van az automatikus skálázás, az automatikus méretezési képlete helyére a tartalma kerül `myNewFormula` .

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Az autoskálázási időköz frissítése

Egy meglévő Automatikus méretezéssel rendelkező készlet automatikus méretezési értékelési intervallumának frissítéséhez hívja meg a műveletet, és engedélyezze újra az automatikus skálázást az új intervallummal. Ha például az autoscale kiértékelési intervallumát 60 percre szeretné beállítani egy olyan készlethez, amely már engedélyezve van a .NET-ben:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Az autoscale képlet kiértékelése

Kiértékelheti a képletet, mielőtt alkalmazná egy készletre. Ez lehetővé teszi a képlet eredményének tesztelését, mielőtt éles környezetbe helyezné őket.

Az automatikus méretezési képlet kiértékelése előtt engedélyeznie kell az automatikus skálázást a készleten érvényes képlettel, például az egysoros képlettel `$TargetDedicatedNodes = 0` . Ezután a következők egyikével értékelje ki a tesztelni kívánt képletet:

- [BatchClient. PoolOperations. EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) vagy [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Ezek a Batch .NET-metódusok megkövetelik egy meglévő készlet AZONOSÍTÓját, valamint egy olyan karakterláncot, amely a kiértékeléshez az autoskálázási képletet tartalmazza.

- [Automatikus skálázási képlet kiértékelése](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Ebben az REST API-kérelemben adja meg a készlet AZONOSÍTÓját az URI-ban, valamint az autoskálázási képletet a *autoScaleFormula* elemben. A művelet válasza tartalmaz minden olyan hibaüzenetet, amely lehet a képlethez köthető.

Ez a [Batch .net](/dotnet/api/microsoft.azure.batch) -példa egy autoskálázási képletet értékel ki. Ha a készlet még nem használja az automatikus skálázást, először engedélyezzük.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on a non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Az ebben a kódrészletben látható képlet sikeres kiértékelése a következőhöz hasonló eredményeket eredményez:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Az autoscale-futtatásokkal kapcsolatos információk beolvasása

Annak érdekében, hogy a képlet a várt módon legyen végrehajtva, javasoljuk, hogy rendszeresen ellenőrizze az automatikus skálázás futtatásának eredményét, hogy a Batch végrehajtsa a készletet. Ehhez szerezzen be (vagy frissítsen) egy hivatkozást a készletre, majd vizsgálja meg az utolsó méretezési Futtatás tulajdonságait.

A Batch .NET-ben a [CloudPool. AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) tulajdonság több olyan tulajdonsággal rendelkezik, amelyek információt nyújtanak a készleten végrehajtott legújabb automatikus skálázási futtatásról:

- [AutoScaleRun. timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun. Results](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun. error](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

A REST API a [készletre vonatkozó kérés információi](/rest/api/batchservice/get-information-about-a-pool) a készletre vonatkozó információkat adnak vissza, amely magában foglalja a legújabb automatikus skálázási futtatási információkat a [autoScaleRun](/rest/api/batchservice/get-information-about-a-pool) tulajdonságban.

A következő C#-példa a Batch .NET-függvénytárat használja a készlet _myPool_ utolsó automatikus skálázási futtatásával kapcsolatos információk nyomtatásához.

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Példa az előző példából származó kimenetre:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>Az autoskálázás futtatási előzményeinek beolvasása a készlet autoskálázási eseményeivel
Az automatikus skálázási előzményeket a [PoolAutoScaleEvent](batch-pool-autoscale-event.md)lekérdezésével is megtekintheti. Ezt az eseményt a Batch szolgáltatás bocsátja ki, amely rögzíti az autoscale Formula kiértékelésének és végrehajtásának minden előfordulását, ami hasznos lehet a lehetséges problémák elhárításához.

Példa a PoolAutoScaleEvent:
```json
{
    "id": "poolId",
    "timestamp": "2020-09-21T23:41:36.750Z",
    "formula": "...",
    "results": "$TargetDedicatedNodes=10;$NodeDeallocationOption=requeue;$curTime=2016-10-14T18:36:43.282Z;$isWeekday=1;$isWorkingWeekdayHour=0;$workHours=0",
    "error": {
        "code": "",
        "message": "",
        "values": []
    }
}
```

## <a name="example-autoscale-formulas"></a>Példa az autoskálázási képletekre

Nézzük meg néhány képletet, amelyek különböző módszereket mutatnak be a készletben lévő számítási erőforrások mennyiségének módosítására.

### <a name="example-1-time-based-adjustment"></a>1. példa: időalapú beállítás

Tegyük fel, hogy a készlet méretét a hét napja és a nap időpontja alapján szeretné beállítani. Ebből a példából megtudhatja, hogyan növelheti vagy csökkentheti a készlet csomópontjainak számát.

A képlet először az aktuális időt kapja meg. Ha hétköznap (1-5) és munkaidőn (8 – 6 ÓRAKOR) belül van megadva, a célként megadott készlet mérete 20 csomópont. Ellenkező esetben 10 csomópontra van beállítva.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

`$curTime` a helyi időzónának megfelelően módosítható úgy, hogy hozzáadja `time()` a termékhez `TimeZoneInterval_Hour` és az UTC-eltoláshoz. Például `$curTime = time() + (-6 * TimeInterval_Hour);` a hegyvidéki nyári idő (MDT) használatával. Ne feledje, hogy az eltolást a nyári időszámítási idő elején és végén kell módosítani (ha van ilyen).

### <a name="example-2-task-based-adjustment"></a>2. példa: feladat-alapú beállítás

Ebben a C# példában a készlet méretét a várólista feladatai száma alapján korrigáljuk. A képletek karakterláncában a megjegyzések és a sortörések is szerepelnek.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>3. példa: párhuzamos feladatok könyvelése

Ez a C#-példa a feladatok száma alapján módosítja a készlet méretét. Ez a képlet a készlethez beállított [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) értéket is figyelembe veszi. Ez a megközelítés olyan helyzetekben hasznos, amikor engedélyezve van a [párhuzamos feladatok végrehajtása](batch-parallel-node-tasks.md) a készleten.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks
// (the TaskSlotsPerNode property on this pool is set to 4, adjust
// this number for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>4. példa: a készlet kezdeti méretének beállítása

Ez a példa egy C#-példát mutat be, amely egy, a kezdeti időszakra vonatkozóan megadott számú csomópontra állítja be a készlet méretét. Ezután a futtatott és az aktív feladatok száma alapján módosítja a készlet méretét.

A képlet a következő műveleteket végzi el:

- A készlet kezdeti méretének beállítása négy csomópontra.
- A készlet életciklusának első 10 percén belül nem módosítja a készlet méretét.
- 10 perc elteltével az elmúlt 60 percben megszerezheti a futó és az aktív feladatok maximális értékét.
  - Ha mindkét érték 0 (ami azt jelzi, hogy az utolsó 60 percben nem futnak vagy nem voltak aktívak), a készlet mérete 0.
  - Ha bármelyik érték nagyobb nullánál, a rendszer nem végez módosítást.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [hajthat végre egyszerre több feladatot a készlet számítási csomópontjain](batch-parallel-node-tasks.md). Az automatikus skálázással együtt az egyes munkaterhelések esetében csökkentheti a feladatok időtartamát, így pénzt takaríthat meg.
- Megtudhatja, hogyan lehet [hatékonyan lekérdezni a Azure batch szolgáltatást](batch-efficient-list-queries.md) a további hatékonyság érdekében.
