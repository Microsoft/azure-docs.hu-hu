---
title: Azure Percept – ismert problémák
description: További információ az Azure Percept ismert problémáiról és azok megkerülő megoldásairól
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: reference
ms.date: 03/25/2021
ms.openlocfilehash: 49ac497505930d82a3ab8e90fb3f386cc1741dc7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605059"
---
# <a name="known-issues"></a>Ismert problémák

Ha ezekkel a problémákkal találkozik, nem kell megnyitnia egy hibát. Ha problémái vannak a megkerülő megoldások valamelyikével, nyisson meg egy problémát.

|Terület|A probléma leírása|Áthidaló megoldás|
|-------|---------|---------|
| Bevezetési élmény | Nem lehet befejezni a bevezetési folyamatot, kivéve, ha az eszköz Wi-Fi konfigurálva van (az Azure-bejelentkezés sikertelen). | 1. [SSH az Azure PERCEPT DK-be](./how-to-ssh-into-percept-dk.md). <br> 2. azonosítsa és másolja az eszköz Ethernet IP-címét. <br> 3. kapcsolódjon a bevezetési élményhez az Ethernet IP-alapú URL-cím használatával. |
| Bevezetési élmény | A végfelhasználói licencszerződésben (licencszerződés) található hivatkozásokra kattintva időnként nem nyílik meg új weblap. | Másolja a hivatkozást, és nyissa meg egy külön böngészőablakban. |
| Bevezetési élmény | Nem végezhető el a bevezetési folyamat a mobil Wi-Fi-hozzáférési pontokhoz való csatlakozáskor. | Az eszköz csatlakoztatása közvetlenül a SoftAP, Wi-Fi hálózathoz vagy Ethernet-kapcsolaton keresztüli hálózatra. |
| Wi-Fi | A SoftAP időnként leválasztható vagy eltűnnek. | Vizsgáljuk.  Az eszköz újraindítása általában visszakerül. |
| Wi-Fi | A Wi-Fi SoftAP be-és kikapcsoló hardveres gomb néha nem működik. | Folytassa a gomb megnyomásával vagy az eszköz újraindításával. |
| Wi-Fi | A Wi-Fi-kapcsolathoz való csatlakozást követően a felhasználók a következő üzenet jelenhetnek meg: <br> "Ez a Wi-Fi-hálózat egy régebbi biztonsági szabványt használ." | A fejlesztői készlet SoftAP a WEP titkosítási algoritmust használja. |
| Wi-Fi | Nem lehet csatlakozni a SoftAP a Windows 10 rendszerű SZÁMÍTÓGÉPRŐL a következő hibaüzenettel: <br> "Nem lehet csatlakozni ehhez a hálózathoz" | Indítsa újra a fejlesztői készlet és a számítógépet. |
| Eszköz frissítése | A tárolók nem futnak OTA-frissítés után. | SSH-t az eszközre, és indítsa újra a IoT Edge tárolót a következő paranccsal: `systemctl restart iotedge` . Ezzel újraindítja az összes tárolót. |
| Eszköz frissítése | A felhasználók egy üzenetet kaphatnak arról, hogy a frissítés sikertelen volt, még akkor is, ha az sikeres volt. | Erősítse meg a frissítést úgy, hogy megkeresi a fejlesztői készlet eszközének két IoT Hub, és ellenőrzi a értékét `swVersion` . Ez az első frissítés után megoldódott. |
| Eszköz frissítése | Az első frissítés után a felhasználók elveszítik Wi-Fi kapcsolati beállításait. | A Wi-Fi-kapcsolatok beállításához a frissítés után futtassa a bevezetési élményt. Ez az első frissítés után megoldódott. |
| Eszköz frissítése | Az OTA-frissítés végrehajtása után a felhasználók már nem jelentkezhetnek be SSH-n keresztül a korábban létrehozott felhasználói fiókok használatával, és az új SSH-felhasználók nem hozhatók létre a bevezetési felületen keresztül. Ez a probléma a következő előre telepített rendszerkép-verzióktól származó, az OTA-frissítéseket végrehajtó rendszereket érinti: 2020.110.114.105 és 2020.109.101.105. | A felhasználói profilok helyreállításához hajtsa végre az alábbi lépéseket az OTA-frissítés után: <br> Az [SSH-t a fejlesztői készlet](./how-to-ssh-into-percept-dk.md) a "root" névvel használja felhasználónévként. Ha letiltotta az SSH "root" felhasználói bejelentkezést a bevezetési felületen keresztül, újra engedélyeznie kell azt. A sikeres csatlakozás után futtassa ezt a parancsot: <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> A korábbi felhasználói otthoni adatok helyreállításához futtassa a következő parancsot: <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| Eszköz frissítése | Az OTA-frissítés megkezdése után a frissítési csoportok elvesznek. | Az [utasításokat](./how-to-update-over-the-air.md#create-a-device-update-group)követve frissítse az eszköz címkéjét. |
| Dev Tools Pack telepítő | Az opcionális Cafe-telepítés meghiúsulhat, ha a Docker nem fut megfelelően a rendszeren. | Győződjön meg arról, hogy a Docker telepítve van és fut, majd próbálja megismételni a Cafe telepítését. |
| Dev Tools Pack telepítő | Nem kötelező a CUDA telepítése a nem kompatibilis rendszereken. | Ellenőrizze, hogy a rendszer kompatibilis-e a CUDA-sel a telepítő futtatása előtt. |
| Docker, hálózat, IoT Edge | Ha a belső hálózata 172. x. x. x protokollt használ, a Docker-tárolók nem tudnak csatlakozni a IoT Edgehoz. | Adjon hozzá egy speciális bip szakaszt a/etc/Docker/daemon.jsa következőhöz hasonló fájlban: `{    "bip": "192.168.168.1/24"}` |
|Azure Percept Studio | Az Azure Percept Studióban található "stream megtekintése" hivatkozások nem nyitnak meg új ablakot, amely az eszköz webes streamjét mutatja. | 1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és válassza a **IoT hub** lehetőséget. <br> 2. kattintson arra a IoT Hubra, amelyhez az eszköz csatlakoztatva van. <br> 3. Válassza ki a **IoT Edget** az **automatikus eszközkezelés** területen a IoT hub oldalon. <br> 4. Válassza ki az eszközt a listából. <br> 5. Válassza az eszköz lap tetején található **modulok beállítása** lehetőséget. <br> 6. kattintson a töröltüzenetek közé ikonra a **HostIpModule** elem mellett a modul törléséhez. <br> 7. a művelet megerősítéséhez kattintson a **felülvizsgálat + létrehozás** , majd a **Létrehozás** elemre. <br> 8. Nyissa meg az [Azure Percept studiót](https://go.microsoft.com/fwlink/?linkid=2135819) , és kattintson az **eszközök** elemre a bal oldali menüben. <br> 9. Válassza ki az eszközt a listából. <br> 10. a **jövőkép** lapon kattintson az **eszköz adatfolyamának megtekintése** elemre. Az eszköz letölti a HostIpModule új verzióját, és megnyit egy böngésző fület az eszköz webes adatfolyamával. |