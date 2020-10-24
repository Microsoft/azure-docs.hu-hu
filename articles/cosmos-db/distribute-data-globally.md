---
title: Az Adatterjesztés globálisan Azure Cosmos DB
description: Megtudhatja, hogyan hozhatja ki a globálisan elosztott, többmodelles adatbázis-szolgáltatást, amely az Azure Cosmos DB globális adatbázisait használó, a világra kiterjedő, a többrégiós és a feladatátvételi és adathelyreállítást is mutatja.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: ddb6bc297a32be856172707a220fe1dfaefb9f6d
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487449"
---
# <a name="distribute-your-data-globally-with-azure-cosmos-db"></a>Globális adatterjesztés az Azure Cosmos DB-vel

A mai alkalmazásoknak alacsony válaszidővel kell rendelkezniük, és mindig elérhetőnek kell lenniük a hálózaton keresztül. A kis késés és a magas rendelkezésre állás elérése érdekében ezeknek az alkalmazásoknak a példányait a felhasználókhoz közel lévő adatközpontokban kell üzembe helyezni. Ezeket az alkalmazásokat jellemzően több adatközpontba helyezik üzembe, és globálisan elosztottnak nevezzük őket. A globálisan elosztott alkalmazásoknak globálisan elosztott adatbázisra van szükségük, amely transzparens módon tudja replikálni az adatokat a világ bármely pontján, hogy az alkalmazások a felhasználóikhoz közel lévő adatpéldányt használhassák. 

A Azure Cosmos DB egy globálisan elosztott adatbázis-szolgáltatás, amely alacsony késést, rugalmas méretezhetőséget és adatátvitelt, valamint jól definiált szemantikai adatkonzisztenciaet és magas rendelkezésre állást biztosít. Röviden, ha az alkalmazásnak a világ bármely pontján gyors válaszidő szükséges, és ha mindig online állapotba kell esnie, és az átviteli sebesség és a tárterület korlátlan és rugalmas méretezhetőségére van szüksége, az alkalmazást Azure Cosmos DB kell létrehoznia.

Az adatbázisokat úgy állíthatja be, hogy globálisan terjeszthetők legyenek, és elérhetők legyenek bármelyik Azure-régióban. Ha csökkenteni szeretné a késést, helyezze az adatait közel oda, ahová a felhasználók tartoznak. A szükséges régiók kiválasztása az alkalmazás globális elérhetősége és a felhasználók helyétől függ. Cosmos DB transzparens módon replikálja az adatait a Cosmos-fiókjához társított összes régióba. Egyetlen rendszerképet biztosít a globálisan elosztott Azure Cosmos-adatbázisról, valamint az alkalmazás által a helyileg olvasható és írható tárolók közül. 

A Azure Cosmos DB segítségével bármikor hozzáadhat vagy eltávolíthat a fiókjához társított régiókat. Az alkalmazást nem kell szüneteltetni vagy újratelepíteni egy régió hozzáadásához vagy eltávolításához. Továbbra is a szolgáltatás által natív módon biztosított többsoros képességek miatt továbbra is nagyon sok időt vesz igénybe.

:::image type="content" source="./media/distribute-data-globally/deployment-topology.png" alt-text="Magasan elérhető üzembe helyezési topológia" border="false":::

## <a name="key-benefits-of-global-distribution"></a>A globális terjesztés fő előnyei

**Hozzon létre globális aktív-aktív alkalmazásokat.** Az új, többrégiós replikálási protokolljának írásával minden régió támogatja az írásokat és az olvasásokat is. A többrégiós írások funkció a következőket is lehetővé teszi:

- Korlátlan rugalmas írási és olvasási méretezhetőség.
- 99,999%-os olvasási és írási rendelkezésre állás a világ minden tájáról.
- A garantált olvasási és írási műveletek száma kevesebb, mint 10 ezredmásodperc a esetek 99% percentilis esetében.

Az Azure Cosmos DB többsoros API-k használatával az alkalmazás ismeri a legközelebbi régiót, és kéréseket küldhet az adott régiónak. A legközelebbi régió konfiguráció módosítása nélkül azonosítható. Az Azure Cosmos-fiókjához tartozó régiók hozzáadásakor és eltávolításakor az alkalmazásnak nem kell újratelepítenie vagy szüneteltetve lennie, ezért a rendszer mindig nagyon elérhető marad.

**Gyorsan reagáló alkalmazásokat készíthet.** Az alkalmazás közel valós idejű olvasási és írási műveleteket hajt végre az adatbázishoz választott összes régióban. A Azure Cosmos DB belsőleg kezeli az adatreplikációt a kiválasztott szinthez tartozó konzisztencia-szintű garanciával rendelkező régiók között.

**Kiválóan elérhető alkalmazások készítése.** Ha az adatbázist több régióban is futtatja, a világszerte növeli az adatbázisok rendelkezésre állását. Ha az egyik régió nem érhető el, a többi régió automatikusan kezeli az alkalmazás kéréseit. A Azure Cosmos DB 99,999%-os olvasási és írási rendelkezésre állást kínál a többrégiós adatbázisok számára.

**Az üzletmenet folytonosságának fenntartása a regionális kimaradások során.** A Azure Cosmos DB a regionális leállás során támogatja az [automatikus feladatátvételt](how-to-manage-database-account.md#automatic-failover) . Regionális leállás esetén a Azure Cosmos DB továbbra is fenntartja a késést, a rendelkezésre állást, a konzisztenciát és az átviteli sebesség SLA-kat. Annak érdekében, hogy a teljes alkalmazás nagyon elérhető legyen, Cosmos DB manuális feladatátvételi API-t biztosít a regionális kimaradás szimulálása érdekében. Az API használatával rendszeres üzletmenet-folytonossági gyakorlatokat végezhet.

**Az olvasási és írási sebesség méretezése globálisan.** Lehetővé teheti, hogy minden régió írható és rugalmasan méretezhető legyen az olvasás és a világ minden tájáról. Az alkalmazás által az Azure Cosmos-adatbázisban vagy egy tárolóban konfigurált átviteli sebesség az Azure Cosmos-fiókhoz társított összes régióban kiépítve. A kiosztott átviteli sebességet a [pénzügyi támogatással rendelkező SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)-kat garantáljuk.

**Számos jól meghatározott konzisztencia-modell közül választhat.** A Azure Cosmos DB replikációs protokoll öt jól definiált, gyakorlatias és intuitív konzisztencia-modellt kínál. Mindegyik modell kompromisszumot biztosít a konzisztencia és a teljesítmény között. Ezekkel a konzisztencia-modellekkel globálisan elosztott alkalmazásokat hozhat létre könnyedén.

## <a name="next-steps"></a><a id="Next Steps"></a>Következő lépések

A globális terjesztésről a következő cikkekben olvashat bővebben:

* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
* [Többrégiós írások konfigurálása az alkalmazásokban](how-to-multi-master.md)
* [Ügyfelek konfigurálása a többhelyű-hez](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Régiók hozzáadása vagy eltávolítása a Azure Cosmos DB-fiókból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Egyéni ütközés-feloldási szabályzat létrehozása SQL API-fiókokhoz](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programozható konzisztencia-modellek Cosmos DB](consistency-levels.md)
* [Az alkalmazás megfelelő konzisztencia-szintjének kiválasztása](./consistency-levels.md)
* [Konzisztencia szintjei Azure Cosmos DB API-k között](./consistency-levels.md)
* [Rendelkezésre állási és teljesítménybeli kompromisszumok különböző konzisztencia-szintekhez](./consistency-levels.md)