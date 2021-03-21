---
title: Azure HDInsight-fürtök automatikus skálázása
description: Az automatikus méretezés funkció használatával automatikusan méretezheti az Azure HDInsight-fürtöket ütemterv vagy teljesítménybeli mérőszámok alapján.
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.date: 12/14/2020
ms.openlocfilehash: 130a5a58fc7dab6f94c011cf9764743f9114e48a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942634"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Azure HDInsight-fürtök automatikus skálázása

Az Azure HDInsight ingyenes automatikus méretezési funkciója a korábban beállított feltételek alapján automatikusan növelheti vagy csökkentheti a fürt munkavégző csomópontjainak számát. Az automatikus skálázási funkció úgy működik, hogy a csomópontok számát a teljesítmény mérőszámai alapján, vagy a skálázási és a horizontális Felskálázási műveletek ütemtervével méretezi.

## <a name="how-it-works"></a>Működés

Az automatikus méretezési funkció kétféle feltételt használ a skálázási események elindításához: a különböző fürt teljesítményének mérőszámai (úgynevezett *terheléses skálázás*) és az időalapú eseményindítók ( *ütemezett skálázás*) küszöbértékeit. A terhelésen alapuló skálázás megváltoztatja a fürtben lévő csomópontok számát a beállított tartományon belül, így biztosítva az optimális CPU-használatot, és csökkentheti a futtatási költségeket. Az ütemezett skálázás a fürt csomópontjainak számát a méretezési és a leskálázási műveletek ütemterve alapján módosítja.

Az alábbi videó áttekintést nyújt azokról a kihívásokról, amelyekkel az autoscale megoldásokkal és a HDInsight-vel kapcsolatos költségek szabályozásával segít.

> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>A Load-based vagy az Schedule-alapú skálázás kiválasztása

Skálázási típus kiválasztásakor vegye figyelembe a következő tényezőket:

* Betöltési eltérés: a fürt terhelése bizonyos időpontokban következetes mintát követ bizonyos napokon? Ha nem, akkor a betöltés alapú ütemezés jobb megoldás.
* SLA-követelmények: az automatikus skálázás a prediktív végrehajtás helyett reaktív. Elegendő késés lesz a terhelés növekedésének megkezdése és a fürtnek a célként megadott méretnél való megadása között? Ha szigorú SLA-követelmények vannak meghatározva, és a terhelés egy rögzített ismert minta, az "ütemezés-alapú" jobb megoldás.

### <a name="cluster-metrics"></a>Fürt metrikái

Az autoscale folyamatosan figyeli a fürtöt, és a következő metrikákat gyűjti össze:

|Metric|Leírás|
|---|---|
|Függőben lévő CPU összesen|Az összes függőben lévő tároló végrehajtásának megkezdéséhez szükséges magok teljes száma.|
|Függőben lévő memória összesen|Az összes függőben lévő tároló végrehajtásának megkezdéséhez szükséges teljes memória (MB).|
|Teljes szabad processzor|Az aktív munkavégző csomópontokon fel nem használt magok összege.|
|Szabad memória összesen|A nem használt memória (MB) összege az aktív munkavégző csomópontokon.|
|Felhasznált memória/csomópont|A munkavégző csomópont terhelése. Egy munkavégző csomópontot, amelyen 10 GB memóriát használ, nagyobb terhelésnek számít, mint egy 2 GB-nyi memóriát használó feldolgozó.|
|Alkalmazás-főkiszolgálók száma csomópont szerint|A munkavégző csomóponton futó Application Master (AM) tárolók száma. A két tárolót üzemeltető munkavégző csomópont sokkal fontosabbnak számít, mint a nulla AM tárolókat üzemeltető munkavégző csomópont.|

A fenti mérőszámok 60 másodpercenként vannak ellenőrizve. Ezen mérőszámok bármelyikével beállíthatja a fürt skálázási műveleteit.

### <a name="load-based-scale-conditions"></a>Terhelésen alapuló méretezési feltételek

Ha a következő feltételek észlelhetők, az autoskálázás egy méretezési kérelmet fog kiadni:

|Vertikális felskálázás|Vertikális leskálázás|
|---|---|
|A függőben lévő CPU összesen több mint 3 percnél nagyobb az összes szabad PROCESSZORnál.|A függőben lévő PROCESSZORok száma több mint 10 percnél kevesebb, mint az összes szabad CPU.|
|A függőben lévő memória összesen több mint 3 percnél nagyobb a teljes szabad memóriánál.|A függőben lévő memória mennyisége kevesebb, mint 10 percnél nagyobb a teljes szabad memória.|

A vertikális felskálázáshoz az autoskálázási szolgáltatás kibővíti a szükséges számú csomópont hozzáadására irányuló kérelmet. A felskálázás azon alapul, hogy hány új feldolgozó csomópontra van szükség a jelenlegi CPU-és memória-követelmények teljesítéséhez.

A leskálázáshoz az autoskálázás bizonyos számú csomópont eltávolítására irányuló kérést bocsát ki. A skálázás a csomóponton lévő AM tárolók számától függ. És a jelenlegi CPU-és memória-követelmények. A szolgáltatás azt is észleli, hogy mely csomópontok vannak kiválasztva az eltávolításra a jelenlegi feladatok végrehajtása alapján. A leskálázási művelet először leszereli a csomópontokat, majd eltávolítja őket a fürtből.

### <a name="cluster-compatibility"></a>Fürt kompatibilitása

> [!Important]
> Az Azure HDInsight automatikus skálázási funkciója 2019. november 7. óta általánosan elérhető a Spark- és Hadoop-fürtökhöz, és olyan fejlesztéseket tartalmaz, amelyek a funkció előzetes verziójában nem voltak elérhetők. Ha a 2019. november 7. előtt létrehozott egy Spark-fürtöt, és használni szeretné rajta az automatikus skálázási funkciót, akkor azt az eljárást javasoljuk, hogy hozzon létre egy új fürtöt, és azon engedélyezze az automatikus skálázást.
>
> Az interaktív lekérdezés (LLAP) autoskálázása a HDI 4,0-es verziójának általánosan elérhetővé vált a 2020. augusztus 27-én. A HBase-fürtök még előzetes verzióban érhetők el. Az automatikus skálázás kizárólag a Spark-, Hadoop-, Interactive Query és HBase-fürtökhöz érhető el.

Az alábbi táblázat az autoscale szolgáltatással kompatibilis fürtök típusát és verzióját ismerteti.

| Verzió | Spark | Hive | Interaktív lekérdezés | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 ESP nélkül | Igen | Igen | Yes | Igen* | Nem | Nem | Nem |
| HDInsight 4,0 ESP nélkül | Igen | Igen | Yes | Igen* | Nem | Nem | Nem |
| HDInsight 3,6, ESP-vel | Igen | Igen | Yes | Igen* | Nem | Nem | Nem |
| HDInsight 4,0, ESP-vel | Igen | Igen | Yes | Igen* | Nem | Nem | Nem |

\* A HBase-fürtök csak az ütemezett skálázáshoz konfigurálhatók, nem pedig a terhelés alapján.

## <a name="get-started"></a>Bevezetés

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Fürt létrehozása terheléselosztási alapú automatikus skálázással

Ha az automatikus skálázási funkciót terheléselosztásos skálázással szeretné engedélyezni, hajtsa végre a következő lépéseket a fürt normál létrehozási folyamatának részeként:

1. A **konfiguráció + díjszabás** lapon jelölje be az automatikus **skálázás engedélyezése** jelölőnégyzetet.
1. Válassza az automatikus **skálázás típusa** alatt a **Betöltés** lehetőséget.
1. Adja meg a kívánt értékeket a következő tulajdonságokhoz:  

    * A **munkavégző csomóponthoz** tartozó **csomópontok kezdeti száma** .
    * A munkavégző csomópontok **minimális** száma.
    * A munkavégző csomópontok **maximális** száma.

    ![Munkavégző csomópont terheléses automatikus méretezésének engedélyezése](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

A munkavégző csomópontok kezdeti számának a minimális és a maximális érték közé kell esnie. Ez az érték határozza meg a fürt kezdeti méretét a létrehozáskor. A munkavégző csomópontok minimális számát három vagy több értékre kell beállítani. Ha a fürtöt kevesebb mint három csomópontra szeretné méretezni, azt eredményezheti, hogy a fájlreplikációs szolgáltatás nem elegendő a biztonságos módban.  További információ: [beragadás csökkentett módban](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Fürt létrehozása Schedule-alapú automatikus skálázással

Ha az automatikus skálázási funkciót ütemezett méretezéssel szeretné engedélyezni, hajtsa végre a következő lépéseket a fürt normál létrehozási folyamatának részeként:

1. A **konfiguráció és díjszabás** lapon jelölje be az automatikus **skálázás engedélyezése** jelölőnégyzetet.
1. Adja meg a **munkavégző csomóponthoz** tartozó **csomópontok számát** , amely a fürt skálázásának korlátját vezérli.
1. Válassza az **ütemezett** beállítás lehetőséget az **autoskálázás típusa** területen.
1. Válassza a **Konfigurálás** lehetőséget az **automatikus skálázási konfiguráció** ablak megnyitásához.
1. Válassza ki az időzónát, majd kattintson a **+ feltétel hozzáadása** elemre.
1. Válassza ki a hét azon napjait, amelyeket az új feltételnek alkalmaznia kell.
1. Szerkessze a feltétel érvénybe léptetésének időpontját, valamint azt, hogy a fürt hány csomópontra legyen méretezhető.
1. Szükség esetén további feltételek is felvehetők.

    ![Munkavégző csomópont ütemezett létrehozásának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

A csomópontok számának 3 és a feltételek hozzáadása előtt a munkavégző csomópontok maximális száma között kell lennie.

### <a name="final-creation-steps"></a>Végső létrehozás lépései

Válassza ki a virtuális gép típusát a munkavégző csomópontok számára egy virtuális gép kiválasztásával a legördülő listából a **csomópont mérete** alatt. Miután kiválasztotta a virtuálisgép-típust az egyes csomópont-típusokhoz, a teljes fürt becsült hatókörét láthatja. Állítsa be úgy a virtuális gépek típusait, hogy illeszkedjenek a költségkerethez.

![Munkavégző csomópont ütemezett méretezési csomópontjának engedélyezése](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

Az előfizetéshez tartozik egy kapacitási kvóta az egyes régiókban. A fő csomópontok és a maximális munkavégző csomópontok teljes száma nem haladhatja meg a kapacitási kvótát. Ez a kvóta azonban egy enyhe korlát; bármikor létrehozhat egy támogatási jegyet, hogy könnyebben megnövelhető legyen.

> [!Note]  
> Ha túllépi a teljes felső korlátot, a következő hibaüzenet jelenik meg: "a maximális csomópont túllépte az elérhető magok számát ebben a régióban, válasszon egy másik régiót, vagy forduljon a támogatási szolgálathoz a kvóta növeléséhez."

További információ a HDInsight-fürtök létrehozásáról a Azure Portal használatával: [Linux-alapú fürtök létrehozása a HDInsight-ben a Azure Portal használatával](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Fürt létrehozása Resource Manager-sablonnal

#### <a name="load-based-autoscaling"></a>Load-alapú automatikus skálázás

Létrehozhat egy HDInsight-fürtöt egy Azure Resource Manager-sablon terheléses automatikus skálázásával, ha hozzáad egy `autoscale` csomópontot a `computeProfile`  >  `workernode` szakaszhoz a tulajdonságok segítségével, `minInstanceCount` és az `maxInstanceCount` alábbi JSON-kódrészletben látható. Teljes Resource Manager-sablon: gyors útmutató [sablon: a Spark-fürt üzembe helyezése az automatikus méretezés engedélyezve van](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-loadbased).

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

#### <a name="schedule-based-autoscaling"></a>Ütemterv-alapú automatikus skálázás

HDInsight-fürtöt úgy hozhat létre, `autoscale` hogy a `computeProfile`  >  szakaszhoz hozzáad egy csomópontot a Azure Resource Manager-sablonhoz `workernode` . A `autoscale` csomópont tartalmaz egy `recurrence` -t, `timezone` `schedule` amely leírja, hogy mikor kerül sor a módosításra. A teljes Resource Manager-sablonokkal kapcsolatban lásd: [Spark-fürt üzembe helyezése az ütemterven alapuló autoscale engedélyezve](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-schedulebased).

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        }
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Az autoskálázás engedélyezése és letiltása egy futó fürtön

#### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Ha egy futó fürtön engedélyezni szeretné az autoskálázást, a **Beállítások** területen válassza a **fürt méretét** . Ezután válassza az **autoskálázás engedélyezése** lehetőséget. Válassza ki a kívánt automatikus méretezési típust, és adja meg a terhelés vagy az ütemterv szerinti skálázás beállításait. Végül válassza a **Mentés** lehetőséget.

![Munkavégző csomópont ütemezett méretezésének engedélyezése](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>A REST API használata

Egy futó fürtön a REST API használatával engedélyezheti vagy letilthatja az autoskálázást, ha POST-kérést küld az autoscale végpontnak:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Használja a kérelem hasznos adatainak megfelelő paramétereket. Az alábbi JSON-adattartalom használatával engedélyezhető az autoskálázás. Az `{autoscale: null}` autoscale letiltásához használja a hasznos adatokat.

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

Tekintse meg az előző szakaszt a [betöltési alapú automatikus skálázás engedélyezéséhez](#load-based-autoscaling) az összes hasznos adat paraméterének teljes leírásához.

## <a name="monitoring-autoscale-activities"></a>Az autoskálázási tevékenységek figyelése

### <a name="cluster-status"></a>Fürt állapota

A Azure Portalban felsorolt fürt állapota segíthet az autoskálázási tevékenységek figyelésében.

![Munkavégző csomópont terheléses automatikus méretezési fürt állapotának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Az alábbi listában az összes olyan fürt állapotüzenetek látható, amelyet látni fog.

| Fürt állapota | Description |
|---|---|
| Futó | A fürt rendesen működik. Az összes korábbi autoskálázási tevékenység sikeresen befejeződött. |
| Frissítés  | A fürt automatikus skálázási konfigurációjának frissítése folyamatban van.  |
| HDInsight-konfiguráció  | Egy fürt vertikális fel-vagy leskálázási művelete folyamatban van.  |
| Frissítési hiba  | A HDInsight az automatikus skálázási konfiguráció frissítése során teljesülnek. Az ügyfelek dönthetnek úgy, hogy megpróbálják megismételni a frissítést vagy letiltani az autoskálázást.  |
| Hiba  | Probléma van a fürttel, és nem használható. Törölje a fürtöt, és hozzon létre egy újat.  |

A fürtben lévő csomópontok aktuális számának megtekintéséhez nyissa meg a fürt **áttekintő** lapjának **fürt mérete** diagramját. Vagy válassza ki a **fürt méretét** a **Beállítások** területen.

### <a name="operation-history"></a>Műveleti előzmények

A fürt metrikáinak részeként megtekintheti a fürt vertikális felskálázásának és leskálázásának előzményeit is. Az összes skálázási műveletet az elmúlt nap, a hét vagy más idő alatt is listázhatja.

A **figyelés** területen válassza a **metrikák** lehetőséget. Ezután válassza a metrika **hozzáadása** és az **aktív feldolgozók száma** elemet a **metrika** legördülő listából. Kattintson a jobb felső sarokban lévő gombra az időtartomány módosításához.

![A feldolgozói csomópont Schedule-alapú autoskálázási metrikájának engedélyezése](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="consider-the-latency-of-scale-up-and-scale-down-operations"></a>Vegye figyelembe a vertikális Felskálázási és leskálázási műveletek késését

A skálázási művelet befejezéséhez 10 – 20 percet is igénybe vehet. Testreszabott ütemterv beállításakor tervezze meg ezt a késleltetést. Ha például a fürt méretének 20-at kell megadnia a 9:00-es verziónál, állítsa az ütemezett triggert egy korábbi időpontra, például a 8:30-re, hogy a skálázási művelet a 9:00-as értékkel fejeződött be.

### <a name="prepare-for-scaling-down"></a>Felkészülés a méretezésre

A fürt skálázási folyamata során az automatikus skálázás leszereli a csomópontokat a célként megadott méret kielégítése érdekében. Ha a feladatok futnak ezeken a csomópontokon, az autoscale megvárja, amíg a feladatok befejeződtek a Spark és a Hadoop-fürtök esetében. Mivel az egyes munkavégző csomópontok is a HDFS szerepkört is kiszolgálják, az ideiglenes adat a többi csomópontra tolódik. Győződjön meg arról, hogy a többi csomóponton elegendő lemezterület áll rendelkezésre az összes ideiglenes adatbázis üzemeltetéséhez.

A futó feladatok továbbra is folytatódnak. A függőben lévő feladatok a kevesebb rendelkezésre álló munkavégző csomóponttal való ütemezésre várnak.

### <a name="be-aware-of-the-minimum-cluster-size"></a>Ügyeljen a fürt minimális méretére

Ne méretezze a fürtöt kevesebb, mint három csomópontra. Ha a fürtöt kevesebb mint három csomópontra szeretné méretezni, azt eredményezheti, hogy a fájlreplikációs szolgáltatás nem elegendő a biztonságos módban. További információ: [beragadás csökkentett módban](hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="increase-the-number-of-mappers-and-reducers"></a>A leképezések és a szűkítők számának növelésével

A Hadoop-fürtökhöz tartozó autoskálázás a HDFS használatát is figyeli. Ha a HDFS foglalt, feltételezi, hogy a fürt továbbra is szüksége van az aktuális erőforrásokra. Ha a lekérdezésben nagy mennyiségű adatok szerepelnek, növelheti a leképezések és a szűkítők számát, hogy növelje a párhuzamosságot, és gyorsítsa fel a HDFS műveleteket. Ily módon a megfelelő skálázást a rendszer akkor aktiválja, ha további erőforrások vannak. 

### <a name="set-the-hive-configuration-maximum-total-concurrent-queries-for-the-peak-usage-scenario"></a>A struktúra konfigurációjának maximális száma egyidejű lekérdezések használata a csúcsérték-használati forgatókönyvhöz

Az automatikus skálázási események nem változtatják meg az *egyidejű lekérdezések maximális számát* a Ambari. Ez azt jelenti, hogy a kaptár-kiszolgáló 2 interaktív szolgáltatása csak a megadott számú egyidejű lekérdezést képes kezelni, még akkor is, ha az interaktív lekérdezési démonok száma a terhelés és az ütemterv alapján fel van skálázásra. Az általános javaslat az, hogy ezt a konfigurációt a maximális használati forgatókönyvhöz állítsa be a manuális beavatkozás elkerülése érdekében.

Előfordulhat azonban, hogy a kaptár-kiszolgáló 2 újraindítási hibát tapasztal, ha csak kis számú feldolgozó csomópont van, és az egyidejű lekérdezések maximális száma beállítás értéke túl magas. Legalább annyi munkavégző csomópontra van szükség, amely képes a megadott számú TEZ AMS-hez alkalmazkodni (egyenlő az egyidejű lekérdezések maximális konfigurációjának összegével). 

## <a name="limitations"></a>Korlátozások

### <a name="node-label-file-missing"></a>Hiányzik a csomópont címkéjének fájlja

Az HDInsight autoscale egy csomópont-címkefájl használatával határozza meg, hogy a csomópont készen áll-e a feladatok végrehajtására. A csomópont feliratának fájlját a HDFS tárolja három replikával. Ha a fürt mérete jelentősen csökken, és nagy mennyiségű ideiglenes adat van, akkor a három replika eldobása kisebb eséllyel történik. Ha ez történik, a fürt hibás állapotba kerül.

### <a name="interactive-query-daemons-count"></a>Interaktív lekérdezési démonok száma

Az interaktív lekérdezési fürtök számára az autoskálázást támogató interaktív lekérdezések esetében az interaktív lekérdezési démonok száma az aktív munkavégző csomópontok számára is méretezhető. A démonok számának változása nem marad meg a `num_llap_nodes` Ambari konfigurációjában. Ha a kaptár-szolgáltatások újraindítása manuálisan történik, az interaktív lekérdezési démonok száma a Ambari konfigurációjának megfelelően alaphelyzetbe áll.

Ha az interaktív lekérdezési szolgáltatást manuálisan indítja újra, manuálisan kell módosítania a `num_llap_node` konfigurációt (a struktúra interaktív lekérdezési démon futtatásához szükséges csomópontok száma) a *speciális struktúra – interaktív env* elemnél, hogy megfeleljen az aktuális aktív munkavégző csomópontok számának.

## <a name="next-steps"></a>Következő lépések

További tudnivalók a fürtök méretezési [útmutatóinak](hdinsight-scaling-best-practices.md) manuális skálázásához
