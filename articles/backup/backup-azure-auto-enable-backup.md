---
title: Biztonsági mentés automatikus engedélyezése a virtuális gép Azure Policyval végzett létrehozásakor
description: A cikk azt ismerteti, hogyan használható a Azure Policy az adott hatókörben létrehozott összes virtuális gép biztonsági mentésének automatikus engedélyezéséhez
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 7e8195d22f54f29b36549b966322623ed0987d72
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896867"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Biztonsági mentés automatikus engedélyezése a virtuális gép Azure Policyval végzett létrehozásakor

A szervezet biztonsági mentési vagy megfelelőségi rendszergazdájának egyik kulcsfontosságú feladata, hogy biztosítsa, hogy az üzleti szempontból kritikus fontosságú gépek biztonsági mentése a megfelelő megőrzéssel történjen.

Napjainkban a Azure Backup számos beépített szabályzatot biztosít ( [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)használatával), így automatikusan biztosíthatja, hogy az Azure-beli virtuális gépek biztonsági mentésre legyenek konfigurálva. A biztonsági mentési csoportok és erőforrások rendszerezésének módjától függően az alábbi szabályzatok bármelyikét használhatja:

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>1. szabályzat – a biztonsági mentés konfigurálása egy adott címkével nem rendelkező virtuális gépeken ugyanazon a helyen, egy meglévő Recovery Services-tárolóban

Ha a szervezete egy központi biztonsági mentési csapattal rendelkezik, amely a biztonsági mentéseket felügyeli az alkalmazások csoportjai között, ezzel a házirenddel konfigurálhatja a biztonsági mentést egy meglévő központi Recovery Services-tárolóra ugyanabban az előfizetésben és helyen, ahol a virtuális gépek szabályozva vannak. Dönthet úgy is, hogy **kizár** egy bizonyos címkét tartalmazó virtuális gépeket a szabályzat hatóköréről.

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>2. szabályzat – [előzetes verzió] a biztonsági mentés konfigurálása egy adott címkével rendelkező virtuális gépeken egy meglévő Recovery Services-tárolóba ugyanazon a helyen
Ez a szabályzat ugyanúgy működik, mint a fenti, 1. szabályzat, az egyetlen különbség, hogy a szabályzattal **olyan virtuális gépeket is tartalmazhat,** amelyek egy adott címkét tartalmaznak a szabályzat hatókörében. 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>3. szabályzat – [előzetes verzió] a biztonsági mentés konfigurálása olyan virtuális gépeken, amelyek nem rendelkeznek adott címkével egy új Recovery Services-tárolóra alapértelmezett házirenddel
Ha az alkalmazásokat dedikált erőforráscsoportok szerint rendezi, és ugyanazon a tárolón szeretné róluk biztonsági mentést készíteni, ez a szabályzat lehetővé teszi a művelet automatikus kezelését. Dönthet úgy is, hogy **kizár** egy bizonyos címkét tartalmazó virtuális gépeket a szabályzat hatóköréről.

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>4. szabályzat – [előzetes verzió] az adott címkével rendelkező virtuális gépek biztonsági mentésének konfigurálása egy új Recovery Services-tárolóra alapértelmezett házirenddel
Ez a szabályzat ugyanúgy működik, mint a fenti 3. szabályzat, az egyetlen különbség, hogy a szabályzattal olyan **virtuális gépeket is** felhasználhat, amelyek egy adott címkét tartalmaznak a szabályzat hatókörében. 

A fentiek mellett a Azure Backup egy [csak naplózási](https://docs.microsoft.com/azure/governance/policy/concepts/effects#audit) házirendet is biztosít, **Azure Backup engedélyezni kell a Virtual Machines**. Ez a házirend azonosítja, hogy mely virtuális gépeken nincs engedélyezve a biztonsági mentés, de nem konfigurálja automatikusan a biztonsági másolatokat ezekhez a virtuális gépekhez. Ez akkor hasznos, ha csak a virtuális gépek általános megfelelőségét szeretné kiértékelni, de nem szeretné azonnal végrehajtani a műveletet.

## <a name="supported-scenarios"></a>Támogatott helyzetek

* A beépített szabályzat jelenleg csak Azure-beli virtuális gépek esetén támogatott. A felhasználóknak ügyelniük kell arra, hogy a hozzárendelés során megadott adatmegőrzési szabályzat a virtuális gép adatmegőrzési szabályzata legyen. Tekintse át [ezt](./backup-azure-policy-supported-skus.md) a dokumentumot a szabályzat által támogatott összes virtuális gép SKU megtekintéséhez.

* Az 1. és a 2. szabályzat egyszerre egyetlen helyhez és előfizetéshez rendelhető hozzá. Ha engedélyezni szeretné a virtuális gépek biztonsági mentését a helyek és az előfizetések között, a házirend-hozzárendelés több példányát is létre kell hoznia, egyet a hely és az előfizetés minden kombinációja esetében.

* Az 1. és a 2. szabályzat esetében a felügyeleti csoport hatóköre jelenleg nem támogatott.

* Az 1. és a 2. szabályzat esetében a megadott tár és a biztonsági mentésre konfigurált virtuális gépek különböző erőforráscsoportok alatt lehetnek.

* Az 1., 2., 3. és 4. szabályzatok jelenleg nem érhetők el az országos felhőkben.

* A 3. és a 4. szabályzat egyszerre egyetlen előfizetéshez (vagy egy előfizetéshez tartozó erőforráscsoporthoz) rendelhető hozzá.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>A beépített szabályzatok használata

Az alábbi lépések ismertetik az 1. házirend hozzárendelésének végpontok közötti folyamatát: a **biztonsági mentés konfigurálása olyan virtuális gépeken, amelyek nem rendelkeznek adott címkével egy meglévő Recovery Services-tárolóban** egy adott hatókörben. Hasonló utasítások vonatkoznak a többi szabályzatra is. A hozzárendelés után a hatókörben létrehozott új virtuális gépek automatikusan biztonsági mentésre vannak konfigurálva.

1. Jelentkezzen be a Azure Portalba, és navigáljon a **szabályzat** irányítópultra.
2. A bal oldali menüben válassza a **definíciók** lehetőséget, hogy lekérje az Azure-erőforrások összes beépített szabályzatának listáját.
3. Szűrje a **Kategória = biztonsági mentés** listáját, és válassza ki a "biztonsági mentés konfigurálása egy helyen lévő virtuális gépeken ugyanazon a helyen" nevű központi tárolóra beállítást.
![Házirend-irányítópult](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Válassza ki a szabályzat nevét. A rendszer átirányítja a szabályzat részletes definícióját.
![Házirend-definíciós ablaktábla](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Kattintson a **hozzárendelés** gombra a panel tetején. A rendszer átirányítja a **házirend-hozzárendelési** panelre.
6. Az **alapok** területen válassza a **hatókör** mező melletti három pontot. Ekkor megnyílik a megfelelő környezeti ablaktábla, ahol kiválaszthatja az előfizetést, amelyre alkalmazni kívánja a szabályzatot. Kiválaszthat egy erőforráscsoportot is, hogy a házirend csak egy adott erőforráscsoport virtuális gépei esetében legyen alkalmazva.
![Szabályzat-hozzárendelés alapjai](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. A **Parameters (paraméterek** ) lapon válasszon ki egy helyet a legördülő menüből, és válassza ki a tároló-és biztonsági mentési szabályzatot, amelyhez társítani kell a hatókörben lévő virtuális gépeket. Dönthet úgy is, hogy megadhatja a címke nevét és a címke értékeinek tömbjét. Az adott címkéhez megadott értékek bármelyikét tartalmazó virtuális gép ki lesz zárva a szabályzat-hozzárendelés hatókörében.
![Szabályzat-hozzárendelési paraméterek](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Győződjön meg arról, hogy a **hatás** a deployIfNotExists értékre van állítva.
9. Keresse meg a **felülvizsgálat + létrehozás** elemet, majd válassza a **Létrehozás** lehetőséget.

> [!NOTE]
>
> A Azure Policy a meglévő virtuális gépeken is felhasználható [szervizeléssel](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Javasoljuk, hogy a szabályzatot egyszerre több mint 200 virtuális géphez lehessen hozzárendelni. Ha a házirend több mint 200 virtuális géphez van hozzárendelve, akkor a biztonsági mentés néhány órával később is elindítható, mint amelyet az ütemterv meghatároz.

## <a name="next-steps"></a>Következő lépések

[További információ a Azure Policy](../governance/policy/overview.md)
