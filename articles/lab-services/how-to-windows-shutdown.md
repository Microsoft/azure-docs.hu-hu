---
title: Útmutató a Windows leállítási viselkedésének szabályozásához a Azure Lab Servicesban | Microsoft Docs
description: A tétlen Windowsos virtuális gépek automatikus leállításának és a Windows leállítás parancs eltávolításának lépései.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: 248bbeabaf704ba636e2f82c7a93d0ee90a09f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647698"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Útmutató a Windows leállítási viselkedésének szabályozásához

A Azure Lab Services számos költségtakarékos szolgáltatást biztosít annak biztosítására, hogy a Windows rendszerű virtuális gépek (VM-EK) ne fussanak váratlanul:
 - [Ütemterv beállítása](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
 - [Kvóták beállítása a felhasználók számára](./how-to-configure-student-usage.md#set-quotas-for-users)
 - [Automatikus leállítás engedélyezése a kapcsolat bontásakor](./how-to-enable-shutdown-disconnect.md)

Ezek a költségadatok még akkor is előfordulhatnak, ha egy Windows rendszerű virtuális gép váratlanul tovább fut; Ennek eredményeképpen a tanulói kvóta levonása:

- **Az RDP-ablak nyitva marad**
  
    Ha egy diák RDP-kapcsolaton keresztül kapcsolódik a virtuális géphez, előfordulhat, hogy az RDP-ablakot véletlenül megnyitva hagyja.  Amíg az RDP-ablak nyitva marad, a **leválasztási beállítás automatikus leállítása** nem lép érvénybe, mert csak az RDP-munkamenet leválasztása után aktiválódik.

- **A Windows Shutdown parancs a virtuális gép kikapcsolására szolgál**
  
    A tanulók a Windows leállítási parancsát vagy a Windowson belül elérhető egyéb leállítási mechanizmusokat is használhatják a virtuális gép kikapcsolására [Azure Lab Services "Leállítás gomb](./how-to-use-classroom-lab.md#start-or-stop-the-vm)használata helyett.  Ha ez történik, a Azure Lab Services szempontjából a virtuális gép továbbra is használatban van.
    
Ennek az útmutatónak a segítségével megakadályozhatja, hogy ezek a helyzetek ne legyenek elérhetők. Ez az útmutató a Windows rendszerű virtuális gépek automatikus leállítását és a **Start** menüből történő eltávolítását ismerteti.  

> [!NOTE]
> Előfordulhat, hogy a virtuális gép váratlanul levonja a kvótát, ha a tanuló elindítja a virtuális gépet, de soha nem kapcsolódik az RDP használatával.  Ez az útmutató jelenleg *nem* foglalkozik ezzel a forgatókönyvvel.  Ehelyett a diákoknak emlékeztetni kell a virtuális géphez való azonnali kapcsolódásra RDP használatával az indítás után. vagy le kell állítania a virtuális gépet.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>A Windows leállítás parancs eltávolítása a Start menüből

A Windows **helyi csoportházirend** beállításai lehetővé teszik a leállítási parancs eltávolítását is a **Start** menüből.

A leállítási parancs eltávolításához csatlakozhat a sablon virtuális géphez, és végrehajthatja az alábbi PowerShell-szkriptet.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

A következő manuális lépéseket is végrehajthatja a sablon virtuális gép használatával:

1. Nyomja le a Windows billentyűt, írja be a következőt: **gpedit**, majd válassza a **csoportházirend szerkesztése (Vezérlőpult)** lehetőséget.

1. Nyissa meg a **Számítógép konfigurációja > Felügyeleti sablonok > a Start menü és a tálca menüpontot**.  

    ![Helyi csoportházirend-szerkesztő](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Kattintson a jobb gombbal az Eltávolítás elemre, **és tiltsa le a leállítási, újraindítási, alvó és hibernált parancsok elérését**, majd kattintson a **Szerkesztés** elemre.

1. Jelölje be az **engedélyezett** beállítást, majd kattintson **az OK** gombra:
 
   ![Leállítás beállítása](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Figyelje meg, hogy a leállítási parancs már nem jelenik meg a Windows **Start** menüjében; csak a **leválasztási** parancs jelenik meg.

    ![Leállítás parancs](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Következő lépések
Tekintse meg a Windows-sablonos virtuális gép előkészítéséről szóló cikket: [útmutató a Windows-sablonok számítógépének beállításához Azure Lab Services](how-to-prepare-windows-template.md)