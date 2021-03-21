---
title: Azure VMware Solution by CloudSimple – virtuális gép létrehozása az Azure-ban virtuálisgép-sablonokkal
description: Ismerteti, hogyan hozhat létre virtuális gépeket az Azure-ban virtuálisgép-sablonokkal a saját CloudSimple-felhőhöz készült VMware-infrastruktúrában
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b01afe60a78a746eb0dc5f03cc7b45989f8cf81e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898759"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Virtuális gép létrehozása az Azure-ban virtuálisgép-sablonok használatával a VMware-infrastruktúrában

Létrehozhat egy virtuális gépet a Azure Portal a VMware-infrastruktúrában található virtuálisgép-sablonok használatával, amelyekkel a CloudSimple rendszergazdája engedélyezte az előfizetését.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>CloudSimple virtuális gép létrehozása

1. Válassza az **Összes szolgáltatás** elemet.

2. Keresse meg a **CloudSimple Virtual Machines**.

3. Kattintson a **Hozzáadás** parancsra.

    ![CloudSimple virtuális gép létrehozása](media/create-cloudsimple-virtual-machine.png)

4. Adja meg az alapszintű adatokat kattintson a **Tovább gombra: méret**.

    > [!NOTE]
    > A CloudSimple virtuális gépek Azure-beli létrehozásához virtuálisgép-sablon szükséges.  A virtuálisgép-sablonnak léteznie kell a saját Felhőbeli vCenter.  Hozzon létre egy virtuális gépet a saját felhőben a vCenter felhasználói felületén a kívánt operációs rendszerrel és konfigurációkkal.  Hozzon létre egy sablont a [virtuális gép klónozása a vSphere webes ügyfélben lévő sablonban](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)található utasítások használatával.

    ![CloudSimple virtuális gép létrehozása – alapismeretek](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Mező | Leírás |
    | ------------ | ------------- |
    | Előfizetés | A saját felhőhöz társított Azure-előfizetés.  |
    | Erőforráscsoport | Az erőforráscsoport, amelyhez a virtuális gép hozzá lesz rendelve. Választhat egy meglévő csoportot, vagy létrehozhat egy újat. |
    | Name | A virtuális gép azonosítására szolgáló név.  |
    | Hely | Az az Azure-régió, amelyben a virtuális gép üzemeltetve van.  |
    | Magánfelhő | CloudSimple, ahol létre szeretné hozni a virtuális gépet. |
    | Erőforráskészlet | A virtuális géphez hozzárendelt erőforráskészlet. Válasszon a rendelkezésre álló erőforráskészlet közül. |
    | vSphere-sablon | a virtuális gép vSphere-sablonja.  |
    | Felhasználónév | A virtuális gép rendszergazdájának felhasználóneve (Windows-sablonok esetén)|
    | Jelszó <br>Jelszó megerősítése | A virtuális gép rendszergazdájának jelszava (Windows-sablonok esetén).  |

5. Válassza ki a virtuális gép számára a magok és a memória kapacitásának számát, majd kattintson a Next (tovább) gombra **: konfigurációk**. Jelölje be a jelölőnégyzetet, ha azt szeretné, hogy a teljes CPU-virtualizálás elérhető legyen a vendég operációs rendszer számára, hogy a hardveres virtualizációt igénylő alkalmazások bináris fordítás vagy paravirtualizációs nélkül is futtathatók legyenek a virtuális gépeken. További információkért lásd a [VMware hardveres virtualizációs szolgáltatás elérhetővé](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)tételével foglalkozó cikket.

    ![CloudSimple virtuális gép létrehozása – méret](media/create-cloudsimple-virtual-machine-size.png)

6. Konfigurálja a hálózati adaptereket és a lemezeket az alábbi táblázatokban leírtak szerint, és kattintson a **felülvizsgálat + létrehozás** gombra.

    ![CloudSimple virtuális gép létrehozása – konfigurációk](media/create-cloudsimple-virtual-machine-configurations.png)

    Hálózati adapterek esetében kattintson a **hálózati adapter hozzáadása** lehetőségre, és konfigurálja a következő beállításokat.

    | Szabályozás | Leírás |
    | ------------ | ------------- |
    | Név | Adja meg a felületet azonosító nevet.  |
    | Network (Hálózat) | Válassza ki a saját Felhőbeli vSphere a konfigurált elosztott portok listájából.  |
    | Adapter | Válasszon ki egy vSphere-adaptert a virtuális géphez konfigurált elérhető típusok listájából. További információkért lásd a VMware tudásbázist a [virtuális gép hálózati adapterének kiválasztásával](https://kb.vmware.com/s/article/1001805)foglalkozó cikkben. |
    | Bekapcsolás rendszerindításkor | Adja meg, hogy engedélyezi-e a hálózati adapter hardverét a virtuális gép indításakor. Az alapértelmezett érték az **Engedélyezés**. |

    Lemezek esetében kattintson a **lemez hozzáadása** elemre, és konfigurálja a következő beállításokat.

    | Elem | Leírás |
    | ------------ | ------------- |
    | Név | Adjon meg egy nevet a lemez azonosításához.  |
    | Méret | Válasszon ki egy rendelkezésre álló méretet.  |
    | SCSI-vezérlő | Válasszon ki egy SCSI-vezérlőt a lemezhez.  |
    | Mód | Meghatározza, hogy a lemez hogyan vegyen részt a pillanatképekben. Válasszon egyet a következő lehetőségek közül: <br> – Független állandó: a lemezre írt összes adattal véglegesen írásra kerül.<br> – Független nem állandó: a lemezre írt módosítások a virtuális gép kikapcsolásakor vagy alaphelyzetbe állításakor törlődnek.  A független, nem állandó mód lehetővé teszi, hogy a virtuális gépet mindig ugyanabban az állapotban indítsa újra. További információt a [VMware dokumentációjában](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)talál.

7. Az érvényesítés befejeződése után tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra. A módosítások elvégzéséhez kattintson a felül található lapfülekre, vagy kattintson a gombra.

    ![CloudSimple virtuális gép létrehozása – áttekintés](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>CloudSimple virtuális gépek listájának megtekintése

1. Válassza az **Összes szolgáltatás** elemet.

2. Keresse meg a **CloudSimple Virtual Machines**.

3. Válassza ki, hogy a saját felhője hogyan lett létrehozva.

    ![A CloudSimple listája Virtual Machines](media/list-cloudsimple-virtual-machines.png)

A CloudSimple virtuális gépek listája a Azure Portal-ból létrehozott virtuális gépeket tartalmazza.  A leképezett vCenter-erőforráskészlet saját Felhőbeli vCenter létrehozott virtuális gépek megjelennek a listában.  
