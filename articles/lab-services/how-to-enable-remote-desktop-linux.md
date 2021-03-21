---
title: A Linux rendszerhez készült grafikus távoli asztal engedélyezése a Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti a Távoli asztalt a Linux rendszerű virtuális gépek számára a Azure Lab Servicesban található laborban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 34c940fec388bb0e79ab5e1db9be6d52fb223873
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647953"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>A grafikus távoli asztal engedélyezése a Linux rendszerű virtuális gépek számára a Azure Lab Servicesban
Ez a cikk bemutatja, hogyan végezheti el a következő feladatokat:

- Grafikus távoli asztali munkamenetek engedélyezése Linux rendszerű virtuális gépekhez
- Kapcsolódás Linux rendszerű virtuális géphez RDP (RDP protokoll) vagy X2Go távoli asztali ügyfelek használatával

## <a name="set-up-graphical-remote-desktop-solution"></a>Grafikus távoli asztali megoldás beállítása
Ha egy tesztkörnyezet **Linux** -rendszerképből lett létrehozva, az **SSH** (Secure Shell) hozzáférés automatikusan konfigurálva van, így az oktató a parancssorból SSH használatával tud csatlakozni a sablon virtuális géphez.  Hasonlóképpen, a sablon virtuális gépe közzétételekor a tanulók is csatlakozhatnak a virtuális gépekhez az SSH használatával.

A Linux rendszerű virtuális **gépek grafikus felhasználói** felülettel való összekapcsolásához használja a következőt: **RDP** vagy **X2Go**.  Mindkét beállításhoz szükséges, hogy az oktató további beállításokat végezzen a sablon virtuális gépén:

### <a name="rdp-setup"></a>RDP-beállítás
Az RDP használatához az oktatónak a következőket kell tennie:
  - Távoli asztali kapcsolat engedélyezése; erre kifejezetten szükség van a virtuális gép RDP-portjának megnyitásához.
  - Telepítse az RDP távoli asztali kiszolgálót.
  - Linux grafikus asztali környezet (például a MATE, az XFCE stb.) telepítése.

### <a name="x2go-setup"></a>X2Go-telepítő
A X2Go használatához az oktatónak a következőket kell tennie:
- Telepítse a X2Go távoli asztali kiszolgálót.
- Linux grafikus asztali környezet (például a MATE, az XFCE stb.) telepítése.

A X2Go ugyanazt a portot használja, amely már engedélyezve van az SSH-hoz.  Ennek eredményeképpen nincs szükség további konfigurálásra a virtuális gépen a X2Go-port megnyitásához.

> [!NOTE]
> Bizonyos esetekben, például Ubuntu LTS 18,04 esetén a X2Go jobb teljesítményt nyújt.  Ha a grafikus asztali környezettel való interakció során RDP-és késési késleltetést használ, érdemes lehet a X2Go próbálkozni, mivel ez javíthatja a teljesítményt.

> [!IMPORTANT]
>  Egyes piactér-lemezképek már telepítve vannak grafikus asztali környezettel és távoli asztali kiszolgálóval.  Például a [linuxos Data Science Virtual Machine (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) már rendelkezik [telepített és konfigurált Xfce-és X2Go-kiszolgálóval, hogy fogadja az ügyfélkapcsolatokat](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#x2go).

## <a name="enable-remote-desktop-connection-for-rdp"></a>Távoli asztali kapcsolat engedélyezése RDP-hez

Ez a lépés csak az RDP használatával való kapcsolódáshoz szükséges.  Ha ehelyett a X2Go használatát tervezi, ugorjon a következő szakaszra, mivel a X2Go az SSH-portot használja.

1.  A labor létrehozása során az oktató **lehetővé teszi távoli asztali kapcsolat engedélyezését**.  Az oktatónak **engedélyeznie kell ezt a** beállítást ahhoz, hogy megnyissa a PORTOT az RDP távoli asztali munkamenethez szükséges linuxos virtuális gépen.  Ellenkező esetben, ha ez a beállítás **le van tiltva**, csak az SSH-port van megnyitva.
  
    ![Képernyőfelvétel: az "új Labor" ablak az "Enable Távoli asztali kapcsolat" lehetőséggel.](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. A **Távoli asztali kapcsolati üzenet engedélyezése** párbeszédpanelen válassza a **Folytatás a távoli asztal** lehetőséget. 

    ![Távoli asztali kapcsolat engedélyezése Linux-rendszerképekhez](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>RDP-vagy X2Go telepítése

A labor létrehozása után az oktatónak meg kell győződnie arról, hogy egy grafikus asztali környezet és egy távoli asztali kiszolgáló telepítve van a sablon virtuális gépre.  Az oktatóknak először SSH-val kell csatlakozniuk a sablonhoz a következő csomagok telepítéséhez:
- Az RDP vagy a X2Go távoli asztali kiszolgáló.
- Egy grafikus asztali környezet, például a MATE, az XFCE stb.

A beállítás után az oktató csatlakozhat a sablon virtuális géphez a **Microsoft távoli asztal (RDP)** ügyfél vagy a **X2Go** -ügyfél használatával.

A sablon virtuális gép beállításához kövesse az alábbi lépéseket:

1. Ha a **sablon testreszabása** elemet látja az eszköztáron, válassza ki azt. Ezután válassza a **Continue (folytatás** ) lehetőséget a **sablon testreszabása** párbeszédpanelen. Ez a művelet elindítja a sablon virtuális gépet.  

    ![Sablon testreszabása](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. A sablon virtuális gépe elindítása után kiválaszthatja a **sablon összekapcsolása** lehetőséget, majd az eszköztáron **keresztül csatlakozhat SSH-n keresztül** . 

    ![Kapcsolódás sablonhoz RDP-kapcsolaton keresztül a tesztkörnyezet létrehozása után](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. Megjelenik a következő **Kapcsolódás a virtuális géphez** párbeszédpanel. Kattintson a szövegmező melletti **Másolás** gombra a vágólapra másoláshoz. Mentse az SSH-kapcsolatok adatait. Használja ezt a kapcsolódási információt egy SSH-terminálról (például a [Putty](https://www.putty.org/)-ból) a virtuális géphez való kapcsolódáshoz.
 
    ![SSH-kapcsolatok karakterlánca](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Telepítse az RDP-t vagy a X2Go-t az Ön által választott grafikus asztali környezettel együtt.  Tekintse át az alábbi utasításokat:
    - [Az RDP telepítése és konfigurálása](../virtual-machines/linux/use-remote-desktop.md)
    - [X2Go telepítése és konfigurálása](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop)

## <a name="connect-to-the-template-vm-via-the-gui"></a>Kapcsolódás a sablon virtuális géphez a grafikus felhasználói felületen keresztül

A sablon virtuális gép beállítása után az oktató a **Microsoft távoli asztal (RDP)** ügyfél vagy a **X2Go** -ügyfél használatával tud kapcsolatot létesíteni a grafikus felhasználói felületen keresztül.  A használt ügyfél attól függ, hogy az RDP vagy a X2Go távoli asztali kiszolgálóként van-e konfigurálva a sablon virtuális gépén.  

### <a name="microsoft-remote-desktop-rdp-client"></a>Microsoft Távoli asztal (RDP) ügyfél

A Microsoft Távoli asztal-(RDP-) ügyfél egy RDP-konfigurációval rendelkező sablonos virtuális géphez való kapcsolódásra szolgál.  A Távoli asztal ügyfél Windows, Chromebook és Mac rendszereken is használható.  További részletekért tekintse meg [Távoli asztal-ügyfelekről](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) szóló cikket.

Kövesse az alábbi lépéseket a sablon virtuális géphez való kapcsolódáshoz használt számítógép típusától függően:

- Windows
  1. Kattintson a **Kapcsolódás sablonhoz** lehetőségre a Labor eszköztárán, majd válassza a **Kapcsolódás RDP** -kapcsolaton keresztül lehetőséget a sablon virtuális géphez való kapcsolódáshoz. 
  1. Mentse az RDP-fájlt, és a használatával csatlakozzon a sablon virtuális géphez a Távoli asztal-ügyféllel. 
  1. Általában a Távoli asztal ügyfél már telepítve van és konfigurálva van a Windows rendszeren.  Ennek eredményeképpen mindössze annyit kell tennie, hogy az RDP-fájlra kattint a megnyitásához, és elindítja a távoli munkamenetet.

- Mac
  1. Kattintson a **Kapcsolódás sablonhoz** elemre a Labor eszköztárán, majd válassza a **Kapcsolódás RDP-n keresztül** lehetőséget az RDP-fájl mentéséhez.  
  1. Ezt követően tekintse át a [Kapcsolódás a virtuális GÉPHEZ RDP használatával Mac](connect-virtual-machine-mac-remote-desktop.md)számítógépen című témakör útmutatását.

- Chromebook
  1. Kattintson a **Kapcsolódás sablonhoz** elemre a Labor eszköztárán, majd válassza a **Kapcsolódás RDP-n keresztül** lehetőséget az RDP-fájl mentéséhez.  
  1. Ezt követően tekintse meg a [Kapcsolódás a virtuális géphez a CHROMEBOOK RDP használatával](connect-virtual-machine-chromebook-remote-desktop.md)című cikket.

### <a name="x2go-client"></a>X2Go-ügyfél

A X2Go-ügyfél a X2Go konfigurált virtuálisgép-virtuális gépekhez való kapcsolódásra szolgál.  A virtuális gép SSH-kapcsolati adatainak használatával kövesse a útmutató a [virtuális géphez való kapcsolódás a X2Go használatával](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go)című témakör lépéseit.

## <a name="next-steps"></a>Következő lépések
Miután az oktató létrehozta a sablon virtuális gépén az RDP-t vagy a X2Go-t, és közzéteszi, a tanulók a grafikus felhasználói felület távoli asztalán vagy SSH-n keresztül kapcsolódhatnak a virtuális gépekhez.

További információkért lásd:
 - [Csatlakozás Linux rendszerű virtuális géphez](how-to-use-remote-desktop-linux-student.md)