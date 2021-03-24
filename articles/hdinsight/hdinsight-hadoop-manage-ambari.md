---
title: Azure-HDInsight figyelése és kezelése a Ambari webes felhasználói felületén
description: Ismerje meg, hogyan használható az Apache Ambari felhasználói felülete a HDInsight-fürtök figyelésére és felügyeletére.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 01/12/2021
ms.openlocfilehash: 1b4570b09994f292de2b175396e4b6d1882658d9
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866488"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>HDInsight-fürtök kezelése az Apache Ambari webes felületével

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Az Apache Ambari leegyszerűsíti egy Apache Hadoop-fürt felügyeletét és figyelését. Ez az egyszerűsítés úgy történik, hogy könnyen használható webes felhasználói felületet és REST API biztosít. A Ambari a HDInsight-fürtökben található, és a fürt figyelésére és a konfiguráció módosítására szolgál.

Ebből a dokumentumból megtudhatja, hogyan használhatja a Ambari webes felhasználói felületét egy HDInsight-fürttel.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>Mi az Apache Ambari?

Az [Apache Ambari](https://ambari.apache.org) egy könnyen használható webes felhasználói felületet biztosít a Hadoop-kezeléshez. A Ambari használatával felügyelheti és figyelheti a Hadoop-fürtöket. A fejlesztők a [AMBARI REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)-k használatával integrálhatja ezeket a képességeket alkalmazásaiba.

## <a name="connectivity"></a>Kapcsolatok

A Ambari webes felhasználói felülete a HDInsight-fürtön érhető el `https://CLUSTERNAME.azurehdinsight.net` , ahol a a `CLUSTERNAME` fürt neve.

> [!IMPORTANT]  
> A HDInsight Ambari-hez való csatlakozáshoz HTTPS szükséges. Ha a rendszer a hitelesítésre kéri, használja a fürt létrehozásakor megadott rendszergazdai fiók nevét és jelszavát. Ha a rendszer nem kéri a hitelesítő adatok megadását, ellenőrizze a hálózati beállításokat, és győződjön meg arról, hogy az ügyfél és az Azure HDInsight-fürtök között nincs kapcsolati probléma.

## <a name="ssh-tunnel-proxy"></a>SSH-alagút (proxy)

Habár a fürt Ambari közvetlenül az interneten keresztül érhető el, néhány hivatkozás a Ambari webes felhasználói felületéről (például a JobTracker) nem érhető el az interneten. A szolgáltatások eléréséhez létre kell hoznia egy SSH-alagutat. További információ: az [SSH-alagút használata a HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambari webes felhasználói felület

> [!WARNING]  
> A Ambari webes felhasználói felületének nem minden funkciója támogatott a HDInsight. További információkért lásd a jelen dokumentum nem [támogatott műveletek](#unsupported-operations) című szakaszát.

A Ambari webes felhasználói felületéhez való csatlakozáskor a rendszer felszólítja, hogy hitelesítse magát a lapon. A fürt létrehozásakor használt fürt rendszergazdai felhasználójának (alapértelmezett rendszergazdája) és jelszavának használata.

Amikor megnyílik az oldal, jegyezze fel a felső sávot. Ez a sáv a következő információkat és vezérlőket tartalmazza:

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png" alt-text="Apache Ambari-irányítópult – áttekintés":::

|Elem |Leírás |
|---|---|
|Ambari embléma|Megnyitja az irányítópultot, amely a fürt figyelésére használható.|
|Fürt neve # Ops|A folyamatban lévő Ambari-műveletek számát jeleníti meg. A fürt nevének kiválasztásakor vagy az **# Ops** megjeleníti a háttérben futó műveletek listáját.|
|riasztások száma|Riasztásokat vagy kritikus riasztásokat jelenít meg, ha vannak ilyenek a fürthöz.|
|Irányítópult|Megjeleníti az irányítópultot.|
|Szolgáltatások|A fürt szolgáltatásainak információi és konfigurációs beállításai.|
|Hosts|A fürt csomópontjainak információi és konfigurációs beállításai.|
|Riasztások|Információk, figyelmeztetések és kritikus riasztások naplója.|
|Rendszergazda|A fürtre, a szolgáltatásfiók-információkra és a Kerberos-biztonságra telepített szoftverek stackje vagy szolgáltatásai.|
|Rendszergazda gomb|Ambari-kezelés, felhasználói beállítások és kijelentkezés.|

## <a name="monitoring"></a>Figyelés

### <a name="alerts"></a>Riasztások

Az alábbi lista a Ambari által használt általános riasztási állapotokat tartalmazza:

* **OK**
* **Figyelmeztetés**
* **KRITIKUS**
* **ISMERETLEN**

Az **októl** eltérő riasztások miatt a **# riasztások** bejegyzés jelenik meg a lap tetején a riasztások számának megjelenítéséhez. Ennek a bejegyzésnek a kiválasztásával megjelennek a riasztások és állapotuk.

A riasztások több alapértelmezett csoportba vannak rendezve, amelyek a **riasztások** lapról jeleníthetők meg.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png" alt-text="Apache Ambari-riasztások lapja – összefoglalás":::

A csoportokat a **műveletek** menü segítségével kezelheti, és a **riasztási csoportok kezelése** lehetőségre kattintva.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png" alt-text="Apache Ambari – riasztási csoportok kezelése":::

A riasztási módszereket kezelheti, és riasztási értesítéseket hozhat létre a **műveletek** menüből az __értesítések kezelése__ lehetőség kiválasztásával. Minden aktuális értesítés megjelenik. Hozzon létre értesítéseket innen. Értesítéseket küldhet **e-mailben** vagy **SNMP** -n, ha adott riasztási/súlyossági kombinációk történnek. Küldhet például egy e-mailt, ha a **fonal alapértelmezett** csoportjában lévő bármelyik riasztás **kritikus** értékre van állítva.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png" alt-text="Apache Ambari riasztási értesítés létrehozása":::

Végül, ha a __műveletek__ menüben a __riasztási beállítások kezelése__ lehetőséget választja, megadhatja, hogy a riasztások hányszor legyenek elküldve az értesítés elküldése előtt. Ezzel a beállítással elkerülhetők az átmeneti hibák értesítései.

Az ingyenes [SendGrid-fiókkal](../sendgrid-dotnet-how-to-send-email.md)rendelkező riasztási értesítések oktatóanyagát az [Apache Ambari e-mail értesítések konfigurálása az Azure HDInsight-ben](./apache-ambari-email.md)című témakörben tekintheti meg.

### <a name="cluster"></a>Fürt

Az irányítópult **metrikák** lapja több widgetet tartalmaz, amelyek megkönnyítik a fürt állapotának figyelését egy pillantással. Több widget (például **CPU-használat**) esetén további információk is megadhatók a kattintáskor.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png" alt-text="Apache Ambari-irányítópult metrikákkal":::

A **intenzitástérképei** lap színes intenzitástérképei jeleníti meg a metrikákat, zöldről pirosra haladva.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png" alt-text="Apache Ambari-irányítópult a intenzitástérképei":::

A fürtben lévő csomópontokkal kapcsolatos további információkért válassza a **gazdagépek** lehetőséget. Ezután válassza ki azt a csomópontot, amelyre kíváncsi.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png" alt-text="Apache Ambari-gazdagép összegzése – részletek":::

### <a name="services"></a>Szolgáltatások

Az irányítópulton található **szolgáltatások** oldalsáv gyors betekintést nyújt a fürtön futó szolgáltatások állapotával. A különböző ikonok jelzik az állapotot vagy a végrehajtandó műveleteket. A sárga Lomtár például akkor jelenik meg, ha egy szolgáltatást újra kell indítani.


:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png" alt-text="Apache Ambari-szolgáltatások oldalsó sáv":::

> [!NOTE]  
> A megjelenített szolgáltatások különböznek a HDInsight-fürtök típusai és verziói között. Az itt megjelenő szolgáltatások eltérhetnek a fürthöz megjelenített szolgáltatástól.

A szolgáltatás kiválasztása részletesebb információkat jelenít meg a szolgáltatásról.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png" alt-text="Apache Ambari szolgáltatás – összefoglaló információk":::

#### <a name="quick-links"></a>Gyorshivatkozások

Egyes szolgáltatások egy **Gyorshivatkozások** hivatkozást jelenítenek meg az oldal tetején. Ez a hivatkozás a szolgáltatás-specifikus webes felület elérésére használható, például:

* **Feladatok előzményei** – MapReduce-feladatok előzményei.
* **Resource Manager** – a fonal Resource Manager felhasználói felülete.
* **NameNode** -Hadoop ELOSZTOTT fájlrendszer (HDFS) NameNode felhasználói felülete.
* **Oozie webes felhasználói felület** – Oozie felhasználói felület.

A hivatkozások bármelyikének kiválasztásával megnyílik egy új lap a böngészőben, amely megjeleníti a kijelölt lapot.

> [!NOTE]  
> Ha egy szolgáltatáshoz tartozó **Gyorshivatkozások** bejegyzést választja, a "kiszolgáló nem található" hibaüzenetet adhat vissza. Ha ezt a hibát tapasztalja, egy SSH-alagutat kell használnia, ha a **gyors hivatkozások** bejegyzést használja ehhez a szolgáltatáshoz. További információ: az [SSH-alagút használata a HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Kezelés

### <a name="ambari-users-groups-and-permissions"></a>Felhasználók, csoportok és engedélyek Ambari

A felhasználók, csoportok és engedélyek használata támogatott. A helyi felügyelethez lásd: [felhasználók engedélyezése Apache Ambari-nézetekhez](./hdinsight-authorize-users-to-ambari.md). Tartományhoz csatlakoztatott fürtök esetében lásd: [tartományhoz csatlakoztatott HDInsight-fürtök kezelése](./domain-joined/hdinsight-security-overview.md).

> [!WARNING]  
> A Linux-alapú HDInsight-fürtön ne törölje vagy módosítsa a Ambari watchdog (hdinsightwatchdog) jelszavát. A jelszó módosítása megszakítja a parancsfájl-műveletek használatát, vagy skálázási műveleteket hajt végre a fürtön.

### <a name="hosts"></a>Hosts

A **gazdagépek** lapon a fürtben található összes gazdagép szerepel. A gazdagépek kezeléséhez kövesse az alábbi lépéseket.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png" alt-text="Az Apache Ambari hosts oldalának áttekintése":::

> [!NOTE]  
> A gazdagépek hozzáadása, leszerelése és újraüzembe helyezése nem használható HDInsight-fürtökkel.

1. Válassza ki a kezelni kívánt gazdagépet.

2. A **műveletek** menüben válassza ki a végrehajtani kívánt műveletet:

    |Elem |Leírás |
    |---|---|
    |Az összes összetevő elindítása|Indítsa el az összes összetevőt a gazdagépen.|
    |Az összes összetevő leállítása|Állítsa le az összes összetevőt a gazdagépen.|
    |Az összes összetevő újraindítása|Állítsa le és indítsa el az összes összetevőt a gazdagépen.|
    |Karbantartási mód bekapcsolása|Letiltja a gazdagép riasztásait. Ezt a módot akkor kell engedélyezni, ha riasztásokat létrehozó műveleteket hajt végre. Például leállíthatja és elindíthatja a szolgáltatást.|
    |Karbantartási mód kikapcsolása|A gazdagépet a normál riasztáshoz adja vissza.|
    |Leállítás|Leállítja a DataNode vagy a Csomópontkezelők a gazdagépen.|
    |Indítás|Elindítja a DataNode vagy a Csomópontkezelők a gazdagépen.|
    |Újraindítás|Leállítja és elindítja a DataNode vagy a Csomópontkezelők a gazdagépen.|
    |Leszerelése|Eltávolít egy gazdagépet a fürtből. **Ne használja ezt a műveletet a HDInsight-fürtökön.**|
    |Recommission|Egy korábban leszerelt gazdagép hozzáadását a fürthöz. **Ne használja ezt a műveletet a HDInsight-fürtökön.**|

### <a name="services"></a><a id="service"></a>Szolgáltatások

Az **irányítópult** vagy **szolgáltatások** lapon a szolgáltatások listájának alján található **műveletek** gomb használatával állítsa le és indítsa el az összes szolgáltatást.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png" alt-text="Apache Ambari-szolgáltatási műveletek listája." border="true":::

> [!WARNING]  
> Az új szolgáltatásokat a fürt üzembe helyezése során parancsfájl-művelettel kell hozzáadni. A parancsfájl-műveletek használatával kapcsolatos további információkért lásd: [HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](hdinsight-hadoop-customize-cluster-linux.md).

Míg a **műveletek** gomb újraindíthatja az összes szolgáltatást, gyakran egy adott szolgáltatás indítását, leállítását vagy újraindítását is elvégezheti. A következő lépésekkel végezheti el a műveleteket egy adott szolgáltatáson:

1. Az **irányítópult** vagy **szolgáltatások** lapon válasszon ki egy szolgáltatást.

2. Az **Összefoglalás** lap tetején kattintson a **szolgáltatási műveletek** gombra, és válassza ki az elvégzendő műveletet. Ez a művelet újraindítja a szolgáltatást az összes csomóponton.

    :::image type="content" source="./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png" alt-text="Az Apache Ambari egyedi szolgáltatási műveletei":::

   > [!NOTE]  
   > Bizonyos szolgáltatások újraindítása, amíg a fürt fut, riasztásokat hozhatnak. A riasztások elkerülése érdekében a **szolgáltatás műveletei** gomb használatával engedélyezheti a **karbantartási módot** a szolgáltatás számára az újraindítás előtt.

3. A művelet kiválasztását követően az oldal tetején lévő **# op** bejegyzés megnöveli a háttérben futó művelet megjelenítését. Ha a megjelenítésre van konfigurálva, megjelenik a háttérben futó műveletek listája.

   > [!NOTE]  
   > Ha engedélyezte a **karbantartási módot** a szolgáltatáshoz, ne felejtse el letiltani a **szolgáltatás műveletei** gomb használatával a művelet befejeződése után.

A szolgáltatás konfigurálásához kövesse az alábbi lépéseket:

1. Az **irányítópult** vagy **szolgáltatások** lapon válasszon ki egy szolgáltatást.

2. Válassza a **konfigurációk** fület. Megjelenik az aktuális konfiguráció. Megjelenik a korábbi konfigurációk listája is.

    :::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png" alt-text="Apache Ambari szolgáltatás konfigurációja":::

3. A megjelenített mezők használatával módosítsa a konfigurációt, majd válassza a **Mentés** lehetőséget. Vagy válasszon egy korábbi konfigurációt, majd válassza az **aktuális** lehetőséget az előző beállításokra való visszalépéshez.

## <a name="ambari-views"></a>Ambari nézetek

A Ambari nézetek lehetővé teszik a fejlesztők számára a felhasználói felületi elemek csatlakoztatását a Ambari webes felhasználói felületéhez az Apache Ambari views keretrendszer használatával. A HDInsight a következő nézeteket biztosítja a Hadoop-fürtökhöz:

* Struktúra nézet: a kaptár nézet lehetővé teszi, hogy közvetlenül a webböngészőből futtasson kaptár-lekérdezéseket. Mentheti a lekérdezéseket, megtekintheti az eredményeket, mentheti az eredményeket a fürt tárolójába, vagy letöltheti az eredményeket a helyi rendszeren. A kaptár-nézetek használatáról további információt a [Apache Hive nézetek használata a HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)című témakörben talál.

* Tez nézet: a TEZ nézet lehetővé teszi a feladatok jobb megismerését és optimalizálását. Megtekintheti a TEZ feladatok végrehajtásának és az erőforrások használatának módját.

## <a name="unsupported-operations"></a>Nem támogatott műveletek

A következő Ambari műveletek nem támogatottak a HDInsight:

* __A metrikák gyűjtő szolgáltatásának áthelyezése__. A metrika-gyűjtő szolgáltatás információinak megtekintésekor a szolgáltatási műveletek menüben elérhető műveletek egyike a __metrika-gyűjtő mozgatása__. Ez a művelet nem támogatott a HDInsight.

## <a name="next-steps"></a>Következő lépések

* Az [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) a HDInsight-mel.
* [Az Apache Ambari használata a HDInsight fürtkonfigurációinak optimalizálására](./hdinsight-changing-configs-via-ambari.md)
* [Azure HDInsight-fürtök méretezése](./hdinsight-scaling-best-practices.md)
