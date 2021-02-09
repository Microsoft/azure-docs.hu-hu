---
title: Az áthelyezési folyamat az Azure-erőforrás-mozgató szolgáltatásban
description: Ismerje meg, hogyan helyezhet át erőforrásokat a régiók között az Azure-erőforrás-mozgató szolgáltatással
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 02/01/2021
ms.author: raynew
ms.openlocfilehash: facbb30201aa6bde2044ca647383cc32ecd9ba26
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980558"
---
# <a name="about-the-move-process"></a>Az áthelyezési folyamat

Az [Azure-erőforrás-mozgató](overview.md) szolgáltatással Azure-erőforrásokat helyezhet át az Azure-régiók között. Ez a cikk az erőforrás-mozgató által használt összetevőket összegzi, és leírja az áthelyezési folyamatot. 


## <a name="components"></a>Összetevők

Ezek az összetevők a régió áthelyezése során használatosak.

**Összetevő** | **Részletek**
--- | ---
**Erőforrás-mozgató** |  Az erőforrás-mozgató koordináták az [Azure erőforrás-szolgáltatókkal](../azure-resource-manager/management/resource-providers-and-types.md) , hogy összehangolják az erőforrások régiók közötti áthelyezését. Az erőforrás-mozgató elemzi az erőforrás-függőségeket, és az áthelyezési folyamat során karbantartja és kezeli az erőforrások állapotát. 
**Gyűjtemény áthelyezése** |  Az áthelyezési gyűjtemény egy [Azure Resource Manager](../azure-resource-manager/management/overview.md) objektum.<br/><br/> Az áthelyezési gyűjtemény a régió mozgatási folyamata során jön létre, az előfizetésben lévő forrás-és célcsoportok minden párosított kombinációja esetén. A gyűjtemény metaadatokat és konfigurációs információkat tartalmaz az áthelyezni kívánt erőforrásokról.<br/><br/>Az áthelyezési gyűjteményhez hozzáadott erőforrásoknak ugyanahhoz az előfizetéshez kell tartoznia, de különböző erőforráscsoportok is lehetnek. 
**Erőforrás áthelyezése** | Ha hozzáad egy erőforrást egy áthelyezési gyűjteményhez, azt az erőforrás-mozgatói az áthelyezési erőforrásként követik nyomon.<br/><br/> Az erőforrás-mozgató az áthelyezési gyűjtemény összes erőforrására vonatkozó információt tárol, és egy-az-egyhez kapcsolatot tart fenn a forrás-és a cél erőforrás között. 
**Függőségek** | Az erőforrás-mozgató ellenőrzi a gyűjteményhez hozzáadott erőforrásokat, és ellenőrzi, hogy az erőforrások rendelkeznek-e olyan függőségekkel, amelyek nem szerepelnek az áthelyezési gyűjteményben.<br/><br/> Az erőforrás függőségeinek azonosítása után hozzáadhatja a függőségeket az áthelyezési gyűjteményhez, és áthelyezheti őket, vagy kiválaszthatja az alternatív meglévő erőforrásokat a célként megadott régióban. Az áthelyezés megkezdése előtt minden függőséget fel kell oldani. 



## <a name="move-region-process"></a>Régió áthelyezése folyamat 

![Az áthelyezési lépéseket bemutató ábra](./media/about-move-process/move-steps.png)

Az egyes áthelyezési erőforrások az összegzett lépésekből állnak.

**Lépés** | **Részletek** | **Állapot/problémák**
--- | --- | --- 
**1. lépés: erőforrások kiválasztása** | Válasszon ki egy erőforrást. A rendszer hozzáadja az erőforrást az áthelyezési gyűjteményhez. | Az erőforrás állapota a *függőben lévő előkészítésre* kerül.<br/><br/> Ha az erőforrás függőségekkel rendelkezik, akkor a *függőség ellenőrzése* azt jelzi, hogy hozzá kell adnia a függő erőforrásokat az áthelyezési gyűjteményhez.
**2. lépés: függőségek ellenőrzése** | Az érvényesítési folyamat elindításához.<br/><br/> Ha az érvényesítés azt mutatja, hogy a függő erőforrások függőben vannak, adja hozzá őket az áthelyezési gyűjteményhez. <br/><br/> Adja hozzá az összes függő erőforrást, még akkor is, ha nem szeretné őket áthelyezni. Később azt is megadhatja, hogy az áthelyezni kívánt erőforrások a célként megadott régióban különböző erőforrásokat használjanak.<br/><br/> A rendszer újra érvényesíti, amíg nincsenek függőben lévő függőségek. | Miután az összes függőséget hozzáadta, és az érvényesítés sikeres volt, az erőforrás-állapot a *függőben lévő előkészítéssel*, problémák nélkül áthelyeződik.
**3. lépés: felkészülés** | Az előkészítési folyamat elindításához. Az előkészítési lépések az áthelyezett erőforrásoktól függenek:<br/><br/> - **Állapot nélküli erőforrások**: az állapot nélküli erőforrások csak konfigurációs adatokkal rendelkeznek. Ezeknek az erőforrásoknak nincs szükségük folyamatos replikációra az adatáthelyezés érdekében. Ilyenek például az Azure Virtual Network (virtuális hálózatok), a hálózati adapterek, a terheléselosztó és a hálózati biztonsági csoportok. Az ilyen típusú erőforrások esetében az előkészítési folyamat egy Azure Resource Manager sablont hoz létre.<br/><br/> - **Állapot-nyilvántartó erőforrások**: az állapot-nyilvántartó erőforrásokhoz konfigurációs adatok és áthelyezhető adatok is tartoznak. Ilyenek például az Azure-beli virtuális gépek és az Azure SQL Database-adatbázisok. Az előkészítési folyamat különbözik az egyes erőforrásokhoz. Ilyen lehet például a forrásoldali erőforrás replikálása a célként megadott régióba. | A Kicking off erőforrás-állapotot helyez el az *előkészítés folyamatban*.<br/><br/> Az előkészítés befejeződése után az erőforrás állapota a *függőben lévő áthelyezés kezdeményezésére* kerül, és nincs probléma.<br/><br/> *Nem sikerült végrehajtani* az állapotot, hogy a felkészülés sikertelen legyen.
**4. lépés: áthelyezés kezdeményezése** | Az áthelyezési folyamat elindításához. Az áthelyezési módszer az erőforrás típusától függ:<br/><br/> - **Állapot nélküli**: az állapot nélküli erőforrások esetében az áthelyezési folyamat általában egy importált sablont helyez üzembe a célként megadott régióban. A sablon a forrás erőforrás-beállításokon alapul, és a megcélzott beállításokon végzett manuális módosításokat is megteheti.<br/><br/> - **Állapot**-nyilvántartó: állapot-nyilvántartó erőforrások esetében az áthelyezési folyamat magában foglalhatja az erőforrás létrehozását, illetve a másolás engedélyezését a céltartományban.<br/><br/>  Csak állapot-nyilvántartó erőforrások esetében az áthelyezés kezdeményezése a forrás erőforrásainak leállását eredményezheti. Például a virtuális gépek és az SQL. | Az áthelyezési *folyamat elindításával elindíthatja* az állapotot.<br/><br/> A sikeres kezdeményező áthelyezés az erőforrás-állapotot áthelyezi a *függőben lévő áthelyezésre*, és nincs probléma. <br/><br/> Egy sikertelen áthelyezési folyamat *nem tudta végrehajtani* az állapotot az áthelyezés indításához.
**5. lépés 1. lehetőség: áthelyezés elvetése** | A kezdeti áthelyezés után eldöntheti, hogy a teljes áthelyezéssel kíván-e továbblépni. Ha nem, akkor elvetheti az áthelyezést, és az erőforrás-mozgató törli a célhelyen létrehozott erőforrásokat. Az állapot-nyilvántartó erőforrások replikálási folyamata az elvetési folyamat után is folytatódik. Ez a beállítás teszteléshez használható. | Az erőforrások figyelmen kívül hagyása az állapot *elvetésével* történik.<br/><br/> Az áthelyezés sikeres elvetése a *függőben állapotba helyezés elindításához*, problémák nélkül.<br/><br/> A meghiúsult elvetési lépések állapota *nem sikerült az áthelyezés elvetéséhez*. 
**5. lépés 2. lehetőség: áthelyezés elkövetése** | Ha a kezdeti lépés után a teljes áthelyezést szeretné használni, ellenőrizze, hogy az erőforrások megtalálhatók-e a cél régióban, és ha elkészült, véglegesítse az áthelyezést.<br/><br/> Csak állapot-nyilvántartó erőforrások esetén a véglegesítés olyan forrás-erőforrásokat eredményezhet, mint például a virtuális gépek vagy az SQL elérhetetlenné válása. | Ha véglegesíti az áthelyezést, az erőforrás állapota a * végrehajtás folyamatban * * állapotba kerül.<br/><br/> A sikeres végrehajtást követően az erőforrás állapota a *véglegesített áthelyezés befejezése* nélkül jelenik meg, és nincs probléma.<br/><br/> Nem sikerült végrehajtani a sikertelen végrehajtás állapotának *áthelyezését*.
**6. lépés: forrás törlése** | Miután elvégezte az áthelyezést, és ellenőrizte az erőforrásokat a céltartományban, törölheti a forrás erőforrást. | A véglegesítést követően egy erőforrás állapota *függőben van a törlési forrásra*. Ezután kiválaszthatja a forrás erőforrást, és törölheti is.<br/><br/> – Csak a *törlési forrás függőben* lévő állapotú erőforrások törölhetők. | Egy erőforráscsoport vagy SQL Server törlése az erőforrás-mozgató portálon nem támogatott. Ezeket az erőforrásokat csak az erőforrás-tulajdonságok lapról lehet törölni.


## <a name="move-region-states"></a>Régió állapotának áthelyezése

Az áthelyezési folyamat számos állapottal rendelkezik, és az egyes állapotokban felmerülő problémákra is felmerülhet. Ezeket a folyamatábra összegzi.

![A lehetséges állapotok és problémák folyamatábrája](./media/about-move-process/process.png)


### <a name="dependency-analysis"></a>Függőségek elemzése

Az áthelyezési folyamat során előfordulhat, hogy a rendszer a következő függőségek érvényesítésére kéri:
- Egy erőforrás olyan függő erőforrásokat használ, amelyek nem szerepelnek az áthelyezési gyűjteményben.
- Az áthelyezési gyűjteményben lévő függő erőforráshoz saját függőségei tartoznak, amelyek nem szerepelnek az áthelyezési gyűjteményben.
- Módosította az erőforrás céljának beállításait, és újra kell érvényesíteni a függőségeket.


### <a name="remove-resources"></a>Erőforrások eltávolítása

Ha nem szeretne áthelyezni egy erőforrást, távolítsa el az áthelyezési gyűjteményből. Általában az erőforrás törlődik a gyűjteményből, valamint a kapcsolódó műveletekkel vagy objektumokkal, például a replikálással vagy a tárolt sablonokkal együtt. Pontosan mi történik, ha eltávolít egy erőforrást az erőforrás típusától és az erőforrás állapotáról a törléskor. [További információ](remove-move-resources.md).

## <a name="move-impact"></a>Hatás áthelyezése

A táblázat összefoglalja, hogy mi befolyásolja a régiók közötti váltást.

**Működés** | **Régiók között**
--- | --- | --- 
**Adatok** | Az erőforrás-adatok és a metaadatok át lesznek helyezve.<br/><br/> A metaadatok ideiglenes tárolása az erőforrás-függőségek és műveletek állapotának nyomon követése érdekében történik.
**Erőforrás** | A forrás erőforrásai érintetlenek maradnak, hogy az alkalmazások továbbra is működőképesek legyenek, és az áthelyezés után is eltávolíthatók.<br/><br/> Egy erőforrás jön létre a célként megadott régióban.
**Folyamat áthelyezése** | Manuális beavatkozást és figyelést igénylő többlépéses folyamat.
**Tesztelés** | Az áthelyezés tesztelése fontos, mivel az alkalmazások az áthelyezést követően továbbra is a várt módon működnek a megcélzott régióban.
**Állásidő** |  Nem várt adatvesztés, de némi állásidő az erőforrások áthelyezéséhez.



## <a name="next-steps"></a>Következő lépések

[Áthelyezés](tutorial-move-region-virtual-machines.md) Azure-beli virtuális gépek egy másik régióba.
[Áthelyezés](tutorial-move-region-sql.md) Azure SQL-erőforrások egy másik régióba.