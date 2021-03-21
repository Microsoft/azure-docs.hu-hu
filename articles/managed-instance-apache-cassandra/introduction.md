---
title: Az Azure felügyelt példányának bemutatása az Apache Cassandra-hoz
description: Ismerje meg az Apache Cassandra Azure felügyelt példányát. Ez a szolgáltatás kezeli az Apache Cassandra natív, nyílt forráskódú példányainak üzembe helyezését és méretezését az Azure-ban.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 03/02/2021
ms.openlocfilehash: d99c62e7d88510c351f87d85b4f8c5c271767cb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748675"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra-preview"></a>Mi az az Azure felügyelt példány az Apache Cassandra-hoz? (Előzetes verzió)

> [!IMPORTANT]
> Az Apache Cassandra Azure felügyelt példánya jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Apache Cassandra szolgáltatás Azure felügyelt példánya automatizált üzembe helyezési és skálázási műveleteket biztosít a felügyelt nyílt forráskódú Apache Cassandra-adatközpontok számára. Ez a szolgáltatás segít felgyorsítani a hibrid forgatókönyveket, és csökkenti a folyamatos karbantartást. Az általános kiadásnál az [Azure Cosmos DB Cassandra APIokkal](../cosmos-db/cassandra-introduction.md) szoros integrációs és adatáthelyezési funkciókkal fog rendelkezni.

:::image type="content" source="./media/introduction/icon.gif" alt-text="Az Apache Cassandra Azure felügyelt példánya az Apache Cassandra felügyelt szolgáltatása." border="false":::

## <a name="key-benefits"></a>Főbb előnyök

### <a name="hybrid-deployments"></a>Hibrid üzembe helyezések

Ezzel a szolgáltatással könnyedén helyezheti üzembe az Apache Cassandra-adatközpontok felügyelt példányait, amelyeket a rendszer automatikusan virtuálisgép-méretezési csoportként telepít egy új vagy meglévő Azure-Virtual Networkba. Ezek az adatközpontok az Azure [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) Azure-ban vagy egy másik felhőalapú környezetben is hozzáadhatók a helyileg futó Apache Cassandra-gyűrűhöz.

- **Egyszerűsített üzembe helyezés:** A hibrid kapcsolat létrejötte után a pletykák protokollon keresztül könnyen megtalálhatók a központi telepítés.
- **Üzemeltetett metrikák:** A metrikák a [Prometheus](https://prometheus.io/docs/introduction/overview/) -ben futnak a tevékenységek figyelésére a fürtben.

### <a name="simplified-scaling"></a>Egyszerűsített skálázás

A felügyelt példányban a csomópontok vertikális felskálázása és skálázása egy adatközpontban teljes mértékben felügyelt. Adja meg a szükséges csomópontok számát, a skálázási Orchestrator pedig gondoskodik a műveleteknek a Cassandra Ringen belüli létrehozásáról.

### <a name="managed-and-cost-effective"></a>Felügyelt és költséghatékony

A szolgáltatás felügyeleti műveleteket biztosít a következő gyakori Apache Cassandra-feladatokhoz:

- Fürt kiépítése
- Adatközpont kiépítése
- Adatközpont skálázása
- Adatközpont törlése
- Javítási művelet elindítása egy alapterületen
- Adatközpont konfigurációjának módosítása
- Biztonsági másolatok beállítása

A díjszabási modell rugalmas, igény szerinti, példányon alapuló, és nem rendelkezik licencelési díjjal. Ez a díjszabási modell lehetővé teszi, hogy az adott számítási feladathoz igazodjon. Kiválaszthatja, hogy hány magot, melyik VM SKU-t, milyen memóriát és mekkora lemezterületet igényel.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg az egyik rövid útmutatóval:

* [Felügyelt példány-fürt létrehozása a Azure Portal](create-cluster-portal.md)
* [Felügyelt Apache Spark-fürt üzembe helyezése Azure Databricks](deploy-cluster-databricks.md)
* [Azure felügyelt példány kezelése Apache Cassandra-erőforrásokhoz az Azure CLI használatával](manage-resources-cli.md)
