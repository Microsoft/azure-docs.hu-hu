---
title: Gyakori kérdések az Azure-beli Linux rendszerű virtuális gépekről
description: Választ ad a Resource Manager-modellel létrehozott linuxos virtuális gépekkel kapcsolatos gyakori kérdésekre.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: d510fa35163a4c4a79352f5f35af9c384bf85304
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596314"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Gyakori kérdések a Linux Virtual Machines
Ez a cikk az Azure-ban a Resource Manager-alapú üzemi modellel létrehozott Linux rendszerű virtuális gépekkel kapcsolatos gyakori kérdéseket tárgyalja. A témakör Windows-verziójával kapcsolatos [Gyakori kérdések a Windows Virtual Machines](../windows/faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Mit futtathatok egy Azure-beli virtuális gépen?
Minden előfizető kiszolgálószoftvereket futtathat az Azure-beli virtuális gépeken. További információ: [Linux on Azure-Endorsed distributes](endorsed-distros.md)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Mennyi tárhelyet használhatok egy virtuális gép esetén?
Az egyes adatlemezek akár 32 767 GiB-t is használhatnak. A használható adatlemezek száma a virtuális gép méretétől függ. Részletek: [Virtuális gépek méretei](../sizes.md).

Az Azure-Managed Disks az Azure Virtual Machines által az adattároláshoz használt ajánlott lemezes tárolási ajánlatok. Az egyes virtuális gépekhez több Managed Disks is használható. Managed Disks kétféle tartós tárolási lehetőséget kínál: prémium és standard szintű Managed Disks. A díjszabással kapcsolatos információkért tekintse meg a [Managed Disks díjszabását](https://azure.microsoft.com/pricing/details/managed-disks).

Az Azure Storage-fiókok az operációs rendszer lemezét és az adatlemezeket is megadhatják. Minden lemez egy lapblobként tárolt .vhd-fájl. A díjszabás részleteiért lásd [a Storage szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hogyan lehet hozzáférni a virtuális géphez?
Hozzon létre egy távoli kapcsolatot a virtuális gépre való bejelentkezéshez Secure Shell (SSH) használatával. Tekintse meg a [Windows](ssh-from-windows.md) vagy [Linux és Mac](mac-create-ssh-keys.md)rendszerről való kapcsolódásra vonatkozó utasításokat. Alapértelmezés szerint az SSH legfeljebb 10 párhuzamos kapcsolatot tesz lehetővé. Ezt a számot a konfigurációs fájl szerkesztésével növelheti.

Ha problémákat tapasztal, tekintse meg a [Secure Shell-(SSH-) kapcsolatokkal kapcsolatos hibaelhárítást](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Használhatom az ideiglenes lemezt (/dev/sdb1) az adattároláshoz?
Ne használja az ideiglenes lemezt (/dev/sdb1) az adattároláshoz. Csak átmeneti tárolás esetén érhető el. A nem helyreállítható adatvesztés kockázatát kockáztatja.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Másolhatok vagy klónozott egy meglévő Azure-beli virtuális gépet?
Igen. Útmutatásért lásd: [Linux rendszerű virtuális gép másolatának létrehozása a Resource Manager-alapú üzemi modellben](copy-vm.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Miért nem látom a közép-és Kelet-Kanada régióját a Azure Resource Manageron keresztül?
A közép-és Kelet-Kanada két új régióját nem regisztrálja automatikusan a rendszer a meglévő Azure-előfizetések virtuális géphez való létrehozásához. Ez a regisztráció automatikusan történik, ha egy virtuális gépet a Azure Portal a Azure Resource Manager használatával bármely más régióba telepítenek. A virtuális gép bármely más Azure-régióba való üzembe helyezését követően az új régióknak elérhetőnek kell lenniük a további virtuális gépek számára.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Hozzá lehet adni egy hálózati adaptert a virtuális géphez a létrehozásuk után?
Igen, ez már lehetséges. Először le kell állítani a virtuális gépet. Ezután hozzáadhat vagy eltávolíthat egy hálózati adaptert (kivéve, ha ez a virtuális gép utolsó hálózati adaptere). 

## <a name="are-there-any-computer-name-requirements"></a>Van-e számítógépnév-követelmény?
Igen. A számítógép neve legfeljebb 64 karakter hosszú lehet. Az erőforrások elnevezésével kapcsolatos további információkért lásd: [elnevezési konvenciók szabályai és korlátozásai](/azure/architecture/best-practices/resource-naming) .

## <a name="are-there-any-resource-group-name-requirements"></a>Vannak az erőforráscsoport-nevekre vonatkozó követelmények?
Igen. Az erőforráscsoport neve legfeljebb 90 karakter hosszú lehet. Az erőforráscsoportok további információit az [elnevezési konvenciók szabályai és korlátozásai](/azure/architecture/best-practices/resource-naming) című témakörben talál.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Milyen felhasználónévre vonatkozó követelmények vonatkoznak a virtuális gépek létrehozásakor?

A felhasználóneveknek 1-32 karakter hosszúnak kell lenniük.

A következő felhasználónevek nem engedélyezettek:

- `1`
- `123`
- `a`
- `actuser`
- `adm`
- `admin`
- `admin1`
- `admin2`
- `administrator`
- `aspnet`
- `backup`
- `console`
- `david`
- `guest`
- `john`
- `owner`
- `root`
- `server`
- `sql`
- `support_388945a0`
- `support`
- `sys`
- `test`
- `test1`
- `test2`
- `test3`
- `user`
- `user1`
- `user2`
- `user3`
- `user4`
- `user5`
- `video`


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Mik a jelszóra vonatkozó követelmények a virtuális gépek létrehozásakor?

Az Ön által használt eszköztől függően eltérő jelszó-hosszúsági követelmények szükségesek:
 - Portál – 12-72 karakter közötti
 - PowerShell – 8-123 karakter közötti
 - CLI – 12-123 karakter közötti
 - Azure Resource Manager-(ARM-) Sablonok – 12-72 karakter és vezérlő karakter használata nem engedélyezett
 

A jelszónak a következő 4 összetettségi követelménynek is meg kell felelnie:

* Tartalmaz kisbetűs karaktereket
* Tartalmaz nagybetűs karaktereket
* Tartalmaz számjegyet
* Tartalmaz speciális karaktert (reguláris kifejezés [\W_])

A következő jelszavak nem engedélyezettek:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@ $ $w 0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">PA $ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Jelszó!</td>
        <td style="text-align:center">Jelszó1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>