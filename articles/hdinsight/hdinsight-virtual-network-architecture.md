---
title: Azure HDInsight virtuális hálózati architektúra
description: Ismerje meg a HDInsight-fürt Azure-Virtual Network való létrehozásakor elérhető erőforrásokat.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 43640a9de91b6b44a236090ff029a49313ee0247
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871673"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight virtuális hálózati architektúra

Ez a cikk ismerteti azokat az erőforrásokat, amelyek akkor jelennek meg, amikor egy HDInsight-fürtöt telepít egy egyéni Azure-Virtual Networkba. Ezek az információk segítséget nyújtanak a helyszíni erőforrások a HDInsight-fürthöz való összekapcsolásában az Azure-ban. Az Azure Virtual Networks szolgáltatással kapcsolatos további információkért lásd: [Mi az az azure Virtual Network?](../virtual-network/virtual-networks-overview.md)

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Erőforrástípusok az Azure HDInsight-fürtökben

Az Azure HDInsight-fürtök különböző típusú virtuális gépekkel vagy csomópontokkal rendelkeznek. Minden csomópont-típus a rendszer működésében játszik szerepet. A következő táblázat összefoglalja ezeket a csomópont-típusokat és azok szerepköreit a fürtben.

| Típus | Leírás |
| --- | --- |
| Átjárócsomópont |  Az Apache Storm kivételével az összes fürtjénél a fő csomópontok futtatják az elosztott alkalmazás végrehajtását kezelő folyamatokat. A fő csomópont egyben a csomópont is, amelyből SSH-ba helyezheti és végrehajthatja azokat az alkalmazásokat, amelyek a fürt erőforrásai között futnak. A fő csomópontok száma a fürt összes típusa esetében kettőnél van rögzítve. |
| ZooKeeper csomópont | A Zookeeper az adatfeldolgozást végző csomópontok között koordinálja a feladatokat. Emellett a fő csomópontot is vezeti, és nyomon követi, hogy melyik főcsomóponton fut egy adott főkiszolgáló. A ZooKeeper-csomópontok száma három helyen van rögzítve. |
| Munkavégző csomópont | Azokat a csomópontokat jelöli, amelyek támogatják az adatfeldolgozási funkciókat. A munkavégző csomópontok hozzáadhatók vagy eltávolíthatók a fürtből a számítási kapacitás méretezéséhez és a költségek kezeléséhez. |
| R Server peremhálózati csomópont | Az R Server Edge csomópont azt a csomópontot jelöli, amelyről SSH-ba, és végrehajthat olyan alkalmazásokat, amelyek a fürt erőforrásai között futnak. Az Edge-csomópontok nem vesznek részt a fürtön belüli adatelemzésben. Ez a csomópont R Studio Servert is üzemeltet, amely lehetővé teszi az R-alkalmazások futtatását böngésző használatával. |
| Régió csomópontja | A HBase-fürt típusához a régió csomópont (más néven adatcsomópont) futtatja a régió-kiszolgálót. A régió-kiszolgálók a HBase által felügyelt adat egy részét szolgálják és kezelik. A régió csomópontjai hozzáadhatók vagy eltávolíthatók a fürtből a számítási kapacitás méretezéséhez és a költségek kezeléséhez.|
| Nimbus csomópont | A Storm-fürt típusához a Nimbus csomópont a fő csomóponthoz hasonló funkciókat biztosít. A Nimbus-csomópont feladatokat rendel a fürt más csomópontjaihoz a Zookeeper használatával, amely a Storm-topológiák futtatását koordinálja. |
| Felügyeleti csomópont | A Storm-fürt típusához a felügyelő csomópont hajtja végre a Nimbus csomópont által megadott utasításokat a feldolgozás elvégzéséhez. |

## <a name="resource-naming-conventions"></a>Erőforrás-elnevezési konvenciók

Használjon teljes tartományneveket (FQDN) a fürt csomópontjainak kezelésekor. A fürt különböző csomópontjaihoz tartozó teljes tartománynevek a [AMBARI API](hdinsight-hadoop-manage-ambari-rest-api.md)használatával szerezhetők be.

Ezek a teljes tartománynevek az űrlapból lesznek `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net` .

A `<node-type-prefix>` lesz *HN* a átjárócsomópontokkal, a  feldolgozói csomópontok és a *Zn* Zookeeper-csomópontok számára.

Ha csak az állomásnévre van szüksége, csak a teljes tartománynév első részét használja: `<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Alapszintű virtuális hálózati erőforrások

A következő ábra a HDInsight-csomópontok és hálózati erőforrások elhelyezését mutatja be az Azure-ban.

:::image type="content" source="./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png" alt-text="Az Azure egyéni VNET létrehozott HDInsight-entitások ábrája" border="false":::

Az Azure-Virtual Network alapértelmezett erőforrásai tartalmazzák az előző táblázatban említett fürtcsomópontok típusait. A virtuális hálózat és a külső hálózatok közötti kommunikációt támogató hálózati eszközök.

A következő táblázat összefoglalja azokat a kilenc fürtcsomópont-csomópontot, amelyek akkor jönnek létre, amikor a HDInsight-t egy egyéni Azure-Virtual Network telepítik.

| Erőforrás típusa | Szám jelen | Részletek |
| --- | --- | --- |
|Átjárócsomópont | kettő |    |
|Zookeeper-csomópont | három | |
|Munkavégző csomópont | kettő | Ez a szám a fürtkonfiguráció és a skálázás alapján változhat. Apache Kafkahez legalább három munkavégző csomópontra van szükség.  |
|Átjárócsomópont | kettő | Az átjáró-csomópontok olyan Azure-beli virtuális gépek, amelyek az Azure-ban jönnek létre, de nem láthatók az előfizetésében. Ha újra kell indítania ezeket a csomópontokat, forduljon az ügyfélszolgálathoz. |

A következő hálózati erőforrások automatikusan létrejönnek a HDInsight használt virtuális hálózaton belül:

| Hálózati erőforrás | Szám jelen | Részletek |
| --- | --- | --- |
|Terheléselosztóval | három | |
|Hálózati illesztők | kilenc | Ez az érték egy normál fürtön alapul, ahol minden egyes csomópont saját hálózati adapterrel rendelkezik. A kilenc csatoló a következő: két fő csomópont, három Zookeeper-csomópont, két feldolgozó csomópont és két, az előző táblázatban említett átjáró-csomópont. |
|Nyilvános IP-címek | kettő |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Végpontok a HDInsight való csatlakozáshoz

A HDInsight-fürtöt háromféle módon érheti el:

- Egy HTTPS-végpont a virtuális hálózaton kívül a következő helyen: `CLUSTERNAME.azurehdinsight.net` .
- Egy SSH-végpont, amely közvetlenül csatlakozik a átjárócsomóponthoz a következő helyen: `CLUSTERNAME-ssh.azurehdinsight.net` .
- Egy HTTPS-végpont a virtuális hálózaton belül `CLUSTERNAME-int.azurehdinsight.net` . Figyelje meg a " `-int` " kifejezést ebben az URL-ben. Ez a végpont a virtuális hálózatban lévő magánhálózati IP-címekre lesz feloldva, és nem érhető el a nyilvános internetről.

Ez a három végpont mindegyike hozzá van rendelve egy terheléselosztó.

A rendszer a nyilvános IP-címeket is megadja a két végpont számára, amelyek engedélyezik a kapcsolódást a virtuális hálózaton kívülről.

1. Egy nyilvános IP-cím van hozzárendelve a terheléselosztó számára a teljes tartománynév (FQDN) számára, amelyet az internetről a fürthöz való csatlakozáskor kell használni `CLUSTERNAME.azurehdinsight.net` .
1. A második nyilvános IP-cím csak az SSH-tartománynévhez használatos `CLUSTERNAME-ssh.azurehdinsight.net` .

## <a name="next-steps"></a>Következő lépések

- [Biztonságos bejövő forgalom HDInsight-fürtökhöz privát végponttal rendelkező virtuális hálózaton](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
