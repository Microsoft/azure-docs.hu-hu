---
title: A virtuális gépek vész-helyreállításának befejezése
description: Ez a cikk bemutatja, hogyan végezheti el a virtuális gépek vész-helyreállítását az Azure VMware-megoldás használatával
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 2ccb6546b9b01255e4a28aed79fd0d3ccbc4516c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580184"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>A virtuális gépek vész-helyreállításának befejezése az Azure VMware-megoldás használatával

Ez a cikk a virtuális gépek (VM-EK) VMware HCX-megoldással való vész-helyreállításának folyamatát, valamint egy Azure VMware-megoldás saját felhőjét használja helyreállítási vagy célhelyként.

A VMware HCX különböző műveleteket biztosít, amelyek a replikációs házirendekben részletes szabályozást és részletességet biztosítanak. Az elérhető műveletek a következők:

- **Fordított** – katasztrófa után. A reverse segít a B helynek a forrás helyének és helyének a megadásában, ahol a védett virtuális gép már él.

- **Szüneteltetés** – szünetelteti a kiválasztott virtuális géphez társított aktuális replikációs házirendet.

- **Folytatás** – szünetelteti a kiválasztott virtuális géphez társított aktuális replikációs házirendet.

- **Eltávolítás** – távolítsa el a kiválasztott virtuális géphez társított aktuális replikációs házirendet.

- **Szinkronizálás most** – a kötött szinkronizálási forrás virtuális gépről a védett virtuális gépre.

Ebben az útmutatóban a következő replikációs forgatókönyvek tartoznak:

- Virtuális gép vagy virtuális gépek egy csoportja elleni védelem.

- Végezzen el egy virtuális gépet vagy egy virtuálisgép-csoportot.

- Virtuális gép vagy virtuális gépek csoportjainak helyreállítása.

- Virtuális gép vagy virtuális gépek egy csoportjának fordított védelme.

## <a name="protect-vms"></a>A virtuális gépek védelme

1. Jelentkezzen be a **vSphere-ügyfélbe** a forrás helyén, és nyissa meg a **HCX beépülő modult**.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="HCX beállítás a vSphere" border="true":::

1. Adja meg a vész- **helyreállítási** területeket, és válassza a **virtuális gépek védelem**lehetőséget.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="HCX beállítás a vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. Válassza ki a forrást és a távoli helyeket. Ebben az esetben a távoli helynek az Azure VMware Solution Private Cloud-nak kell lennie.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="HCX beállítás a vSphere" border="true":::

1. Ha szükséges, válassza ki az **alapértelmezett replikációs** beállításokat:

   - **Tömörítés engedélyezése:** Alacsony átviteli sebességű forgatókönyvek esetén ajánlott.

   - **Quiescence engedélyezése:** Szünetelteti a virtuális gépet annak biztosítására, hogy a konzisztens másolat szinkronizálva legyen a távoli hellyel.

   - **Cél tárterülete:** A védett virtuális gépek távoli adattárát, valamint egy Azure VMware-megoldás saját felhőjét, a vSAN adattárnak kell lennie.

   - **Számítási tároló:** Távoli vSphere-fürt vagy erőforráskészlet.

   - **Célmappa:** Távoli célmappa, amely nem kötelező, és ha nincs kiválasztva mappa, a virtuális gépek közvetlenül a kijelölt fürt alá kerülnek.

   - **RPO:** A forrás virtuális gép és a védett virtuális gép közötti szinkronizálási időköz, amely 5 perc és 24 óra között lehet.

   - **Pillanatkép időköze:** A pillanatképek közötti intervallum.

   - **Pillanatképek száma:** A pillanatképek teljes száma a beállított pillanatkép-intervallumon belül.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="HCX beállítás a vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. Válasszon ki egy vagy több virtuális gépet a listából, és szükség szerint konfigurálja a replikációs beállításokat.

   Alapértelmezés szerint a virtuális gépek öröklik az alapértelmezett replikációs beállításokban konfigurált globális beállítások házirendet. A kiválasztott virtuális gép minden hálózati adaptere esetében konfigurálja a távoli **hálózati port csoportot** , és válassza a **Befejezés** lehetőséget a védelmi folyamat elindításához.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="HCX beállítás a vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. Figyelje meg az egyes kiválasztott virtuális gépek folyamatát ugyanabban a vész-helyreállítási régióban.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="HCX beállítás a vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. A virtuális gép védelme után a **Pillanatképek** lapon tekintheti meg a különböző pillanatképeket.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="HCX beállítás a vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   A sárga háromszög azt jelenti, hogy a pillanatképek és a virtuális nem tesztelt teszt-helyreállítási műveletben.

   Fontos különbségek vannak a kikapcsolt virtuális gépek és az azt bekapcsoló virtuális gépek között. A képen látható a szinkronizált folyamat a virtuális gépeken. Elindítja a szinkronizálási folyamatot egészen addig, amíg be nem fejeződik az első pillanatkép, amely a virtuális gép teljes másolata, majd befejezi a következőket a beállított intervallumban. A kikapcsolt virtuális gépek esetében szinkronizál egy másolatot, majd a virtuális gép inaktívként jelenik meg, és a védelmi művelet befejeződött.  Amikor a virtuális gép be van kapcsolva, elindítja a szinkronizálási folyamatot a távoli helyre.

## <a name="complete-a-test-recover-of-vms"></a>Virtuális gépek teszt-helyreállításának befejezése

1. Jelentkezzen be a **vSphere-ügyfélbe** a távoli helyen, amely az Azure VMware megoldás privát felhője. 
1. A **HCX beépülő modulban**, a vész-helyreállítási területen jelölje ki a függőleges ellipsziseket bármelyik virtuális gépen az operatív menü megjelenítéséhez, majd válassza a **virtuális gép helyreállítása**lehetőséget.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="HCX beállítás a vSphere" border="true":::

1. Válassza ki a teszt és a pillanatkép beállításait, amelyet a virtuális gép különböző állapotának tesztelésére kíván használni.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="HCX beállítás a vSphere" border="true":::

1. A **teszt**kiválasztása után megkezdődik a helyreállítási művelet.

1. Ha elkészült, megtekintheti az új virtuális gépet az Azure VMware-megoldás saját Felhőbeli vCenter.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="HCX beállítás a vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. Miután végzett a teszteléssel a virtuális gépen vagy bármely rajta futó alkalmazáson, végezze el a karbantartást a tesztelési példány törléséhez.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="HCX beállítás a vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>Virtuális gépek helyreállítása

1. Jelentkezzen be a **vSphere-ügyfélre** a távoli helyen, amely az Azure VMware megoldás privát felhője, és hozzáfér a **HCX beépülő modulhoz**.

   A helyreállítási forgatókönyvhöz a példaként használt virtuális gépek egy csoportja.

1. Válassza ki a listából a helyreállítani kívánt virtuális gépet, nyissa meg a **műveletek** menüt, és válassza a **virtuális gépek helyreállítása**lehetőséget.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="HCX beállítás a vSphere" border="true":::

1. Konfigurálja az egyes példányok helyreállítási beállításait, és **válassza a helyreállítás lehetőséget** a helyreállítási művelet elindításához.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="HCX beállítás a vSphere" border="true":::

1. A helyreállítási művelet befejezése után az új virtuális gépek megjelennek a távoli vCenter Server leltárban.

## <a name="complete-a-reverse-replication-on-vms"></a>Visszirányú replikálás végrehajtása a virtuális gépeken

1. Jelentkezzen be a **vSphere-ügyfélre** az Azure VMware-megoldás saját felhőbe, és nyissa meg a **HCX beépülő modult**.
   
   >[!NOTE]
   >A visszirányú replikálás megkezdése előtt győződjön meg arról, hogy a forrás helyén lévő eredeti virtuális gépek ki vannak kapcsolva. A művelet meghiúsul, ha a virtuális gépek nincsenek kikapcsolva.

1. A listából válassza ki a forrás helyre replikálni kívánt virtuális gépeket, nyissa meg a **műveletek** menüt, és válassza a **fordított**lehetőséget. 
1. A replikáció indításához válassza a **fordított** lehetőséget.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="HCX beállítás a vSphere" border="true":::

1. Figyelje az egyes virtuális gépek részletek szakaszát.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="HCX beállítás a vSphere" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>Vész-helyreállítási terv automatizálása

A VMware HCX jelenleg nem rendelkezik beépített mechanizmussal a vész-helyreállítási terv létrehozásához és automatizálásához. A VMware HCX azonban REST API-kat biztosít, beleértve a vész-helyreállítási művelethez szükséges API-kat is. Az API-specifikáció a VMware HCX Managerben érhető el az URL-címben.

Ezek az API-k a vész-helyreállítás következő műveleteit tárgyalják.

- Védelem

- Helyreállítás

- Teszt helyreállítása

- Tervezett helyreállítás

- Reverse

- Lekérdezés

- Teszt tisztítása

- Szünet

- Folytatás

- Védelem eltávolítása

- Újrakonfigurálás

Az alábbi példa egy helyreállítási művelet hasznos adatait mutatja be a JSON-ban.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Ezen API-k használatával az ügyfél létrehozhat egy egyéni mechanizmust a vész-helyreállítási terv létrehozásának és végrehajtásának automatizálására.
