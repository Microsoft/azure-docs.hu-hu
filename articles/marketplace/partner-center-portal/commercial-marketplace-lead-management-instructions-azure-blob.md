---
title: Vezető felügyelet az Azure Blob Storage-ban – Microsoft kereskedelmi piactér
description: Ismerje meg, hogyan konfigurálhatja az Azure blobot az Microsoft AppSource és az Azure Marketplace-hez készült érdeklődők konfigurálásához
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 05/01/2020
ms.openlocfilehash: bd2f3d40b1aea1c0133f95e069ebfd527d30bd59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94491103"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Az Azure Blob Storage használata a kereskedelmi Piactéri érdeklődők felügyeletéhez

>[!Caution]
>Az Azure Blob Storage kereskedelmi piactérről való támogatása elavult, és már nem áll fenn az ajánlatból származó érdeklődők feldolgozására szolgáló lehetőség. Ha jelenleg az Azure Blobhoz konfigurált érdeklődői felügyelettel rendelkező kereskedelmi Piactéri ajánlat van, többé nem kapja meg az ügyfél-érdeklődőket. Frissítse az érdeklődői felügyeleti konfigurációt bármely más vezető felügyeleti lehetőségre. További információ a [vezető felügyeleti kezdőlapon](./commercial-marketplace-get-customer-leads.md)elérhető egyéb lehetőségekről.

 Ha az Ügyfélkapcsolat-kezelési (CRM) rendszer nem támogatott a partner Centerben a Microsoft AppSource és az Azure Marketplace-érdeklődők fogadásához, használhatja az Azure Blob Storage-t. Ezután exportálhatja az adatait, és importálhatja azt a CRM-rendszerbe. A cikkben szereplő utasítások végigvezetik az Azure Storage-fiók létrehozásának folyamatán, valamint az adott fiókhoz tartozó blobon. Emellett létrehozhat egy új folyamatot a Power automatizálás használatával, amely e-mailben értesítést küld, ha az ajánlata érdeklődőt kap.

>[!NOTE]
>Az ebben az útmutatóban használt energiagazdálkodási összekötőhöz fizetős előfizetés szükséges a Power automatizáláshoz. Mielőtt ezt megtenné, kövesse a jelen cikk utasításait.

## <a name="configure-azure-blob-storage"></a>Az Azure Blob Storage konfigurálása

1. Ha nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/pricing/free-trial/).

2. Ha az Azure-fiókja aktív, jelentkezzen be a [Azure Portalba](https://portal.azure.com).

3. A Azure Portal a következő eljárással hozzon létre egy Storage-fiókot.  
    1. Válassza az **+ erőforrás létrehozása** lehetőséget a bal oldali menüsorban.  Ekkor megjelenik az **új** ablaktábla (panel) a jobb oldalon.
    2. Válassza a **tároló** lehetőséget az **új** ablaktáblán.  Egy **Kiemelt** lista jelenik meg a jobb oldalon.
    3. A fiók létrehozásának megkezdéséhez válassza ki a **Storage-fiókot** .  Kövesse a [Storage-fiók létrehozása](../../storage/common/storage-account-create.md?tabs=azure-portal)című cikk utasításait.

    ![Azure Storage-fiók létrehozásának lépései](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    A Storage-fiókokkal kapcsolatos további információkért tekintse [meg ezt a](../../storage/blobs/storage-quickstart-blobs-portal.md)rövid útmutatót.  További információ a Storage díjszabásáról: [Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

4. Várjon, amíg megtörténik a Storage-fiók üzembe helyezése, egy folyamat, amely általában néhány percet vesz igénybe.  Ezután nyissa meg a Storage-fiókját a Azure Portal **kezdőlapján** az **összes erőforrás** megjelenítése lehetőség kiválasztásával, vagy a Azure Portal bal oldali navigációs sávján található **összes erőforrás** kiválasztásával.

    ![Hozzáférés az Azure Storage-fiókhoz](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. A Storage-fiók ablaktáblán válassza a **hozzáférési kulcsok** lehetőséget, és másolja a kulcshoz tartozó *kapcsolati sztring* értéket. Mentse ezt az értéket, mivel ez a *Storage-fiók kapcsolódási karakterláncának* értéke, amelyet a közzétételi portálon kell megadnia a Piactéri ajánlathoz tartozó érdeklődők fogadásához.

     Egy példa a kapcsolatok karakterláncára:

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Képernyőkép, amely a "kapcsolati sztring" szövegmezővel jelenik meg a "hozzáférési kulcsok" oldalon.](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. A Storage-fiók lapon válassza a **Blobok** lehetőséget.

   ![Képernyőfelvétel az Azure Storage-fiókról lap kiválasztva Blobokkal](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. A Blobok lapon kattintson a **+ tároló** gombra.

8. Adja meg az új tároló **nevét** . A tároló neve csak kisbetűket tartalmazhat, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat. A tárolók és a Blobok neveivel kapcsolatos további információkért lásd: [tárolók, blobok és metaadatok elnevezése és hivatkozása](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

    Mentse ezt az értéket, mivel ez a *tároló neve* érték, amelyet a közzétételi portálon kell megadnia, hogy fogadja az érdeklődőket a Piactéri ajánlathoz.

9. Adja meg a tároló nyilvános hozzáférésének szintjét **magánjellegűként (névtelen hozzáférés nélkül)**.

10. A tároló létrehozásához válassza az **OK** gombot.

    ![Új tároló](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>Az ajánlat beállítása az Azure Blob Storage-ba való küldésre

Ha készen áll az ajánlathoz tartozó érdeklődői felügyeleti információk konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket:

1. Navigáljon az ajánlat **telepítési** lapjára.
2. Az **ügyfél-érdeklődők** szakaszban válassza a **kapcsolat** lehetőséget.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="Ügyfél-érdeklődők":::

3. A kapcsolat részletei előugró ablakban válassza az **Azure Blob** lehetőséget az érdeklődő célhelyéhez.

    ![Részletek összekapcsolása](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. Adja meg a **tároló nevét** és a Storage-fiókhoz tartozó, a következő utasításokat követve kapott **hálózati karakterláncot** .

    * Példa a tároló nevére: `marketplaceleadcontainer`
    * A Storage-fiók kapcsolatainak karakterlánc-példája: a `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![ kapcsolatok részletei](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt.