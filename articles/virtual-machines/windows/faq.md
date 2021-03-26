---
title: Gyakori kérdések az Azure-beli Windows rendszerű virtuális gépekről
description: Választ ad a Resource Manager-modellel létrehozott Windows rendszerű virtuális gépekkel kapcsolatos gyakori kérdésekre.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 2dc94ea904da128be01decf36e2d36dc9510c2a5
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043879"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Gyakori kérdések a Windows Virtual Machines
Ez a cikk az Azure-ban a Resource Manager-alapú üzemi modellel létrehozott Windows rendszerű virtuális gépekkel kapcsolatos gyakori kérdéseket tárgyalja. A jelen témakör Linux-verziójával kapcsolatban lásd: [Linux Virtual Machines kapcsolatos gyakori kérdések](../linux/faq.md).

## <a name="what-can-i-run-on-an-azure-vm"></a>Mit futtathatok egy Azure-beli virtuális gépen?
Minden előfizető kiszolgálószoftvereket futtathat az Azure-beli virtuális gépeken. További információ az Azure-beli Microsoft Server-szoftverek futtatására vonatkozó támogatási szabályzatról: a [Microsoft Server szoftver támogatása az azure Virtual Machines](https://support.microsoft.com/kb/2721672).

A Windows 7, a Windows 8,1 és a Windows 10 bizonyos verziói elérhetők az MSDN Azure Benefit előfizetők és az MSDN dev and test utólagos elszámolású előfizetők számára fejlesztési és tesztelési feladatokhoz. Részletekért, többek között az utasításokért és korlátozásokért tekintse meg az [MSDN-előfizetők számára elérhető Windows-rendszerképeket](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/) ismertető cikket. 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Mennyi tárhelyet használhatok egy virtuális gép esetén?
Az egyes adatlemezek akár 32 767 GiB-t is használhatnak. A használható adatlemezek száma a virtuális gép méretétől függ. Részletek: [Virtuális gépek méretei](../sizes.md).

Az Azure-Managed Disks az Azure Virtual Machines által az adattároláshoz használt ajánlott lemezes tárolási ajánlatok. Az egyes virtuális gépekhez több Managed Disks is használható. Managed Disks kétféle tartós tárolási lehetőséget kínál: prémium és standard szintű Managed Disks. A díjszabással kapcsolatos információkért tekintse meg a [Managed Disks díjszabását](https://azure.microsoft.com/pricing/details/managed-disks).

Az Azure Storage-fiókok az operációs rendszer lemezét és az adatlemezeket is megadhatják. Minden lemez egy lapblobként tárolt .vhd-fájl. A díjszabás részleteiért lásd [a Storage szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hogyan lehet hozzáférni a virtuális géphez?
Hozzon létre egy távoli kapcsolatot egy Windows rendszerű virtuális gép Távoli asztali kapcsolat (RDP) használatával. Útmutatásért lásd: [Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális gépre](connect-logon.md). Legfeljebb két egyidejű kapcsolat támogatott, kivéve, ha a kiszolgáló Távoli asztali szolgáltatások munkamenet-gazdagépként van konfigurálva.  

Ha problémákat tapasztal a Távoli asztalával kapcsolatban, tekintse [meg a Windows-alapú Azure-beli virtuális gépek távoli asztal kapcsolatainak hibaelhárításával](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)foglalkozó témakört. 

Ha már ismeri a Hyper-V-t, előfordulhat, hogy a VMConnect hasonló eszközt keres. Az Azure nem nyújt hasonló eszközt, mert a virtuális géphez való hozzáférés nem támogatott.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Használhatom az ideiglenes lemezt (a D: meghajtó alapértelmezés szerint) az adattároláshoz?
Ne használja az ideiglenes lemezt az adattároláshoz. Csak átmeneti tárterület, ezért a nem helyreállítható adatvesztés kockázatát kockáztatja. Adatvesztés történhet, ha a virtuális gép egy másik gazdagépre lép. A virtuális gép átméretezése, a gazdagép frissítése vagy a gazdagépen bekövetkezett hardverhiba lehet az oka egy virtuális gép áthelyezésének.

Ha van olyan alkalmazás, amelynek a D: meghajtó betűjelét kell használnia, a meghajtóbetűjelek ismételt hozzárendelésével az ideiglenes lemez nem a D:.-t használja. Útmutatásért lásd a [Windows ideiglenes lemezének meghajtóbetűjel-módosítását](change-drive-letter.md) ismertető cikket.


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Hogyan módosíthatom egy ideiglenes lemez meghajtóbetűjelét?
A meghajtóbetűjelet úgy módosíthatja, hogy áthelyezi a lapozófájlt, és újból hozzárendeli a meghajtóbetűjeleket, de meg kell győződnie arról, hogy a lépéseket egy adott sorrendben hajtja végre. Útmutatásért lásd a [Windows ideiglenes lemezének meghajtóbetűjel-módosítását](change-drive-letter.md) ismertető cikket.

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Felvehetek meglévő virtuális gépet egy rendelkezésre állási csoportba?
Nem. Ha azt szeretné, hogy a virtuális gép egy rendelkezésre állási csoport része legyen, létre kell hoznia a virtuális gépet a készleten belül. A létrehozást követően jelenleg nem lehet hozzáadni egy virtuális gépet egy rendelkezésre állási csoporthoz.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Fel lehet tölteni egy virtuális gépet az Azure-ba?
Igen. Útmutatásért lásd: helyszíni [virtuális gépek áttelepítése az Azure-ba](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Átméretezhetim az operációsrendszer-lemezt?
Igen. Útmutatásért lásd: [virtuális gép operációsrendszer-meghajtójának kibontása Azure-erőforráscsoport](expand-os-disk.md)esetén.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Másolhatok vagy klónozott egy meglévő Azure-beli virtuális gépet?
Igen. A felügyelt lemezképek használatával létrehozhat egy virtuális gép lemezképét, majd a lemezkép használatával több új virtuális gépet építhet ki. Útmutatásért tekintse meg a [virtuális gép egyéni rendszerképének létrehozása](tutorial-custom-images.md)című témakört.

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Miért nem látom a közép-és Kelet-Kanada régióját a Azure Resource Manageron keresztül?

A közép-és Kelet-Kanada két új régióját nem regisztrálja automatikusan a rendszer a meglévő Azure-előfizetések virtuális géphez való létrehozásához. Ez a regisztráció automatikusan történik, ha egy virtuális gépet a Azure Portal a Azure Resource Manager használatával bármely más régióba telepítenek. A virtuális gép bármely más Azure-régióba való üzembe helyezését követően az új régióknak elérhetőnek kell lenniük a további virtuális gépek számára.

## <a name="does-azure-support-linux-vms"></a>Támogatja az Azure a Linux rendszerű virtuális gépeket?
Igen. Linux rendszerű virtuális gép gyors létrehozásához a kipróbáláshoz lásd: [Linux rendszerű virtuális gép létrehozása az Azure-ban a portál használatával](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Hozzá lehet adni egy hálózati adaptert a virtuális géphez a létrehozásuk után?
Igen, ez már lehetséges. Először le kell állítani a virtuális gépet. Ezután hozzáadhat vagy eltávolíthat egy hálózati adaptert (kivéve, ha ez a virtuális gép utolsó hálózati adaptere). 

## <a name="are-there-any-computer-name-requirements"></a>Van-e számítógépnév-követelmény?
Igen. A számítógép neve legfeljebb 15 karakter hosszú lehet. Az erőforrások elnevezésével kapcsolatos további információkért lásd: [elnevezési konvenciók szabályai és korlátozásai](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging) .

## <a name="are-there-any-resource-group-name-requirements"></a>Vannak az erőforráscsoport-nevekre vonatkozó követelmények?
Igen. Az erőforráscsoport neve legfeljebb 90 karakter hosszú lehet. Az erőforráscsoportok további információit az [elnevezési konvenciók szabályai és korlátozásai](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) című témakörben talál.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Milyen felhasználónévre vonatkozó követelmények vonatkoznak a virtuális gépek létrehozásakor?

A Felhasználónév legfeljebb 20 karakter hosszúságú lehet, és nem végződhet ponttal ("."). 

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

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Mik a jelszóra vonatkozó követelmények a virtuális gépek létrehozásakor?

Az Ön által használt eszköztől függően eltérő jelszó-hosszúsági követelmények szükségesek:
 - Portál – 12-72 karakter közötti
 - PowerShell – 8-123 karakter közötti
 - CLI – 12-123 között

* Tartalmaz kisbetűs karaktereket
* Tartalmaz nagybetűs karaktereket
* Tartalmaz számjegyet
* Tartalmaz speciális karaktert (reguláris kifejezés [\W_])

A következő jelszavak nem engedélyezettek:

<table>
    <tr>
        <td>abc@123</td>
        <td>ILOVEYOU!</td>
        <td>P@ $ $w 0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>PA $ $word</td>
        <td>pass@word1</td>
        <td>Jelszó!</td>
        <td>Jelszó1</td>
        <td>Password22</td>
    </tr>
</table>