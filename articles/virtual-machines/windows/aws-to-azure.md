---
title: Windows AWS virtuális gépek áthelyezése az Azure-ba
description: Helyezzen át egy Amazon Web Services (AWS) EC2 Windows-példányt egy Azure-beli virtuális gépre.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 9bd01f24ac2cada02f51089d238519cd6c7e0248
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039287"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Windows rendszerű virtuális gép áthelyezése Amazon Web Services (AWS) rendszerből egy Azure-beli virtuális gépre

Ha Azure-beli virtuális gépeket értékel ki a számítási feladatok üzemeltetéséhez, exportálhat egy meglévő Amazon Web Services (AWS) EC2 Windows VM-példányba, majd feltöltheti a virtuális merevlemezt (VHD) az Azure-ba. A virtuális merevlemez feltöltése után létrehozhat egy új virtuális gépet az Azure-ban a VHD-ből. 

Ez a cikk az AWS-ből az Azure-ba irányuló egyetlen virtuális gép áthelyezését ismerteti. Ha az AWS-ből az Azure-ba szeretné áthelyezni a virtuális gépeket, tekintse meg a következőt: [Virtual Machines in Amazon Web Services (AWS) az Azure-ba Azure site Recoveryokkal](../../site-recovery/site-recovery-migrate-aws-to-azure.md).

## <a name="prepare-the-vm"></a>A virtuális gép előkészítése 
 
Az általános és a speciális virtuális merevlemezeket is feltöltheti az Azure-ba. Minden típushoz elő kell készítenie a virtuális gépet az AWS-ből való exportálás előtt. 

- **Általánosított VHD** – az ÁLTALÁNOSÍTOTT virtuális merevlemezen az összes személyes fiók adatai el lettek távolítva a Sysprep használatával. Ha a virtuális merevlemezt képként szeretné létrehozni új virtuális gépek létrehozásához, tegye a következőket: 
 
    * [Windows rendszerű virtuális gép előkészítése](prepare-for-upload-vhd-image.md).  
    * A virtuális gép általánosítása a Sysprep használatával.  

 
- **Speciális VHD** – a speciális virtuális merevlemezek a felhasználói fiókokat, az alkalmazásokat és az eredeti virtuális gépről származó egyéb állapotinformációkat kezelik. Ha a virtuális merevlemezt egy új virtuális gép létrehozásához kívánja használni, győződjön meg arról, hogy a következő lépések befejeződtek.  
    * [Készítse elő a Windows VHD-t az Azure-ba való feltöltéshez](prepare-for-upload-vhd-image.md). **Ne általánosítsa** a virtuális gépet a Sysprep használatával. 
    * Távolítsa el a virtuális gépre telepített összes vendég virtualizációs eszközt és ügynököt (azaz a VMware-eszközöket). 
    * Győződjön meg arról, hogy a virtuális gép úgy van konfigurálva, hogy az IP-címét és DNS-beállításait DHCP protokollon keresztül húzza Ez biztosítja, hogy a kiszolgáló az indításkor megszerezze az IP-címet a VNet belül.  


## <a name="export-and-download-the-vhd"></a>A VHD exportálása és letöltése 

Exportálja a EC2-példányt egy virtuális merevlemezre egy Amazon S3-gyűjtőben. Kövesse az Amazon dokumentációjában, a [példányok virtuális gépek importálásával/exportálásával](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) és a [create-instance-export-Task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) paranccsal történő exportálásával foglalkozó cikk lépéseit. 

Az exportált VHD-fájlt a rendszer az Ön által megadott Amazon S3-gyűjtőbe menti. A virtuális merevlemez exportálásának alapvető szintaxisa a következő, csak cserélje le a helyőrző szövegét \<zárójelben > az adataival.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Miután a VHD-t exportálta, kövesse a [Hogyan tölthetek le egy objektumot az S3 gyűjtőből?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) a VHD-fájl az S3 gyűjtőből való letöltéséhez. 

> [!IMPORTANT]
> Az AWS a VHD-fájl letöltéséhez szükséges adatátviteli díjakat számítja fel. További információkért tekintse meg az [Amazon S3 díjszabását](https://aws.amazon.com/s3/pricing/) .


## <a name="next-steps"></a>Következő lépések

Most feltöltheti a VHD-t az Azure-ba, és létrehozhat egy új virtuális gépet. 

- Ha az exportálás előtt **általánosítva** futtatta a sysprept a forráson, tekintse meg az ÁLTALÁNOSÍTOTT [virtuális merevlemez feltöltése és használata új virtuális gépek létrehozásához az Azure-ban](upload-generalized-managed.md) című témakört.
- Ha nem futtatta a Sysprept az exportálás előtt, a VHD **speciálisnak**minősül, lásd: [speciális virtuális merevlemez feltöltése az Azure-ba, és új virtuális gép létrehozása](create-vm-specialized.md)

 
