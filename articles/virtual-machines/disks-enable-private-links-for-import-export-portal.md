---
title: Azure Portal – a felügyelt lemezekre vonatkozó importálási/exportálási hozzáférés korlátozása privát hivatkozásokkal
description: Engedélyezze a felügyelt lemezekre Azure Portal-vel való magánhálózati hivatkozásokat. Lehetővé teszi, hogy biztonságosan exportálja és importálja a virtuális hálózaton lévő lemezeket.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b80100216003e91fde54b5e555bafb755c942810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682919"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>A Azure Portal használata a felügyelt lemezek importálási/exportálási hozzáférésének korlátozására privát hivatkozásokkal

A felügyelt lemezek privát hivatkozásai lehetővé teszik a felügyelt lemezek exportálásának és importálásának korlátozását úgy, hogy az csak az Azure-beli virtuális hálózaton belül történjen. Létrehozhat egy idő kötött közös hozzáférésű aláírás (SAS) URI-t a nem csatlakoztatott felügyelt lemezekhez és pillanatképekhez az adat más régióba való exportálásához a regionális terjeszkedés, a vész-helyreállítás és a kriminalisztikai elemzéshez szükséges információk beolvasása érdekében. A SAS URI-val közvetlenül is feltöltheti a VHD-t egy üres lemezre a helyszíni környezetből. A virtuális hálózaton és a felügyelt lemezeken lévő ügyfelek közötti hálózati forgalom csak a virtuális hálózaton keresztül halad át, a Microsoft gerinc hálózatán pedig egy privát kapcsolaton keresztül, ami kiküszöböli a nyilvános internetre való kitettséget.

Létrehozhat egy lemez-hozzáférési erőforrást, és összekapcsolhatja azt a virtuális hálózattal ugyanabban az előfizetésben egy privát végpont létrehozásával. Az adatok magánhálózati kapcsolaton keresztüli exportálásához és importálásához egy lemez vagy pillanatképet kell rendelni. Emellett a lemez NetworkAccessPolicy tulajdonságát vagy a pillanatképet is be kell állítania `AllowPrivate` . 

A NetworkAccessPolicy tulajdonságot beállíthatja úgy `DenyAll` , hogy megakadályozza, hogy bárki létrehozzon egy lemez vagy PILLANATKÉP sas URI-ját. A NetworkAccessPolicy tulajdonság alapértelmezett értéke: `AllowAll` .

## <a name="limitations"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="create-a-disk-access-resource"></a>Lemez-hozzáférési erőforrás létrehozása

1. Jelentkezzen be a Azure Portalba, és navigáljon a **lemez elérésére** a [hivatkozással](https://aka.ms/disksprivatelinks).

    > [!IMPORTANT]
    > A lemez elérési paneljének megnyitásához a [megadott hivatkozást](https://aka.ms/disksprivatelinks) kell használnia. Jelenleg nem látható a nyilvános portálon a hivatkozás használata nélkül.

1. Válassza a **+ Hozzáadás** lehetőséget egy új lemez-hozzáférési erőforrás létrehozásához.
1. A létrehozás panelen válassza ki az előfizetést, egy erőforráscsoportot, adjon meg egy nevet, és válasszon ki egy régiót.
1. Válassza az **Áttekintés + létrehozás** lehetőséget.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Képernyőfelvétel a lemezes hozzáférés létrehozási paneljéről. Adja meg a kívánt nevet, válasszon ki egy régiót, válasszon ki egy erőforráscsoportot, és folytassa":::

Az erőforrás létrehozása után navigáljon közvetlenül rá.

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Képernyőkép a portálon elérhető forrás gombról":::

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

Most, hogy van egy lemez-hozzáférési erőforrása, használhatja a lemez exportálási/importálási hozzáférésének kezelésére, ez privát végpontokon keresztül történik. Ennek megfelelően létre kell hoznia egy privát végpontot, és konfigurálnia kell a lemezes hozzáféréshez.

1. A lemez-hozzáférési erőforrás területen válassza a **privát végponti kapcsolatok** lehetőséget.
1. Válassza a **+ privát végpont** lehetőséget.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Képernyőkép a lemez-hozzáférési erőforrás áttekintés paneljéről. A magánhálózati végpontok kapcsolatai ki vannak emelve.":::

1. Erőforráscsoport kiválasztása
1. Adja meg a nevet, és válassza ki ugyanazt a régiót, amelyhez a lemez-hozzáférési erőforrás létrejött.
1. Válassza a Next (tovább) lehetőséget **: erőforrás >**

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Képernyőkép a privát végpont létrehozási munkafolyamatáról, első panel. Ha nem választja ki a megfelelő régiót, később problémákba ütközhet.":::

1. Az **erőforrás** panelen válassza a **Kapcsolódás Azure-erőforráshoz a saját címtárban** lehetőséget.
1. Az **erőforrástípus** területen válassza a **Microsoft. számítás/diskAccesses lehetőséget.**
1. Az **erőforrás** lapon válassza ki a korábban létrehozott lemez-hozzáférési erőforrást.
1. A **cél alerőforrás** **lemezként** való elhagyása
1. Válassza a Next (tovább) lehetőséget **: Configuration >**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Képernyőkép a privát végpont létrehozási munkafolyamatáról, második panel. Az összes Kiemelt értékkel (erőforrás típusa, erőforrás, cél alerőforrás)":::

1. Válassza ki azt a virtuális hálózatot, amelyre korlátozni szeretné a lemez exportálását, más virtuális hálózatok nem fogják tudni exportálni a lemezt.

    > [!NOTE]
    > Ha a kiválasztott alhálózathoz engedélyezve van egy hálózati biztonsági csoport (minősítések), akkor az csak ezen az alhálózaton található privát végpontok esetében le lesz tiltva. Az alhálózaton található egyéb erőforrásoknak továbbra is NSG-kényszerítéssel kell rendelkezniük.

1. Válassza ki a megfelelő alhálózatot
1. Válassza az **Áttekintés + létrehozás** lehetőséget.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Képernyőkép a privát végpont létrehozási munkafolyamatáról, a harmadik panelről. A virtuális hálózat és az alhálózat kiemelten van kiemelve.":::

## <a name="enable-private-endpoint-on-your-disk"></a>Privát végpont engedélyezése a lemezen

1. Navigáljon ahhoz a lemezhez, amelyet konfigurálni szeretne
1. **Hálózatkezelés** kiválasztása
1. Válassza ki a **privát végpontot (a lemezes hozzáférésen keresztül)** , és válassza ki a korábban létrehozott lemez-hozzáférést.
1. Kattintson a **Mentés** gombra.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Képernyőkép a felügyelt lemez hálózatkezelés paneljéről. Kiemelve a privát végpontok kijelölését, valamint a kiválasztott lemezes hozzáférést. Ennek a beállításnak a mentésekor a lemez erre a hozzáférésre van konfigurálva.":::

Ezzel befejezte a felügyelt lemez importálására/exportálására használható privát hivatkozások konfigurálását.

## <a name="next-steps"></a>Következő lépések

- [Privát hivatkozások – GYIK](./faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Felügyelt Pillanatképek exportálása/másolása virtuális merevlemezként egy másik régióban lévő Storage-fiókba a PowerShell használatával](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)