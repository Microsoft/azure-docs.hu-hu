---
title: Számítási csomópontok automatikus méretezése egy Azure Batch-készletben
description: Engedélyezze az automatikus skálázást a felhőkészleten a készletben lévő számítási csomópontok számának dinamikus módosításához.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: f70c29f0e8a313233991c7363dc4b8a41a1b1cd5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389366"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Automatikus képlet létrehozása számítási csomópontok méretezése egy Batch-készletben

Azure Batch automatikusan skálázhatja a készleteket az Ön által megadott paraméterek alapján, így időt és pénzt takaríthat meg. Az automatikus skálázásnak megfelelően a Batch dinamikusan ad hozzá csomópontokat a készlethez a tevékenységek igényeinek növekedésével, és eltávolítja a számítási csomópontokat a tevékenységek igényeinek csökkenésével.

Ha engedélyezni szeretné az automatikus skálázást a számítási csomópontok készletében, társítsa *a* készletet egy Ön által definiált automatikus skálázási képlettel. A Batch szolgáltatás az automatikus skálázási képlettel határozza meg, hogy hány csomópontra van szükség a számítási feladat végrehajtásához. Ezek a csomópontok lehetnek dedikált csomópontok vagy [alacsony prioritású csomópontok.](batch-low-pri-vms.md) A Batch ezután rendszeres időközönként áttekinti a szolgáltatásmetrika-adatokat, és a készletben lévő csomópontok számát a képlet alapján, az Ön által megadott időközönként módosítja.

Az automatikus skálázást akkor engedélyezheti, ha létrehoz egy készletet, vagy egy meglévő készletre alkalmazza. A Batch lehetővé teszi a képletek kiértékelét, mielőtt készletekhez rendeli őket, és megfigyelheti az automatikus méretezési futtatásokat. Miután konfigurált egy készletet automatikus méretezéssel, később is módosíthatja a képletet.

> [!IMPORTANT]
> Batch-fiók létrehozásakor megadhatja a [](accounts.md)készletfelosztási módot, amely meghatározza, hogy a készletek egy Batch-szolgáltatás-előfizetésben (alapértelmezés) vagy a felhasználói előfizetésben vannak-e lefoglalva. Ha a Batch-fiókot az alapértelmezett Batch szolgáltatáskonfigurációval hozta létre, akkor a fiók a feldolgozáshoz használható magok maximális számára van korlátozva. A Batch szolgáltatás a számítási csomópontokat csak a magkorlátnak megfelelő méretre skáláz. Emiatt előfordulhat, hogy a Batch szolgáltatás nem éri el az automatikus skálázási képlet által megadott számítási csomópontok célszámát. A [fiókkvóták megtekintésével](batch-quota-limit.md) és növelésével kapcsolatos információkért lásd a Azure Batch szolgáltatás kvótái és korlátait.
>
>Ha felhasználói előfizetési móddal hozta létre a fiókját, akkor a fiókja az előfizetés magkvótáját használja. További információkért lásd [az Azure-előfizetésekre és -szolgáltatásokra vonatkozó korlátozásokat, kvótákat és megkötéseket](../azure-resource-manager/management/azure-subscription-service-limits.md) ismertető témakör [a virtuális gépek korlátaira](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) vonatkozó részét.

## <a name="autoscale-formulas"></a>Automatikus méretezési képletek

Az automatikus méretezési képlet egy olyan sztringérték, amely egy vagy több utasításból áll. Az automatikus méretezési képletet a rendszer hozzárendeli egy készlet [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) elemhez (Batch REST) vagy [CloudPool.AutoScaleFormula tulajdonsághoz](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) (Batch .NET). A Batch szolgáltatás a képlet alapján határozza meg a készletben lévő számítási csomópontok célszámát a következő feldolgozási időszakra. A képletsring nem haladhatja meg a 8 KB-t, legfeljebb 100 pontosvesszővel elválasztott utasításokat tartalmazhat, valamint sortöréseket és megjegyzéseket is tartalmazhat.

Az automatikus skálázási képletek a Batch automatikus skálázási "nyelvének" ként is feletethetnek. A képlet utasítások szabadon formázott kifejezések, amelyek szolgáltatás által definiált (a Batch szolgáltatás által definiált) változókat és felhasználó által definiált változókat is tartalmazhatnak. A képletek különböző műveleteket hajthat végre ezeken az értékeken beépített típusok, operátorok és függvények használatával. Egy utasítás például a következő formában lehet:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

A képletek általában több olyan utasításokat tartalmaznak, amelyek az előző utasításokban kapott értékeken hajtják végre a műveleteket. Először például be kell szerezenünk egy értéket a számára, majd át kell adni egy `variable1` függvénynek a `variable2` feltöltéséhez:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Foglalja bele ezeket az utasításokat az automatikus skálázási képletbe, hogy a számítási csomópontok célszámára érkezzen. A dedikált csomópontoknak és az alacsony prioritású csomópontoknak saját célbeállításaik vannak. Az automatikus méretezési képlet tartalmazhat célértéket a dedikált csomópontokhoz, egy célértéket az alacsony prioritású csomópontokhoz, vagy mindkettőt.

A csomópontok célszáma lehet magasabb, alacsonyabb, vagy megegyezik az adott típusú csomópontok aktuális számmal a készletben. A Batch egy adott automatikus skálázási időközönként értékeli ki a készlet [automatikus skálázási képletét.](#automatic-scaling-interval) A Batch a készlet egyes csomóponttípusai célszámát az automatikus méretezési képlet által a kiértékeléskor megadott számra állítja be.

### <a name="sample-autoscale-formulas"></a>Példa automatikus méretezési képletre

Az alábbiakban két automatikus skálázási képletet mutatunk be, amelyek a legtöbb forgatókönyvhöz módosíthatók. A változók és a `startingNumberOfVMs` `maxNumberofVMs` példaképletekben használt képletek igény szerint módosíthatók.

#### <a name="pending-tasks"></a>Függőben lévő feladatok

Ezzel az automatikus skálázási képlettel a készlet először egyetlen virtuális géphez jön létre. A `$PendingTasks` metrika határozza meg a futó vagy várakozó tevékenységek számát. A képlet megkeresi a függőben lévő tevékenységek átlagos számát az elmúlt 180 másodpercben, és ennek megfelelően állítja `$TargetDedicatedNodes` be a változót. A képlet biztosítja, hogy a dedikált csomópontok célszáma soha ne haladja meg a 25 virtuális gépét. Az új feladatok beküldtével a készlet automatikusan növekszik. A feladatok befejeződésével a virtuális gépek szabadok lesznek, és az automatikus skálázás képlete zsugorodik a készlettel.

Ez a képlet skálázja a dedikált csomópontokat, de módosítható úgy, hogy az alacsony prioritású csomópontok skálázhatóak is legyen.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Előre meghatározott csomópontok

Ez a példa létrehoz egy készletet, amely 25 alacsony prioritású csomóponttal kezdődik. Minden alkalommal, amikor egy alacsony prioritású csomópont elő van ásva, azt egy dedikált csomópont váltja fel. Ahogy az első példában is, a változó megakadályozza, hogy a készlet meghaladja `maxNumberofVMs` a 25 virtuális gépét. Ez a példa akkor hasznos, ha alacsony prioritású virtuális gépeket használ, ugyanakkor gondoskodik arról, hogy a készlet teljes élettartama során csak meghatározott számú előtag legyen bekövetkezve.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Az automatikus méretezési [képletek](#write-an-autoscale-formula) létrehozásáról és az [](#example-autoscale-formulas) automatikus skálázási képletek további példáiról a témakör későbbi, további példáiban olvashat.

## <a name="variables"></a>Változók

Az automatikus **méretezési** képletekben szolgáltatás által definiált és felhasználó által definiált változókat is használhat. 

A szolgáltatás által definiált változók a Batch szolgáltatásba vannak beépítve. Egyes szolgáltatás által definiált változók olvashatók és írhatóak, némelyik pedig csak olvasható.

A felhasználó által definiált változók az Ön által definiált változók. A fent látható példaképletben a és a szolgáltatás által definiált változók, míg a és `$TargetDedicatedNodes` a felhasználó által definiált `$PendingTasks` `startingNumberOfVMs` `maxNumberofVMs` változók.

> [!NOTE]
> A szolgáltatás által definiált változókat mindig dollárjel ($) előzi meg. Felhasználó által megadott változók esetén a dollárjel megadása nem kötelező.

Az alábbi táblázatok a Batch szolgáltatás által definiált írási és olvasási változókat mutatják be.

### <a name="read-write-service-defined-variables"></a>Írási/olvasási szolgáltatás által definiált változók

Ezeknek a szolgáltatás által definiált változóknak az értékeit lekérte és beállíthatja a készletben lévő számítási csomópontok számának kezeléséhez.

| Változó | Leírás |
| --- | --- |
| $TargetDedicatedNodes |A készlet dedikált számítási csomópontjainak célszáma. Ez célként van megadva, mert a készlet nem mindig éri el a kívánt számú csomópontot. Ha például a dedikált csomópontok célszámát egy automatikus méretezési értékelés módosítja, mielőtt a készlet elérnék a kezdeti célt, előfordulhat, hogy a készlet nem éri el a célt. <br /><br /> Előfordulhat, hogy a Batch szolgáltatás módban létrehozott fiókban található készlet nem éri el a célértékét, ha a cél meghaladja a Batch-fiók csomópontját vagy magkvótáját. Előfordulhat, hogy a felhasználói előfizetési módban létrehozott fiókban található készlet nem éri el a célértékét, ha a cél túllépi az előfizetés megosztott magkvótáját.|
| $TargetLowPriorityNodes |A készlet alacsony prioritású számítási csomópontjainak célszáma. Ez azért van megadva célként, mert a készlet nem mindig éri el a kívánt számú csomópontot. Ha például az alacsony prioritású csomópontok célszámát egy automatikus méretezési értékelés módosítja, mielőtt a készlet elérnék a kezdeti célt, előfordulhat, hogy a készlet nem éri el a célt. Előfordulhat, hogy egy készlet akkor sem éri el a célértékét, ha a cél meghaladja a Batch-fiók csomópontját vagy magkvótáját. <br /><br /> Az alacsony prioritású számítási csomópontokkal kapcsolatos további információkért lásd: Alacsony prioritású virtuális [gépek használata a Batch használatával.](batch-low-pri-vms.md) |
| $NodeDeallocationOption |A számítási csomópontok készletből való eltávolításakor bekövetkező művelet. Lehetséges értékek:<ul><li>**requeue**: Az alapértelmezett érték. Azonnal befejezi a tevékenységeket, és visszateszi őket a feladat várólistájára, hogy átütemezték őket. Ez a művelet biztosítja, hogy a csomópontok célszáma a lehető leggyorsabban elérhető legyen. Előfordulhat azonban, hogy kevésbé hatékony, mivel a futó tevékenységek megszakadnak, majd teljesen újra kell indítani őket. <li>**terminate**: Azonnal befejezi a tevékenységeket, és eltávolítja őket a feladatsorból.<li>**taskcompletion:** Megvárja, amíg az aktuálisan futó tevékenységek befejeződnek, majd eltávolítja a csomópontot a készletből. Ezzel a beállítással elkerülheti, hogy a tevékenységek megszakadnak és újrasoroljanak, és feleslegesen pazarolják a tevékenység által végzett munkát.<li>**retaineddata:** Megvárja, amíg a csomóponton található összes helyi tevékenység által megőrzett adat el lesz távolítva, mielőtt eltávolítaná a csomópontot a készletből.</ul> |

> [!NOTE]
> A `$TargetDedicatedNodes` változó az alias használatával is meg lehet `$TargetDedicated` adni. A változó `$TargetLowPriorityNodes` hasonlóképpen a alias használatával is meg lehet `$TargetLowPriority` adni. Ha a képlet a teljesen elnevezett változót és az aliasát is beállítja, a teljesen elnevezett változóhoz rendelt érték élvez elsőbbséget.

### <a name="read-only-service-defined-variables"></a>Csak olvasható, szolgáltatás által definiált változók

Ezeknek a szolgáltatás által definiált változóknak az értékét lekértve a Batch szolgáltatás metrikai alapján végez módosításokat.

> [!IMPORTANT]
> A feladat-kiadási tevékenységek jelenleg nem szerepelnek olyan változókban, amelyek feladatszámokat biztosítanak, például $ActiveTasks és $PendingTasks. Az automatikus méretezési képlettől függően ez azt eredményezheti, hogy a csomópontok el vannak távolítva, és nem állnak rendelkezésre csomópontok a feladat-kiadási tevékenységek futtatásához.

| Változó | Leírás |
| --- | --- |
| $CPUPercent |A processzorhasználat átlagos százalékos aránya. |
| $WallClockSeconds |A felhasznált másodpercek száma. |
| $MemoryBytes |A felhasznált megabájtok átlagos száma. |
| $DiskBytes |A helyi lemezeken használt gigabájtok átlagos száma. |
| $DiskReadBytes |Az olvasott bájtok száma. |
| $DiskWriteBytes |Az írt bájtok száma. |
| $DiskReadOps |Az olvasási lemezműveletek száma. |
| $DiskWriteOps |A végrehajtott írási lemezműveletek száma. |
| $NetworkInBytes |A bejövő bájtok száma. |
| $NetworkOutBytes |A kimenő bájtok száma. |
| $SampleNodeCount |A számítási csomópontok száma. |
| $ActiveTasks |A végrehajtásra kész, de még nem végrehajtható feladatok száma. Ez magában foglalja az aktív állapotban lévő összes olyan feladatot, amelynek függőségei teljesültek. Az aktív állapotban lévő, de nem teljesült függőségekkel kapcsolatos tevékenységek ki lesznek zárva a $ActiveTasks számból. Többpéldányos feladatok esetén a $ActiveTasks tartalmazni fogja a feladathoz beállított példányok számát.|
| $RunningTasks |A futó állapotban futó feladatok száma. |
| $PendingTasks |A $ActiveTasks és $RunningTasks. |
| $SucceededTasks |A sikeresen befejezett tevékenységek száma. |
| $FailedTasks |A meghiúsult feladatok száma. |
| $TaskSlotsPerNode |Azon tevékenységhelyek száma, amelyek párhuzamos tevékenységek futtatására használhatók a készlet egyetlen számítási csomópontján. |
| $CurrentDedicatedNodes |A dedikált számítási csomópontok aktuális száma. |
| $CurrentLowPriorityNodes |Az alacsony prioritású számítási csomópontok aktuális száma, beleértve az esetlegesen előemelt csomópontokat is. |
| $PreemptedNodeCount | A készletben lévő, előtagú csomópontok száma. |

> [!TIP]
> Ezek a csak olvasható, szolgáltatás  által definiált változók olyan objektumok, amelyek különböző módszereket biztosítanak az egyes változókhoz tartozó adatok eléréséhez. További információt a cikk későbbi, [Mintaadatok beszerzése](#obtain-sample-data) cikkében talál.

> [!NOTE]
> Akkor használja, ha az adott időpontban futó tevékenységek száma alapján, illetve a futtatáskor várólistán lévő tevékenységek száma alapján `$RunningTasks` `$ActiveTasks` skáláz.

## <a name="types"></a>Típusok

Az automatikus méretezési képletek a következő típusokat támogatják:

- double
- doubleVec
- doubleVecList
- sztring
- timestamp – összetett struktúra, amely a következő tagokat tartalmazza:
  - év
  - hónap (1-12)
  - nap (1-31)
  - weekday (szám formátumban, például 1 hétfő)
  - hour (24 órás számformátumban; a 13 például 13 pm-et jelent)
  - perc (00-59)
  - másodperc (00-59)
- timeinterval (időinterval)
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

Ezek a műveletek az előző szakaszban felsorolt típusokon engedélyezettek.

| Művelet | Támogatott operátorok | Eredmény típusa |
| --- | --- | --- |
| double *operátor* |+, -, *, / |double |
| double *operátor* timeinterval |* |timeinterval (időinterval) |
| doubleVec *operátor double* |+, -, *, / |doubleVec |
| doubleVec *operátor* doubleVec |+, -, *, / |doubleVec |
| timeinterval *operátor double* |*, / |timeinterval (időinterval) |
| timeinterval *operátor* timeinterval |+, - |timeinterval (időinterval) |
| timeinterval *operátor időbélyege* |+ |időbélyeg |
| timestamp *operátor* timeinterval |+ |időbélyeg |
| időbélyegző *operátor időbélyege* |- |timeinterval (időinterval) |
| *operátor* double |-, ! |double |
| *operátori* időinterval |- |timeinterval (időinterval) |
| double *operátor* |<, <=, ==, >=, >, != |double |
| sztring *operátori* sztring |<, <=, ==, >=, >, != |double |
| timestamp *operátor* időbélyegzője |<, <=, ==, >=, >, != |double |
| timeinterval *operátor* timeinterval |<, <=, ==, >=, >, != |double |
| double *operátor* |&&, &#124;&#124; |double |

Ha egy dupla értéket tesztel egy ternáris operátorral ( ), a nonzero igaz , a `double ? statement1 : statement2` nulla pedig **hamis.** 

## <a name="functions"></a>Functions

Ezeket az előre meghatározott függvényeket **használhatja** az automatikus skálázási képletek definiálása során.

| Függvény | Visszatérési típus | Leírás |
| --- | --- | --- |
| avg(doubleVecList) |double |A doubleVecList összes értékének átlagértékét adja vissza. |
| len(doubleVecList) |double |A doubleVecList alapján létrehozott vektor hosszát adja vissza. |
| lg(double) |double |A dupla 2. naplóbázisát adja vissza. |
| lg(doubleVecList) |doubleVec |A doubleVecList 2. összetevőre alapként megadott naplóbázisát adja vissza. A vec(double) paramétert explicit módon át kell adnunk a paraméternek. Ellenkező esetben a rendszer a dupla lg(double) verziót feltételezi. |
| ln(double) |double |A double természetes naplóját adja vissza. |
| ln(doubleVecList) |doubleVec |A double természetes naplóját adja vissza. |
| log(double) |double |A kettős érték 10-es naplóbázisát adja vissza. |
| log(doubleVecList) |doubleVec |A doubleVecList 10 összetevőre alapként megadott naplóbázisát adja vissza. A vec(double) paramétert explicit módon kell átadva az egyetlen dupla paraméterhez. Ellenkező esetben a rendszer a double log(double) verziót feltételezi. |
| max(doubleVecList) |double |A doubleVecList maximális értékét adja vissza. |
| min(doubleVecList) |double |A doubleVecList minimális értékét adja vissza. |
| norm(doubleVecList) |double |A doubleVecList alapján létrehozott vektor két normáját adja vissza. |
| percentile(doubleVec v, double p) |double |A v vektor percentilis elemét adja vissza. |
| rand() |double |Egy 0,0 és 1,0 közötti véletlenszerű értéket ad vissza. |
| range(doubleVecList) |double |A doubleVecList minimális és maximális értékei közötti különbséget adja vissza. |
| std(doubleVecList) |double |A doubleVecList értékeinek szórását adja vissza. |
| stop() | |Leállítja az automatikus skálázás kifejezés kiértékelését. |
| sum(doubleVecList) |double |A doubleVecList összes összetevőének összegét adja vissza. |
| time(sztring dateTime="") |időbélyeg |Az aktuális idő időbélyegét adja vissza, ha nem ad át paramétereket, vagy a dateTime sztring időbélyegét, ha ez meg van telve. A támogatott dateTime formátumok a W3C-DTF és az RFC 1123. |
| val(doubleVec v, double i) |double |Az i helyen található elem értékét adja vissza a vektor v-ban, nulla kezdőindexszel. |

Az előző táblázatban leírt függvények némelyike elfogadhat egy listát argumentumként. A vesszővel elválasztott lista a *double* és *doubleVec* bármely kombinációja. Például:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

A *doubleVecList* érték a kiértékelés előtt egyetlen *doubleVec értékké* lesz konvertálva. Ha például a , akkor `v = [1,2,3]` a `avg(v)` hívása egyenértékű az `avg(1,2,3)` hívásának. A `avg(v, 7)` hívása egyenértékű a hívásának. `avg(1,2,3,7)`

## <a name="metrics"></a>Mérőszámok

Képlet definiálása során erőforrás- és tevékenységmetrikákat is használhat. A készletben található dedikált csomópontok célszámát a lekért és kiértékelt metrikaadatok alapján módosíthatja. Az egyes metrikákról a fenti [Változók szakaszban](#variables) található további információ.

<table>
  <tr>
    <th>Metric</th>
    <th>Leírás</th>
  </tr>
  <tr>
    <td><b>Erőforrás</b></td>
    <td><p>Az erőforrás-metrikák a processzoron, a sávszélességen, a számítási csomópontok memóriahasználatán és a csomópontok számán alapulnak.</p>
        <p> Ezek a szolgáltatás által definiált változók hasznosak a csomópontok száma alapján való módosításokhoz:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Ezek a szolgáltatás által definiált változók hasznosak a csomóponterőforrás-használat alapján való módosításokhoz:</p>
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
    <td><p>A tevékenységmetrikák a tevékenységek állapotán alapulnak, például Aktív, Függőben és Befejezve. A következő szolgáltatás által definiált változók hasznosak a készletméret-módosítások végrehajtásához a tevékenységmetrikák alapján:</p>
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

Az automatikus méretezési képletek alapvető művelete a feladat- és erőforrásmetrika-adatok (minták) beszerzése, majd a készlet méretének módosítása az adatok alapján. Ezért fontos, hogy pontosan tudja, hogyan kommunikálnak az automatikus méretezési képletek a mintákkal.

### <a name="methods"></a>Metódusok

Az automatikus méretezési képletek a Batch szolgáltatás által biztosított metrikaadatok mintáira biztosítanak adatokat. A képletek a készlet mérete a bejövekedő értékek alapján növekednek vagy csökkennek. A szolgáltatás által definiált változók olyan objektumok, amelyek metódusokat biztosítanak az adott objektumhoz társított adatok eléréséhez. Az alábbi kifejezés például az utolsó öt perc CPU-használat lekérését kéri le:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

A következő metódusok használhatók a szolgáltatás által definiált változók mintaadatának beszerzésére.

| Metódus | Leírás |
| --- | --- |
| GetSample() |A `GetSample()` metódus adatminták vektorát adja vissza.<br/><br/>A minta 30 másodpercnyi metrikaadatot tartalmaz. Ez azt jelenti, hogy a minták 30 másodpercenként vannak lekért adatok. Ahogy azonban az alábbiakban már említettük, késés van a minta gyűjtésének és a képlet számára elérhetővé válik között. Ezért előfordulhat, hogy egy adott időszakra vonatkozóan nem minden minta érhető el kiértékelhető egy képlettel.<ul><li>`doubleVec GetSample(double count)`: Megadja a legutóbb gyűjtött mintákból lekért minták számát. `GetSample(1)` A az utolsó elérhető mintát adja vissza. Az olyan metrikák esetében azonban, mint a , nem használható, mert nem lehet tudni, hogy mikor lett `$CPUPercent` `GetSample(1)` összegyűjtve a minta.  Lehet közelmúltbeli, vagy rendszer-problémák miatt sokkal régebbi is. Ilyen esetekben jobb az alább látható időintervallumot használni.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: Megadja a mintaadatok gyűjtésének időkeretét. Azt is megszabadítja, hogy hány minta legyen elérhető a kért időkeretben. A például 20 mintát ad vissza, ha az elmúlt 10 perc összes mintája jelen `$CPUPercent.GetSample(TimeInterval_Minute * 10)` van az `CPUPercent` előzményekben. Ha az előzmények utolsó perce nem állna rendelkezésre, csak 18 mintát adna vissza. Ebben az esetben a hiba azért lenne sikertelen, mert a mintáknak csak `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` 90%-a érhető el, de `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` sikeres lenne.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: Meghatározza az adatgyűjtés időkeretét, kezdési és záró időpontokkal együtt. Ahogy korábban említettük, késés van a minta összegyűjtése és a képlet számára elérhetővé válnak között. A metódus használata esetén vegye figyelembe ezt a `GetSample` késést. Lásd `GetSamplePercent` alább. |
| GetSamplePeriod() |Az előzményadatkészletben vett minták időszakát adja vissza. |
| Count() |A metrikák előzményeiben található minták teljes számát adja vissza. |
| HistoryBeginTime() |A metrika legrégebbi elérhető adatmintája időbélyegét adja vissza. |
| GetSamplePercent() |Az adott időintervallumban elérhető minták százalékos arányát adja vissza. Például: `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. Mivel a metódus sikertelen, ha a visszaadott minták százalékos aránya kisebb a megadottnál, a metódussal először `GetSample` `samplePercent` `GetSamplePercent` ellenőrizheti. Ezután végrehajthat egy alternatív műveletet, ha nem áll rendelkezésre elegendő minta az automatikus skálázás értékelésének leállítása nélkül. |

### <a name="samples"></a>Példák

A Batch szolgáltatás rendszeres időközönként mintákat vesz a tevékenységekről és az erőforrás-metrikákról, és elérhetővé teszi őket az automatikus skálázási képletek számára. Ezeket a mintákat a Batch szolgáltatás 30 másodpercenként rögzíti. Általában azonban késés van a minták rögzítése és az automatikus méretezési képletek számára való elérhetővé (és olvashatóvé tehető) között. Emellett előfordulhat, hogy a minták nem lesznek rögzítve egy adott időtartamra olyan tényezők miatt, mint például a hálózattal vagy más infrastruktúrával kapcsolatos problémák.

### <a name="sample-percentage"></a>Minta százalékos aránya

Ha a metódust vagy a metódust a rendszer meghívja, a percent (százalék) a Batch szolgáltatás által rögzített minták teljes lehetséges számának és az automatikus skálázási képlet számára elérhető minták számának összehasonlítását `samplePercent` `GetSample()` `GetSamplePercent()` jelenti. 

Példaként nézzünk meg egy 10 perces időkorrekta. Mivel a rendszer 30 másodpercenként rögzíti a mintákat a 10 perces időtartamon belül, a Batch által rögzített minták maximális száma 20 minta (percenként 2). A jelentéskészítési mechanizmus eredendő késése és az Azure-beli egyéb problémák miatt azonban csak 15 minta érhető el az automatikus skálázási képlet számára az olvasáshoz. Így például a 10 perces időszakban a rögzített minták teljes számának csak 75%-a lesz elérhető a képlet számára.

### <a name="getsample-and-sample-ranges"></a>GetSample() és mintatartományok

Az automatikus méretezési képletek csomópontok hozzáadásával vagy eltávolításával növekednek és csökkennek a készletek. Mivel a csomópontok költséggel jár, győződjön meg arról, hogy a képletek megfelelő adatokon alapuló intelligens elemzési módszert használnak. Javasoljuk, hogy a képletekben trend típusú elemzést használjon. Ez a típus számos összegyűjtött minta alapján növekszik és csökken a készletek méretével.

A használatával adja `GetSample(interval look-back start, interval look-back end)` vissza a minták vektorát:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Ha a Batch kiértékeli a fenti sort, egy mintatartományt ad vissza értékvektorként. Például:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Miután begyűjtötte a minták vektorát, olyan függvényeket használhat, mint a , a és a az, amelyek értelmes értékeket származtatnak az `min()` `max()` összegyűjtött `avg()` tartományból.

A nagyobb biztonság érdekében kényszerítheti a képletek kiértékelése sikertelen lesz, ha egy adott időszakban a minta egy bizonyos százalékának kisebb értéke érhető el. Ha egy képlet kiértékelését a sikertelenre kényszeríti, a Batch arra utasítja a Batchet, hogy szünnítsa meg a képlet további kiértékelését, ha a minták megadott százaléka nem érhető el. Ebben az esetben a készlet mérete nem változik. A sikeres értékeléshez szükséges minták százalékos arányának megadásához adja meg azt a harmadik `GetSample()` paramétereként. Itt a minták 75%-ának követelménye van megadva:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Mivel a minta rendelkezésre állása késhet, mindig olyan időtartományt adjon meg, amely egy percnél régebbi, visszatekebbi kezdési idővel. A minták propagálása a rendszeren belül körülbelül egy percet vesz igénybe, ezért előfordulhat, hogy a tartományban található `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` minták nem lesznek elérhetők. A százalékos paraméterével is kényszeríthet egy adott százalékos `GetSample()` mintakövetelményt.

> [!IMPORTANT]
> Határozottan javasoljuk, hogy ne támaszkodjon kizárólag az **automatikus `GetSample(1)` skálázási képletekben.** Ennek az az oka, hogy lényegében a következőt mondja a Batch szolgáltatásnak: "Adja meg az utolsó mintát, függetlenül attól, hogy milyen régen `GetSample(1)` lekérte." Mivel ez csak egy minta, és lehet egy régebbi minta is, nem biztos, hogy a legutóbbi feladat vagy erőforrás állapotának nagyobb képét képviseli. Ha használja a függvényt, győződjön meg arról, hogy az egy nagyobb utasítás része, és nem az egyetlen adatpont, amelyre a képlet `GetSample(1)` támaszkodik.

## <a name="write-an-autoscale-formula"></a>Automatikus méretezési képlet írása

Automatikus méretezési képletet úgy építhet ki, hogy utasításokat alakít ki, amelyek a fenti összetevőket használják, majd ezeket az utasításokat egy teljes képletben kombinálja. Ebben a szakaszban egy automatikus skálázási képletet hozunk létre, amely valós méretezési döntéseket hajthat végre, és módosításokat végezhet.

Először határozzuk meg az új automatikus skálázási képlet követelményeit. A képletnek a következőnek kell lennie:

- Növelje a készletben található dedikált számítási csomópontok célszámát, ha a processzorhasználat magas.
- Csökkentse a készletben található dedikált számítási csomópontok célszámát, ha a processzorhasználat alacsony.
- Mindig korlátozza a dedikált csomópontok maximális számát 400-ra.
- A csomópontok számának csökkentésekor ne távolítsa el a tevékenységeket futtató csomópontokat; Ha szükséges, a csomópontok eltávolítása előtt várja meg, amíg a tevékenységek le nem fejeződnek.

A képlet első utasítása növeli a csomópontok számát a magas processzorhasználat során. Meghatározunk egy utasítást, amely feltölt egy felhasználó által megadott változót ( ) egy olyan értékkel, amely a dedikált csomópontok aktuális célszámának 110 százaléka, de csak akkor, ha az elmúlt 10 percben a minimális átlagos `$totalDedicatedNodes` PROCESSZORhasználat 70% felett volt. Ellenkező esetben a dedikált csomópontok aktuális számának értékét használja.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Az alacsony PROCESSZORhasználat alatti dedikált csomópontok számának csökkentéséhez a képlet következő utasítása ugyanezt a változót a dedikált csomópontok aktuális célszámának 90 százalékára állítja be, ha az elmúlt 60 percben az átlagos `$totalDedicatedNodes` processzorhasználat 20% alatt volt. Ellenkező esetben a jelenlegi értékét `$totalDedicatedNodes` használja, amely a fenti utasításban ki van töltve.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Most a dedikált számítási csomópontok célszámát legfeljebb 400-ra korlátozjuk.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Végül gondoskodunk arról, hogy a csomópontok ne törlődnek, amíg a tevékenységek be nem fejeződnek.

```
$NodeDeallocationOption = taskcompletion;
```

A teljes képlet a következő:

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
> Ha úgy dönt, megjegyzéseket és sortöréseket is tartalmazhat a képletsringekben. Azt is vegye figyelembe, hogy a hiányzó pontosvesszők kiértékelési hibákat eredményezhetnek.

## <a name="automatic-scaling-interval"></a>Automatikus skálázás időköze

Alapértelmezés szerint a Batch szolgáltatás 15 percenként módosítja a készlet méretét az automatikus skálázási képletnek megfelelően. Ez az időköz az alábbi készlettulajdonságok használatával konfigurálható:

- [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Batch .NET)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

A minimális időköz öt perc, a maximális pedig 168 óra. Ha ezen a tartományon kívüli időköz van megadva, a Batch szolgáltatás Hibás kérés (400) hibát ad vissza.

> [!NOTE]
> Az automatikus skálázásnak jelenleg nem célja, hogy egy percnél rövidebb idő alatt reagáljon a változásokra, hanem inkább a készlet méretének fokozatos módosítására szolgál a számítási feladatok futtatása során.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Automatikus skálázásra képes készlet létrehozása Batch SDK-okkal

A készlet automatikus skálázása a [Batch](batch-apis-tools.md#azure-accounts-for-batch-development)bármelyikÉNEK, a [Batch REST API](/rest/api/batchservice/) [Batch PowerShell-parancsmagok](batch-powershell-cmdlets-get-started.md)és a Batch parancssori felületének használatával [konfigurálható.](batch-cli-get-started.md) Ebben a szakaszban a .NET-re és a Pythonra is talál példákat.

### <a name="net"></a>.NET

Ha olyan készletet kell létrehoznia, amelynél engedélyezve van az automatikus skálázás a .NET-en, kövesse az alábbi lépéseket:

1. Hozza létre a készletet a [BatchClient.PoolOperations.CreatePool segítségével.](/dotnet/api/microsoft.azure.batch.pooloperations.createpool)
1. Állítsa a [CloudPool.AutoScaleEnabled tulajdonságot a következőre:](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) `true` .
1. Állítsa be [a CloudPool.AutoScaleFormula tulajdonságot](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) az automatikus méretezési képlettel.
1. (Nem kötelező) Állítsa be [a CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) tulajdonságot (az alapértelmezett érték 15 perc).
1. Véglegesítés a készletben a [CloudPool.Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) vagy [a CommitAsync használatával.](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync)

Az alábbi példa egy automatikus skálázást engedélyező készletet hoz létre a .NET-en. A készlet automatikus skálázási képlete a dedikált csomópontok célszámát hétfőnként 5-re, a hét minden második napján pedig 1-re állítja. Az [automatikus skálázás időköze](#automatic-scaling-interval) 30 perc. Ebben és a cikkben a többi C#-kódrészlet a BatchClient osztály megfelelően inicializált `myBatchClient` [példánya.](/dotnet/api/microsoft.azure.batch.batchclient)

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    VirtualMachineConfiguration: new VirtualMachineConfiguration(
                        imageReference: new ImageReference(
                                            publisher: "MicrosoftWindowsServer",
                                            offer: "WindowsServer",
                                            sku: "2019-datacenter-core",
                                            version: "latest"),
                        nodeAgentSkuId: "batch.node.windows amd64");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Automatikus skálázást engedélyező készlet létrehozásakor ne adja meg a _targetDedicatedNodes_ paramétert vagy a _targetLowPriorityNodes_ paramétert a **CreatePool hívására.** Ehelyett adja meg az **AutoScaleEnabled és** **az AutoScaleFormula tulajdonságokat** a készleten. Ezen tulajdonságok értékei határozzák meg az egyes csomópontok célszámát.
>
> Ha manuálisan szeretné átméretezni az automatikus skálázást engedélyező készletet (például [a BatchClient.PoolOperations.ResizePoolAsync](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)használatával), először le kell tiltania az automatikus skálázást a készleten, majd át kell méreteznie azt.

### <a name="python"></a>Python

Automatikus skálázásra képes készlet létrehozása a Python SDK-val:

1. Hozzon létre egy készletet, és adja meg annak konfigurációját.
1. Adja hozzá a készletet a szolgáltatásoldali ügyfélhez.
1. Engedélyezze az automatikus méretezést a készleten egy Ön által írt képlettel.

Az alábbi példa ezeket a lépéseket mutatja be.

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
> További példák a Python SDK használatára: [Batch Python Gyorsindítási adattár](https://github.com/Azure-Samples/batch-python-quickstart) a GitHubon.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Automatikus skálázás engedélyezése meglévő készleten

Minden Batch SDK lehetőséget biztosít az automatikus skálázás engedélyezésére. Például:

- [BatchClient.PoolOperations.EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (Batch .NET)
- [Készlet automatikus skálázásának](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) engedélyezése (REST API)

Ha egy meglévő készleten engedélyezi az automatikus skálázást, tartsa szem előtt a következőt:

- Ha az automatikus skálázás jelenleg le van tiltva a készleten, a kérelem kiállításakor érvényes automatikus skálázási képletet kell megadnia. Megadhatja az automatikus skálázás időközét is. Ha nem ad meg időközt, a rendszer az alapértelmezett 15 perces értéket használja.
- Ha az automatikus skálázás jelenleg engedélyezve van a készleten, megadhat egy új képletet, egy új időközt vagy mindkettőt. Ezen tulajdonságok közül legalább egyet meg kell adnia.
  - Ha új automatikus skálázási időközt ad meg, a meglévő ütemezés leáll, és új ütemezés lesz elindítva. Az új ütemezés kezdési ideje az az időpont, amikor az automatikus skálázás engedélyezésére vonatkozó kérés ki lett bocsátva.
  - Ha kihagyja az automatikus skálázási képletet vagy időközt, a Batch szolgáltatás továbbra is ennek a beállításnak az aktuális értékét fogja használni.

> [!NOTE]
> Ha értékeket adott meg a **CreatePool** metódus *targetDedicatedNodes* vagy *targetLowPriorityNodes* paraméteréhez, amikor a készletet a .NET-ben hozta létre, vagy a más nyelven összehasonlítható paramétereket, akkor ezeket az értékeket a rendszer figyelmen kívül hagyja az automatikus skálázási képlet kiértékelésekor.

Ez a C#-példa a [Batch .NET-kódtárat](/dotnet/api/microsoft.azure.batch) használja az automatikus skálázás meglévő készleten való engedélyezéséhez.

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Automatikus méretezési képlet frissítése

Egy meglévő automatikus skálázást engedélyező készlet képletének frissítéséhez hívja meg a műveletet az automatikus skálázás új képlettel való engedélyezéséhez. Ha például az automatikus skálázás már engedélyezve van a következő .NET-kód végrehajtásakor, az automatikus skálázási képlete a tartalmára `myexistingpool` lesz `myNewFormula` lecserélve.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Az automatikus skálázási időköz frissítése

Egy meglévő automatikus skálázást engedélyező készlet automatikus skálázási kiértékelési időközének frissítéséhez hívja meg a műveletet az automatikus skálázás új időközzel való újra engedélyezéséhez. Ha például az automatikus skálázás kiértékelési időközét 60 percre kell állítani egy olyan készlethez, amely már engedélyezve van az automatikus skálázáshoz a .NET-ben:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Automatikus méretezési képlet kiértékelése

A képletet kiértékelheti a készletre való alkalmazás előtt. Ez lehetővé teszi a képlet eredményeinek tesztelését, mielőtt éles környezetbe kerül.

Az automatikus skálázási képlet kiértékeléséhez először engedélyeznie kell az automatikus skálázást a készleten egy érvényes képlettel, például a egysoros `$TargetDedicatedNodes = 0` képlettel. Ezután használja a következők egyikét a tesztelni kívánt képlet kiértékeléséhez:

- [BatchClient.PoolOperations.EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) vagy [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Ezekhez a Batch .NET-metódusokhoz szükség van egy meglévő készlet azonosítójára és egy sztringre, amely az automatikus skálázási képletet tartalmazza az értékeléshez.

- [Automatikus méretezési képlet kiértékelése](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Ebben a REST API adja meg a készlet azonosítóját az URI-ban, valamint az automatikus skálázási képletet a kérelem törzsének *autoScaleFormula* elemében. A művelet válasza tartalmazza a képlettel kapcsolatos esetleges hibainformációt.

Ez a [Batch .NET-példa](/dotnet/api/microsoft.azure.batch) egy automatikus skálázási képletet értékel ki. Ha a készlet még nem használ automatikus skálázást, először engedélyezni kell.

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

Az ebben a kódrészletben látható képlet sikeres kiértékelése az alábbihoz hasonló eredményeket ad vissza:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Az automatikus méretezési futtatásokkal kapcsolatos információk lekérte

Annak érdekében, hogy a képlet a várt módon teljesítsen, javasoljuk, hogy rendszeresen ellenőrizze a Batch által a készleten futtatott automatikus méretezési futtatás eredményeit. A művelethez szerezze be (vagy frissítse) a készletre való hivatkozást, majd vizsgálja meg az utolsó automatikus méretezési futtatás tulajdonságait.

A Batch .NET-en a [CloudPool.AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) tulajdonság számos olyan tulajdonsággal rendelkezik, amelyek információt nyújtanak a készleten végrehajtott legújabb automatikus skálázási futtatásról:

- [AutoScaleRun.Timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun.Results](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun.Error](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

A REST API get information [about a pool](/rest/api/batchservice/get-information-about-a-pool) request (Információk lekérése egy készletre vonatkozó kérelemről) beállítás a készletre vonatkozó információkat ad vissza, amely tartalmazza a legújabb automatikus skálázási futtatás információit az [autoScaleRun tulajdonságban.](/rest/api/batchservice/get-information-about-a-pool)

Az alábbi C#-példa a Batch .NET-kódtárat használja a myPool készleten legutóbb futtatott automatikus skálázás információinak _kinyomtatása érdekében._

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Példa az előző példában látható kimenetre:

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

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>Automatikus méretezési futtatás előzményeinek lekérte a készlet automatikus méretezési eseményeinek használatával
Az automatikus skálázási előzményeket a [PoolAutoScaleEvent](batch-pool-autoscale-event.md)lekérdezésével is ellenőrizheti. Ezt az eseményt a Batch szolgáltatás bocsátja ki az automatikus skálázási képletek értékelésének és végrehajtásának minden előfordulásának rögzítéséhez, ami hasznos lehet a lehetséges problémák elhárításában.

Mintaesemény a PoolAutoScaleEvent eseményhez:
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

## <a name="example-autoscale-formulas"></a>Példa automatikus méretezési képletre

Nézzünk meg néhány képletet, amelyek a készletben található számítási erőforrások mennyiségének különböző beállítási módjait mutatják be.

### <a name="example-1-time-based-adjustment"></a>1. példa: Időalapú módosítás

Tegyük fel, hogy módosítani szeretné a készlet méretét a hét napja és a nap ideje alapján. Ez a példa bemutatja, hogyan lehet ennek megfelelően növelni vagy csökkenteni a készletben lévő csomópontok számát.

A képlet először az aktuális időt nyeri ki. Ha hétköznap van (1-5) és munkaidőn belül (08:00 és 18:00 között), a célkészlet mérete 20 csomópontra van beállítva. Ellenkező esetben 10 csomópontra van beállítva.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

`$curTime` A úgy állítható be, hogy a helyi időzónát tükrözze a termék és az UTC-eltolás `time()` `TimeZoneInterval_Hour` hozzáadásával. Használja például a `$curTime = time() + (-6 * TimeInterval_Hour);` nyári nyári csúcsidőt (MDT). Ne feledje, hogy az eltolást a nyári időszámítás elején és végén kell módosítani (ha van).

### <a name="example-2-task-based-adjustment"></a>2. példa: Feladatalapú módosítás

Ebben a C#-példában a készlet mérete az üzenetsorban lévő feladatok száma alapján lesz módosítva. A képletsringek megjegyzéseket és sortöréseket is tartalmaznak.

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

### <a name="example-3-accounting-for-parallel-tasks"></a>3. példa: Párhuzamos tevékenységek nyilvántartása

Ez a C#-példa a tevékenységek száma alapján módosítja a készlet méretét. Ez a képlet a készlethez beállított [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) értéket is figyelembe veszi. Ez a megközelítés olyan helyzetekben [hasznos,](batch-parallel-node-tasks.md) amikor a készleten engedélyezve van a párhuzamos feladatvégrehajtás.

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

### <a name="example-4-setting-an-initial-pool-size"></a>4. példa: Kezdeti készletméret beállítása

Ez a példa egy olyan C#-példát mutat be egy automatikus skálázási képlettel, amely a készlet méretét a kezdeti időszakban megadott számú csomópontra állítja be. Ezután a futó és aktív feladatok száma alapján módosítja a készlet méretét.

Ez a képlet a következőket teszi:

- A kezdeti készletméretet négy csomópontra állítja be.
- Nem módosítja a készlet méretét a készlet életciklusának első 10 percében.
- 10 perc után a le tudja szerezni a futó és aktív tevékenységek maximális számát az elmúlt 60 percben.
  - Ha mindkét érték 0 (ami azt jelzi, hogy az elmúlt 60 percben egyik tevékenység sem futott vagy nem volt aktív), a készlet mérete 0-ra van állítva.
  - Ha bármelyik érték nagyobb nullánál, nem történik módosítás.

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

- Ismerje meg, hogyan hajthat végre egyszerre több feladatot a készlet [számítási csomópontjain.](batch-parallel-node-tasks.md) Az automatikus skálázás mellett ez egyes számítási feladatok időtartamának csökkentésében is segíthet, így pénzt takaríthat meg.
- Ismerje meg, hogyan [lehet hatékonyan lekérdezni Azure Batch szolgáltatást a](batch-efficient-list-queries.md) további hatékonyság érdekében.
