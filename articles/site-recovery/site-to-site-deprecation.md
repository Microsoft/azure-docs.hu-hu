---
title: Az ügyfél által felügyelt webhelyek (VMM) közötti vész-helyreállítás elavult a Azure Site Recovery használatával | Microsoft Docs
description: A DR a Hyper-V-t használó és a SCVMM által az Azure-ba és az alternatív lehetőségekkel felügyelt helyek közötti, az ügyfél által birtokolt webhelyek közötti közelgő elavult elavulás
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: sharrai
ms.openlocfilehash: 9ffe7a3158b1de6828350947dcf81ef41d08708d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87421841"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Az ügyfél által felügyelt webhelyek (VMM) közötti vész-helyreállítás elavult Azure Site Recovery használatával

Ez a cikk a közelgő elavult tervet, a kapcsolódó következményeket és az ügyfelek számára elérhető alternatív lehetőségeket ismerteti a következő esetekben:

DR System Center Virtual Machine Manager (SCVMM) által kezelt, az ügyfél által birtokolt helyek között Site Recovery

> [!IMPORTANT]
> Javasoljuk, hogy a legkorábbi módon végezze el a Szervizelési lépéseket, hogy elkerülje a környezet megszakadását. 

## <a name="what-changes-should-you-expect"></a>Milyen módosításokat kell elvárnia?

- A 2020 márciusa után Azure Portal értesítéseket kap, & e-mailes kommunikációt a Hyper-V virtuális gépek helyek közötti replikálásának közelgő elavulása alapján. Az elavultság tervezett értéke 2023 március.

- Ha rendelkezik meglévő konfigurációval, a beállítás nem lesz hatással.

- Ha a forgatókönyvek elavultak, kivéve, ha az ügyfél nem követi az alternatív módszereket, a meglévő replikálások megszakadnak. Az ügyfelek nem tekinthetik meg, kezelhetik és nem hajthatják végre a DR-vel kapcsolatos műveleteket a Azure Portal Azure Site Recovery felületén keresztül.
 
## <a name="alternatives"></a>Alternatív megoldások 

Az alábbiakban azokat az alternatívákat láthatja, amelyekkel az ügyfél kiválaszthatja, hogy a DR-stratégiájuk ne legyen hatással a forgatókönyv elavulása után. 

- 1. lehetőség (ajánlott): dönthet úgy, hogy [Az Azure-t használja Dr célként](hyper-v-vmm-azure-tutorial.md).


- 2. lehetőség: válassza ki a helyek közötti replikálást a mögöttes [Hyper-V replika megoldás](/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)használatával, de nem tudja kezelni a Dr-konfigurációkat a Azure Portal Azure site Recovery használatával. 


## <a name="remediation-steps"></a>Megoldási lépések

Ha az 1. lehetőséget választja, hajtsa végre a következő lépéseket:

1. [Tiltsa le a VMMs társított összes virtuális gép védelmét](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). A replikáció **letiltása és eltávolítása** lehetőséggel vagy a megemlített szkriptek futtatásával gondoskodhat arról, hogy a helyszíni replikációs beállítások törlődnek. 

2. [Szüntesse meg az összes VMM-kiszolgáló regisztrációját](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) a helyek közötti replikációs konfigurációból.

3. [Készítse elő az Azure-erőforrásokat](tutorial-prepare-azure-for-hyperv.md) a virtuális gépek replikálásának engedélyezéséhez.
4. [Helyszíni Hyper-V-kiszolgálók előkészítése](hyper-v-prepare-on-premises-tutorial.md)
5. [A VMM-felhőben található virtuális gépek replikálásának beállítása](hyper-v-vmm-azure-tutorial.md)
6. Nem kötelező, de ajánlott: [Dr-részletezés futtatása](tutorial-dr-drill-azure.md)

Ha a Hyper-V replika használatának 2. lehetőségét választja, hajtsa végre a következő lépéseket:

1. A **védett elemek**  >  **replikált elemek** területen kattintson a jobb gombbal a gépre > a **replikáció letiltása** lehetőségre.
2. A **replikáció letiltása** lapon válassza az **Eltávolítás** lehetőséget.

    Ezzel eltávolítja a replikált elemet a Azure Site Recoveryról (a számlázás leállt). A helyszíni virtuális gépen a replikálási konfiguráció **nem** lesz törölve. 

## <a name="next-steps"></a>Következő lépések
Tervezze meg az elavult alkalmazást, és válasszon egy alternatív lehetőséget, amely az infrastruktúra és a vállalat számára legmegfelelőbb. Ha bármilyen kérdése van, forduljon Microsoft ügyfélszolgálata

