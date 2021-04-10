---
title: Fájlok megnyitása és mentése az Azure-ban üzembe helyezett SSIS-csomagokkal
description: Ismerje meg, hogyan nyithatja meg és mentheti a fájlokat a helyszínen és az Azure-ban, amikor helyi fájlrendszereket használó SSIS-csomagokat helyez át az Azure-ba a SSIS-ben
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: jburchel
ms.openlocfilehash: 9775266c986b3be547d8b78b8c74765c3b72a92e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786855"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Fájlok megnyitása és mentése a helyszínen és az Azure-ban az Azure-ban üzembe helyezett SSIS-csomagokkal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogyan nyithatók meg és menthetők a fájlok a helyszínen és az Azure-ban, amikor a helyi fájlrendszereket használó SSIS-csomagokat az Azure-ban SSIS.

## <a name="save-temporary-files"></a>Ideiglenes fájlok mentése

Ha az ideiglenes fájlokat egyetlen csomag végrehajtása során kell tárolnia és feldolgoznia, a csomagok használhatják a Azure-SSIS Integration Runtime-csomópontok aktuális munkakönyvtárát ( `.` ) vagy ideiglenes mappáját () `%TEMP%` .

## <a name="use-on-premises-file-shares"></a>Helyszíni fájlmegosztás használata

Ha a helyi fájlrendszert használó csomagokat a SSIS az Azure-ban szeretné használni, folytassa a helyszíni **fájlmegosztás** használatát, tegye a következőket:

1. Fájlok átvitele helyi fájlrendszerből a helyszíni fájlmegosztásba.

2. Csatlakoztassa a helyszíni fájlmegosztást egy Azure-beli virtuális hálózathoz.

3. Csatlakoztassa a Azure-SSIS IRt ugyanahhoz a virtuális hálózathoz. További információ: [Az Azure SSIS Integration Runtime csatlakoztatása egy virtuális hálózathoz](./join-azure-ssis-integration-runtime-virtual-network.md).

4. A Windows-hitelesítést használó hozzáférési hitelesítő adatok beállításával összekapcsolhatja a Azure-SSIS IR az ugyanazon a virtuális hálózaton belüli helyi fájlmegosztás használatával. További információ: [Kapcsolódás adatokhoz és fájlmegosztáshoz Windows-hitelesítéssel](ssis-azure-connect-with-windows-auth.md).

5. Frissítse a csomagok helyi fájlelérési útvonalait a helyszíni fájlmegosztás felé mutató UNC elérési utakra. Például frissítsen a következőre: `C:\abc.txt` `\\<on-prem-server-name>\<share-name>\abc.txt` .

## <a name="use-azure-file-shares"></a>Azure-fájlmegosztás használata

Ha helyi fájlrendszereket használó csomagokat SSIS az Azure-ban, akkor a következő műveleteket kell elvégeznie a **Azure Files** használatához:

1. Fájlok átvitele helyi fájlrendszerből Azure Filesba. További információ: [Azure Files](https://azure.microsoft.com/services/storage/files/).

2. A Windows-hitelesítést használó hozzáférési hitelesítő adatok beállításával Azure Files Azure-SSIS IR összekapcsolásához. További információ: [Kapcsolódás adatokhoz és fájlmegosztáshoz Windows-hitelesítéssel](ssis-azure-connect-with-windows-auth.md).

3. Frissítse a csomagok helyi fájlelérési útvonalait az Azure Filesra mutató UNC elérési utakra. Például frissítsen a következőre: `C:\abc.txt` `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt` .

## <a name="next-steps"></a>Következő lépések

- Telepítse a csomagokat. További információ: [SSIS-projekt üzembe helyezése az Azure-ban a SSMS használatával](/sql/integration-services/ssis-quickstart-deploy-ssms).
- Futtassa a csomagokat. További információ: [SSIS-csomagok futtatása az Azure-ban a SSMS-](/sql/integration-services/ssis-quickstart-run-ssms)mel.
- A csomagok beosztása. További információ: [SSIS-csomagok beosztása az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).