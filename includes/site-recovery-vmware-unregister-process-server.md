---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: bff9457220efbb6a6b318550a5b2dd9af4ae230f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019188"
---
Kövesse az adott körülményeknek megfelelő lépéseket.

### <a name="unregister-a-connected-process-server"></a>Csatlakoztatott folyamat kiszolgálójának regisztrációjának törlése

1. Hozzon létre távoli kapcsolatot a Process Serverrel rendszergazdaként.
2. A **Vezérlőpulton** nyissa meg a **programok > a program eltávolítása** elemet.
3. Távolítsa el a programot **Microsoft Azure site Recovery mobilitási szolgáltatás/fő célkiszolgáló**.
4. Távolítsa el a programot **Microsoft Azure site Recovery Configuration/Process Server**.
5. A 3. és a 4. lépésben szereplő programok eltávolítása után távolítsa el **Microsoft Azure site Recovery konfiguráció/folyamat kiszolgáló függőségeit**.

### <a name="unregister-a-disconnected-process-server"></a>Leválasztott folyamat-kiszolgáló regisztrációjának törlése

Csak akkor használja ezeket a lépéseket, ha nincs mód arra, hogy újraéleszteni azt a gépet, amelyen a Process Server telepítve van.

1. Jelentkezzen be rendszergazdaként a konfigurációs kiszolgálóra.
2. Nyisson meg egy rendszergazdai parancssort, és keresse meg a következőt: `%ProgramData%\ASR\home\svsystems\bin` .
3. Futtassa ezt a parancsot egy vagy több folyamat-kiszolgáló listájának lekéréséhez.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Nem: a Process Server sorozatszáma.
    - IP/Name: a Process Servert futtató gép IP-címe és neve.
    - Szívverés: az utolsó szívverés a Process Server gépről.
    ! [Képernyőfelvétel: a folyamat-kiszolgálókkal és a szöveggel kapcsolatos információk szöveges megjelenítése a regisztráció megszüntetése érdekében válassza a fenti kiszolgálók egyikét. (média/hely-helyreállítás-VMware-Regisztráció törlése-Process-Server/Unregister-cmd.PNG)

4. A regisztrálni kívánt folyamat-kiszolgáló sorozatszámának meghatározása.
5. A folyamat-kiszolgáló regisztrációjának törlése eltávolítja a rendszer összes adatát, és a következő üzenetet jeleníti meg: a **kiszolgáló neve sikeresen megszüntetve> (kiszolgáló-IP-cím)**

