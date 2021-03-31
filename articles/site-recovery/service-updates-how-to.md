---
title: Frissítések és összetevők frissítése Azure Site Recovery
description: Áttekintést nyújt Azure Site Recovery szolgáltatás frissítéseiről és az összetevők frissítéseiről.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: ramamill
ms.openlocfilehash: a1ea8b6fb9800d796670161288be0d86ce6ffc42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89424939"
---
# <a name="service-updates-in-site-recovery"></a>Szolgáltatási frissítések a Site Recovery

Ez a cikk áttekintést nyújt a [Azure site Recovery](site-recovery-overview.md) frissítéseiről, és leírja, hogyan lehet frissíteni site Recovery összetevőket.

Site Recovery rendszeresen közzéteszi a szolgáltatás frissítéseit. A frissítések közé tartoznak az új funkciók, a támogatás fejlesztése, az összetevők frissítései és a hibajavítások. A legújabb funkciók és javítások kihasználása érdekében javasoljuk, hogy a Site Recovery-összetevők legújabb verzióit futtassa. 
 
 
## <a name="updates-support"></a>Frissítések támogatása

### <a name="support-statement-for-azure-site-recovery"></a>A Azure Site Recovery támogatási nyilatkozata

Javasoljuk, hogy mindig frissítsen a legújabb verziójú összetevőkre:

Az **Azure site Recovery összes új, "n" verziójának kiadása után az "n-4" alatti összes verzió nem támogatott**. 

> [!IMPORTANT]
> A hivatalos támogatás az > N-4 verzióról N verzióra való frissítésre. Ha például N-6-t használ, először frissítsen az N-4-re, majd frissítsen N-re.


### <a name="links-to-currently-supported-update-rollups"></a>A jelenleg támogatott kumulatív frissítésekre mutató hivatkozások

 Tekintse át a legújabb kumulatív frissítést (N verzió) [ebben a cikkben](site-recovery-whats-new.md). Ne feledje, hogy Site Recovery támogatást biztosít az N-4 verziókhoz.



## <a name="component-expiry"></a>Összetevő lejárata

Site Recovery értesíti Önt a lejárt összetevőkről (vagy hamarosan lejár) e-mailben (ha előfizetett e-mail-értesítésekre) vagy a portál irányítópultján.

- Továbbá, ha elérhetők a frissítések, a portálon a forgatókönyv infrastruktúra nézetében megjelenik egy **elérhető frissítés** gomb az összetevő mellett. A gomb átirányítja a legújabb összetevő-verzió letöltésére szolgáló hivatkozásra.
-  A tárolók irányítópult-értesítései nem érhetők el, ha a Hyper-V virtuális gépeket replikálja. 

Az e-mail-értesítések küldése a következőképpen történik.

**Idő** | **Gyakoriság**
--- | ---
60 nappal az összetevő lejárata előtt | Hetente egyszer
Következő 53 nap | Hetente egyszer
Elmúlt 7 nap | Naponta egyszer
Lejárat után | Hetente egyszer


### <a name="upgrading-outside-official-support"></a>A hivatalos támogatáson kívüli frissítés

Ha az összetevő-verzió és a legújabb kiadási verzió közötti különbség meghaladja a négyet, akkor ez nem támogatott. Ebben az esetben a frissítés a következőképpen történik: 

1. Frissítse a jelenleg telepített összetevőt a jelenlegi verzióra és négyre. Ha például az 9,16-es verziót, majd a 9,20-re frissíti.
2. Ezután frissítsen a következő kompatibilis verzióra. Tehát a példánkban a 9,16-9,20 frissítése után frissítsen a 9,24-ra. 

Kövesse ugyanezt a folyamatot az összes kapcsolódó összetevőnél.

### <a name="support-for-latest-operating-systemskernels"></a>A legújabb operációs rendszerek/kernelek támogatása

> [!NOTE]
> Ha van ütemezett karbantartási időszak, és a rendszer újraindítást tartalmaz, javasoljuk, hogy először frissítse Site Recovery összetevőket, majd folytassa a karbantartási időszak további ütemezett tevékenységeivel.

1. Az operációs rendszer/kernel verziók frissítése előtt ellenőrizze, hogy a cél verziója támogatott-e Site Recovery. 

    - [Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) -beli virtuális gépek támogatása.
    - [VMware/fizikai kiszolgáló](vmware-physical-azure-support-matrix.md#replicated-machines) támogatása
    - [Hyper-V-](hyper-v-azure-support-matrix.md#replicated-vms) támogatás.
2. Tekintse át az [elérhető frissítéseket](site-recovery-whats-new.md) , hogy megtudja, mit szeretne frissíteni.
3. Frissítsen a legújabb Site Recovery verzióra.
4. Frissítse az operációs rendszert/kernelt a szükséges verzióra.
5. Újraindítás.


Ez a folyamat biztosítja, hogy a gép operációs rendszere/kernele frissítve legyen a legújabb verzióra, és hogy az új verzió támogatásához szükséges legújabb Site Recovery változások betöltődik a gépre.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure-beli virtuális gépek vészhelyreállítása az Azure-ba

Ebben az esetben erősen ajánlott [engedélyezni az automatikus frissítéseket](azure-to-azure-autoupdate.md). A következőképpen engedélyezheti Site Recovery a frissítések kezelését:

- A replikálás engedélyezése folyamatban van.
- A bővítmény frissítési beállításainak a tárolón belüli beállításával.

Ha manuálisan szeretné kezelni a frissítéseket, az alábbi lehetőségek közül választhat:

1. Ha egy új ügynök frissítése elérhetővé válik, Site Recovery a lap tetején lévő értesítéseket biztosít a tárolóban. A tárolóban > **replikált elemek** területen kattintson az értesítésre a képernyő tetején: 
    
    **Az új Site Recovery replikációs ügynök frissítése elérhető. Kattintson ide a következő telepítéséhez: >** <br/><br/>Válassza ki azokat a virtuális gépeket, amelyekre alkalmazni kívánja a frissítést, majd kattintson **az OK** gombra.

2. A virtuális gép vész-helyreállítási áttekintése lapon megtalálja az "ügynök állapota" mezőt, amely a "kritikus frissítés" lehetőséget fogja kimutatni, ha az ügynök lejár. Kattintson rá, és kövesse a következő utasításokat a virtuális gép manuális frissítéséhez.

## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>VMware virtuális gép/fizikai kiszolgáló vész-helyreállítás az Azure-ba

1. A jelenlegi verzió és a [támogatási nyilatkozat](#support-statement-for-azure-site-recovery)alapján telepítse az első frissítést a helyszíni konfigurációs kiszolgálón a [következő utasítások](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)használatával. 
2. Ha kibővített folyamat-kiszolgálókkal rendelkezik, [ezeket az utasításokat követve](vmware-azure-manage-process-server.md#upgrade-a-process-server)frissítse a következőt.
3. Ha frissíteni szeretné a mobilitási ügynököt az egyes védett gépeken, tekintse meg [ezt](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) a cikket.

### <a name="reboot-after-mobility-service-upgrade"></a>Újraindítás a mobilitási szolgáltatás frissítése után

A mobilitási szolgáltatás minden frissítése után újraindítás szükséges, hogy a rendszer a legújabb módosításokat betöltse a forrásoldali gépen.

Az újraindítás nem kötelező, kivéve, ha az ügynök verziószáma az utolsó újraindítás során, a jelenlegi verzió pedig nagyobb, mint négy.

A táblázatban szereplő példa azt mutatja be, hogy ez hogyan működik.

|**Ügynök verziója (utolsó újraindítás)** | **Frissítés erre a kiadásra** | **Kötelező újraindítás?**|
|---------|---------|---------|
|9,16 |  9,18 | Nem kötelező|
|9,16 | 9,19 | Nem kötelező|
| 9,16 | 9,20 | Nem kötelező
 | 9,16 | 9,21 | Kötelező.<br/><br/> Frissítsen 9,20-re, majd indítsa újra a 9,21-es verzióra való frissítés előtt.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Hyper-V rendszerű virtuális gépek vészhelyreállítása az Azure-ba

### <a name="between-a-hyper-v-site-and-azure"></a>Hyper-V-hely és az Azure között

1. Töltse le a Microsoft Azure Site Recovery-szolgáltató frissítését.
2. Telepítse a szolgáltatót minden Site Recovery-ban regisztrált Hyper-V-kiszolgálón. Ha fürtöt futtat, frissítsen az összes fürtcsomóponton.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Egy helyszíni VMM-hely és az Azure között
1. Töltse le a Microsoft Azure Site Recovery-szolgáltató frissítését.
2. Telepítse a szolgáltatót a VMM-kiszolgálóra. Ha a VMM egy fürtben van telepítve, telepítse a szolgáltatót a fürt összes csomópontjára.
3. Telepítse a legújabb Microsoft Azure Recovery Services-ügynököt minden Hyper-V-gazdagépre vagy fürtcsomópontokon.


## <a name="between-two-on-premises-vmm-sites"></a>Két helyszíni VMM-hely között
1. Töltse le a Microsoft Azure Site Recovery Provider legújabb frissítését.
2. Telepítse a legújabb szolgáltatót a másodlagos helyreállítási helyet kezelő VMM-kiszolgálóra. Ha a VMM egy fürtben van telepítve, telepítse a szolgáltatót a fürt összes csomópontjára.
3. A helyreállítási hely frissítése után telepítse a szolgáltatót az elsődleges helyet kezelő VMM-kiszolgálóra.

## <a name="next-steps"></a>Következő lépések

Kövesse az [Azure Updates](https://azure.microsoft.com/updates/?product=site-recovery) oldalt az új frissítések és kiadások nyomon követéséhez.
