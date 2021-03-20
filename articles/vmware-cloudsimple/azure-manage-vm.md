---
title: Azure VMware-megoldás CloudSimple – privát felhőalapú virtuális gépek kezelése az Azure-ban
description: Ismerteti, hogyan lehet felügyelni a CloudSimple saját felhőalapú virtuális gépeket a Azure Portalban, beleértve a lemezek hozzáadását, a virtuális gép kapacitásának módosítását és hálózati adapterek hozzáadását
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 090b7711ab061b989eae13113fe7048e8dd875ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895189"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Saját CloudSimple-Felhőbeli virtuális gépek kezelése az Azure-ban

A [CloudSimple saját felhőhöz létrehozott](azure-create-vm.md)virtuális gépek kezeléséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com). Keresse meg és válassza ki a virtuális (Keresés az **összes szolgáltatás** vagy **Virtual Machines** oldalon a helyi menüben).

## <a name="control-virtual-machine-operation"></a>Virtuális gép műveletének vezérlése

A kiválasztott virtuális gép **Áttekintés** lapján a következő vezérlők érhetők el.

| Szabályozás | Description |
| ------------ | ------------- |
| Csatlakozás | Kapcsolódjon a megadott virtuális géphez.  |
| Indítás | Indítsa el a megadott virtuális gépet.  |
| Újraindítás | Állítsa le, majd kapcsolja be a megadott virtuális gépet.  |
| Leállítás | Állítsa le az adott virtuális gépet.  |
| Rögzítés | Rögzítsen egy rendszerképet a megadott virtuális gépről, hogy más virtuális gépek létrehozásához képként is használható legyen. Lásd: [általánosított virtuális gép felügyelt rendszerképének létrehozása az Azure-ban](../virtual-machines/windows/capture-image-resource.md).   |
| Áthelyezés | Ugrás a megadott virtuális gépre.  |
| Törlés | Távolítsa el a megadott virtuális gépet.  |
| Frissítés | A kijelzőben lévő Adatfrissítés.  |

### <a name="view-performance-information"></a>Teljesítményadatok megtekintése

Az **áttekintő** oldal alsó területén lévő diagramok a kiválasztott intervallumhoz tartozó teljesítményadatokat jelenítik meg (az utolsó órában az elmúlt 30 napban; az alapértelmezett érték az utolsó óra). Az egyes diagramokon az intervallumon belül bármikor megjelenítheti a numerikus értékeket úgy, hogy az egérmutatót a diagramon oda-vissza mozgatja.

A következő diagramok jelennek meg.

| Elem | Leírás |
| ------------ | ------------- |
| CPU (átlag) | A CPU átlagos kihasználtsága a kiválasztott intervallum százalékában kifejezve.   |
| Network (Hálózat) | A hálózat (MB) bejövő és kimenő forgalma a kiválasztott intervallumban.  |
| Lemez bájtjai | A lemezről beolvasott és lemezre (MB) írt összes adat a kiválasztott intervallumon belül.  |
| Lemezes műveletek | A lemezes műveletek átlagos sebessége (művelet/másodperc) a kijelölt intervallumban. |

## <a name="manage-vm-disks"></a>VM-lemezek kezelése

VM-lemez hozzáadásához nyissa meg a kiválasztott virtuális gép **lemezek** lapját. Lemez hozzáadásához kattintson a **lemez hozzáadása** elemre. Konfigurálja az alábbi beállításokat egy beágyazott lehetőség beírásával vagy kiválasztásával. Kattintson a **Mentés** gombra.

   | Elem | Leírás |
   | ------------ | ------------- |
   | Név | Adjon meg egy nevet a lemez azonosításához.  |
   | Méret | Válasszon ki egy rendelkezésre álló méretet.  |
   | SCSI-vezérlő | Válasszon ki egy SCSI-vezérlőt. A rendelkezésre álló vezérlők eltérőek lehetnek a különböző támogatott operációs rendszereken.  |
   | Mód | Meghatározza, hogy a lemez hogyan vegyen részt a pillanatképekben. Válasszon egyet a következő lehetőségek közül: <br> – Független állandó: a lemezre írt összes adattal véglegesen írásra kerül.<br> -Független, nem állandó: a lemezre írt módosításokat a rendszer a virtuális gép kikapcsolásakor vagy alaphelyzetbe állításakor elveti.  Ez a mód lehetővé teszi, hogy mindig ugyanabban az állapotban indítsa újra a virtuális gépet. További információt a [VMware dokumentációjában](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)talál. |

Lemez törléséhez jelölje ki azt, és kattintson a **Törlés** gombra.

## <a name="change-the-capacity-of-the-vm"></a>A virtuális gép kapacitásának módosítása

A virtuális gép kapacitásának módosításához nyissa meg a kiválasztott virtuális gép **méret** lapját. Határozza meg a következők bármelyikét, majd kattintson a **Mentés** gombra.

| Elem | Leírás |
| ------------ | ------------- |
| Magok száma | A virtuális géphez rendelt magok száma.  |
| Hardveres virtualizálás | Jelölje be a jelölőnégyzetet, hogy a hardveres virtualizálás elérhető legyen a vendég operációs rendszeren. Tekintse meg a [VMware-hardveres virtualizációs szolgáltatást támogató](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)VMware-cikket. |
| Memória mérete | Adja meg a virtuális gép számára lefoglalható memória mennyiségét.  

## <a name="manage-network-interfaces"></a>Hálózati adapterek kezelése

Illesztőfelület hozzáadásához kattintson a **hálózati adapter hozzáadása** lehetőségre. Konfigurálja az alábbi beállításokat egy beágyazott lehetőség beírásával vagy kiválasztásával. Kattintson a **Mentés** gombra.

   | Szabályozás | Leírás |
   | ------------ | ------------- |
   | Név | Adja meg a felületet azonosító nevet.  |
   | Network (Hálózat) | Válasszon a saját Felhőbeli vSphere konfigurált hálózatok listájából.  |
   | Adapter | Válasszon ki egy vSphere-adaptert a virtuális géphez konfigurált elérhető típusok listájából. További információkért lásd a VMware tudásbázist a [virtuális gép hálózati adapterének kiválasztásával](https://kb.vmware.com/s/article/1001805)foglalkozó cikkben. |
   | Bekapcsolás rendszerindításkor | Adja meg, hogy engedélyezi-e a hálózati adapter hardverét a virtuális gép indításakor. Az alapértelmezett érték az **Engedélyezés**. |

Hálózati adapter törléséhez jelölje ki azt, és kattintson a **Törlés** gombra.
