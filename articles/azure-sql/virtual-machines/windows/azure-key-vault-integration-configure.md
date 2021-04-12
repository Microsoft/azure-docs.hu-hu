---
title: Az Azure-beli Windows rendszerű virtuális gépeken futó SQL Server Key Vault integrálása (Resource Manager) | Microsoft Docs
description: Megtudhatja, hogyan automatizálhatja SQL Server titkosítás konfigurációját Azure Key Vault használatával. Ez a témakör azt ismerteti, hogyan használható Azure Key Vault integráció a Resource Managerrel létrehozott SQL Virtual Machines szolgáltatással.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a6955b7fc4948faaea6db426545f8cc3d1eece35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359897"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Az Azure-beli virtuális gépeken futó SQL Server Azure Key Vault integrációjának konfigurálása (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Több SQL Server titkosítási funkció, például [transzparens adattitkosítás (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption), az [oszlop szintű titkosítás (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql)és a [biztonsági másolatok titkosítása](/sql/relational-databases/backup-restore/backup-encryption). Ezek a titkosítási formák a titkosításhoz használt titkosítási kulcsok felügyeletét és tárolását igénylik. A Azure Key Vault szolgáltatás úgy lett kialakítva, hogy javítsa a kulcsok biztonságát és felügyeletét egy biztonságos és magasan elérhető helyen. A [SQL Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) lehetővé teszi, hogy a SQL Server ezeket a kulcsokat a Azure Key Vault használatával használhassa.

Ha helyszíni SQL Server futtat, akkor a helyszíni [SQL Server-példányról Azure Key Vault eléréséhez](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)szükséges lépéseket követve elérheti azokat. Az Azure-beli virtuális gépeken való SQL Server azonban időt takaríthat meg a *Azure Key Vault Integration* szolgáltatás használatával.

Ha ez a szolgáltatás engedélyezve van, a automatikusan telepíti a SQL Server Connector, konfigurálja a EKM-szolgáltatót a Azure Key Vault eléréséhez, és létrehozza a hitelesítő adatokat, amely lehetővé teszi a tár elérését. Ha a korábban említett helyszíni dokumentáció lépéseit kereste, láthatja, hogy ez a funkció automatizálja a 2. és a 3. lépést. A Key Vault és a kulcsok létrehozásához az egyetlen dolog, amit manuálisan kell elvégezni. Innentől kezdve a SQL Server VM teljes telepítése automatizálható. Miután a funkció befejezte a telepítést, a Transact-SQL (T-SQL) utasítások végrehajtásával megkezdheti az adatbázisok vagy biztonsági másolatok titkosítását, ahogy azt a szokásos módon tenné.

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > A bővíthető kulcskezelő (EKM) szolgáltatói verziójának 1.0.4.0 telepítve van az SQL- [infrastruktúra (IaaS) bővítmény](./sql-server-iaas-agent-extension-automate-management.md)használatával a SQL Server VM. Az SQL IaaS bővítmény frissítése nem frissíti a szolgáltató verzióját. Ha szükséges (például egy SQL felügyelt példányra való áttelepítéskor), vegye figyelembe, hogy szükség esetén manuálisan frissíti a EKM-szolgáltató verzióját.


## <a name="enabling-and-configuring-key-vault-integration"></a>Key Vault integráció engedélyezése és konfigurálása
A meglévő virtuális gépek üzembe helyezése vagy konfigurálása során engedélyezheti Key Vault integrációját.

### <a name="new-vms"></a>Új virtuális gépek
Ha új SQL-alapú virtuális gépet épít ki a Resource Managerrel, a Azure Portal lehetővé teszi Azure Key Vault-integrációt. A Azure Key Vault funkció csak a SQL Server vállalati, fejlesztői és próbaverziós kiadásaihoz érhető el.

![SQL Azure Key Vault-integráció](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

A kiépítés részletes ismertetését lásd: [SQL-alapú virtuális gép létesítése a Azure Portal](create-sql-vm-portal.md).

### <a name="existing-vms"></a>Meglévő virtuális gépek

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Meglévő SQL-alapú virtuális gépek esetén nyissa meg az [SQL Virtual Machines-erőforrást](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) , és válassza a **Biztonság** lehetőséget a **Beállítások** területen. Azure Key Vault integráció engedélyezéséhez válassza az **Engedélyezés** lehetőséget. 

![SQL Key Vault-integráció meglévő virtuális gépekhez](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

Ha elkészült, kattintson a **Biztonság** lap alján található **alkalmaz** gombra a módosítások mentéséhez.

> [!NOTE]
> Az itt létrehozott hitelesítő adatok neve később egy SQL-bejelentkezésre lesz leképezve. Ez lehetővé teszi, hogy az SQL-bejelentkezés hozzáférjen a kulcstartóhoz. 


> [!NOTE]
> A Key Vault-integrációt sablon használatával is konfigurálhatja. További információ: Azure rövid útmutató [sablon Azure Key Vault integrációhoz](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]