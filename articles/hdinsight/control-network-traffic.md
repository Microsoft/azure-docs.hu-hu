---
title: Hálózati forgalom szabályozása az Azure HDInsight
description: Megtudhatja, hogyan vezérelheti a bejövő és a kimenő adatforgalmat az Azure HDInsight-fürtökön.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 0cbda0b533a64e627bfeef9589ab95c4163ae73e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943374"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Hálózati forgalom szabályozása az Azure HDInsight

Az Azure-beli virtuális hálózatok hálózati forgalmát a következő módszerekkel lehet vezérelni:

* A **hálózati biztonsági csoportok** (NSG) lehetővé teszik a bejövő és a kimenő forgalom szűrését a hálózatra. További információ: [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/network-security-groups-overview.md) dokumentum.

* A **hálózati virtuális berendezések** (NVA-EK) csak kimenő forgalom esetén használhatók. A NVA replikálja az eszközök, például a tűzfalak és az útválasztók működését. További információ: [hálózati berendezések](https://azure.microsoft.com/solutions/network-appliances) dokumentum.

Felügyelt szolgáltatásként a HDInsight a HDInsight állapot-és felügyeleti szolgáltatásokhoz való korlátlan hozzáférést igényel mind a VNET érkező bejövő, mind kimenő forgalmához. A NSG használatakor biztosítania kell, hogy ezek a szolgáltatások továbbra is kommunikálhatnak a HDInsight-fürttel.

![Az Azure egyéni VNET létrehozott HDInsight-entitások ábrája](./media/control-network-traffic/hdinsight-vnet-diagram.png)

## <a name="hdinsight-with-network-security-groups"></a>HDInsight hálózati biztonsági csoportokkal

Ha **hálózati biztonsági csoportokat** kíván használni a hálózati forgalom szabályozásához, hajtsa végre a következő műveleteket a HDInsight telepítése előtt:

1. Azonosítsa az HDInsight használni kívánt Azure-régiót.

2. Azonosítsa a HDInsight által a régiója számára szükséges szolgáltatási címkéket. A szolgáltatási címkék több módon is beszerezhetők:
    1. A közzétett szolgáltatások címkéit az [Azure HDInsight hálózati biztonsági csoport (NSG) szolgáltatásbeli címkék](hdinsight-service-tags.md)listájában tájékozódhat. 
    2. Ha a régió nem szerepel a listában, használja a [Service tag Discovery API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) -t a régiója szolgáltatási címkéjének megtalálásához.
    3. Ha nem tudja használni az API-t, töltse le a [Service tag JSON-fájlját](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) , és keresse meg a kívánt régiót.


3. Hozza létre vagy módosítsa annak az alhálózatnak a hálózati biztonsági csoportjait, amelyre telepíteni kívánja a HDInsight-et.

    * __Hálózati biztonsági csoportok__: engedélyezze a __bejövő__ forgalmat az __443__ -as porton az IP-címekről. Ezzel biztosíthatja, hogy a HDInsight-kezelési szolgáltatások a virtuális hálózaton kívülről is elérjék a fürtöt. A __KAFKA Rest proxyt__ használó fürtök esetében engedélyezze a __bejövő__ adatforgalmat is a __9400__ -as porton. Ez biztosítja, hogy a Kafka REST-proxykiszolgáló elérhető legyen.

A hálózati biztonsági csoportokkal kapcsolatos további információkért tekintse meg a [hálózati biztonsági csoportok áttekintése](../virtual-network/network-security-groups-overview.md)című témakört.

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>HDInsight-fürtök kimenő forgalmának szabályozása

További információ a HDInsight-fürtök kimenő forgalmának szabályozásáról: a [kimenő hálózati forgalom korlátozásának konfigurálása az Azure HDInsight-fürtökhöz](hdinsight-restrict-outbound-traffic.md).

### <a name="forced-tunneling-to-on-premises"></a>Kényszerített bújtatás a helyszíni környezetbe

A kényszerített bújtatás egy felhasználó által megadott útválasztási konfiguráció, amelyben az alhálózat összes forgalma egy adott hálózatra vagy helyre, például a helyszíni hálózatra vagy a tűzfalra van kényszerítve. A nagy mennyiségű adatátvitel és a lehetséges teljesítmény-hatás miatt a rendszer nem javasolja, hogy a helyszíni adatforgalom kényszerített bújtatása _nem_ ajánlott.

Azok az ügyfelek, akik számára a kényszerített bújtatást kívánják beállítani, [Egyéni metaadattárak](./hdinsight-use-external-metadata-stores.md) kell használniuk, és a megfelelő kapcsolatot kell beállítania a fürt alhálózatán vagy a helyszíni hálózatán ezen egyéni metaadattárak.

Ha Azure Firewall használatával szeretné megtekinteni a UDR telepítőjét, tekintse meg a [kimenő hálózati forgalom korlátozásának konfigurálása az Azure HDInsight-fürtökhöz](hdinsight-restrict-outbound-traffic.md)című témakört.

## <a name="required-ports"></a>Szükséges portok

Ha **tűzfalat** szeretne használni, és bizonyos portokon kívülről fér hozzá a fürthöz, lehetséges, hogy engedélyeznie kell a forgalmat az adott forgatókönyvhöz szükséges portokon. Alapértelmezés szerint a portok speciális szűrése nem szükséges, ha az előző szakaszban ismertetett Azure felügyeleti forgalom a 443-es porton keresztül elérhetővé teszi a fürtöt.

Az egyes szolgáltatásokhoz tartozó portok listáját lásd: [Apache Hadoop Services által használt portok a HDInsight](hdinsight-hadoop-port-settings-for-services.md) -dokumentumban.

A virtuális készülékekre vonatkozó tűzfalszabályok részletes ismertetését lásd: [virtuális készülék forgatókönyvének](../virtual-network/virtual-network-scenario-udr-gw-nva.md) dokumentuma.

## <a name="next-steps"></a>Következő lépések

* Az Azure-beli virtuális hálózatok létrehozásával kapcsolatos Példákért lásd: [virtuális hálózatok létrehozása az Azure HDInsight-fürtökhöz](hdinsight-create-virtual-network.md).
* A helyszíni hálózathoz való kapcsolódás HDInsight konfigurálásának teljes körű példáját lásd: [a HDInsight összekapcsolása egy helyszíni hálózattal](./connect-on-premises-network.md).
* Az Azure Virtual Networks szolgáltatással kapcsolatos további információkért tekintse meg az [azure Virtual Network áttekintését](../virtual-network/virtual-networks-overview.md).
* A hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md).
* A felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd: [felhasználó által definiált útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md).
* További információ a virtuális hálózatokról: [virtuális hálózatok tervezése a HDInsight](./hdinsight-plan-virtual-network-deployment.md).