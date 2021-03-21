---
title: A Synapse Studio és a tároló csatlakoztatásának hibaelhárítása
description: A Synapse Studio és a tároló csatlakoztatásának hibaelhárítása
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d570b4a8df5d59cf8828985bee20852d6bc79b1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98117061"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Az Azure Synapse Analytics, a Synapse Studio és a tároló közötti kapcsolat hibaelhárítása

A szinapszis Studióban a társított tárolóban található adatforrásokat is megismerheti. Ez az útmutató segítséget nyújt a kapcsolódási problémák megoldásához, amikor megpróbál hozzáférni az adaterőforrásokhoz. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Case #1: a Storage-fiók nem rendelkezik megfelelő engedélyekkel

Ha a Storage-fiókja nem rendelkezik a megfelelő engedélyekkel, nem tudja kibontani a tárolási struktúrát "adat" – > "csatolt" értékkel a szinapszis Studióban. Lásd a probléma tünetét az alábbi képernyőképen. 

A részletes hibaüzenet változhat, de a hibaüzenet általános jelentése: "Ez a kérelem nem jogosult a művelet végrehajtására."

A csatolt tár csomópontban:  
![Storage-kapcsolati probléma 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

A tároló-tároló csomópontban:  
![Tárolási kapcsolódási probléma (1a)](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**Megoldás**: a fiók megfelelő szerepkörhöz rendeléséhez lásd: [a Azure Portal használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-információk eléréséhez](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Case #2: nem sikerült elküldeni a kérést a Storage-kiszolgálónak

Amikor kijelöli a nyilat, hogy kibontsa a tárolási struktúrát a szinapszis Studióban található "adat"--> "csatolt" elemnél, a bal oldali panelen a "REQUEST_SEND_ERROR" probléma látható. Tekintse meg a probléma tünetét az alábbi képernyőképen:

A csatolt tár csomópontban:  
![Storage-kapcsolati probléma 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

A tároló-tároló csomópontban:  
![Tárolási kapcsolat – 2a. probléma](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

A probléma több lehetséges oka is lehet:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>A tárolási erőforrás egy vNet mögött található, és a tárolási magánhálózati végpontot konfigurálni kell

**Megoldás**: ebben az esetben konfigurálnia kell a Storage-fiókhoz tartozó privát végpontot. A Storage privát végpontjának vNet való konfigurálásával kapcsolatban lásd: [a Azure Portal használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-információk eléréséhez](../security/how-to-connect-to-workspace-from-restricted-network.md).

Az "nslookup \<storage-account-name\> . DFS.Core.Windows.net" parancs használatával ellenőrizheti a kapcsolatot, miután konfigurálta a tároló magánhálózati végpontját. A következőhöz hasonló karakterláncot kell visszaadnia: " \<storage-account-name\> . privatelink.DFS.Core.Windows.net".

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>A tárolási erőforrás nem egy vNet mögött van, de a Blob service (Azure AD) végpontja nem érhető el a tűzfal konfigurálása miatt

**Megoldás**: ebben az esetben meg kell nyitnia a Storage-fiókot a Azure Portal. A bal oldali navigációs sávon görgessen le a **támogatás + hibaelhárítás** elemre, és válassza a **kapcsolat-ellenőrzési** lehetőséget a **blob Service (Azure ad)** kapcsolati állapotának megtekintéséhez. Ha nem érhető el, kövesse az előléptetett útmutatót, és ellenőrizze a **tűzfal és a virtuális hálózatok** konfigurációját a Storage-fiók lapján. További információ a tárolási tűzfalakról: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../../storage/common/storage-network-security.md).

### <a name="other-issues-to-check"></a>További ellenőrzési problémák 

* Az elérni kívánt tárolási erőforrás Azure Data Lake Storage Gen2, és egy tűzfal-és vNet (a tároló magánhálózati végpontja konfigurálva) mögött található.
* Az elérni kívánt tároló-erőforrás törölve lett, vagy nem létezik.
* Átkelés – bérlő: az a munkaterület-bérlő, amelyet a felhasználó a bejelentkezéshez használt, nem egyezik meg a Storage-fiók Bérlővel. 


## <a name="next-steps"></a>Következő lépések
Ha az előző lépések nem segítenek a probléma megoldásában, [hozzon létre egy támogatási jegyet](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).