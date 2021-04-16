---
title: A Synapse Studio és a tároló csatlakoztatásának hibaelhárítása
description: A Synapse Studio és a tároló csatlakoztatásának hibaelhárítása
author: saveenr
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d5f79131608315f7e1c05cbfc0117300eea6c511
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566273"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Az Azure Synapse Analytics, a Synapse Studio és a tároló közötti kapcsolat hibaelhárítása

A Synapse Studio a csatolt tárolóban található adatforrásokat tárhatja fel. Ez az útmutató segít megoldani a csatlakozási problémákat, amikor megpróbál hozzáférni az adatforráshoz. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Eset #1: A tárfiók nem rendelkezik a megfelelő engedélyekkel

Ha a tárfiók nem rendelkezik a megfelelő engedélyekkel, nem tudja kibővíteni a társtruktúrát a "Data" (Adatok) > "Linked" (Csatolt) Synapse Studio. Tekintse meg a probléma tünetének alábbi képernyőképét. 

A részletes hibaüzenet eltérhet, de a hibaüzenet általános jelentése a következő: "Ez a kérés nem jogosult a művelet elvégzésére.".

A csatolt tárolócsomóponton:  
![1. tárolókapcsolati probléma](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

A Storage-tároló csomópontján:  
![1a. tárolókapcsolati probléma](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**MEGOLDÁS:** A fiók a megfelelő szerepkörhöz való hozzárendeléséhez lásd a Azure Portal azure-beli szerepkör hozzárendelését a blob- és üzenetsoradatokhoz [való hozzáféréshez](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Eset #2: Nem sikerült elküldeni a kérést a tárolókiszolgálónak

Amikor a nyílra kattintva kibontja a társtruktúrát az "Adatok" > --Synapse Studio "Csatolt" elemében, a bal oldali panelen a "REQUEST_SEND_ERROR" probléma látható. Tekintse meg a probléma tünetének alábbi képernyőképét:

A csatolt tárolócsomóponton:  
![2. tárolókapcsolati probléma](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

A Storage-tároló csomópontján:  
![Tárolókapcsolati probléma – 2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

Ennek a problémának több oka is lehet:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>A tárolási erőforrás egy virtuális hálózat mögött található, és egy privát tárolóvégpontot kell konfigurálnia

**MEGOLDÁS:** Ebben az esetben konfigurálnia kell a storage privát végpontját a tárfiókhoz. A Storage privát végpontjának vNethez való konfigurálásról a blob- és üzenetsoradatok azure Azure Portal azure-beli szerepkör hozzárendelésével kapcsolatos [fejezetében található.](../security/how-to-connect-to-workspace-from-restricted-network.md)

A privát tárolóvégpont konfigurálása után az "nslookup .dfs.core.windows.net" paranccsal ellenőrizheti a \<storage-account-name\> kapcsolatot. A következő sztringet kell visszaadni: " \<storage-account-name\> .privatelink.dfs.core.windows.net".

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>A tárolási erőforrás nincs virtuális hálózat mögött, de a Blob service (Azure AD) végpontja nem érhető el a konfigurált tűzfal miatt

**MEGOLDÁS:** Ebben az esetben meg kell nyitnia a tárfiókot a Azure Portal. A bal oldali navigációs sávon görgessen le a Támogatás **+** hibaelhárítás lehetőséghez, és válassza a Kapcsolat ellenőrzése lehetőséget a Blob service  **(Azure AD)** kapcsolati állapotának ellenőrzéséhez. Ha nem érhető el, az előléptetett útmutatót használva ellenőrizze a tűzfalak és virtuális hálózatok konfigurációját a tárfiók oldalán.  További információ a storage-tűzfalakról: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása.](../../storage/common/storage-network-security.md)

### <a name="other-issues-to-check"></a>Egyéb ellenőrizve 

* A tárerőforrás, amelyhez hozzáfér, Azure Data Lake Storage Gen2 egy tűzfal és egy virtuális hálózat mögött található (privát tárolóvégponttal) egyidejűleg.
* Az elérni szükséges tároló-erőforrás törölve lett, vagy nem létezik.
* Átkelés-bérlő: a felhasználó által a bejelentkezéshez használt munkaterület-bérlő nem ugyanaz, mint a tárfiók bérlője. 


## <a name="next-steps"></a>Következő lépések
Ha az előző lépések nem oldják meg a problémát, [hozzon létre egy támogatási jegyet.](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)