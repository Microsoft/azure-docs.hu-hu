---
title: Az Azure HDInsight kiadási megjegyzései
description: A legújabb kibocsátási megjegyzések a Azure HDInsight. Fejlesztési tippeket és részleteket kaphat a Hadoophoz, a Sparkhoz, R Server Hive-hoz és sok máshoz.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 3676889fc56f6dc4746282e5001e33d1bc3c7c9a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501229"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight kibocsátási megjegyzések

Ez a cikk a legújabb **kiadási** Azure HDInsight információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight Release Notes Archive (A HDInsight kibocsátási megjegyzéseinek archívuma).](hdinsight-release-notes-archive.md)

## <a name="summary"></a>Összefoglalás

Azure HDInsight a vállalati ügyfelek körében az egyik legnépszerűbb szolgáltatás a nyílt forráskódú elemzésekhez az Azure-ban.

Ha elő szeretne fizetni a kibocsátási megjegyzésekre, tekintse meg a [githubos adattárban elérhető kiadásokat.](https://github.com/hdinsight/release-notes/releases)

## <a name="release-date-03242021"></a>Kiadási dátum: 2021.03.24.

Ez a kiadás a HDInsight 3.6-ra és a HDInsight 4.0-ra is vonatkozik. A HDInsight-kiadás több napon keresztül érhető el az összes régióban. A kiadási dátum itt az első régió kiadási dátumát jelzi. Ha nem látja az alábbi módosításokat, várjon, amíg a kiadás néhány napon belül az Ön régiójában lesz.

## <a name="new-features"></a>Új funkciók
### <a name="spark-30-preview"></a>Spark 3.0 előzetes verzió
A HDInsight előzetes verziójú funkcióként hozzáadta a [Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) támogatását a HDInsight 4.0-hoz. 

### <a name="kafka-24-preview"></a>Kafka 2.4 előzetes verzió
A HDInsight előzetes verziójú funkcióként hozzáadta a [Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) támogatását a HDInsight 4.0-hoz.

### <a name="eav4-series-support"></a>Eav4-sorozat támogatása
Ebben a kiadásban a HDInsight Eav4-sorozatú támogatást adott hozzá. A [Dav4 sorozatról itt olvashat bővebben.](../virtual-machines/eav4-easv4-series.md) A sorozat a következő régiókban érhető el: 

* Kelet-Ausztrália
* Dél-Brazília
* Az USA középső régiója
* Kelet-Ázsia
* USA keleti régiója
* Kelet-Japán
* Délkelet-Ázsia
* Az Egyesült Királyság déli régiója
* Nyugat-Európa
* USA 2. nyugati régiója

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Áthelyezés Azure-beli virtuálisgép-méretezési készletekre
A HDInsight mostantól Azure-beli virtuális gépekkel létesíti a fürtöt. A szolgáltatás fokozatosan milegál az [Azure-beli virtuálisgép-méretezési készletekbe.](../virtual-machine-scale-sets/overview.md) A teljes folyamat hónapokba telhet. A régiók és előfizetések áttelepítése után az újonnan létrehozott HDInsight-fürtök ügyfélműveletek nélkül fognak futni virtuálisgép-méretezési csoportokon. Nem várható feltöréses változás.

## <a name="deprecation"></a>Elavulás
Ebben a kiadásban nincs elalasztás.

## <a name="behavior-changes"></a>Viselkedésbeli változások
### <a name="default-cluster-version-is-changed-to-40"></a>A fürt alapértelmezett verziója 4.0-ra módosult
A HDInsight-fürt alapértelmezett verziója 3.6-osról 4.0-ra módosult. További információ az elérhető verziókról: [Elérhető verziók.](./hdinsight-component-versioning.md) További információ a [HDInsight 4.0 újdonságiról.](./hdinsight-version-release.md)

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>A fürtök alapértelmezett virtuálisgép-méretei Ev3-sorozatra módosulnak 
A fürtök alapértelmezett virtuálisgép-méretei D-sorozatról Ev3-sorozatra módosulnak. Ez a módosítás az főcsomópontokra és a munkavégző csomópontokra vonatkozik. Ha el szeretné kerülni, hogy ez a módosítás hatással legyen a tesztelt munkafolyamatra, adja meg az ARM-sablonban használni kívánt virtuálisgép-méreteket.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>A hálózati adapter erőforrása nem látható az Azure-beli virtuálisgép-méretezési csoportokon futó fürtök számára
A HDInsight fokozatosan milagosul azure-beli virtuálisgép-méretezési készletekbe. A virtuális gépek hálózati adapterei már nem láthatók az ügyfelek számára az Azure-beli virtuálisgép-méretezési csoportokat felhasználó fürtök esetében.

## <a name="upcoming-changes"></a>Közelgő változások
A következő módosításokra a következő kiadásokban lesz szükség.

### <a name="os-version-upgrade"></a>Operációs rendszer verziófrissítése
A HDInsight-fürtök jelenleg Ubuntu 16.04 LTS-en futnak. Az [Ubuntu](https://ubuntu.com/about/release-cycle)kiadási ciklusában hivatkozottak szerint az Ubuntu 16.04 kernel 2021 áprilisában eléri az End of Life (EOL) elérést. 2021 májusában elkezdjük az Ubuntu 18.04-en futó új HDInsight 4.0-fürt rendszerképének bevezetését. Az újonnan létrehozott HDInsight 4.0-fürtök alapértelmezés szerint ubuntu 18.04-en futnak, amint elérhetővé válik. Az Ubuntu 16.04-en futó meglévő fürtök a jelenlegitől függően, teljes körű támogatással fognak futni.

A HDInsight 3.6 továbbra is ubuntu 16.04-en fog futni. A támogatás 2021. június 30-ig véget ér, és 2021. július 1-től alapszintű támogatás lesz. A dátumokkal és a támogatási lehetőségekkel kapcsolatos további információkért lásd: [Azure HDInsight verziók.](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions) A HDInsight 3.6 nem támogatja az Ubuntu 18.04-es verzió használatát. Ha az Ubuntu 18.04-et szeretné használni, át kelltelepítenie a fürtöt a HDInsight 4.0-ra. 

Ha meglévő fürtök Ubuntu 18.04-re való áthelyezését szeretné, el kellejtenie, majd újra létre kell hoznia a fürtöt. Miután elérhetővé válik az Ubuntu 18.04 támogatása, tervezze meg a fürtöt, vagy hozza létre újra. Egy újabb értesítést küldünk, miután az új rendszerkép elérhetővé válik az összes régióban.

Erősen ajánlott előre tesztelni a szkriptműveleteket és az élcsomópontokon üzembe helyezett egyéni alkalmazásokat egy Ubuntu 18.04 virtuális gépen (VM). Létrehozhat egy egyszerű Ubuntu Linux virtuális gépet [a 18.04-LTS-en,](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)majd létrehozhat és használhat egy [Secure Shell- (SSH-)](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys#ssh-into-your-vm) kulcspárt a virtuális gépen a szkriptműveletek és az élcsomópontokon üzembe helyezett egyéni alkalmazások futtatásához és teszteléséhez.

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>alapszintű támogatás HDInsight 3.6-os és 2021. július 1-től kezdődően
2021. július 1-től [](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) kezdve a Microsoft alapszintű támogatás HDInsight 3.6-fürttípusokhoz. A alapszintű támogatás terv 2022. április 3-ig lesz elérhető. 2021. július 1-től alapszintű támogatás automatikusan beléptetjük a szolgáltatásba. A feliratkozáshoz nincs szükség beavatkozásra. Tekintse [meg dokumentációnkban,](hdinsight-36-component-versioning.md) hogy mely fürttípusok szerepelnek a alapszintű támogatás. 

Nem javasoljuk, hogy új megoldásokat építsen a HDInsight 3.6-ban, és állítsa be a módosításokat a meglévő 3.6-os környezetekben. Javasoljuk, hogy a fürtök [áttelepítése a HDInsight 4.0-ra.](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40) További információ a [HDInsight 4.0 újdonságiról.](hdinsight-version-release.md#whats-new-in-hdinsight-40)

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is javítja a fürt megbízhatóságát és teljesítményét. 

## <a name="component-version-change"></a>Összetevő verziójának módosítása
A Spark 3.0.0 és a Kafka 2.4.1 előzetes verziója már támogatott. A HDInsight 4.0 és a HDInsight 3.6 aktuális összetevőverzióit ebben a [dokumentumban találja.](./hdinsight-component-versioning.md)

## <a name="recommanded-features"></a>Újraküldött funkciók
### <a name="service-tags"></a>Szolgáltatáscímkék
A szolgáltatáscímkék leegyszerűsítik az Azure-szolgáltatásokhoz való hálózati hozzáférés korlátozását az Azure-beli virtuális gépekhez és az Azure-beli virtuális hálózatokhoz. A hálózati biztonsági csoport (NSG) szabályaiban a szolgáltatáscímkék engedélyezik vagy megtagadják az adott Azure-szolgáltatásba való forgalmat. A szabály beállítható globálisan vagy Azure-régiónként. Az Azure biztosítja az egyes címkék alapjául szolgáló IP-címek karbantartását. A hálózati biztonsági csoportok (NSG-k) HDInsight-szolgáltatáscímkéi az állapot- és felügyeleti szolgáltatások IP-címeinek csoportjai. Ezek a csoportok segítenek minimálisra csökkenteni a biztonsági szabályok létrehozásának összetettségét. A HDInsight-ügyfelek szolgáltatáscímkét Azure Portal, a PowerShell és a REST API. További információ: Hálózati [biztonsági csoport (NSG) szolgáltatáscímkék a Azure HDInsight.](./hdinsight-service-tags.md)
