---
title: A Microsoft Azure Recovery Services-(MARS-) ügynök frissítése
description: Ismerje meg, hogyan frissítheti a Microsoft Azure Recovery Services (MARS) ügynököt.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: a1ee26db962781643e9599069282647658301bac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89181476"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>A Microsoft Azure Recovery Services-(MARS-) ügynök frissítése

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

* A MARS-ügynök korábbi verzióit tartalmazó kiszolgálók azonosítása
* A MARS-telepítések frissítése ezeken a kiszolgálókon

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>A MARS-ügynök korábbi verzióit tartalmazó kiszolgálók azonosítása

Azure Backup ügynök és Azure Backup kiszolgáló telepítése esetén:

1. Navigáljon ahhoz a Recovery Services-tárolóhoz, amelyen regisztrálta azokat a kiszolgálókat, amelyek biztonsági mentése az ügynök régebbi verzióival lehetséges. A régebbi Azure Backup ügynökökkel rendelkező tárolók listáját a Azure Backup frissítési riasztások az Azure-ban című részében találja.
1. A Recovery Services-tároló bal oldali **Beállítások** szakaszában válassza a **kezelés** szakasz **biztonsági mentési infrastruktúra** elemét.
1. A Azure Backup Server-telepítések részeként telepített Azure Backup-ügynökök felderítéséhez tekintse meg a **felügyeleti kiszolgálók** alatt lévő **biztonsági mentési felügyeleti kiszolgálókat** . Ez a lista azokat a kiszolgálókat sorolja fel, amelyeken Azure Backup Server-telepítések szerepelnek a társított Azure Backup-ügynök verziószámával együtt.

    ![A Azure Backup Server-telepítések részeként telepített MARS-ügynökök listája](./media/upgrade-mars-agent/backup-management-servers.png)

1. A Microsoft Azure Recovery Services-(MARS-) ügynök telepítéséhez vagy a Azure Backup-ügynökhöz tartozó ügynökök verzióinak vizsgálatához a **felügyeleti kiszolgálók** területen válassza a **védett kiszolgálók** lehetőséget. Ezután válassza ki **Azure Backup ügynököt** a biztonsági mentési felügyelet típusa területen. Ez a lista azokat a kiszolgálókat sorolja fel, amelyeken Azure Backup ügynök telepítései szerepelnek, valamint a telepítés verziószáma.

    ![Azon kiszolgálók listája, amelyeken telepítve van a MARS-ügynök](./media/upgrade-mars-agent/protected-servers.png)

1. A Azure Backup ügynök verzió oszlop rendezéséhez válassza ki **a Mars** -ügynök telepítése vagy az **Azure Backup ügynök verziója** oszlopot a Azure Backup Server telepítéséhez.

1. Az előző lépéssel megadhatja azoknak a Azure Backup kiszolgálóknak a listáját, amelyeken a 2.0.9083.0-nál alacsonyabb verziójú, illetve az ügynök verziója üresként szerepel. Ezek azok a kiszolgálók, amelyeken frissíteni kell a Azure Backup ügynököket.

## <a name="update-the-mars-agent-installation-on-the-server"></a>A MARS-ügynök telepítésének frissítése a kiszolgálón

Miután azonosította a Azure Backup ügynök frissítését igénylő kiszolgálókat, hajtsa végre a következő lépéseket az egyes azonosított kiszolgálókon (Azure Backup kiszolgáló vagy a MARS-ügynök használatával). Az alábbi lépések végrehajtása előtt [töltse le a Azure Backup-ügynök legújabb verzióját](https://aka.ms/azurebackup_agent) .

1. Olyan sort válasszon, amelynek Azure Backup ügynöke alacsonyabb, mint 2.0.9083.0 vagy üres. Ekkor megnyílik a kiszolgáló részletei képernyő.

    ![Elavult ügynök-verziókkal rendelkező védett kiszolgálók](./media/upgrade-mars-agent/old-agent-version.png)

    ![Elavult ügynök-verziókkal rendelkező kiszolgálók Azure Backup](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Válassza a **Kapcsolódás** lehetőséget, ha egy távoli asztali kapcsolati fájlt szeretne csatlakoztatni a kiszolgálóhoz, vagy közvetlenül kapcsolódnia kell a kiszolgálóhoz távoli asztal kapcsolaton keresztül.

    ![Kapcsolódás a kiszolgálóhoz távoli asztali kapcsolaton keresztül](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Ha a felsorolt kiszolgáló nem létezik vagy már le van szerelve, figyelmen kívül hagyhatja az alábbi további lépéseket, és kihagyhatja a következő kiszolgálót.

1. Adja meg a rendszergazdai bejelentkezési adatait, és jelentkezzen be.

1. Ha a kiszolgáló vagy a kiszolgáló proxyja korlátozott internet-hozzáféréssel rendelkezik, győződjön meg arról, hogy a kiszolgálón vagy proxyn a tűzfal beállításai úgy vannak konfigurálva, hogy engedélyezzék az Ön által használt Azure-felhőnek megfelelő URL-címet:

    Azure-felhő | URL-cím
    --- | ---
    Azure Cloud (nyilvános) |   `https://login.windows.net`
    Azure China 21Vianet-felhő   | `https://login.chinacloudapi.cn`
    Azure USA-beli kormányzati felhő |   `https://login.microsoftonline.us`
    Azure German Cloud  |  `https://login.microsoftonline.de`

1. Másolja a Azure Backup Agent Update telepítőjét a kiszolgálóra.

    ![Azure Backup ügynök frissítési telepítőjének másolása kiszolgálóra](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Indítsa el a telepítőt. Megnyílik a Microsoft Azure Recovery Services ügynök frissítése varázsló.

    ![A Microsoft Azure Recovery Services ügynök frissítése varázsló](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Kattintson a **Tovább** gombra.

1. Válassza a **Frissítés** lehetőséget.

    ![Az Microsoft Azure Recovery Services-ügynök telepítése](./media/upgrade-mars-agent/upgrade-installation.png)

1. A végső megerősítési képernyő azt jelzi, hogy a Azure Backup ügynök frissítése sikeresen megtörtént.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>A System Center Data Protection Manager (SC DPM) ügyfelei számára

Ha Azure Backup ügynököket telepített a System Center Data Protection Manager (SC DPM) kiszolgálókra, az alábbi lépéseket követve azonosíthatja, hogy a DPM-kiszolgálóknak szüksége van-e Azure Backup ügynök frissítésére:

1. Jelentkezzen be rendszergazdaként az SC DPM-kiszolgálóra.
2. Nyissa meg a DPM-konzolt.
3. A konzol bal alsó navigációs sávján kattintson a **felügyelet** elemre.
4. A bal oldali navigációs sávon megjelenő információk között keresse meg a Azure Backup ügynök verziójának adatait.
5. Ha a verzió alacsonyabb, mint a 2.0.9083.0, töltse le a legújabb Azure Backup ügynök telepítőjét, és futtassa a telepítőt a DPM-kiszolgálón a Azure Backup-ügynök frissítéséhez.

Ismételje meg a fenti lépéseket a környezetben lévő összes DPM-kiszolgálón.

## <a name="next-steps"></a>Következő lépések

[A Windows rendszerű gépek biztonsági mentésének ismertetése a Azure Backup Mars-ügynök használatával](backup-windows-with-mars-agent.md)
