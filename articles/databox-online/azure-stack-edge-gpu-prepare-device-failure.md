---
title: Felkészülés Azure Stack Edge Pro-eszköz meghibásodására
description: Ismerteti, hogyan kérhető le egy Azure Stack Edge Pro rendszerű eszköz cseréje.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 60469dc834c28bd8dbc73a1883ea01b06797c01f
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442978"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Felkészülés Azure Stack Edge Pro GPU-eszköz meghibásodására

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Ebből a cikkből megtudhatja, hogyan készítheti elő az eszköz meghibásodását, ha részletesen leírja, hogyan menti és biztonsági másolatot készít az eszköz konfigurációjának és adatairól az Azure Stack Edge Pro GPU-eszközön. 

A cikk nem tartalmazza a Azure Stack Edge Pro GPU-eszközön üzembe helyezett Kubernetes és IoT-tárolók biztonsági mentésének lépéseit. 

## <a name="understand-device-failures"></a>Az eszköz meghibásodásának ismertetése

Az Azure Stack Edge Pro GPU-eszköz kétféle hardveres hibát képes tapasztalni.

- A hardver-összetevők lecserélését igénylő tolerálható hibák. Ezek a hibák lehetővé teszik az eszköz csökkentett teljesítményű állapotú üzemeltetését. Ilyen hibák például egyetlen sikertelen tápegység (PSU) vagy egyetlen hibás lemez az eszközön. Ezekben az esetekben az eszköz továbbra is működhet. A sikertelen összetevők cseréjéhez lépjen kapcsolatba Microsoft ügyfélszolgálata a lehető leghamarabb.

- Nem tolerálható hibák, amelyek megkövetelik a teljes eszköz cseréjét – például ha az eszközön két lemez meghiúsult. Ezekben az esetekben azonnal forduljon Microsoft ügyfélszolgálatahoz. Miután meghatározták, hogy az eszköz cseréje szükséges, a támogatás segítséget nyújt az Azure Stack Edge-eszköz cseréjéhez.

A nem tolerálható hibák előkészítéséhez biztonsági másolatot kell készítenie az eszközön az alábbi dolgokról:

- Az eszköz konfigurációjával kapcsolatos információk
- Az Edge helyi megosztások és az Edge Cloud shares-megosztások
- Az eszközön futó virtuális gépekhez társított fájlok és mappák


## <a name="back-up-device-configuration"></a>Eszköz konfigurációjának biztonsági mentése

Az eszköz kezdeti konfigurálása során fontos, hogy az eszköz konfigurációs információinak másolatát az [üzembe helyezési ellenőrzőlistán](azure-stack-edge-gpu-deploy-checklist.md)vázolt módon őrizze meg. A helyreállítás során a rendszer ezt a konfigurációs információt fogja használni az új helyettesítő eszközre való alkalmazásához. 

## <a name="protect-device-data"></a>Eszközadatok védelme

Az eszköz adattípusa a következő típusok egyike lehet:

- Az Edge Felhőbeli megosztások
- Helyi megosztásokban lévő adatforgalom
- Virtuális gépek fájljai és mappái

A következő részekben ismertetjük azokat a lépéseket és ajánlásokat, amelyek az eszközön az ilyen típusú adattípusok védelme érdekében szükségesek.

## <a name="protect-data-in-edge-cloud-shares"></a>Az Edge Cloud shares adatainak védelme

Az eszközről az Azure-ba létrehozott Edge Cloud shares-megosztásokat hozhat létre. A rendelkezésre álló hálózati sávszélességtől függően állítsa be a sávszélesség-sablonokat az eszközön, hogy az adatvesztést csökkentse, ha nem tolerálható hiba történik.

> [!IMPORTANT]
> Ha az eszköz nem tolerálható hibával rendelkezik, előfordulhat, hogy az eszközről az Azure-ra nem kivont helyi adatok elvesznek. 

## <a name="protect-data-in-edge-local-shares"></a>Az adatvédelmet a peremhálózat helyi megosztásai között

Ha a Kubernetes-t vagy IoT Edge-t telepíti, konfigurálja úgy az alkalmazást, hogy helyileg mentse az alkalmazásadatok az eszközön, és ne szinkronizáljon az Azure Storage szolgáltatással. Az adattárolást az eszköz egyik megosztásán tárolja a rendszer. Előfordulhat, hogy fontos, hogy biztonsági másolatot készítsen az ezekben a megosztásokban található adatforgalomról.

A következő harmadik féltől származó adatvédelmi megoldások biztonsági mentési megoldást nyújthatnak a helyi SMB-vagy NFS-megosztások adatvédelmére. 

| Külső gyártótól származó szoftverek           | Hivatkozás a megoldásra                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Részletekért vegye fel a kapcsolatot az adatkapcsolattal.          |
| CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> A részletekért forduljon a CommVault.          |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Részletekért forduljon a Veritashez.   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> A részletekért forduljon a Veeam. |


## <a name="protect-files-and-folders-on-vms"></a>A virtuális gépek fájljainak és mappáinak a védelemmel való ellátása

Az Azure Stack Edge együttműködik a Azure Backup és más, harmadik féltől származó adatvédelmi megoldásokkal, így biztosítva az eszközön üzembe helyezett virtuális gépeken tárolt adatvédelemre vonatkozó biztonsági mentési megoldást. Az alábbi táblázat a választható megoldásokra mutató hivatkozásokat tartalmazza.


| Biztonsági mentési megoldások        | Támogatott operációs rendszer   | Referencia                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Microsoft Azure Recovery Services (MARS) ügynök a Azure Backup | Windows        | [A MARS-ügynök ismertetése](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Microsoft Azure integráció, biztonsági mentési & helyreállítási megoldás rövid](https://www.cohesity.com/solution/cloud/azure) <br>Részletekért vegye fel a kapcsolatot az adatkapcsolattal.                          |
| CommVault               | Windows, Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br>A részletekért forduljon a CommVault.                          |
| Veritas                 | Windows, Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370) <br> Részletekért forduljon a Veritashez.                    |
| Veeam                   | Windows, Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> A részletekért forduljon a Veeam. |


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan lehet [helyreállítani egy sikertelen Azure stack Edge Pro GPU-eszközről](azure-stack-edge-gpu-recover-device-failure.md).