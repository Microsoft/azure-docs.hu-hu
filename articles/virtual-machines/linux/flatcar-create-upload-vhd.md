---
title: Flatcar-tároló létrehozása és feltöltése az Azure-ban való használatra
description: Megtudhatja, hogyan hozhat létre és tölthet fel egy virtuális merevlemezt, amely egy Flatcar-tároló Linux operációs rendszert tartalmaz.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 5d8be9493b7a312270301e3520f301f797fe2167
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565291"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Előre elkészített Flatcar-rendszerkép használata az Azure-hoz

A Flatcar-tároló Linux rendszerhez készült előre elkészített Azure virtuális merevlemez-lemezképeket a Flatcar által támogatott összes csatornára letöltheti:

- [stabil](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [bétaverzió](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [alpha](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [Edge](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

Ez a rendszerkép már teljesen be van állítva, és az Azure-on való futtatásra van optimalizálva. Csak azt kell kibontania.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Saját Flatcar-alapú virtuális gép kiépítése az Azure-hoz

Azt is megteheti, hogy felépíti a saját Flatcar-tároló linuxos rendszerképét.

A Linux-alapú gépeken kövesse az [Flatcar-tároló linuxos fejlesztői SDK-útmutatójában](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/)részletes útmutatást. A parancsfájl futtatásakor `image_to_vm.sh` Győződjön meg arról, `--format=azure` hogy egy Azure-beli virtuális merevlemezt hoz létre.

## <a name="next-steps"></a>Következő lépések

A. vhd fájl használata után az eredményül kapott fájllal új virtuális gépeket hozhat létre az Azure-ban. Ha első alkalommal tölt fel egy. vhd-fájlt az Azure-ba, tekintse meg a Linux rendszerű [virtuális gép létrehozása egyéni lemezről](upload-vhd.md#option-1-upload-a-vhd)című témakört.
