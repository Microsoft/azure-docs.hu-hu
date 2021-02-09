---
title: A célhely beállításainak módosítása az Azure-beli virtuális gépek régiók közötti áthelyezéséhez az Azure-erőforrás-Mozgatóvel
description: Megtudhatja, hogyan módosíthatja az Azure-beli virtuális gépek régiók közötti áthelyezését az Azure-erőforrás-mozgató használatával.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: eb28e4c8f6b465e2a9b38cc4571bc4a00baf4ef7
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979623"
---
# <a name="modify-target-settings"></a>Célbeállítások módosítása

Ez a cikk azt ismerteti, hogyan lehet módosítani a célhelyeket az Azure-régiók Azure-beli [erőforrás](overview.md)-áthelyezéssel való áthelyezése során.


## <a name="modify-vm-settings"></a>Virtuális gép beállításainak módosítása

Az Azure-beli virtuális gépek és a kapcsolódó erőforrások áthelyezésekor módosíthatja a célhely beállításait. 

- Azt javasoljuk, hogy az áthelyezési gyűjtemény ellenőrzése után csak a cél beállításait módosítsa.
- Azt javasoljuk, hogy az erőforrások előkészítése előtt módosítsa a beállításokat, mert előfordulhat, hogy egyes cél tulajdonságok nem érhetők el szerkesztésre az előkészítés befejezése után.

Azonban
- Ha áthelyezi a forrás-erőforrást, a célként megadott beállításokat általában addig módosíthatja, amíg el nem indítja a kezdeményező áthelyezési folyamatot.
- Ha egy meglévő erőforrást rendel a forrás régióhoz, a cél beállításait addig módosíthatja, amíg be nem fejeződik az áthelyezési véglegesítés.

### <a name="settings-you-can-modify"></a>Módosítható beállítások

A módosítható konfigurációs beállítások összegzése a táblázatban látható.

**Erőforrás** | **Beállítások** 
--- | --- | --- 
**a virtuális gép neve** | Paraméterek:<br/><br/> – Hozzon létre egy új virtuális gépet ugyanazzal a névvel a célként megadott régióban.<br/><br/> – Hozzon létre egy új virtuális gépet egy másik névvel a célként megadott régióban.<br/><br/> – Használjon egy meglévő virtuális gépet a célként megadott régióban.<br/><br/> Ha új virtuális gépet hoz létre, a módosított beállítások kivételével az új célként megadott virtuális gép ugyanazokat a beállításokat rendeli hozzá, mint a forrás.
**Virtuális gép rendelkezésre állási zónája** | A rendelkezésre állási zóna, amelyben a célként megadott virtuális gép el lesz helyezve. Válassza a **nem alkalmazható** lehetőséget, ha nem szeretné módosítani a forrás beállításait, vagy ha nem szeretné a virtuális gépet egy rendelkezésre állási zónában elhelyezni.
**Virtuális gép termékváltozata** | A [virtuális gép típusa](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (a célként megadott régióban elérhető), amelyet a cél virtuális géphez használni fog.<br/><br/> A kiválasztott cél virtuális gép nem lehet kisebb, mint a forrásoldali virtuális gép.
* * Virtuális gép rendelkezésre állási készlete | A rendelkezésre állási csoport, amelyben a célként megadott virtuális gép el lesz helyezve. Válassza a **nem alkalmazható**  lehetőséget, ha nem szeretné módosítani a forrás beállításait, vagy ha nem szeretné a virtuális gépet egy rendelkezésre állási csoportba helyezni.
**VM Key Vault** | A társított kulcstartó, amikor engedélyezi az Azure Disk Encryption szolgáltatást egy virtuális gépen.
**Lemezes titkosítási csoport** | A társított lemez-titkosítási készlet, ha a virtuális gép ügyfél által felügyelt kulcsot használ a kiszolgálóoldali titkosításhoz.
**Erőforráscsoport** | Az az erőforráscsoport, amelyben a célként megadott virtuális gép el lesz helyezve.
**Hálózati erőforrások** | Hálózati adapterek, virtuális hálózatok (virtuális hálózatok/) és hálózati biztonsági csoportok/hálózati adapterek beállításai:<br/><br/> – Hozzon létre egy új erőforrást ugyanazzal a névvel a célként megadott régióban.<br/><br/> – Hozzon létre egy új erőforrást egy másik névvel a célként megadott régióban.<br/><br/> – Egy meglévő hálózati erőforrást használhat a célként megadott régióban.<br/><br/> Ha új cél-erőforrást hoz létre, a módosított beállítások kivételével a forrás erőforrással megegyező beállításokat rendel hozzá.
**Nyilvános IP-cím neve, SKU és zóna** | Megadja a szabványos nyilvános IP-címek nevét, az [SKU](../virtual-network/public-ip-addresses.md#sku)-t és a [zónát](../virtual-network/public-ip-addresses.md#standard) .<br/><br/> Ha azt szeretné, hogy a zóna redundáns legyen, adja meg a **zónát redundánsként**.
* * Terheléselosztó neve, SKU és zóna * * | Megadja a terheléselosztó nevét, SKU-jának (alapszintű vagy standard) és zónáját.<br/><br/> A standard sKU használatát javasoljuk.<br/><br/> Ha azt szeretné, hogy a zóna redundáns legyen, a **zónát redundánsként** kell megadni.
**Erőforrás-függőségek** | Az egyes függőségek beállításai:<br/><br/>– Az erőforrás a forrástól függő erőforrásokat használ, amelyek átkerülnek a célként megadott régióba.<br/><br/> – Az erőforrás a célként megadott régióban található különböző függő erőforrásokat használja. Ebben az esetben a megcélzott régióban található hasonló erőforrások közül választhat.

### <a name="edit-vm-target-settings"></a>Virtuálisgép-cél beállításainak szerkesztése

Ha nem szeretné, hogy a forrás régióból származó erőforrások ne legyenek a célhelyre, néhány további lehetőség közül választhat:

- Hozzon létre egy új erőforrást a célként megadott régióban. Ha nem ad meg eltérő beállításokat, az új erőforrás ugyanazokkal a beállításokkal fog rendelkezni, mint a forrás erőforrás.
- Meglévő erőforrás használata a célként megadott régióban.

A pontos viselkedés az erőforrás típusától függ. [További](modify-target-settings.md) információ a célhelyek beállításainak módosításáról.

Az erőforrás céljának beállításait az erőforrás-áthelyezési gyűjtemény **cél konfigurációs** bejegyzésével módosíthatja. 

Beállítások módosítása: 

1. A **különböző régiók** oldalon > **cél konfigurációja** oszlopban kattintson az erőforrás bejegyzésére mutató hivatkozásra.
2. A **konfigurációs beállítások** területen létrehozhat egy új virtuális gépet a célként megadott régióban.
3. Rendeljen hozzá egy új rendelkezésre állási zónát, rendelkezésre állási készletet vagy SKU-t a célként megadott virtuális géphez. **Rendelkezésre állási zóna** és **SKU**.

Csak a szerkesztett erőforrás módosításait kell elvégezni. Minden függő erőforrást külön kell frissítenie.


## <a name="modify-sql-settings"></a>SQL-beállítások módosítása

Azure SQL Database erőforrások áthelyezésekor módosíthatja az áthelyezés célhelyének beállításait. 

- SQL Database esetén:
    - Azt javasoljuk, hogy az áthelyezés előkészítése előtt módosítsa a cél konfigurációs beállításait.
    - Módosíthatja a céladatbázis beállításait és a zóna redundanciát az adatbázishoz.
- Rugalmas készletek esetén:
    -  A cél konfigurációját bármikor módosíthatja, mielőtt elindítja az áthelyezést.
    - Módosíthatja a cél rugalmas készletet, és a készlethez a zóna redundanciát is. 

### <a name="sql-settings-you-can-modify"></a>Módosítható SQL-beállítások

**Beállítás** | **SQL-adatbázis** | **Rugalmas készlet**
--- | --- | ---
**Név** | Hozzon létre egy új adatbázist ugyanazzal a névvel a célként megadott régióban.<br/><br/> Hozzon létre egy új adatbázist egy másik névvel a célként megadott régióban.<br/><br/> Meglévő adatbázis használata a célként megadott régióban. | Hozzon létre egy új rugalmas készletet ugyanazzal a névvel a célként megadott régióban.<br/><br/> Hozzon létre egy új rugalmas készletet egy másik névvel a megcélzott régióban.<br/><br/> Meglévő rugalmas készlet használata a céltartományban.
**Zónaredundancia** | Ha olyan régióból szeretne áttérni, amely támogatja a zóna-redundanciát, akkor a zóna beállításnál írja be a **Letiltás** parancsot.<br/><br/> Ha olyan régióból szeretne áttérni, amely nem támogatja a zóna-redundanciát, akkor a zóna beállításban az **Engedélyezés** lehetőséget kell beírnia. | Ha olyan régióból szeretne áttérni, amely támogatja a zóna-redundanciát, akkor a zóna beállításnál írja be a **Letiltás** parancsot.<br/><br/> Ha olyan régióból szeretne áttérni, amely nem támogatja a zóna-redundanciát, akkor a zóna beállításban az **Engedélyezés** lehetőséget kell beírnia.

### <a name="edit-sql-target-settings"></a>SQL-cél beállításainak szerkesztése

Egy Azure SQL Database erőforráshoz tartozó cél beállításait a következőképpen módosíthatja: 

1. Az **egyes régiókban** a módosítani kívánt erőforráshoz kattintson a **cél konfigurációs** bejegyzésre.
2. A **konfigurációs beállítások** területen határozza meg a fenti táblázatban összefoglalt célként megadott beállításokat.

## <a name="next-steps"></a>Következő lépések

[Azure-beli virtuális gép áthelyezése](tutorial-move-region-virtual-machines.md) egy másik régióba.