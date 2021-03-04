---
title: Kapcsolódás az Azure Percept DK-hoz SSH-kapcsolaton keresztül
description: Ismerje meg, hogyan hozhat SSH-t az Azure Percept DK-be a PuTTY segítségével
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 8dda18271de9b7d65246f0882ee7a68191031c05
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096615"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Kapcsolódás az Azure Percept DK-hoz SSH-kapcsolaton keresztül

Az alábbi lépéseket követve állíthatja be az Azure Percept DK-hoz való SSH-kapcsolatokat a [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)segítségével.

## <a name="prerequisites"></a>Előfeltételek

- Windows, Linux vagy OS X rendszerű gazdagép Wi-Fi képességgel
- SSH-ügyfél
    - Ha a gazdagépen a Windows fut, a [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) egy hatékony SSH-ügyfél, és az útmutatóban is szerepel.
    - Ha a gazdagépen Linux vagy OS X rendszer fut, az SSH-szolgáltatásokat az operációs rendszerek tartalmazzák, és külön ügyfélalkalmazás nélkül is futtathatók. Az SSH-szolgáltatások futtatásával kapcsolatos további információkért olvassa el az operációs rendszer termékdokumentációját.
- Azure Percept DK
- SSH bejelentkezési fiók beállítása az [Azure PERCEPT DK helyszíni](./quickstart-percept-dk-set-up.md) üzembe helyezési felületén

## <a name="initiate-the-ssh-connection"></a>Az SSH-kapcsolatok kezdeményezése

1. Az Azure Percept DK bekapcsolása (fejlesztői csomag)

1. Ha a fejlesztői csomag Ethernet-vagy Wi-Fi-kapcsolaton keresztül már csatlakozik egy hálózathoz, ugorjon a következő lépésre. Ellenkező esetben csatlakoztassa a gazdagépet közvetlenül a fejlesztői csomag Wi-Fi hozzáférési pontjához, ugyanúgy, mint bármely más Wi-Fi hálózathoz való csatlakozáshoz:
    - **hálózatnév**: scz-XXXX (ahol az "xxxx" a dev Kit Mac hálózati címe utolsó négy számjegye)
    - **Password (jelszó**): a fejlesztői csomaggal kapott üdvözlő kártyán található.

    > [!WARNING]
    > Amikor csatlakozik az Azure Percept DK Wi-Fi hozzáférési ponthoz, a gazdaszámítógép átmenetileg elveszti a kapcsolatát az internettel. Az aktív videokonferencia-hívások, a webes adatfolyamok vagy más hálózati élmények megszakadnak, amíg az Azure Percept DK-on belüli felületének 3. lépése be nem fejeződik.

1. Nyissa meg a PuTTY eszközt. Adja meg a következőt, majd kattintson a **Megnyitás** az SSH-val lehetőségre a fejlesztői készlet:

    1. Állomásnév: 10.1.1.1
    1. Port: 22
    1. Kapcsolattípus: SSH

    > [!NOTE]
    > Az **állomásnév** az eszköz IP-címe. Ha a fejlesztői csomag a fejlesztői csomag Wi-Fi hozzáférési pontjához csatlakozik, az IP-cím 10.1.1.1 lesz. Ha a fejlesztői csomag Ethernet-kapcsolaton keresztül csatlakozik, használja az eszköz helyi IP-címét, amelyet az Ethernet-útválasztóról vagy a központból szerezhet be. Ha az eszköz Wi-Fi-kapcsolaton keresztül csatlakozik, az [Azure PERCEPT DK helyszíni](./quickstart-percept-dk-set-up.md)környezetében gyűjtött IP-címet kell használnia.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Lemezkép.":::

1. Jelentkezzen be a PuTTY-terminálba az SSH-felhasználónévvel és jelszóval, amelyet a helyszíni folyamat során hoztak létre.

## <a name="next-steps"></a>Következő lépések

Miután sikeresen kapcsolódott az Azure Percept DK-hez az SSH-n keresztül, számos feladatot elvégezhet, például a hibaelhárítást, az USB-frissítéseket, valamint a DiagTool vagy a SoftAP eszköz futtatását.