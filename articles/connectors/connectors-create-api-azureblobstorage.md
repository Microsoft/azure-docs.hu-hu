---
title: Kapcsolódás az Azure Blob Storagehoz
description: Blobok létrehozása és kezelése az Azure Storage-fiókokban Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: cd23ff0f5ad9912440d38903a344011b069aaf16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677730"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-by-using-azure-logic-apps"></a>Blobok létrehozása és kezelése az Azure Blob Storageban Azure Logic Apps használatával

Ez a cikk bemutatja, hogyan érheti el és kezelheti a blobként tárolt fájlokat az Azure Storage-fiókban egy logikai alkalmazásban az Azure Blob Storage-összekötő használatával. Így olyan logikai alkalmazásokat hozhat létre, amelyek automatizálják a feladatokat és munkafolyamatokat a fájlok kezeléséhez. Létrehozhat például olyan logikai alkalmazásokat, amelyek létrehozzák, lekérik, frissítik és törlik a tárfiókban lévő fájlokat.

Tegyük fel, hogy rendelkezik egy olyan eszközzel, amely frissítve lesz egy Azure-webhelyen. Ez a logikai alkalmazás triggerként működik. Ha ez az esemény történik, a logikai alkalmazás a blob Storage-tárolóban is frissítheti a fájlt, amely egy művelet a logikai alkalmazásban.

Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a [Mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)lehetőséget. Az összekötő-specifikus technikai információk az [Azure Blob Storage-összekötő dokumentációjában](/connectors/azureblobconnector/)olvashatók.

> [!IMPORTANT]
> A Logic apps nem fér hozzá közvetlenül a tűzfalak mögött lévő Storage-fiókokhoz, ha mindkettő ugyanabban a régióban található. Megkerülő megoldásként különböző régiókban lehet a Logic apps és a Storage-fiók. További információ a Azure Logic Apps és a tűzfalak mögötti Storage-fiókok hozzáférésének engedélyezéséről: [hozzáférés a tűzfal mögötti fiókokhoz](#storage-firewalls) szakasz a jelen témakör későbbi részében.

<a name="blob-storage-limits"></a>

## <a name="limits"></a>Korlátok

* Alapértelmezés szerint az Azure Blob Storage-műveletek a *50 MB vagy annál kisebb* fájlokat képesek olvasni vagy írni. Ha 50 MB-nál nagyobb fájlokat szeretne kezelni, de legfeljebb 1024 MB-ra, az Azure Blob Storage-műveletek támogatják az [üzenetek darabolását](../logic-apps/logic-apps-handle-large-messages.md). A **blob-tartalom beolvasása** művelet implicit módon adatdarabolást használ.

* Az Azure Blob Storage-eseményindítók nem támogatják a darabolást. Fájl tartalmának kérésekor a triggerek csak 50 MB vagy annál kisebb fájlokat választanak ki. A 50 MB-nál nagyobb fájlok lekéréséhez kövesse az alábbi mintát:

  * Használjon olyan Azure Blob Storage triggert, amely a fájl tulajdonságait adja vissza, például **egy blob hozzáadásakor vagy módosításakor (csak tulajdonságok)**.

  * Kövesse a triggert az Azure Blob Storage **blob-tartalom lekérése** művelettel, amely beolvassa a teljes fájlt, és implicit módon használja a darabolást.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* [Azure Storage-fiók és Storage-tároló](../storage/blobs/storage-quickstart-blobs-portal.md)

* Az a logikai alkalmazás, amelyen hozzá kell férnie az Azure Blob Storage-fiókhoz. A logikai alkalmazás Azure Blob Storage triggerrel való indításához [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>BLOB Storage-trigger hozzáadása

Azure Logic Apps minden logikai alkalmazásnak egy [eseményindítóval](../logic-apps/logic-apps-overview.md#logic-app-concepts)kell kezdődnie, amely akkor következik be, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. A Logic Apps motor létrehoz egy Logic app-példányt, és elindítja az alkalmazás munkafolyamatát.

Ebből a példából megtudhatja, hogyan indíthat el egy logikai alkalmazás-munkafolyamatot a **Blobok hozzáadásakor vagy módosításakor (csak tulajdonságok)** , ha a blob tulajdonságai hozzáadódnak vagy frissülnek a tárolóban.

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic app designert. Ez a példa a Azure Portal használja.

2. A keresőmezőbe írja be szűrőként az "Azure Blob" kifejezést. Az eseményindítók listából válassza ki a kívánt eseményindítót.

   Ez a példa a következő triggert használja: **blob hozzáadásakor vagy módosításakor (csak tulajdonságok)**

   ![Azure Blob Storage trigger kiválasztása](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-trigger.png)

3. Ha a rendszer megkérdezi a kapcsolat részleteit, [hozza létre most a blob Storage-kapcsolatát](#create-connection). Ha már létezik a kapcsolatai, adja meg a szükséges információkat az triggerhez.

   Ebben a példában válassza ki a figyelni kívánt tárolót és mappát.

   1. A **tároló** mezőben válassza a mappa ikont.

   2. A mappalistában válassza a jobb oldali szögletes zárójelet ( **>** ), majd tallózással keresse meg és válassza ki a kívánt mappát.

      ![Válassza ki az triggerrel használandó tárolási mappát](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Válassza ki az intervallumot és a gyakoriságot, hogy milyen gyakran szeretné megkeresni a triggert a mappa változásaihoz.

4. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

5. Most folytassa a logikai alkalmazáshoz egy vagy több művelet hozzáadását azokkal a feladatokkal, amelyeket el szeretne végezni az trigger eredményeivel.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>BLOB Storage-művelet hozzáadása

Azure Logic Apps a [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan lépés a munkafolyamatban, amely egy triggert vagy egy másik műveletet követ. Ebben a példában a logikai alkalmazás az [ismétlődési eseményindítóval](../connectors/connectors-native-recurrence.md)kezdődik.

1. A [Azure Portal](https://portal.azure.com) vagy a Visual Studióban nyissa meg a logikai alkalmazást a Logic app Designerben. Ez a példa a Azure Portal használja.

2. A Logic app Designerben az trigger vagy a művelet alatt válassza az **új lépés** lehetőséget.

   ![Új lépés hozzáadása a Logic app-munkafolyamathoz](./media/connectors-create-api-azureblobstorage/add-new-step-logic-app-workflow.png) 

   A meglévő lépések közötti művelet hozzáadásához vigye az egeret a csatlakozás nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

3. A keresőmezőbe írja be szűrőként az "Azure Blob" kifejezést. A műveletek listából válassza ki a kívánt műveletet.

   Ez a példa a következő műveletet használja: **blob tartalmának beolvasása**

   ![Azure Blob Storage művelet kiválasztása](./media/connectors-create-api-azureblobstorage/add-azure-blob-storage-action.png)

4. Ha a rendszer kéri a kapcsolat részleteit, [hozza létre most az Azure Blob Storage-kapcsolatát](#create-connection).
Ha a kapcsolat már létezik, adja meg a szükséges információkat a művelethez.

   Ebben a példában válassza ki a kívánt fájlt.

   1. A **blob** mezőben válassza a mappa ikont.
  
      ![Válassza ki a művelettel használni kívánt tárolási mappát](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Keresse meg és válassza ki a kívánt fájlt a blob **azonosítójának** száma alapján. Ezt az **azonosítót** a blob metaadatokban találja, amelyeket a korábban leírt blob Storage-trigger ad vissza.

5. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.
A logikai alkalmazás teszteléséhez győződjön meg arról, hogy a kiválasztott mappa blobot tartalmaz.

Ez a példa csak egy blob tartalmát kéri le. A tartalom megtekintéséhez adjon hozzá egy másik műveletet, amely létrehoz egy fájlt a blobtal egy másik összekötő használatával. Adjon meg például egy olyan OneDrive műveletet, amely létrehoz egy fájlt a blob tartalma alapján.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Kapcsolódás a Storage-fiókhoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Amikor a rendszer felszólítja a kapcsolódás létrehozására, adja meg a következő információkat:

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Kapcsolat neve** | Yes | <*kapcsolattípus*> | A kapcsolódáshoz létrehozandó név |
   | **Storage-fiók** | Yes | <*Storage – fiók*> | Válassza ki a Storage-fiókját a listából. |
   ||||

   Például:

   ![Azure Blob Storage-fiókkal létesített kapcsolatok létrehozása](./media/connectors-create-api-azureblobstorage/create-storage-account-connection.png) 

1. Ha elkészült, válassza a **Létrehozás** lehetőséget.

1. A kapcsolat létrehozása után folytassa a [blob Storage-trigger hozzáadásával](#add-trigger) vagy a [blob Storage-művelet hozzáadásával](#add-action).

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő részletes technikai részleteiről, például az eseményindítók, a műveletek és a korlátok az összekötő hencegő fájljában leírtak alapján: az [összekötő hivatkozási lapja](/connectors/azureblobconnector/).

> [!NOTE]
> Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében az összekötő ISE által címkézett verziója az [ISE-üzenetek korlátait](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja helyette.

<a name="storage-firewalls"></a>

## <a name="access-storage-accounts-behind-firewalls"></a>Hozzáférés a Storage-fiókokhoz a tűzfalak mögött

Egy Azure Storage-fiókhoz is hozzáadhat hálózati biztonságot, ha korlátozza a hozzáférést a [tűzfal és a tűzfalszabályok](../storage/common/storage-network-security.md)használatával. Ez a beállítás azonban kihívást jelent az Azure-hoz és más Microsoft-szolgáltatásokhoz, amelyeknek hozzáféréssel kell rendelkezniük a Storage-fiókhoz. Az adatközpont helyi kommunikációja elvonta a belső IP-címeket, így nem állíthatja be a tűzfalszabályok IP-korlátozásokkal. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../storage/common/storage-network-security.md).

Az Azure Blob Storage Connector vagy más megoldások használatával a Azure Logic Apps tűzfalak mögött található Storage-fiókok elérésének különböző lehetőségei közül választhat:

* Azure Storage Blob-összekötő

  * [Hozzáférés a Storage-fiókokhoz más régiókban](#access-other-regions)
  * [Storage-fiókok elérése megbízható virtuális hálózaton keresztül](#access-trusted-virtual-network)

* Egyéb megoldások

  * [A Storage-fiókok megbízható szolgáltatásként való elérése felügyelt identitásokkal](#access-trusted-service)
  * [Hozzáférés a Storage-fiókokhoz az Azure API Management](#access-api-management)

<a name="access-other-regions"></a>

### <a name="problems-accessing-storage-accounts-in-the-same-region"></a>Problémák a Storage-fiókok ugyanabban a régióban való elérésekor

A Logic Apps szolgáltatás nem tud közvetlenül hozzáférni a tűzfal mögötti Storage-fiókokhoz, ha mindkettő ugyanabban a régióban van. Áthidaló megoldásként helyezze el a logikai alkalmazásokat egy olyan régióban, amely eltér a Storage-fióktól, és hozzáférést biztosít a [régiójában lévő felügyelt összekötők kimenő IP-címeihez](../logic-apps/logic-apps-limits-and-config.md#outbound).

> [!NOTE]
> Ez a megoldás nem vonatkozik az Azure Table Storage connectorra és az Azure Queue Storage-összekötőre. Ehelyett a Table Storage vagy Queue Storage eléréséhez használja a beépített HTTP-triggert és műveleteket.

<a name="access-trusted-virtual-network"></a>

### <a name="access-storage-accounts-through-a-trusted-virtual-network"></a>Storage-fiókok elérése megbízható virtuális hálózaton keresztül

A Storage-fiókot egy felügyelt Azure-beli virtuális hálózaton helyezheti el, majd hozzáadhatja a virtuális hálózatot a megbízható virtuális hálózatok listájához. Ahhoz, hogy a logikai alkalmazás hozzáférhessen a Storage-fiókhoz egy [megbízható virtuális hálózaton](../virtual-network/virtual-networks-overview.md)keresztül, telepítenie kell a logikai alkalmazást egy [integrációs szolgáltatási környezetre (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), amely egy virtuális hálózat erőforrásaihoz tud csatlakozni. Ezután hozzáadhatja az ISE alhálózatait a megbízható listához. Az Azure Storage-összekötők, például az Blob Storage-összekötő közvetlenül hozzáférhetnek a tárolóhoz. Ez a beállítás ugyanaz, mint az ISE szolgáltatásbeli végpontok használatával.

<a name="access-trusted-service"></a>

### <a name="access-storage-accounts-as-a-trusted-service-with-managed-identities"></a>A Storage-fiókok megbízható szolgáltatásként való elérése felügyelt identitásokkal

Ahhoz, hogy a Microsoft megbízható szolgáltatások hozzáférjenek a Storage-fiókhoz a tűzfalon keresztül, beállíthat egy kivételt ezen szolgáltatások tárolási fiókjához. Ez a megoldás lehetővé teszi az olyan Azure-szolgáltatások számára, amelyek támogatják [a hitelesítéshez szükséges felügyelt identitásokat](../active-directory/managed-identities-azure-resources/overview.md) , és megbízható szolgáltatásként férnek hozzá a tűzfalak mögötti tároló A globális több-bérlős Azure-beli logikai alkalmazások számára a Storage-fiókok eléréséhez először engedélyeznie kell a [felügyelt identitások támogatását](../logic-apps/create-managed-service-identity.md) a logikai alkalmazásban. Ezután a logikai alkalmazásban használja a HTTP-műveletet vagy-triggert, és [állítsa be a hitelesítési típusát a logikai alkalmazás felügyelt identitásának használatára](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). Ebben a forgatókönyvben *csak* a http műveletet vagy triggert használhatja.

A kivétel és a felügyelt identitás támogatásának beállításához kövesse az alábbi általános lépéseket:

1. A Storage-fiók **Beállítások** területén válassza a **tűzfalak és virtuális hálózatok** lehetőséget. A **hozzáférés engedélyezése** lehetőségnél válassza a **kiválasztott hálózatok** lehetőséget, hogy megjelenjenek a kapcsolódó beállítások.

1. A **kivételek** területen jelölje be **a megbízható Microsoft-szolgáltatások számára a Storage-fiók elérésének engedélyezése** jelölőnégyzetet, majd kattintson a **Mentés** gombra.

   ![Válassza ki a Microsoft megbízható szolgáltatásokat engedélyező kivételt](./media/connectors-create-api-azureblobstorage/allow-trusted-services-firewall.png)

1. A logikai alkalmazás beállításaiban [engedélyezze a felügyelt identitás támogatását](../logic-apps/create-managed-service-identity.md).

1. A logikai alkalmazás munkafolyamatában adja hozzá és állítsa be a HTTP-műveletet vagy-triggert a Storage-fiók vagy-entitás eléréséhez.

   > [!IMPORTANT]
   > A kimenő HTTP-művelethez vagy az Azure Storage-fiókokhoz indított hívások elindításához ellenőrizze, hogy a kérelem fejléce tartalmazza-e a `x-ms-version` Storage-fiókban futtatni kívánt művelethez tartozó tulajdonságot és API-verziót. További információ: [hozzáférés hitelesítése felügyelt identitással](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) és [verziószámozás az Azure Storage-szolgáltatásokhoz](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests).

1. Ezen a műveleten [válassza ki a hitelesítéshez használandó felügyelt identitást](../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity) .

<a name="access-api-management"></a>

### <a name="access-storage-accounts-through-azure-api-management"></a>Hozzáférés a Storage-fiókokhoz az Azure API Management

Ha [API Management](../api-management/api-management-key-concepts.md)dedikált szintet használ, a tárolási API-t a API Management használatával is elvégezheti, és engedélyezheti az utóbbi IP-címeit a tűzfalon keresztül. Alapvetően adja hozzá a API Management által használt Azure-beli virtuális hálózatot a Storage-fiók tűzfal-beállításához. Ezután használhatja a API Management műveletet vagy a HTTP-műveletet az Azure Storage API-k meghívásához. Ha azonban ezt a lehetőséget választja, a hitelesítési folyamatot saját kezűleg kell kezelnie. További információ: [Simple Enterprise Integration Architecture](/azure/architecture/reference-architectures/enterprise-integration/basic-enterprise-integration).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése