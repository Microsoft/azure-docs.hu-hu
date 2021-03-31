---
title: Replikációs házirendek beállítása a VMware vész-helyreállításhoz Azure Site Recoverykal | Microsoft Docs
description: Ismerteti, hogyan lehet konfigurálni a VMware vész-helyreállítás replikációs beállításait az Azure-ba Azure Site Recovery használatával.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84699600"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>A VMware-vészhelyreállítás replikációs szabályzatainak konfigurálása és kezelése

Ez a cikk azt ismerteti, hogyan konfigurálhat egy replikációs házirendet, ha VMware virtuális gépeket replikál az Azure-ba a [Azure site Recovery](site-recovery-overview.md)használatával.

## <a name="create-a-policy"></a>Szabályzat létrehozása

1. Válassza a  >  **site Recovery infrastruktúra** kezelése lehetőséget.
2. A **VMware és a fizikai gépek esetében** válassza a **replikációs házirendek** lehetőséget.
3. Kattintson a **+ replikációs házirend** elemre, és adja meg a házirend nevét.
4. Az **RPO küszöbértéke** beállításnál adja meg az RPO-korlátot. A riasztások akkor jönnek létre, ha a folyamatos replikálás meghaladja ezt a korlátot.
5. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú legyen az egyes helyreállítási pontok adatmegőrzési időtartama (a beállítás értéke órákban adható meg). A védelemmel ellátott gépeket az adatmegőrzési időtartamon belüli bármelyik pontra visszaállíthatja. A rendszer a prémium tárolóra replikált gépek esetében 24 órás megőrzést támogat. A standard szintű tárolás akár 72 órát is támogat.
6. Az alkalmazás **-konzisztens Pillanatképek gyakorisága** területen válassza ki azt a legördülő listát, hogy milyen gyakran (órában) szeretné létrehozni az alkalmazás-konzisztens pillanatképeket tartalmazó helyreállítási pontokat. Ha ki szeretné kapcsolni az alkalmazás-konzisztencia-pontok generációját, válassza a legördülő lista "ki" értékét.
7. Kattintson az **OK** gombra. A házirend létrehozása 30–60 másodpercet vesz igénybe.

Replikációs házirend létrehozásakor a rendszer automatikusan létrehoz egy megfelelő feladat-visszavételi replikációs házirendet, a "feladat-visszavétel" utótaggal együtt. A szabályzat létrehozása után szerkesztheti azt > **beállítások szerkesztése** lehetőség kiválasztásával.

## <a name="associate-a-configuration-server"></a>Konfigurációs kiszolgáló hozzárendelése

Társítsa a replikációs házirendet a helyszíni konfigurációs kiszolgálóval.

1. Kattintson a **hozzárendelés** elemre, majd válassza ki a konfigurációs kiszolgálót.

    ![Konfigurációs kiszolgáló társítása](./media/vmware-azure-set-up-replication/associate1.png)
2. Kattintson az **OK** gombra. A konfigurációs kiszolgáló társításának végrehajtása átlagosan 1–2 percet vesz igénybe.

    ![A konfigurációs kiszolgáló társítása](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Szabályzat szerkesztése

A replikációs házirendet a létrehozása után módosíthatja.

- A házirend módosításait a rendszer a szabályzatot használó összes gépre alkalmazza.
- Ha a replikált gépeket eltérő replikációs házirenddel szeretné hozzárendelni, le kell tiltania és újra engedélyeznie kell a megfelelő gépek védelmét.

A szabályzatok a következőképpen szerkeszthetők:
1. Válassza a  >  **site Recovery infrastruktúra**-  >  **replikációs házirendek** kezelése lehetőséget.
2. Válassza ki a módosítani kívánt replikációs házirendet.
3. Kattintson a **beállítások szerkesztése** elemre, és frissítse a RPO küszöbértékét/a helyreállítási pont megőrzési idejét, illetve az alkalmazás-konzisztens Pillanatképek gyakorisága mezőket a szükséges módon.
4. Ha ki szeretné kapcsolni az alkalmazás-konzisztencia-pontok előállítását, válassza ki a "ki" értéket a mező **alkalmazás-konzisztens pillanatkép gyakoriságának** legördülő menüjében.
5. Kattintson a **Mentés** gombra. A szabályzatot 30 – 60 másodperc múlva kell frissíteni.



## <a name="disassociate-or-delete-a-replication-policy"></a>Replikációs házirend hozzárendelésének vagy törlésének megszüntetése

1. Válassza ki a replikációs házirendet.
    a. A szabályzatnak a konfigurációs kiszolgálóról való leválasztásához győződjön meg arról, hogy egyetlen replikált gép sem használja a szabályzatot. Ezután kattintson a Leválasztás elemre.
    b. A szabályzat törléséhez ellenőrizze, hogy nincs-e társítva konfigurációs kiszolgálóval. Ezután kattintson a **Törlés** gombra. A törléshez 30-60 másodperc szükséges.
2. Kattintson az **OK** gombra.
