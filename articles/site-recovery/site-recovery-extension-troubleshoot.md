---
title: A vész-helyreállítási Azure-beli virtuálisgép-bővítmény hibáinak elhárítása Azure Site Recovery
description: Az Azure virtuálisgép-bővítményével kapcsolatos hibák elhárítása Azure Site Recovery-mel.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009704"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Azure-beli virtuális gépek bővítményeivel kapcsolatos hibaelhárítás

Ez a cikk hibaelhárítási lépéseket tartalmaz, amelyek segítségével megoldhatja a virtuálisgép-ügynökkel és-bővítménysel kapcsolatos Azure Site Recovery hibákat.

## <a name="low-system-resources"></a>Alacsony rendszererőforrások

Ez a probléma akkor fordul elő, ha a rendszer alacsony rendelkezésre állású memóriával rendelkezik, és nem tud memóriát lefoglalni a mobilitási szolgáltatás telepítéséhez. Győződjön meg arról, hogy a telepítés folytatásához és sikeres befejezéséhez elegendő memória van kiszabadítva.

## <a name="azure-site-recovery-extension-time-out"></a>Azure Site Recovery bővítmény időtúllépése  

Hibaüzenet: "a feladat végrehajtása túllépte az időkorlátot a bővítmény műveletének elindításához."<br>
Hibakód: "151076"

 Azure Site Recovery telepített egy bővítményt a virtuális gépen a védelem engedélyezése feladatok részeként. A következő feltételek bármelyike megakadályozhatja a védelem aktiválását, és a feladat meghibásodását okozhatja. Hajtsa végre az alábbi hibaelhárítási lépéseket, majd próbálja megismételni a műveletet:

- [Az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [A virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [A Site Recovery bővítmény frissítése vagy betöltése sikertelen](#the-site-recovery-extension-fails-to-update-or-load)

Hibaüzenet: "az előző Site Recovery bővítmény művelet a vártnál több időt vesz figyelembe."<br>
Hibakód: "150066"

- [Az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [A virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [A Site Recovery bővítmény állapota helytelen](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>A védelem sikertelen, mert a virtuálisgép-ügynök nem válaszol

Hibaüzenet: "a feladat végrehajtása túllépte az időkorlátot a bővítmény műveletének elindításához."<br>
Hibakód: "151099"

Ez a hiba akkor fordulhat elő, ha a virtuális gépen lévő Azure Guest Agent ügynök nem üzemkész állapotban van.

Az Azure Guest Agent állapotát a [Azure Portalban](https://portal.azure.com/)tekintheti meg. Lépjen a védelemmel ellátni kívánt virtuális gépre, és tekintse meg az állapotot a **virtuálisgép-**  >  **Beállítások**  >  **Tulajdonságok**  >  **ügynökének állapota** mezőben. A legtöbb esetben az ügynök állapota a virtuális gép újraindítása után készen áll. Ha azonban nem tudja újraindítani a problémát, vagy még mindig szembesül a problémával, hajtsa végre az alábbi hibaelhárítási lépéseket:

- [Az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [A virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Hibaüzenet: "a feladat végrehajtása túllépte az időkorlátot a bővítmény műveletének elindításához."<br>
Hibakód: "151095"

Ez a hiba akkor fordul elő, ha a Linux rendszerű számítógépen elavult az ügynök verziója. Hajtsa végre a következő hibaelhárítási lépést:

- [A virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Okok és megoldások

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Az ügynök telepítve van a virtuális gépen, de nem válaszol (Windows rendszerű virtuális gépek esetén)

#### <a name="solution"></a>Megoldás
Lehet, hogy a virtuálisgép-ügynök sérült, vagy a szolgáltatást leállították. A virtuálisgép-ügynök újratelepítése segíti a legújabb verzió beszerzését. A szolgáltatással folytatott kommunikáció újraindítását is lehetővé teszi.

1. Állapítsa meg, hogy a Windows Azure Guest Agent szolgáltatás fut-e a virtuálisgép-szolgáltatásokban (Services. msc). Indítsa újra a Windows Azure Guest Agent szolgáltatást.    
1. Ha a Windows Azure Guest Agent ügynök szolgáltatás nem látható a szolgáltatások területen, nyissa meg a Vezérlőpultot. Lépjen a **programok és szolgáltatások elemre, és** ellenőrizze, hogy telepítve van-e a Windows Guest Agent szolgáltatás.
1. Ha a Windows Azure Guest Agent ügynök megjelenik a **programok és szolgáltatások szolgáltatásban**, távolítsa el a Windows Azure Guest Agent ügynököt.
1. Töltse le és telepítse az [Agent MSI legújabb verzióját](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). A telepítés befejezéséhez rendszergazdai jogosultságok szükségesek.
1. Győződjön meg arról, hogy a Windows Azure Guest Agent szolgáltatás megjelenik a szolgáltatások között.
1. Indítsa újra a védelmi feladatot.

Ellenőrizze azt is, hogy a [Microsoft .NET 4,5 telepítve van](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) -e a virtuális gépen. A szolgáltatással való kommunikációhoz a virtuálisgép-ügynökhöz .NET 4,5 szükséges.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>A virtuális gépen telepített ügynök elavult (Linux rendszerű virtuális gépek esetén)

#### <a name="solution"></a>Megoldás
A Linux rendszerű virtuális gépek esetében a legtöbb ügynökkel kapcsolatos vagy kiterjesztéssel kapcsolatos hibát az elavult virtuálisgép-ügynököt érintő problémák okozzák. A probléma megoldásához kövesse az alábbi általános irányelveket:

1. Kövesse a Linux rendszerű [virtuális gép ügynökének frissítésével](../virtual-machines/extensions/update-linux-agent.md)kapcsolatos utasításokat.

   > [!NOTE]
   > Javasoljuk *, hogy csak* terjesztési tárházon keresztül frissítse az ügynököt. Nem javasoljuk, hogy közvetlenül a GitHubról töltse le az ügynököt, és frissítse azt. Ha a disztribúcióhoz tartozó legújabb ügynök nem érhető el, a telepítésével kapcsolatos útmutatásért forduljon az elosztási támogatáshoz. A legutóbbi ügynök kereséséhez nyissa meg a [Windows Azure Linux Agent](https://github.com/Azure/WALinuxAgent/releases) lapot a GitHub-tárházban.

1. A következő parancs futtatásával győződjön meg arról, hogy az Azure-ügynök fut a virtuális gépen: `ps -e`

   Ha a folyamat nem fut, indítsa újra a következő parancsok használatával:

   - Ubuntu esetén: `service walinuxagent start`
   - Egyéb disztribúciók esetén: `service waagent start`

1. [Konfigurálja az automatikus újraindítási ügynököt](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Engedélyezze a virtuális gép védelmét.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>A Site Recovery bővítmény frissítése vagy betöltése sikertelen

A bővítmény állapota "Empty", "nem letaposott" vagy "áttérés".

#### <a name="solution"></a>Megoldás

Távolítsa el a bővítményt, majd indítsa újra a műveletet.

A bővítmény eltávolítása:

1. A [Azure Portal](https://portal.azure.com/)lépjen a biztonsági mentési hibát észlelő virtuális gépre.
1. Válassza a **Beállítások** lehetőséget.
1. Válassza a **Bővítmények** lehetőséget.
1. Válassza ki **site Recovery bővítményt**.
1. Válassza az **Eltávolítás** lehetőséget.

Linux rendszerű virtuális gépek esetén, ha a VMSnapshot bővítmény nem jelenik meg a Azure Portalban, [frissítse az Azure Linux-ügynököt](../virtual-machines/extensions/update-linux-agent.md). Ezután futtassa a védelmet.

A lépések végrehajtásakor a bővítmény újratelepítése a védelem során történik.
