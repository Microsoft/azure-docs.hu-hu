---
title: Új Azure Policy definíció hatásának kiértékelése
description: Ismerkedjen meg az új házirend-definíció Azure-környezetbe való bevezetésének folyamatával.
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 187a64ce3581c4aaa893e2a805ce787a0bfd6c79
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091718"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy-definition"></a>Új Azure Policy definíció hatásának kiértékelése

A Azure Policy hatékony eszköz az Azure-erőforrások üzleti szabványokhoz való felügyeletéhez és a megfelelőségi igények kielégítéséhez. Ha a felhasználók, folyamatok vagy folyamatok erőforrásokat hoznak létre vagy frissítenek, Azure Policy áttekinti a kérést. Ha a házirend-definíció hatása [módosítva](./effects.md#modify), [Hozzáfűzés](./effects.md#deny)vagy [DeployIfNotExists](./effects.md#deployifnotexists), a házirend módosítja a kérést, vagy hozzáadja azt. Ha a házirend-definíció hatással van a [naplózásra](./effects.md#audit) vagy a [AuditIfNotExists](./effects.md#auditifnotexists), a házirend az új és a frissített erőforrásokra vonatkozó műveletnapló-bejegyzést hoz létre. Ha a házirend-definíció hatása [megtagadva](./effects.md#deny), a házirend leállítja a kérelem létrehozását vagy módosítását.

Ezek az eredmények pontosan olyankor szükségesek, amikor tudja, hogy a szabályzat helyesen van definiálva. Fontos azonban, hogy egy új szabályzatot a kívánt módon érvényesítse, mielőtt engedélyezi, hogy megváltoztassa vagy letiltsa a munkát. Az ellenőrzésnek biztosítania kell, hogy csak a kívánt erőforrások legyenek nem megfelelőek, és a rendszer helytelenül tartalmazza a megfelelő erőforrásokat az eredményekben ( _hamis pozitív_ néven).

Az új házirend-definíció érvényesítéséhez ajánlott módszer a következő lépések végrehajtása:

- A szabályzat szigorú meghatározása
- Meglévő erőforrások naplózása
- Új vagy frissített erőforrás-kérelmek naplózása
- A szabályzat üzembe helyezése az erőforrásokon
- Folyamatos monitorozás

## <a name="tightly-define-your-policy"></a>A szabályzat szigorú meghatározása

Fontos tisztában lennie azzal, hogy az üzleti házirend hogyan lett implementálva házirend-definícióként, illetve az Azure-erőforrások más Azure-szolgáltatásokkal való kapcsolatával. Ez a lépés a [követelmények azonosításával](../tutorials/create-custom-policy-definition.md#identify-requirements) és [az erőforrás-tulajdonságok meghatározásával](../tutorials/create-custom-policy-definition.md#determine-resource-properties)valósítható meg.
Azonban fontos, hogy az üzleti szabályzat szűk definícióján túl is látható legyen. A házirend állapota például az "összes Virtual Machines kell lennie..."? Mi a helyzet a virtuális gépeket használó egyéb Azure-szolgáltatásokkal, például a HDInsight vagy az AK-val? A szabályzatok meghatározásakor figyelembe kell venni, hogy ez a szabályzat milyen hatással van a más szolgáltatások által használt erőforrásokra.

Emiatt a házirend-definícióknak szigorúan meghatározottnak kell lenniük, és az erőforrásokra és a megfelelőség kiértékeléséhez szükséges tulajdonságokra kell összpontosítaniuk.

## <a name="audit-existing-resources"></a>Meglévő erőforrások naplózása

Mielőtt új vagy frissített erőforrásokat szeretne felügyelni az új szabályzat-definícióval, érdemes megtekinteni, hogyan értékeli ki a meglévő erőforrások (például egy tesztelési erőforráscsoport) korlátozott részhalmazát. A szabályzat-hozzárendelésben _letiltott_ [kényszerítési mód](./assignment-structure.md#enforcement-mode) (DoNotEnforce) használatával megakadályozhatja [, hogy a rendszer](./effects.md) kiváltsa az aktiválási vagy a tevékenységi naplóbejegyzések létrehozását.

Ez a lépés lehetőséget ad arra, hogy kiértékelje a meglévő erőforrásokra vonatkozó új szabályzat megfelelőségi eredményeit anélkül, hogy ez hatással lenne a munkahelyi folyamatra. Győződjön meg arról, hogy a megfelelő erőforrások nem megfelelőként vannak megjelölve (_hamis pozitív_), és hogy az összes várhatóan meg nem felelő erőforrás helyesen van megjelölve.
Miután az erőforrások kezdeti részhalmaza ellenőrzi a várt módon, lassan bontsa ki a kiértékelést az összes meglévő erőforrásra.

A meglévő erőforrások ily módon történő kiértékelése lehetőséget biztosít a nem megfelelő erőforrások javítására az új szabályzat teljes megvalósítása előtt. Ez a tisztítás manuálisan vagy [szervizelési feladaton](../how-to/remediate-resources.md) végezhető el, ha a házirend-definíciós hatás _DeployIfNotExists_.

## <a name="audit-new-or-updated-resources"></a>Új vagy frissített erőforrások naplózása

Miután ellenőrizte, hogy az új házirend-definíció helyesen van-e bejelentve a meglévő erőforrásokon, ideje, hogy megtekintse a házirend hatását az erőforrások létrehozásakor vagy frissítésekor. Ha a házirend-definíció támogatja a Effect paraméterezés, használja a [naplózást](./effects.md#audit). Ez a konfiguráció lehetővé teszi az erőforrások létrehozásának és frissítésének figyelését annak ellenőrzéséhez, hogy az új házirend-definíció egy olyan erőforráshoz tartozó bejegyzést indít el az Azure-beli tevékenység naplójában, amely nem felel meg a meglévő munka vagy kérések befolyásolása nélkül.

Azt javasoljuk, hogy frissítsen és hozzon létre olyan új erőforrásokat, amelyek megfelelnek a szabályzat definíciójának, hogy a _naplózási_ effektus a várt módon induljon el. Olyan erőforrás-kérelmeket kell kikeresni, amelyeket az új házirend-definíció nem befolyásolhat a _naplózás_ hatásának kiváltása esetén.
Ezek az érintett erőforrások egy másik példa a _téves pozitív értékekre_ , és a házirend-definícióban kell rögzíteni a teljes megvalósítás előtt.

Abban az esetben, ha a szabályzat definíciója módosul a tesztelési fázisban, ajánlott megkezdeni az ellenőrzési folyamatot a meglévő erőforrások naplózásával. Az új vagy frissített erőforrásokra vonatkozó _hamis pozitív_ házirend-definíció módosítása valószínűleg hatással lesz a meglévő erőforrásokra is.

## <a name="deploy-your-policy-to-resources"></a>A szabályzat üzembe helyezése az erőforrásokon

Miután befejezte az új házirend-definíció érvényesítését a meglévő erőforrásokkal és az új vagy frissített erőforrás-kérelmekkel, megkezdi a szabályzat megvalósításának folyamatát. Azt javasoljuk, hogy az új házirend-definícióhoz tartozó szabályzat-hozzárendelést először az összes erőforrás egy részhalmazára, például egy erőforráscsoporthoz hozza létre. A kezdeti telepítés ellenőrzése után kiterjesztheti a házirend hatókörét szélesebb és szélesebb szintekre, például az előfizetésekre és a felügyeleti csoportokra. Ezt a terjeszkedést úgy érheti el, ha eltávolítja a hozzárendelést, és létrehoz egy újat a cél hatókörökön, amíg az új házirend-definícióban szereplő erőforrások teljes köréhez hozzá nem rendeli azokat.

A bevezetés során, ha olyan erőforrások találhatók, amelyek mentesülnek az új szabályzat-definíciótól, a következő módokon kezelheti őket:

- A házirend-definíció frissítése, hogy világosabb legyen a nem kívánt hatás csökkentése érdekében
- A szabályzat-hozzárendelés hatókörének módosítása (új hozzárendelés eltávolításával és létrehozásával)
- Erőforrások csoportjának hozzáadása a kizárási listához a szabályzat-hozzárendeléshez

A hatókör (szint vagy kizárás) módosításait teljes mértékben ellenőrizni kell, és a biztonsági és megfelelőségi szervezetekkel kell kommunikálni annak biztosítása érdekében, hogy ne legyenek lefedettségi különbségek.

## <a name="monitor-your-policy-and-compliance"></a>A szabályzat és a megfelelőség figyelése

A házirend-definíció megvalósítása és kiosztása nem az utolsó lépés. Folyamatosan figyelheti az erőforrások [megfelelőségi](../how-to/get-compliance-data.md) szintjét az új szabályzat-definícióba, és beállíthatja a megfelelő [Azure monitor riasztásokat és értesítéseket](../../../azure-monitor/alerts/alerts-overview.md) a nem megfelelő eszközök azonosításához. Javasoljuk továbbá, hogy a házirend-definíciót és a kapcsolódó hozzárendeléseket ütemezett alapon értékelje ki, hogy ellenőrizze a házirend-definíciót az üzleti szabályzatok és a megfelelőségi igények kielégítése érdekében. Ha már nincs rá szükség, el kell távolítania a házirendeket. A szabályzatoknak időről időre frissíteniük kell a mögöttes Azure-erőforrások alakulását, és hozzá kell adni új tulajdonságokat és képességeket.

## <a name="next-steps"></a>Következő lépések

- A szabályzat- [definíciós struktúra](./definition-structure.md)megismerése.
- A szabályzat- [hozzárendelési struktúra](./assignment-structure.md)megismerése.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).