---
title: Hálózati biztonsági csoport (NSG) szolgáltatás címkéi az Azure HDInsight
description: A HDInsight szolgáltatás-címkék használatával engedélyezheti a fürtre irányuló bejövő forgalmat az állapot-és felügyeleti szolgáltatások csomópontjairól anélkül, hogy IP-címeket kellene hozzáadnia a NSG.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: d17d067b88add3006bc5c7fb10caa6b80a80a827
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931770"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Az Azure HDInsight NSG szolgáltatásának címkéi

Az Azure HDInsight Service-címkék hálózati biztonsági csoportokhoz (NSG) IP-címek csoportjai az állapot-és felügyeleti szolgáltatásokhoz. Ezek a csoportok segítenek a biztonsági szabályok létrehozásának összetettségének minimalizálásában. A [szolgáltatás címkéi](../virtual-network/network-security-groups-overview.md#service-tags) lehetővé teszik a megadott IP-címekről érkező bejövő adatforgalmat anélkül, hogy a NSG minden [felügyeleti IP-címét](hdinsight-management-ip-addresses.md) be kellene írni.

A HDInsight szolgáltatás kezeli ezeket a szolgáltatási címkéket. Nem hozhat létre saját szolgáltatási címkét, vagy nem módosíthat meglévő címkét. A Microsoft kezeli a szolgáltatási címkének megfelelő címet, és automatikusan frissíti a szolgáltatási címkét a címek módosításaként.

Ha egy adott régiót szeretne használni, és a szolgáltatás címkéje még nincs dokumentálva ezen az oldalon, a Service tag [felderítési API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) használatával megkeresheti a szolgáltatás címkéjét. Letöltheti továbbá a [Service tag JSON-fájlját](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) , és megkeresheti a kívánt régiót.

## <a name="get-started-with-service-tags"></a>A szolgáltatás-címkék első lépései

A hálózati biztonsági csoportokban két lehetőség van a szolgáltatási címkék használatára:

- **Egyetlen globális HDInsight szolgáltatás használata**: ezzel a beállítással megnyithatja a virtuális hálózatot minden olyan IP-címre, amelyet a HDInsight szolgáltatás használ a fürtök összes régión belüli figyelésére. Ez a legegyszerűbb módszer, de előfordulhat, hogy nem megfelelő, ha korlátozó biztonsági követelményekkel rendelkezik.

- **Több regionális szolgáltatási címke használata**: ezzel a beállítással megnyithatja a virtuális hálózatot, hogy csak az adott régióban használt IP-címek HDInsight. Ha azonban több régiót használ, több szolgáltatási címkét is hozzá kell adnia a virtuális hálózathoz.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Egyetlen globális HDInsight szolgáltatási címke használata

A HDInsight-fürthöz tartozó szolgáltatás-címkék használatának legegyszerűbb módja, ha hozzáadja a globális címkét `HDInsight` egy NSG-szabályhoz.

1. A [Azure Portal](https://portal.azure.com/)válassza ki a hálózati biztonsági csoportot.

1. A **Beállítások** területen válassza a **bejövő biztonsági szabályok** elemet, majd válassza a **+ Hozzáadás** lehetőséget.

1. A **forrás** legördülő listából válassza ki a **szolgáltatás címkéje** elemet.

1. A **forrás szolgáltatás címkéje** legördülő listában válassza a **HDInsight** lehetőséget.

    ![Adja hozzá a szolgáltatás címkéjét a Azure Portal](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Ez a címke tartalmazza az állapot-és felügyeleti szolgáltatások IP-címeit minden olyan régió esetében, ahol a HDInsight elérhető. A címke biztosítja, hogy a fürt a létrehozásuk helyétől függetlenül képes legyen kommunikálni a szükséges egészségügyi és felügyeleti szolgáltatásokkal.

## <a name="use-regional-hdinsight-service-tags"></a>Területi HDInsight szolgáltatásbeli címkék használata

Ha a globális címke lehetőség nem fog működni, mert szigorúbb engedélyekre van szüksége, csak az adott régióhoz tartozó szolgáltatási címkéket engedélyezheti. Több szolgáltatási címke is lehet, attól függően, hogy melyik régiót hozza létre a fürt.

Ha szeretné megtudni, hogy mely szolgáltatási címkéket szeretné hozzáadni a régióhoz, olvassa el a cikk következő részeit.

### <a name="use-a-single-regional-service-tag"></a>Egyetlen regionális szolgáltatási címke használata

Ha a fürt egy ebben a táblázatban felsorolt régióban található, csak egyetlen regionális szolgáltatási címkét kell hozzáadnia a NSG.

| Country | Régió | Szolgáltatáscímke |
| ---- | ---- | ---- |
| Ausztrália | Kelet-Ausztrália | HDInsight. AustraliaEast |
| &nbsp; | Délkelet-Ausztrália | HDInsight. AustraliaSoutheast |
| &nbsp; | Ausztrália középső régiója | HDInsight. AustraliaCentral |
| Kína | Kelet-Kína 2 | HDInsight. ChinaEast2 |
| &nbsp; | Észak-Kína 2 | HDInsight. ChinaNorth2 |
| Egyesült Államok | USA északi középső régiója | HDInsight. NorthCentralUS |
| &nbsp; | USA 2. nyugati régiója | HDInsight. WestUS2 |
| &nbsp; | USA nyugati középső régiója | HDInsight. WestCentralUS |
| Kanada | Kelet-Kanada | HDInsight. CanadaEast |
| Brazília | Dél-Brazília | HDInsight. BrazilSouth |
| Dél-Korea | Dél-Korea középső régiója | HDInsight. KoreaCentral |
| &nbsp; | Dél-Korea déli régiója | HDInsight. KoreaSouth |
| India | Közép-India | HDInsight. CentralIndia |
| &nbsp; | Dél-India | HDInsight. SouthIndia |
| Japán | Nyugat-Japán | HDInsight. JapanWest |
| Franciaország | Közép-Franciaország| HDInsight. FranceCentral |
| Egyesült Királyság | Az Egyesült Királyság déli régiója | HDInsight. UKSouth |
| Azure Government | USA középső régiója | HDInsight. USDoDCentral |
| &nbsp; | USGov Texas | HDInsight. USGovTexas |
| &nbsp; | USA keleti régiója | HDInsight. USDoDEast |
| &nbsp; | USGov Arizona | HDInsight. USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Több regionális szolgáltatási címke használata

Ha a régióban, ahol a fürt létrejött, nem szerepel az előző táblázatban, engedélyeznie kell több regionális szolgáltatás címkéjét. A különböző régiókban az erőforrás-szolgáltatók megegyezése miatt a többit kell használnia.

A fennmaradó régiók csoportokba vannak osztva, az általuk használt regionális szolgáltatási címkék alapján.

#### <a name="group-1"></a>1. csoport

Ha a fürt az alábbi táblázat egyik régiójában jön létre, engedélyezze a szolgáltatás címkéit `HDInsight.WestUS` és `HDInsight.EastUS` . Továbbá a felsorolt regionális szolgáltatás címkéje. Az ebben a szakaszban található régiók három szolgáltatási címkét igényelnek.

Ha például a fürt a régióban lett létrehozva `East US 2` , akkor a következő szolgáltatási címkéket kell hozzáadnia a hálózati biztonsági csoportjához:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Country | Régió | Szolgáltatáscímke |
| ---- | ---- | ---- |
| Egyesült Államok | USA 2. keleti régiója | HDInsight. EastUS2 |
| &nbsp; | Az USA középső régiója | HDInsight. CentralUS |
| &nbsp; | NorthCentral minket | HDInsight. NorthCentralUS |
| &nbsp; | USA déli középső régiója | HDInsight. SouthCentralUS |
| &nbsp; | USA keleti régiója | HDInsight. EastUS |
| &nbsp; | USA nyugati régiója | HDInsight. WestUS |
| Japán | Kelet-Japán | HDInsight. JapanEast |
| Európa | Észak-Európa | HDInsight. NorthEurope |
| &nbsp; | Nyugat-Európa| HDInsight. WestEurope |
| Ázsia | Kelet-Ázsia | HDInsight. EastAsia |
| &nbsp; | Délkelet-Ázsia | HDInsight. SoutheastAsia |
| Ausztrália | Kelet-Ausztrália | HDInsight. AustraliaEast |

#### <a name="group-2"></a>2. csoport

*Észak-Kína* és *Kelet-Kína* régiójában lévő fürtöknek két szolgáltatási címkét kell engedélyezniük: `HDInsight.ChinaNorth` és `HDInsight.ChinaEast` .

#### <a name="group-3"></a>3. csoport

*US gov Iowa* és *US gov Virginia* régiójában lévő fürtöknek két szolgáltatási címkét kell engedélyezniük: `HDInsight.USGovIowa` és `HDInsight.USGovVirginia` .

#### <a name="group-4"></a>4. csoport

A *németországi Közép* -és *Kelet-Németország* régiójában lévő fürtökön két szolgáltatási címkét kell engedélyezni: `HDInsight.GermanyCentral` és `HDInsight.GermanyNortheast` .

## <a name="next-steps"></a>Következő lépések

- [Hálózati biztonsági csoportok: szolgáltatás címkéi](../virtual-network/network-security-groups-overview.md#security-rules)
- [Virtuális hálózatok létrehozása az Azure HDInsight-fürtökhöz](hdinsight-create-virtual-network.md)