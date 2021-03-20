---
title: Célhely beállításainak módosítása az Azure-beli virtuális gépek régiók közötti áthelyezéséhez az Azure-erőforrás-mozgató
description: Megtudhatja, hogyan módosíthatja a célhely beállításait az Azure-beli virtuális gépek régiók közötti áthelyezéséhez az Azure-erőforrás-mozgató használatával.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 936a667948c888f3ca7c53eaa5be9cc97facf5f7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375357"
---
# <a name="modify-destination-settings"></a>Célhelybeállítások módosítása

Ez a cikk azt ismerteti, hogyan lehet módosítani a célhelyeket az Azure-régiók Azure-beli [erőforrás-mozgató](overview.md)használatával történő áthelyezése során.


## <a name="modify-vm-settings"></a>Virtuális gép beállításainak módosítása

Az Azure-beli virtuális gépek és a kapcsolódó erőforrások áthelyezésekor módosíthatja a célhely beállításait. 

- Azt javasoljuk, hogy az áthelyezési gyűjtemény ellenőrzése után csak a célhely beállításait módosítsa.
- Javasoljuk, hogy az erőforrások előkészítése előtt módosítsa a beállításokat, mert előfordulhat, hogy egyes célhelyek tulajdonságai nem érhetők el szerkesztésre az előkészítés befejezése után.

Azonban
- Ha áthelyezi a forrás-erőforrást, a célhelyek beállítását általában addig módosíthatja, amíg el nem indítja az áthelyezési folyamat kezdeményezését.
- Ha egy meglévő erőforrást rendel a forrás régióhoz, a célhely beállításait módosíthatja, amíg be nem fejeződik az áthelyezési véglegesítés.

### <a name="settings-you-can-modify"></a>Módosítható beállítások

A módosítható konfigurációs beállítások összegzése a táblázatban látható.

**Erőforrás** | **Beállítások** 
--- | --- | --- 
**a virtuális gép neve** | Paraméterek:<br/><br/> – Hozzon létre egy új virtuális gépet ugyanazzal a névvel a célként megadott régióban.<br/><br/> – Hozzon létre egy új virtuális gépet egy másik névvel a célhely régióban.<br/><br/> – Használjon egy meglévő virtuális gépet a célhely régióban.<br/><br/> Ha új virtuális gépet hoz létre, a módosított beállítások kivételével az új célként megadott virtuális gép ugyanazokat a beállításokat rendeli hozzá, mint a forrás.
**Virtuális gép rendelkezésre állási zónája** | A rendelkezésre állási zóna, amelyben a célként megadott virtuális gép el lesz helyezve. Válassza a **nem alkalmazható** lehetőséget, ha nem szeretné módosítani a forrás beállításait, vagy ha nem szeretné a virtuális gépet egy rendelkezésre állási zónában elhelyezni.
**Virtuális gép termékváltozata** | A cél virtuális géphez használni kívánt virtuálisgép- [típus](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (a rendeltetési régióban elérhető).<br/><br/> A kiválasztott cél virtuális gép nem lehet kisebb, mint a forrásoldali virtuális gép.
* * Virtuális gép rendelkezésre állási készlete | A rendelkezésre állási csoport, amelyben a célként megadott virtuális gép el lesz helyezve. Válassza a **nem alkalmazható**  lehetőséget, ha nem szeretné módosítani a forrás beállításait, vagy ha nem szeretné a virtuális gépet egy rendelkezésre állási csoportba helyezni.
**VM Key Vault** | A társított kulcstartó, amikor engedélyezi az Azure Disk Encryption szolgáltatást egy virtuális gépen.
**Lemezes titkosítási csoport** | A társított lemez-titkosítási készlet, ha a virtuális gép ügyfél által felügyelt kulcsot használ a kiszolgálóoldali titkosításhoz.
**Erőforráscsoport** | Az az erőforráscsoport, amelyben a célként megadott virtuális gép el lesz helyezve.
**Hálózati erőforrások** | Hálózati adapterek, virtuális hálózatok (virtuális hálózatok/) és hálózati biztonsági csoportok/hálózati adapterek beállításai:<br/><br/> – Hozzon létre egy új erőforrást ugyanazzal a névvel a célként megadott régióban.<br/><br/> – Hozzon létre egy új erőforrást egy másik névvel a célhely régióban.<br/><br/> – Meglévő hálózati erőforrás használata a célhelyen.<br/><br/> Ha új cél-erőforrást hoz létre, a módosított beállítások kivételével a forrás erőforrással megegyező beállításokat rendel hozzá.
**Nyilvános IP-cím neve, SKU és zóna** | Megadja a szabványos nyilvános IP-címek nevét, az [SKU](../virtual-network/public-ip-addresses.md#sku)-t és a [zónát](../virtual-network/public-ip-addresses.md#standard) .<br/><br/> Ha azt szeretné, hogy a zóna redundáns legyen, adja meg a **zónát redundánsként**.
* * Terheléselosztó neve, SKU és zóna * * | Megadja a terheléselosztó nevét, SKU-jának (alapszintű vagy standard) és zónáját.<br/><br/> A standard sKU használatát javasoljuk.<br/><br/> Ha azt szeretné, hogy a zóna redundáns legyen, a **zónát redundánsként** kell megadni.
**Erőforrás-függőségek** | Az egyes függőségek beállításai:<br/><br/>– Az erőforrás a forrástól függő erőforrásokat használ, amelyeket a rendszer a célhelyre helyez át.<br/><br/> – Az erőforrás különböző, a célhelyen található függő erőforrásokat használ. Ebben az esetben a rendeltetési régióban található hasonló erőforrások közül választhat.

### <a name="edit-vm-destination-settings"></a>Virtuálisgép-célhely beállításainak szerkesztése

Ha nem szeretné, hogy az erőforrások a forrás régióból a célhelyre ne legyenek, néhány további lehetőség közül választhat:

- Hozzon létre egy új erőforrást a célként megadott régióban. Ha nem ad meg eltérő beállításokat, az új erőforrás ugyanazokkal a beállításokkal fog rendelkezni, mint a forrás erőforrás.
- Meglévő erőforrás használata a célként megadott régióban.

A pontos viselkedés az erőforrás típusától függ. [További](modify-target-settings.md) információ a célhelyek beállításainak módosításáról.

Egy erőforrás célhelyének beállításait az erőforrás-áthelyezési gyűjtemény **cél konfigurációs** bejegyzésével módosíthatja. 

Beállítások módosítása: 

1. A **különböző régiók** oldalon > **cél konfigurációja** oszlopban kattintson az erőforrás bejegyzésére mutató hivatkozásra.
2. A **konfigurációs beállítások** lehetőségnél létrehozhat egy új virtuális gépet a célhely régióban.
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
**Név** | Hozzon létre egy új adatbázist ugyanazzal a névvel a célként megadott régióban.<br/><br/> Hozzon létre egy új adatbázist egy másik névvel a célhely régióban.<br/><br/> Meglévő adatbázis használata a célként megadott régióban. | Hozzon létre egy új rugalmas készletet ugyanazzal a névvel a célként megadott régióban.<br/><br/> Hozzon létre egy új rugalmas készletet egy másik névvel a célhely régióban.<br/><br/> Meglévő rugalmas készlet használata a célhelyen.
**Zónaredundancia** | Ha olyan régióból szeretne áttérni, amely támogatja a zóna-redundanciát, akkor a zóna beállításnál írja be a **Letiltás** parancsot.<br/><br/> Ha olyan régióból szeretne áttérni, amely nem támogatja a zóna-redundanciát, akkor a zóna beállításban az **Engedélyezés** lehetőséget kell beírnia. | Ha olyan régióból szeretne áttérni, amely támogatja a zóna-redundanciát, akkor a zóna beállításnál írja be a **Letiltás** parancsot.<br/><br/> Ha olyan régióból szeretne áttérni, amely nem támogatja a zóna-redundanciát, akkor a zóna beállításban az **Engedélyezés** lehetőséget kell beírnia.

### <a name="edit-sql-destination-settings"></a>SQL-célhely beállításainak szerkesztése

A Azure SQL Database erőforrás célhelyének beállításait a következőképpen módosíthatja: 

1. Az **egyes régiókban** a módosítani kívánt erőforráshoz kattintson a **cél konfigurációs** bejegyzésre.
2. A **konfigurációs beállítások** területen határozza meg a fenti táblázatban összefoglalt beállításokat.

## <a name="next-steps"></a>Következő lépések

[Azure-beli virtuális gép áthelyezése](tutorial-move-region-virtual-machines.md) egy másik régióba.
