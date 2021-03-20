---
title: SSH-kulcsok létrehozása a Azure Portalban
description: Útmutató SSH-kulcsok létrehozásához és tárolásához a Azure Portal a linuxos virtuális gépek csatlakoztatásához.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/25/2020
ms.author: cynthn
ms.openlocfilehash: abc9a2ae130d987c90ce87ffaecbf2bb44b06010
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88929438"
---
# <a name="generate-and-store-ssh-keys-in-the-azure-portal"></a>SSH-kulcsok generálása és tárolása a Azure Portal

Ha gyakran használja a portált a Linux rendszerű virtuális gépek üzembe helyezéséhez, az SSH-kulcsok egyszerűbb használatával hozhatók létre közvetlenül a portálon, vagy feltöltheti őket a számítógépéről.

Létrehozhat egy SSH-kulcsot, amikor először hoz létre egy virtuális gépet, és más virtuális gépekre használja fel őket. Emellett az SSH-kulcsokat külön is létrehozhatja, így az Azure-ban tárolt kulcsok készlete a szervezet igényeinek megfelelően elvégezhető. 

Ha rendelkezik meglévő kulcsokkal, és egyszerűbbé szeretné őket használni a portálon, feltöltheti őket, és tárolhatja őket az Azure-ban újbóli használatra.

Az SSH-kulcsok Linux rendszerű virtuális gépekkel való létrehozásával és használatával kapcsolatos részletesebb információkért lásd: [ssh-kulcsok használata a Linux rendszerű virtuális gépekhez való kapcsolódáshoz](./linux/ssh-from-windows.md).

## <a name="generate-new-keys"></a>Új kulcsok generálása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

1. A lap tetején írja be az *SSH* kifejezést a kereséshez. A **piactér** területen válassza az **ssh-kulcsok** elemet.

1. Az **SSH-kulcs** lapon válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="./media/ssh-keys/portal-sshkey.png" alt-text="Új erőforráscsoport létrehozása és SSH-kulcspár létrehozása":::

1. Az **erőforráscsoport** területen válassza az **új létrehozása** elemet, és hozzon létre egy új erőforráscsoportot a kulcsok tárolásához. Adja meg az erőforráscsoport nevét, majd kattintson **az OK gombra**.

1. A **régió** területen válasszon ki egy régiót a kulcsok tárolásához. Bármelyik régióban használhatja a kulcsokat, ez csak az a régió, ahol a rendszer tárolja őket.

1. Írja be a kulcs nevét a kulcspár **neve** mezőbe.

1. Az **SSH nyilvános kulcs forrása** területen válassza a **nyilvános kulcs forrásának előállítása** lehetőséget. 

1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet.

1. Az érvényesítést követően válassza a **Létrehozás** lehetőséget.

1. Ekkor megjelenik egy előugró ablak, válassza a **titkos kulcs letöltése és az erőforrás létrehozása** lehetőséget. Ezzel letölti az SSH-kulcsot. PEM-fájlként.

   :::image type="content" source="./media/ssh-keys/download-key.png" alt-text="A titkos kulcs letöltése. PEM-fájlként":::

1. Ha letöltötte a. PEM-fájlt, előfordulhat, hogy valahol a számítógépén szeretné áthelyezni, ahol könnyen rámutathat az SSH-ügyfélre.


## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

A helyi számítógépen nyisson meg egy PowerShell-parancssort, és írja be a következőt:

```powershell
ssh -i <path to the .pem file> username@<ipaddress of the VM>
```

Írja be például a következőt: `ssh -i /Downloads/mySSHKey.pem azureuser@123.45.67.890`


## <a name="upload-an-ssh-key"></a>SSH-kulcs feltöltése

Feltöltheti az Azure-ban tárolt nyilvános SSH-kulcsot is. Az SSH-kulcspár létrehozásával kapcsolatos információkért lásd: SSH- [kulcsok használata a Linux rendszerű virtuális gépekhez való kapcsolódáshoz](./linux/ssh-from-windows.md).

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

1. A lap tetején írja be az *SSH* kifejezést a kereséshez. A **piactér* területen válassza az **ssh-kulcsok** elemet.

1. Az **SSH-kulcs** lapon válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="./media/ssh-keys/upload.png" alt-text="Az Azure-ban tárolandó nyilvános SSH-kulcs feltöltése":::

1. Az **erőforráscsoport** területen válassza az **új létrehozása** elemet, és hozzon létre egy új erőforráscsoportot a kulcsok tárolásához. Adja meg az erőforráscsoport nevét, majd kattintson **az OK gombra**.

1. A **régió** területen válasszon ki egy régiót a kulcsok tárolásához. Bármelyik régióban használhatja a kulcsokat, ez csak az a régió, ahol a rendszer tárolja őket.

1. Írja be a kulcs nevét a kulcspár **neve** mezőbe.

1. Az **SSH nyilvános kulcs forrása** területen válassza a **meglévő nyilvános kulcs feltöltése** lehetőséget. 

1. Illessze be a nyilvános kulcs teljes tartalmát a **feltöltési kulcsba** , majd válassza a **felülvizsgálat + létrehozás** elemet.

1. Az érvényesítés befejezése után válassza a **Létrehozás** lehetőséget. 

A kulcs feltöltése után dönthet úgy, hogy a virtuális gép létrehozásakor használja.

## <a name="list-keys"></a>Kulcsok listázása

A portálon létrehozott SSH-kulcsokat erőforrásként tárolja a rendszer, így az erőforrások nézetét szűrheti, így az összeset megjelenítheti.

1. A portálon válassza a **minden erőforrás** elemet.
1. A szűrők területen válassza a **típus** lehetőséget, majd törölje a jelölést az **összes kijelölése** lehetőséggel a lista törléséhez.
1. Írja be az **SSH** -t a szűrőbe, és válassza az **SSH-kulcs** lehetőséget.

   :::image type="content" source="./media/ssh-keys/filter.png" alt-text="Képernyőkép arról, hogyan szűrheti a listát az összes SSH-kulcs megtekintéséhez.":::

## <a name="get-the-public-key"></a>A nyilvános kulcs beszerzése

Ha a nyilvános kulcsra van szüksége, egyszerűen másolhatja a kulcsot a portál oldaláról. Csak sorolja fel a kulcsokat (az utolsó szakasz folyamatával), majd válasszon ki egy kulcsot a listából. Megnyílik a kulcs oldala, és a másolás a **vágólapra** ikonra kattintva a kulcs melletti Másolás gombra kattinthat.

## <a name="next-steps"></a>Következő lépések

További információ az SSH-kulcsok Azure-beli virtuális gépekkel való használatáról: [ssh-kulcsok használata a Linux rendszerű virtuális gépekhez való kapcsolódáshoz](./linux/ssh-from-windows.md).
