---
title: Beágyazott virtualizáció engedélyezése sablonbeli virtuális gépen Azure Lab Servicesban | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan állíthatja be a beágyazott virtualizációt egy Azure Lab Services lévő sablonos gépen.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 319695088e967dc3156ecab5c1b9458e77fcc186
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91251474"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Beágyazott virtualizálás engedélyezése sablonbeli virtuális gépen Azure Lab Services

Jelenleg a Azure Lab Services lehetővé teszi egy sablonban lévő virtuális gép beállítását egy laborban, és az egyes felhasználók számára egyetlen másolatot készíthet. Ha Ön pedagógusa a hálózatkezelés, a biztonság vagy az informatikai osztályok számára, előfordulhat, hogy minden tanulót egy olyan környezettel kell megadnia, amelyben több virtuális gép kommunikálhat egymással egy hálózaton keresztül.

A beágyazott virtualizálás lehetővé teszi, hogy több virtuális gépre kiterjedő környezetet hozzon létre a labor sablonjának virtuális gépén. A sablon közzétételével minden felhasználó számára elérhetővé válik a laborban egy virtuális gép, amelyben több virtuális gép van beállítva.  Ez a cikk bemutatja, hogyan állíthatja be a beágyazott virtualizálás szolgáltatást egy Azure Lab Services található sablonos gépen.

## <a name="what-is-nested-virtualization"></a>Mit jelent a beágyazott virtualizálás?

A beágyazott virtualizálás lehetővé teszi, hogy virtuális gépeket hozzon létre egy virtuális gépen belül. A beágyazott virtualizálás a Hyper-V-n keresztül történik, és csak Windows rendszerű virtuális gépeken érhető el.

A beágyazott virtualizálás szolgáltatással kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Beágyazott virtualizálás az Azure-ban](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Beágyazott virtualizálás engedélyezése Azure-beli virtuális gépen](../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Megfontolandó szempontok

A tesztkörnyezet beágyazott virtualizálás előtti beállítása előtt néhány dolgot figyelembe kell venni.

- Új Labor létrehozásakor válassza a **közepes (beágyazott virtualizálás)** vagy a **nagy (beágyazott virtualizációs)** méretek lehetőséget a virtuális gép méretének kiválasztásához. Ezek a virtuálisgép-méretek támogatják a beágyazott virtualizálás szolgáltatást.
- Válasszon egy olyan méretet, amely a gazdagép és az ügyfél virtuális gépei számára is jó teljesítményt nyújt.  Ne feledje, hogy a virtualizálás használatakor a kiválasztott méretnek megfelelőnek kell lennie a nem csak egy gépen, hanem a gazdagépen, valamint a párhuzamosan futó Hyper-V-gépeken is.
- Az ügyfél virtuális gépei nem férnek hozzá Azure-erőforrásokhoz, például DNS-kiszolgálókhoz az Azure-beli virtuális hálózaton.
- A gazdagép virtuális gépén a telepítőnek engedélyeznie kell, hogy az ügyfélszámítógép internetkapcsolattal rendelkezzen.
- Az ügyfél virtuális gépei független gépekként vannak engedélyezve. A Microsoft-üzemeltetési rendszerek és termékek licencelésével kapcsolatos információkért tekintse meg a [Microsoft licencelését](https://www.microsoft.com/licensing/default) ismertető témakört. A sablon számítógépének beállítása előtt a használatban lévő egyéb szoftverek licencelési szerződéseit is megvizsgálhatja.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Beágyazott virtualizálás engedélyezése sablonalapú virtuális gépen

Ez a cikk azt feltételezi, hogy létrehozott egy labor-fiókot és egy labort.  Az új Labor-fiókok létrehozásával kapcsolatos további információkért lásd: [oktatóanyag a labor-fiók beállításához](tutorial-setup-lab-account.md). A labor létrehozásával kapcsolatos további információkért tekintse [meg a tanterem Lab-oktatóanyagának beállítása](tutorial-setup-classroom-lab.md)című témakört.

>[!IMPORTANT]
>A tesztkörnyezet létrehozásakor válassza a **nagyméretű (beágyazott virtualizálás)** vagy **közepes (beágyazott virtualizálás** ) lehetőséget a virtuális gép méretének kiválasztásához.  A beágyazott virtualizálás más módon nem működik.  

A sablonhoz való kapcsolódáshoz lásd: [tantermi sablon létrehozása és kezelése](how-to-create-manage-template.md).

A beágyazott virtualizálás engedélyezéséhez néhány feladat végrehajtása szükséges.  

- **Engedélyezze a Hyper-V szerepkört**. A Hyper-V szerepkörnek engedélyezve kell lennie a Hyper-V virtuális gépek létrehozásához és futtatásához a labor Services virtuális gépen.
- **Engedélyezze a DHCP-t**.  Ha a labor Services virtuális gépnek engedélyezve van a DHCP-szerepkör, a Hyper-V virtuális gépek automatikusan IP-címet rendelhetnek hozzá.
- **NAT-hálózat létrehozása Hyper-V virtuális gépekhez**.  A NAT-hálózat úgy van beállítva, hogy engedélyezze a Hyper-V virtuális gépek számára az internet-hozzáférést.  A Hyper-V virtuális gépek kommunikálhatnak egymással.

>[!NOTE]
>A labor Services virtuális gépen létrehozott NAT-hálózat lehetővé teszi, hogy egy Hyper-V virtuális gép hozzáférhessen az internethez és más Hyper-V virtuális gépekhez ugyanazon labor Services virtuális gépen.  A Hyper-V virtuális gép nem tud hozzáférni Azure-erőforrásokhoz, például DNS-kiszolgálókhoz az Azure Virtual Network szolgáltatásban.

A fent felsorolt feladatok végrehajtása parancsfájl használatával vagy Windows-eszközök használatával végezhető el.  További részletekért olvassa el az alábbi szakaszt.

### <a name="using-script-to-enable-nested-virtualization"></a>Beágyazott virtualizálás engedélyezése parancsfájl használatával

Ha a Windows Server 2016 vagy a Windows Server 2019 rendszerű beágyazott virtualizálás automatikus beállítását szeretné használni, tekintse [meg a beágyazott virtualizálás engedélyezése Azure Lab Services sablon virtuális gépén parancsfájl használatával](how-to-enable-nested-virtualization-template-vm-using-script.md)című témakört. A Hyper-V szerepkör telepítéséhez parancsfájlokat kell használni a [labor Services Hyper-v parancsfájljaiból](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) .  A parancsfájlok emellett hálózatkezelést is biztosítanak, így a Hyper-V virtuális gépek hozzáférhetnek az internethez.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>A Windows-eszközök használata a beágyazott virtualizálás engedélyezéséhez

A Windows Server 2016 vagy Windows Server 2019 rendszerhez készült beágyazott virtualizáció beállítása Windows-szerepkörökkel és felügyeleti eszközökkel: a [beágyazott virtualizálás engedélyezése sablonbeli virtuális gépen Azure Lab Services manuálisan](how-to-enable-nested-virtualization-template-vm-ui.md).  Az utasítások azt is ismertetik, hogyan állíthatja be a hálózatkezelést, hogy a Hyper-V virtuális gépek hozzáférhessenek az internethez.
