---
title: Kapcsolódás Linux rendszerű virtuális géphez Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Távoli asztalt a Linux rendszerű virtuális gépekhez a Azure Lab Servicesban található laborban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d13868477ff2e3378d87d7785789a7498ed17e59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85443417"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Kapcsolódás Linux rendszerű virtuális gépekhez a Azure Lab Services egy osztályterem laborjában
Ez a cikk bemutatja, hogyan csatlakozhatnak a tanulók a Linux rendszerű virtuális gépekhez (VM) egy tesztkörnyezetben a következő használatával:
- SSH (Secure Shell Protocol) terminál
- GUI (grafikus felhasználói felület) távoli asztal

> [!IMPORTANT] 
> Az SSH automatikusan konfigurálva van, így a tanulók és az oktatók további beállítások nélkül is csatlakozhatnak SSH-val Linux rendszerű virtuális gépekhez. Ha azonban a tanulóknak a grafikus felhasználói felület távoli asztalához kell csatlakozniuk, előfordulhat, hogy az oktatónak további telepítést kell végeznie.  Részletekért lásd: [a távoli asztal engedélyezése Linux rendszerű virtuális gépekhez](how-to-enable-remote-desktop-linux.md).

## <a name="connect-to-the-student-vm-using-ssh"></a>Kapcsolódás a tanuló virtuális géphez SSH használatával

1. Ha egy tanuló közvetlenül a Labs-portálra jelentkezik be ( `https://labs.azure.com` ) vagy egy regisztrációs hivatkozás használatával ( `https://labs.azure.com/register/<registrationCode>` ), akkor a tanuló által elérhetővé tett laborok csempéje megjelenik. 
   
1. A csempén állítsa be a gombot a virtuális gép elindításához, ha leállított állapotban van. 

2. Válassza a **Kapcsolódás** lehetőséget. Két lehetőség jelenik meg a virtuális géphez való kapcsolódáshoz: **SSH** és **RDP**.

    ![Tanulói virtuális gép – kapcsolatok beállításai](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. Válassza az **SSH** lehetőséget, és megjelenik a **Kapcsolódás virtuális géphez** párbeszédpanel:  

    ![SSH-kapcsolatok karakterlánca](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Kattintson a szövegmező melletti **Másolás** gombra az SSH-kapcsolódási adatok vágólapra másolásához. 

5. Mentse az SSH-kapcsolatok adatait, például a Text pad-t, így a következő lépésben használhatja ezt a kapcsolatfelvételi információt.

6. Egy SSH-terminálról (például a [Putty](https://www.putty.org/)-ról) csatlakozzon a virtuális géphez.

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>Kapcsolódás a tanuló virtuális géphez a GUI távoli asztal használatával
Az oktató dönthet úgy, hogy úgy konfigurálja a virtuális gépeket, hogy a tanulók a grafikus felhasználói felület távoli asztalával is csatlakozzanak.  Ebben az esetben a tanulóknak meg kell ismerniük az oktatótól, hogy a **Microsoft távoli asztal (RDP)** vagy a **X2Go** ügyfélalkalmazás használatával csatlakoznak-e a virtuális gépekhez.  Mindkét alkalmazás lehetővé teszi a tanulók számára, hogy távolról csatlakozzanak a virtuális géphez, és megjelenítik a Linux grafikus asztalt a helyi számítógépen.

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>Kapcsolódás a tanuló virtuális géphez Microsoft Távoli asztal (RDP) használatával
A tanulók a Microsoft Távoli asztal (RDP) használatával csatlakozhatnak a linuxos virtuális gépekhez, miután az oktatójuk beállítja a labort RDP-és GUI-csomagokkal egy linuxos grafikus asztali környezethez (például MATE, XFCE stb.). A kapcsolódás lépései a következők: 

1. A virtuális gép csempén ellenőrizze, hogy a virtuális gép fut-e, majd kattintson a **kapcsolat** elemre. Két lehetőség jelenik meg a virtuális géphez való kapcsolódáshoz: **SSH** és **RDP**.

    ![Tanulói virtuális gép – kapcsolatok beállításai](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. Válassza az **RDP** lehetőséget.  Ha az RDP-fájl le van töltve a gépre, mentse azt a virtuális gépre.

3. Ha Windows-számítógépről csatlakozik, általában a Microsoft Távoli asztal (RDP) ügyfél már telepítve van és konfigurálva van.  Ennek eredményeképpen mindössze annyit kell tennie, hogy az RDP-fájlra kattint a megnyitásához, és elindítja a távoli munkamenetet.

    Ehelyett ha Mac vagy Chromebook csatlakozik, tekintse át a következő lépéseket:
   - [Kapcsolódás virtuális GÉPHEZ RDP használatával Mac számítógépen](connect-virtual-machine-mac-remote-desktop.md).
   - [Kapcsolódás virtuális GÉPHEZ RDP használatával egy Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).  

### <a name="connect-to-the-student-vm-using-x2go"></a>Kapcsolódás a tanuló virtuális géphez a X2Go használatával
A tanulók a X2Go segítségével csatlakozhatnak a linuxos virtuális gépekhez, miután az oktatójuk beállítja a labort a X2Go-mel, valamint a Linux grafikus asztali környezethez készült grafikus csomagokat (például a MATE, az XFCE stb.).

A tanulóknak meg kell ismerniük az oktatótól, hogy az oktatójuk milyen linuxos grafikus asztali környezetben van telepítve.  Ezek az információk a X2Go-ügyfél használatával történő kapcsolódás következő lépéseiben szükségesek.

1. Telepítse a [X2Go-ügyfelet](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) a helyi számítógépre.

1. A virtuális gép SSH-kapcsolódási adatainak másolásához kövesse az [első szakasz](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) utasításait.  Ezekre az információkra szüksége lesz a X2Go-ügyfél használatával történő kapcsolódáshoz.

1. Az SSH-kapcsolat adatainak megnyitása után nyissa meg a X2Go-ügyfelet, és válassza a **munkamenet**  >  **új munkamenet** lehetőséget.
   ![Új munkamenet létrehozása a X2Go](./media/how-to-use-classroom-lab/x2go-new-session.png)

1. Adja meg az értékeket a **munkamenet-beállítások** ablaktáblán az SSH-kapcsolat adatai alapján.  A kapcsolódási adatok például a következőhöz hasonlóak lesznek:

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    Ebben a példában a következő értékeket kell megadni:

   - **Munkamenet neve** – adjon meg egy nevet, például a virtuális gép nevét.
   - **Gazdagép** – a virtuális gép azonosítója; például: **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** .
   - **Bejelentkezés** – a virtuális géphez tartozó Felhasználónév; például: **tanuló**.
   - **SSH-port** – a virtuális géphez rendelt egyedi port; például **12345**.
   - **Munkamenet típusa** – válassza ki azt a Linux grafikus asztali környezetet, amelyhez az oktató konfigurálta a virtuális gépet.  Ezeket az információkat az oktatótól kell megszereznie.

    Végül kattintson az **OK** gombra a munkamenet létrehozásához.

    ![X2Go-munkamenet beállításai](./media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  Kattintson a munkamenetre a jobb oldali ablaktáblán.

    ![X2Go új munkamenet elindítása](./media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > Ha a rendszer egy hasonló üzenettel kéri, válassza az **Igen** lehetőséget a jelszó megadásának folytatásához: **a (z) "[ `00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com` ]: 12345" gazdagép hitelességét nem lehet létrehozni.  A ECDSA kulcs ujjlenyomata SHA256:00000000000000000000000000000000000000000000. biztos, hogy folytatja a kapcsolódást (igen/nem)?**

2. Ha a rendszer kéri, adja meg a jelszavát, majd kattintson **az OK** gombra.  Ekkor távolról csatlakozik a virtuális gép grafikus felhasználói felületének asztali környezetéhez.

## <a name="next-steps"></a>Következő lépések
A Linux rendszerű virtuális gépekhez készült Távoli asztali kapcsolat funkciónak a tantermi laborban való engedélyezésével kapcsolatban lásd: a [Távoli asztal használatának engedélyezése a linuxos virtuális gépek számára](how-to-enable-remote-desktop-linux.md). 

