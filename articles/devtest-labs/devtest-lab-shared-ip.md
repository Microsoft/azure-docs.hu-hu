---
title: A Azure DevTest Labs megosztott IP-címeinek ismertetése | Microsoft Docs
description: Megtudhatja, hogyan használja a Azure DevTest Labs megosztott IP-címeket a tesztkörnyezet virtuális gépei eléréséhez szükséges nyilvános IP-címek minimalizálására.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 36a5da1b2b6252d0adb480a622c461b33425e675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85484094"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>A Azure DevTest Labs megosztott IP-címeinek megismerése

Azure DevTest Labs lehetővé teszi, hogy a labor virtuális gépek ugyanazt a nyilvános IP-címet használják, hogy minimálisra csökkentsék az egyes laboratóriumi virtuális gépekhez való hozzáféréshez szükséges nyilvános IP-címek számát.  Ez a cikk a megosztott IP-címek működését és a hozzájuk kapcsolódó konfigurációs lehetőségeket ismerteti.

## <a name="shared-ip-setting"></a>Megosztott IP-beállítás

Labor létrehozásakor a rendszer egy virtuális hálózat alhálózatán hozza létre.  Alapértelmezés szerint ez az alhálózat a **megosztott nyilvános IP-cím engedélyezése** *Igen* értékre állításával jön létre.  Ez a konfiguráció létrehoz egy nyilvános IP-címet a teljes alhálózathoz.  A virtuális hálózatok és alhálózatok konfigurálásával kapcsolatos további információkért lásd: [virtuális hálózat konfigurálása Azure DevTest Labsban](devtest-lab-configure-vnet.md).

![Új Lab-alhálózat](media/devtest-lab-shared-ip/lab-subnet.png)

A meglévő Labs esetében engedélyezheti ezt a beállítást a **konfiguráció és a házirendek > a virtuális hálózatok** lehetőség kiválasztásával. Ezután válasszon ki egy virtuális hálózatot a listából, és válassza a **megosztott nyilvános IP-cím engedélyezése** egy kiválasztott alhálózathoz lehetőséget. Ezt a beállítást bármely tesztkörnyezetben letilthatja, ha nem szeretne nyilvános IP-címet megosztani a tesztkörnyezet virtuális gépei között.

A laborban létrehozott összes virtuális gép megosztott IP-címet használ.  A virtuális gép létrehozásakor ez a beállítás a **Speciális beállítások** lapon, az **IP-cím konfigurálása** területen figyelhető meg.

![Új virtuális gép](media/devtest-lab-shared-ip/new-vm.png)

- **Megosztott:** Minden **megosztottként** létrehozott virtuális gép egyetlen ERŐFORRÁSCSOPORT (RG) alá kerül. A RG számára egyetlen IP-cím van hozzárendelve, és a RG összes virtuális gépe ezt az IP-címet fogja használni.
- **Nyilvános:** Minden létrehozott virtuális gép saját IP-címmel rendelkezik, és a saját erőforráscsoporthoz jön létre.
- **Magánjellegű:** Minden létrehozott virtuális gép magánhálózati IP-címet használ. Nem lehet közvetlenül az internetről Távoli asztal használatával csatlakozni a virtuális géphez.

Ha egy megosztott IP-címmel rendelkező virtuális gép hozzá van adva az alhálózathoz, a DevTest Labs automatikusan hozzáadja a virtuális gépet egy terheléselosztó szolgáltatáshoz, és hozzárendeli a TCP-portszámot a nyilvános IP-címhez, amely a virtuális gép RDP-portjára lesz továbbítva.  

## <a name="using-the-shared-ip"></a>A megosztott IP-cím használata

- **Linux-felhasználók:** SSH-t a virtuális géphez az IP-cím vagy teljes tartománynév használatával, majd egy kettősponttal, majd a porttal. Az alábbi képen például a virtuális géphez való kapcsolódáshoz használt RDP-címnek kell lennie `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661` .

  ![VM-példa](media/devtest-lab-shared-ip/vm-info.png)

- **Windows-felhasználók:** A Azure Portal a **Csatlakozás** gombra kattintva letöltheti az előre konfigurált RDP-fájlt, és elérheti a virtuális gépet.

## <a name="next-steps"></a>Következő lépések

* [Tesztkörnyezet-házirendek meghatározása a Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Virtuális hálózat konfigurálása Azure DevTest Labsban](devtest-lab-configure-vnet.md)





