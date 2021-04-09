---
title: Kapcsolódás az Azure Percept DK-hoz SSH-kapcsolaton keresztül
description: Ismerje meg, hogyan hozhat SSH-t az Azure Percept DK-be a PuTTY segítségével
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39ee1c1cc5b52dc62e3199536234c1f7d9381436
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721477"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Kapcsolódás az Azure Percept DK-hoz SSH-kapcsolaton keresztül

Az alábbi lépéseket követve állíthatja be az Azure Percept DK-hoz való SSH-kapcsolatokat az OpenSSH vagy a [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)segítségével.

## <a name="prerequisites"></a>Előfeltételek

- Windows, Linux vagy OS X rendszerű gazdagép Wi-Fi képességgel
- Egy SSH-ügyfél (lásd a telepítési útmutató következő szakaszát)
- Egy Azure Percept DK (fejlesztői csomag)
- Az [Azure PERCEPT DK telepítési felületén](./quickstart-percept-dk-set-up.md) létrehozott ssh-bejelentkezés

## <a name="install-your-preferred-ssh-client"></a>Az előnyben részesített SSH-ügyfél telepítése

Ha a gazdagépen Linux vagy OS X rendszer fut, az SSH-szolgáltatásokat az operációs rendszerek tartalmazzák, és külön ügyfélalkalmazás nélkül is futtathatók. Az SSH-szolgáltatások futtatásával kapcsolatos további információkért olvassa el az operációs rendszer termékdokumentációját.

Ha a gazdaszámítógépen Windows fut, akkor lehet, hogy két SSH-ügyfél közül választhat: OpenSSH és Putty.

### <a name="openssh"></a>OpenSSH

A Windows 10 tartalmaz egy OpenSSH nevű beépített SSH-ügyfelet, amely egy parancssorban egy egyszerű parancs használatával futtatható. Azt javasoljuk, hogy az OpenSSH-t az Azure Percept használja, ha az elérhető az Ön számára. A következő lépésekkel ellenőrizheti, hogy a Windows rendszerű számítógépén van-e telepítve az OpenSSH:

1. Lépjen a **Start**  ->  **Beállítások menüpontra**.

1. Válassza az **alkalmazások** lehetőséget.

1. Az **alkalmazások & szolgáltatások** területen válassza a **választható funkciók** lehetőséget.

1. Írja be az **OpenSSH-ügyfelet** a **telepített szolgáltatások** keresési sávjába. Ha az OpenSSH megjelenik, az ügyfél már telepítve van, és a következő szakaszra léphet. Ha nem látja az OpenSSH-t, kattintson **a szolgáltatás hozzáadása** lehetőségre.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="Képernyőkép az OpenSSH telepítési állapotát bemutató beállításokról.":::

1. Válassza az **OpenSSH-ügyfél** lehetőséget, majd kattintson a **telepítés** gombra. Most már a következő szakaszra léphet. Ha az OpenSSH nem érhető el a számítógépre való telepítéshez, kövesse az alábbi lépéseket a PuTTY, egy külső SSH-ügyfél telepítéséhez.

### <a name="putty"></a>PuTTY

Ha a Windows-számítógép nem tartalmazza az OpenSSH-t, javasoljuk, hogy használja a [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)-t. A PuTTY letöltéséhez és telepítéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a [Putty letöltési lapját](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. A **csomag Files** alatt kattintson a 32 bites vagy 64 bites. msi fájlra a telepítő letöltéséhez. Ha nem biztos abban, hogy melyik verziót szeretné kiválasztani, tekintse meg a [gyakori kérdéseket](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit).

1. A telepítési folyamat elindításához kattintson a telepítőre. Kövesse az utasításokat igény szerint.

1. Gratulálunk! Sikeresen telepítette a PuTTY SSH-ügyfelet.

## <a name="initiate-the-ssh-connection"></a>Az SSH-kapcsolatok kezdeményezése

1. Kapcsolja be az Azure Percept DK-t.

1. Ha a fejlesztői csomag Ethernet-vagy Wi-Fi-kapcsolaton keresztül már csatlakozik egy hálózathoz, ugorjon a következő lépésre. Ellenkező esetben a gazdagépet közvetlenül a fejlesztői csomag Wi-Fi hozzáférési pontjához kell kötni. Ha más Wi-Fi hálózathoz szeretne csatlakozni, nyissa meg a hálózati és internetes beállításokat a számítógépen, kattintson a következő hálózatra, majd adja meg a hálózati jelszót, ha a rendszer kéri:

    - **Hálózat neve**: a fejlesztői csomag operációs rendszerének verziójától függően az Wi-Fi hozzáférési pont neve **scz-XXXX** vagy **APD-XXXX** (ahol az "xxxx" a fejlesztői csomag MAC-címe utolsó négy számjegye)
    - **Password (jelszó**): a fejlesztői csomaggal kapott üdvözlő kártyán található.

    > [!WARNING]
    > Amikor csatlakozik az Azure Percept DK Wi-Fi hozzáférési ponthoz, a gazdaszámítógép átmenetileg elveszti a kapcsolatát az internettel. Az aktív videokonferencia-hívások, a webes streaming vagy más hálózati élmények megszakadnak.

1. Fejezze be az SSH-kapcsolódási folyamatot az SSH-ügyfélnek megfelelően.

### <a name="using-openssh"></a>Az OpenSSH használata

1. Nyisson meg egy parancssort (**indítsa el** a  ->  **parancssort**).

1. Írja be a következőt a parancssorba:

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    Ha a számítógép a fejlesztői csomag Wi-Fi hozzáférési pontjához csatlakozik, az IP-cím 10.1.1.1 lesz. Ha a fejlesztői csomag Ethernet-kapcsolaton keresztül csatlakozik, használja az eszköz helyi IP-címét, amelyet az Ethernet-útválasztóról vagy a központból szerezhet be. Ha a fejlesztői csomag Wi-Fi-kapcsolaton keresztül csatlakozik, a fejlesztői csomaghoz rendelt IP-címet kell használnia az [Azure PERCEPT DK telepítési felületén](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Ha a fejlesztői csomag egy Wi-Fi hálózathoz csatlakozik, de nem ismeri az IP-címét, lépjen az Azure Percept studióba, és [nyissa meg az eszköz video streamjét](./how-to-view-video-stream.md). Az eszköz IP-címe a videó stream böngésző lap címsorában jelenik meg.

1. Ha a rendszer kéri, adja meg az SSH-jelszavát.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="Képernyőkép a nyílt SSH-parancssor bejelentkezéséről.":::

1. Ha első alkalommal csatlakozik a fejlesztői csomaghoz az OpenSSH-n keresztül, a rendszer kérheti, hogy fogadja el a gazdagép kulcsát is. A kulcs elfogadásához adja meg az **Igen** értéket.

1. Gratulálunk! Sikeresen csatlakozott a fejlesztői csomaghoz SSH-kapcsolaton keresztül.

### <a name="using-putty"></a>A PuTTY használata

1. Nyissa meg a PuTTY eszközt. Adja meg a következőt a **Putty konfigurációs** ablakban, és kattintson a **Megnyitás** az SSH-ba lehetőségre a fejlesztői csomagban:

    1. Állomásnév: [IP-cím]
    1. Port: 22
    1. Kapcsolattípus: SSH

    A **gazdagép neve** a fejlesztői csomag IP-címe. Ha a számítógép a fejlesztői csomag Wi-Fi hozzáférési pontjához csatlakozik, az IP-cím 10.1.1.1 lesz. Ha a fejlesztői csomag Ethernet-kapcsolaton keresztül csatlakozik, használja az eszköz helyi IP-címét, amelyet az Ethernet-útválasztóról vagy a központból szerezhet be. Ha a fejlesztői csomag Wi-Fi-kapcsolaton keresztül csatlakozik, a fejlesztői csomaghoz rendelt IP-címet kell használnia az [Azure PERCEPT DK telepítési felületén](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Ha a fejlesztői csomag egy Wi-Fi hálózathoz csatlakozik, de nem ismeri az IP-címét, lépjen az Azure Percept studióba, és [nyissa meg az eszköz video streamjét](./how-to-view-video-stream.md). Az eszköz IP-címe a videó stream böngésző lap címsorában jelenik meg.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="A PuTTY konfigurációs ablak képernyőképe.":::

1. Ekkor megnyílik egy Putty-terminál. Ha a rendszer kéri, adja meg az SSH-felhasználónevét és-jelszavát a terminálban.

1. Gratulálunk! Sikeresen csatlakozott a fejlesztői csomaghoz SSH-kapcsolaton keresztül.

## <a name="next-steps"></a>Következő lépések

Miután SSH-n keresztül kapcsolódott az Azure Percept DK-hoz, számos feladatot végrehajthat, például az [eszközök hibaelhárítását](./troubleshoot-dev-kit.md) és az [USB-frissítéseket](./how-to-update-via-usb.md).