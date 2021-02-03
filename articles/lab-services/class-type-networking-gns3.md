---
title: Hálózati labor beállítása Azure Lab Services és GNS3 | Microsoft Docs
description: Megtudhatja, hogyan állíthat be egy labort a Azure Lab Services használatával a GNS3-vel való hálózatkezelés megtanításához.
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500512"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>Tesztkörnyezet beállítása hálózati osztály tanításához 
Ebből a cikkből megtudhatja, hogyan állíthat be egy olyan osztályt, amely arra összpontosít, hogy a tanulók a virtuális és a valós hálózatok [GNS3](https://www.gns3.com/) szoftverrel való emulálása, konfigurálása, tesztelése és hibakeresése révén. 

Ez a cikk két fő szakaszt tartalmaz. Az első szakasz ismerteti, hogyan hozhatja létre az osztályterem labort. A második szakasz ismerteti, hogyan hozhatja létre a sablon gépet, amelyen engedélyezve van a beágyazott virtualizálás, valamint a GNS3 telepítése és konfigurálása.

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja
A tesztkörnyezet beállításához Azure-előfizetésre van szükség a kezdéshez. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/). Az Azure-előfizetés beszerzése után létrehozhat egy új Labor-fiókot Azure Lab Services, vagy használhat meglévő fiókot is. A következő oktatóanyagban talál egy új Labor-fiókot: [oktatóanyag a labor-fiók beállításához](tutorial-setup-lab-account.md).

[Ezt az oktatóanyagot](tutorial-setup-classroom-lab.md) követve hozzon létre egy új labort, majd alkalmazza a következő beállításokat:

| Virtuális gép mérete | Kép |
| -------------------- | ----- | 
| Nagyméretű (beágyazott virtualizálás) | Windows 10 Pro, 1909-es verzió |

## <a name="template-machine"></a>Sablon számítógép 

A sablon létrehozása után indítsa el a gépet, és kapcsolódjon ahhoz, hogy elvégezze a következő három fő feladatot. 
 
1. Készítse elő a sablon számítógépét a beágyazott virtualizálás számára.
2. Telepítse a GNS3.
3. Beágyazott GNS3 virtuális gép létrehozása a Hyper-V-ben.
4. Konfigurálja a GNS3 a Windows Hyper-V virtuális gép használatára.
5. Adja hozzá a megfelelő berendezéseket.
6. Sablon közzététele.


### <a name="prepare-template-machine-for-nested-virtualization"></a>Sablon számítógépének előkészítése beágyazott virtualizálás esetén
- A [cikk](how-to-enable-nested-virtualization-template-vm.md) utasításait követve készítse elő a sablon virtuális gépet a beágyazott virtualizálás számára. 

### <a name="install-gns3"></a>A GNS3 telepítése
- Kövesse a [GNS3 Windows rendszeren való telepítésének](https://docs.gns3.com/docs/getting-started/installation/windows)utasításait.  Győződjön meg arról, hogy a **GNS3 virtuális gép** telepítése az összetevő párbeszédpanelen is megtalálhatók, lásd alább.

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

Végül elérheti a GNS3 virtuális gép kijelölését. Győződjön meg arról, hogy a **Hyper-V** lehetőség van kiválasztva.

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  Ezzel a beállítással letöltheti a PowerShell-parancsfájlt és a VHD-fájlokat a GNS3 virtuális gép létrehozásához a Hyper-V kezelőjében. A telepítés folytatásához használja az alapértelmezett értékeket. **A telepítés befejezése után ne indítsa el a GNS3**.

### <a name="create-gns3-vm"></a>GNS3 virtuális gép létrehozása
A telepítés befejezése után a rendszer a (z) **"GNS3.VM.Hyper-V.2.2.17.zip"** zip-fájlt a telepítési fájllal megegyező mappába tölti le, amely tartalmazza a meghajtókat és a PowerShell-parancsfájlt a Hyper-V virtuális gép létrehozásához.
- Az **összes GNS3.VM.Hyper-V.2.2.17.zip kibontása** .  Ez a művelet Kinyeri a meghajtókat és a PowerShell-szkriptet a virtuális gép létrehozásához.
- **Futtassa a PowerShell** -lel a "create-vm.ps1" PowerShell-szkriptet a fájlra való jobb kattintással.
- Előfordulhat, hogy a végrehajtási házirend módosítására vonatkozó kérelem megjelenik. A szkript végrehajtásához írja be az "Y" értéket.

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- A parancsfájl befejeződése után megerősítheti, hogy a "GNS3 VM" virtuális gép a Hyper-V kezelőjében lett létrehozva.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>A GNS3 konfigurálása Hyper-V rendszerű virtuális gép használatára
Most, hogy a GNS3 telepítve van, és a GNS3 virtuális gép hozzá lett adva, indítsa el a GNS3 a kettő összekapcsolásához.  A [GNS3 telepítővarázslója automatikusan elindul.](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard)  
- Használja a **készülékek futtatása virtuális gépről.** beállítást.  A varázsló többi részén használja az alapértelmezett értékeket, amíg nem találja a **VMware vmrun eszközt.** hibát ad vissza.

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- Kattintson az **OK gombra**, és **szakítsa** meg a varázsló utasításait.
- A Hyper-v virtuális géphez való kapcsolódás befejezéséhez nyissa meg **a**  ->  **Beállítások** szerkesztése  ->  **GNS3 virtuális gépet** , és válassza a **GNS3 virtuális gép engedélyezése** lehetőséget, és válassza a **Hyper-v** beállítást.
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>Megfelelő berendezések hozzáadása

Ezen a ponton hozzá kell adnia a megfelelő [berendezéseket a osztályhoz.](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace)

### <a name="publish-template"></a>Sablon közzététele

Most, hogy a sablon virtuális gépe megfelelően van beállítva, és készen áll a közzétételre, néhány fontos szempontot kell ellenőriznie.
- Győződjön meg arról, hogy a GNS3 virtuális gép le van állítva vagy ki van kapcsolva.  Ha a virtuális gép továbbra is fut, akkor a virtuális gép sérült.
- A GNS3 lezárása, a közzététel és a Futtatás nem kívánt mellékhatásokhoz vezethet.
- Törölje a telepítési fájlokat vagy más szükségtelen fájlokat.

## <a name="cost"></a>Költségek  

Ha a labor költségeit szeretné megbecsülni, a következő példát használhatja: 
 
Egy 25 tanulós osztály esetében, amely 20 órányi ütemezett időpontot és 10 órányi munkafeladatot vagy hozzárendelést használ, a labor ára a következő lesz: 

25 tanuló * (20 + 10) óra * 84 labor egység * 0,01 USD/óra = 630 USD. 

**Fontos:** A költségbecslés csak példaként szolgál.  A díjszabással kapcsolatos aktuális információk: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Összegzés
Ez a cikk végigvezeti a tesztkörnyezet hálózati konfigurációhoz való létrehozásának lépésein a GNS3 használatával.

## <a name="next-steps"></a>Következő lépések
A következő lépések közösek a laborok beállításához:

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail-regisztráció a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users).