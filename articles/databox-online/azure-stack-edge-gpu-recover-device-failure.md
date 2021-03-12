---
title: Helyreállítás egy Azure Stack Edge Pro-eszköz meghibásodása esetén
description: Ismerteti, hogyan lehet helyreállítani egy Azure Stack Edge Pro-eszközről.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: b1bfbda007619bf5bd94d47297845881758037bc
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "102636640"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>Helyreállítás sikertelen Azure Stack Edge Pro GPU-eszközről 

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Ez a cikk azt ismerteti, hogyan lehet helyreállítani az Azure Stack Edge Pro GPU-eszközön nem tolerálható meghibásodás esetén. A Azure Stack Edge Pro GPU-eszközön nem tolerálható meghibásodás esetén az eszköz cseréje szükséges.

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy rendelkezik az alábbiakkal:

- A rendszer felvette a kapcsolatot Microsoft ügyfélszolgálata az eszköz meghibásodásával kapcsolatban, és javasolta az eszköz cseréjét. 
- Az eszköz konfigurációjának biztonsági mentése a következő témakörben leírtak szerint: [felkészülés az eszköz meghibásodására](azure-stack-edge-gpu-prepare-device-failure.md).


## <a name="configure-replacement-device"></a>Helyettesítő eszköz konfigurálása

Ha az eszköz nem tolerálható hibát észlel, egy helyettesítő eszközt kell megrendelnie. A helyettesítő eszköz konfigurációs lépései változatlanok maradnak. 

Kérje le az eszközön a sikertelenül mentett konfigurációs adatokat. Ezt az információt fogja használni a helyettesítő eszköz konfigurálásához.  

A következő lépésekkel konfigurálhatja a helyettesítő eszközt:

1. Gyűjtse össze a [központi telepítési ellenőrzőlistában](azure-stack-edge-gpu-deploy-checklist.md)szükséges adatokat. Használhatja az előző eszköz konfigurációjától mentett adatokat. 
1. Rendeljen egy olyan új eszközt, amelynek a konfigurációja nem sikerült.  Rendelés elhelyezéséhez [hozzon létre egy új Azure stack Edge-erőforrást](azure-stack-edge-gpu-deploy-prep.md#) a Azure Portalban.
1. Az eszköz [kicsomagolása](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), [rack csatlakoztatása](azure-stack-edge-gpu-deploy-install.md#rack-the-device) és [kábele](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Kapcsolódjon az eszköz helyi felhasználói felületéhez](azure-stack-edge-gpu-deploy-connect.md).
1. Konfigurálja a hálózatot a régi eszközhöz használt IP-címek használatával. Ha ugyanazt az IP-címet használja, az a környezetben használt összes ügyfélgépre kihatással csökken. Lásd: a [hálózati beállítások konfigurálása](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Rendelje hozzá ugyanazt az eszköznév és DNS-tartományt a régi eszközként. Így az ügyfelek ugyanazzal az eszközzel használhatják az új eszközt. Lásd: az [eszköz beállításának konfigurálása](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Konfigurálja a tanúsítványokat az új eszközön ugyanúgy, mint a régi eszköz esetében. Ne feledje, hogy az új eszköz új csomópont-sorozatszámmal rendelkezik. Ha saját tanúsítványait használta a régi eszközön, új csomópont-tanúsítványt kell beszereznie. Lásd: [tanúsítványok konfigurálása](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Szerezze be az aktiválási kulcsot a Azure Portal és aktiválja az új eszközt. Lásd: [az eszköz aktiválása](azure-stack-edge-gpu-deploy-activate.md).

Most már készen áll a régi eszközön futó munkaterhelések üzembe helyezésére.

## <a name="restore-edge-cloud-shares"></a>Edge Cloud shares-megosztások visszaállítása

Az alábbi lépéseket követve állíthatja vissza az eszközön lévő Felhőbeli megosztások adatait:

1. A korábban a hibás eszközön létrehozott megosztási névvel rendelkező [megosztásokat is hozzáadhat](azure-stack-edge-gpu-manage-shares.md#add-a-share) . Győződjön meg arról, hogy a megosztások létrehozásakor a **blob Container** a **meglévő lehetőség használatára** van beállítva, majd válassza ki az előző eszközzel használt tárolót.
1. [Adja hozzá](azure-stack-edge-gpu-manage-users.md#add-a-user) az előző eszközhöz hozzáféréssel rendelkező felhasználókat.
1. [Adja hozzá](azure-stack-edge-gpu-manage-storage-accounts.md#add-an-edge-storage-account) a korábban az eszközön található megosztásokhoz társított Storage-fiókokat. Edge Storage-fiókok létrehozásakor válasszon egy meglévő tárolóból, és mutasson arra a tárolóra, amely az előző eszközön leképezett Azure Storage-fiókra van leképezve. Az eszközön az előző eszköz Edge Storage-fiókjába írt összes adatait a rendszer feltöltötte a hozzárendelt Azure Storage-fiók kiválasztott tárolójába.
1. [A megosztási adatok frissítése](azure-stack-edge-gpu-manage-shares.md#refresh-shares) az Azure-ból. Ezzel lekéri a meglévő tároló összes Felhőbeli adatait a megosztásokra.

## <a name="restore-edge-local-shares"></a>Peremhálózati helyi megosztások visszaállítása

A lehetséges eszköz meghibásodására való felkészüléshez lehetséges, hogy a következő biztonsági mentési megoldásokat telepítette, hogy megvédje a helyi megosztások adatait a Kubernetes vagy IoT számítási feladatokból:

| Külső gyártótól származó szoftverek           | Hivatkozás a megoldásra                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Részletekért vegye fel a kapcsolatot az adatkapcsolattal.          |
| CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> A részletekért forduljon a CommVault. |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Részletekért forduljon a Veritashez.   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> A részletekért forduljon a Veeam. |

Miután a helyettesítő eszköz teljesen konfigurálva van, engedélyezze az eszközt a helyi tároláshoz. 

A helyi megosztásokból származó adatok helyreállításához kövesse az alábbi lépéseket:

1. [Állítsa be a számítást az eszközön](azure-stack-edge-gpu-deploy-configure-compute.md).
1. [Helyi megosztást adjon](azure-stack-edge-gpu-manage-shares.md#add-a-local-share) vissza.
1. Futtassa a választható adatvédelmi megoldás által biztosított helyreállítási eljárást. Lásd az előző táblázat hivatkozásait.

## <a name="restore-vm-files-and-folders"></a>Virtuálisgép-fájlok és-mappák visszaállítása

A lehetséges eszköz meghibásodására való felkészüléshez előfordulhat, hogy a következő biztonsági mentési megoldások egyikét telepítette a virtuális gépek adatainak védelme érdekében:



| Biztonsági mentési megoldások        | Támogatott operációs rendszer   | Referencia                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Microsoft Azure Recovery Services (MARS) ügynök a Azure Backup | Windows        | [A MARS-ügynök ismertetése](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Microsoft Azure integráció, biztonsági mentési & helyreállítási megoldás rövid](https://www.cohesity.com/solution/cloud/azure) <br>Részletekért vegye fel a kapcsolatot az adatkapcsolattal.                          |
| CommVault               | Windows, Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> A részletekért forduljon a CommVault.
| Veritas                 | Windows, Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370) <br> Részletekért forduljon a Veritashez.                    |
| Veeam                   | Windows, Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> A részletekért forduljon a Veeam. |

Miután a helyettesítő eszköz teljesen konfigurálva van, újra üzembe helyezheti a virtuális gépeket a korábban használt virtuálisgép-lemezképpel. 

A virtuális gépek adatainak helyreállításához kövesse az alábbi lépéseket:
 
1. [Helyezzen üzembe egy virtuális gépet egy virtuálisgép-rendszerképből](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) az eszközön. 
1. Telepítse a választható adatvédelmi megoldást a virtuális gépre.
1. Futtassa a választható adatvédelmi megoldás által biztosított helyreállítási eljárást. Lásd az előző táblázat hivatkozásait.

## <a name="restore-a-kubernetes-deployment"></a>Kubernetes-telepítés visszaállítása

Ha a Kubernetes üzembe helyezését az Azure arc használatával végezte el, az üzemelő példány nem tolerálható eszköz meghibásodása után visszaállítható. Az `git` alkalmazás definícióját tároló adattárból újra kell telepítenie a Customer Application/containers szolgáltatást. [Információk a Kubernetes Azure arc-beli üzembe helyezéséről](./azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)<!--Original text: Kubernetes deployments can be restored from a non-tolerated failure with the device when deployed with Azure Arc. Customer application/containers deployed onto a Kubernetes on Azure Stack Edge via Azure Arc can be redeployed from the git repository where the application definition is. Here is a link to the article to deploy Kubernetes with Arc -->
 
## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan adhat [vissza Azure stack Edge Pro-eszközt](azure-stack-edge-return-device.md).
