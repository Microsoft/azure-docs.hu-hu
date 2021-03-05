---
title: Azure Machine Learning Studio engedélyezése virtuális hálózaton
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan konfigurálhatja Azure Machine Learning studiót a virtuális hálózatban tárolt adatmennyiség eléréséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperf-fy20q4, tracking-python
ms.openlocfilehash: ca3957563f7c7a7021ad994cc323823763c2936e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171545"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Azure Machine Learning Studio használata Azure-beli virtuális hálózaton

Ebből a cikkből megtudhatja, hogyan használhatja a Azure Machine Learning Studio alkalmazást egy virtuális hálózaton. A Studio olyan funkciókat tartalmaz, mint a AutoML, a Designer és az adatcímkéző. A szolgáltatások virtuális hálózatban való használatához a jelen cikkben ismertetett lépéseket kell követnie.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> - A Studio hozzáférést biztosít a virtuális hálózaton belül tárolt adatmennyiségekhez.
> - A Studio egy virtuális hálózaton belüli erőforrásból érhető el.
> - Ismerje meg, hogy a Studio hogyan befolyásolja a tárolók biztonságát.

Ez a cikk egy öt részes sorozat ötödik része, amely végigvezeti egy Azure Machine Learning munkafolyamat biztonságossá tételének lépésein. Javasoljuk, hogy a virtuális hálózati környezet beállítása érdekében olvassa el az előző részekben ismertetett tudnivalókat.

Tekintse meg a sorozat egyéb cikkeit:

[1. VNet – áttekintés](how-to-network-security-overview.md)  >  [2. A](how-to-secure-workspace-vnet.md)  >  [3. munkaterület védelme Gondoskodjon a 4. képzési környezet védelméről](how-to-secure-training-vnet.md)  >  [. Gondoskodjon az 5. következtetési környezet védelméről](how-to-secure-inferencing-vnet.md)  >  **. A Studio funkcióinak engedélyezése**


> [!IMPORTANT]
> Ha a munkaterület __szuverén felhőben__ van, például Azure Government vagy az Azure China 21Vianet, az integrált jegyzetfüzetek _nem_ támogatják a virtuális hálózatban lévő tárolók használatát. Ehelyett használhatja egy számítási példány Jupyter-notebookjait. További információkért tekintse meg a [hozzáférési adatokat egy számítási példányú jegyzetfüzet](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook) szakaszban.

## <a name="prerequisites"></a>Előfeltételek

+ A [hálózati biztonság áttekintése című témakörben](how-to-network-security-overview.md) megismerheti a gyakori virtuális hálózati forgatókönyveket és architektúrát.

+ Egy korábban létező virtuális hálózat és alhálózat.

+ Meglévő [Azure Machine learning munkaterület, amelyen engedélyezve van a privát hivatkozás](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Egy meglévő [Azure Storage-fiók lett hozzáadva a virtuális hálózathoz](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints).

## <a name="configure-data-access-in-the-studio"></a>Adathozzáférés konfigurálása a Studióban

A Studio egyes funkciói alapértelmezés szerint le vannak tiltva egy virtuális hálózaton. Ezeknek a szolgáltatásoknak az újbóli engedélyezéséhez engedélyeznie kell a felügyelt identitást a Studióban használni kívánt Storage-fiókok számára. 

A virtuális hálózatokban alapértelmezés szerint le vannak tiltva a következő műveletek:

* A Studióban tárolt előzetes verzió.
* Jelenítse meg a tervezőben tárolt adatmegjelenítést.
* Modell üzembe helyezése a Designerben ([alapértelmezett Storage-fiók](#enable-managed-identity-authentication-for-default-storage-accounts)).
* AutoML-kísérlet küldése ([alapértelmezett Storage-fiók](#enable-managed-identity-authentication-for-default-storage-accounts)).
* Címkéző projekt elindítása.

A Studio a következő adattár-típusokból származó adatok olvasását támogatja egy virtuális hálózatban:

* Azure-blob
* 1. generációs Azure Data Lake Storage
* 2. generációs Azure Data Lake Storage
* Azure SQL Database

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Adattárolók konfigurálása a munkaterület által felügyelt identitás használatára

Miután hozzáadta az Azure Storage-fiókot a virtuális hálózathoz egy [szolgáltatási végponttal](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) vagy [privát végponttal](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints), konfigurálnia kell az adattárat a [felügyelt identitásos](../active-directory/managed-identities-azure-resources/overview.md) hitelesítés használatára. Ezzel lehetővé teszi, hogy a Studio hozzáférhessen a Storage-fiókban tárolt adataihoz.

Azure Machine Learning adattárolókat [használ a Storage-](concept-data.md#datastores) fiókokhoz való kapcsolódáshoz. A következő lépésekkel konfigurálhatja az adattárt a felügyelt identitás használatára:

1. A Studióban __válassza az__ adattárolók lehetőséget.

1. Meglévő adattár frissítéséhez válassza ki az adattárt, és válassza a __hitelesítő adatok frissítése__ lehetőséget.

    Új adattár létrehozásához válassza az __+ új adattár__ lehetőséget.

1. Az adattár beállításainál válassza az __Igen__ lehetőséget a  __munkaterület által felügyelt identitás használata adatelőnézethez és profilkészítéshez Azure Machine learning Studióban__.

    ![A felügyelt munkaterület identitásának engedélyezését bemutató képernyőkép](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

Ezek a lépések hozzáadják a munkaterület által felügyelt identitást __olvasóként__ a Storage szolgáltatáshoz az Azure RBAC használatával. Az __olvasói__ hozzáférés lehetővé teszi, hogy a munkaterület beolvassa a tűzfal beállításait, így biztosítva, hogy az adatközpontok ne hagyják el A módosítások érvénybe léptetése akár 10 percet is igénybe vehet.

### <a name="enable-managed-identity-authentication-for-default-storage-accounts"></a>Felügyelt identitás hitelesítésének engedélyezése az alapértelmezett Storage-fiókoknál

Minden Azure Machine Learning munkaterület két alapértelmezett Storage-fiókkal, egy alapértelmezett blob Storage-fiókkal és egy alapértelmezett file Store-fiókkal rendelkezik, amely a munkaterület létrehozásakor van meghatározva. Új alapértelmezett értékeket is beállíthat az **adattár** kezelése lapon.

![Az alapértelmezett adattárolók helyét bemutató képernyőkép](./media/how-to-enable-studio-virtual-network/default-datastores.png)

A következő táblázat azt ismerteti, hogy miért kell engedélyeznie a felügyelt identitások hitelesítését a munkaterület alapértelmezett tárolási fiókjaihoz.

|A(z)  | Jegyzetek  |
|---------|---------|
|Munkaterület alapértelmezett blob Storage| Modell típusú eszközöket tárol a tervezőtől. A tervezőben a modellek üzembe helyezéséhez engedélyeznie kell a felügyelt identitás hitelesítését ezen a Storage-fiókon. <br> <br> Ha olyan nem alapértelmezett adattárat használ, amely felügyelt identitás használatára lett konfigurálva, megjelenítheti és futtathatja a tervezői folyamatokat. Ha azonban olyan betanított modellt próbál telepíteni, amely nem engedélyezte a felügyelt identitást az alapértelmezett adattáron, akkor a telepítés a használatban lévő többi adattártól függetlenül meghiúsul.|
|Munkaterület alapértelmezett fájljának tárolója| A AutoML-kísérleti eszközöket tárolja. A AutoML kísérletek elküldéséhez engedélyeznie kell a felügyelt identitások hitelesítését ezen a Storage-fiókon. |

> [!WARNING]
> Létezik egy ismert probléma, amelyben az alapértelmezett tár nem hozza létre automatikusan a `azureml-filestore` mappát, amely a AutoML-kísérletek beküldéséhez szükséges. Ez akkor fordul elő, amikor a felhasználók egy meglévő Filestore hoznak létre alapértelmezett Filestore a munkaterület létrehozása során.
> 
> A probléma elkerüléséhez két lehetőség közül választhat: 1) használja az alapértelmezett Filestore, amelyet a rendszer automatikusan hoz létre a munkaterület létrehozásához. 2) Ha saját Filestore szeretne létrehozni, győződjön meg róla, hogy a Filestore kívül esik a VNet a munkaterület létrehozása során. A munkaterület létrehozása után adja hozzá a Storage-fiókot a virtuális hálózathoz.
>
> A probléma megoldásához távolítsa el a Filestore fiókot a virtuális hálózatról, majd adja hozzá újra a virtuális hálózathoz.

### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Munkaterület felügyelt identitás- __olvasó__ hozzáférésének engedélyezése a Storage privát hivatkozásához

Ha az Azure Storage-fiók privát végpontot használ, meg kell adnia a munkaterület által felügyelt személyazonosság- **olvasó** hozzáférést a privát hivatkozáshoz. További információkért tekintse meg az [olvasó](../role-based-access-control/built-in-roles.md#reader) beépített szerepkörét. 

Ha a Storage-fiók szolgáltatási végpontot használ, akkor kihagyhatja ezt a lépést.

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>A studióhoz való hozzáférés a VNet belüli erőforrásból

Ha egy virtuális hálózaton (például egy számítási példányon vagy virtuális gépen) belül található erőforráshoz fér hozzá a studióhoz, engedélyeznie kell a kimenő forgalmat a virtuális hálózatról a studióhoz. 

Ha például hálózati biztonsági csoportokat (NSG) használ a kimenő forgalom korlátozására, adjon hozzá egy szabályt a __AzureFrontDoor. frontend__ nevű __szolgáltatási címkéhez__ .

## <a name="technical-notes-for-managed-identity"></a>Technikai megjegyzések a felügyelt identitáshoz

A felügyelt identitás használata a tárolási szolgáltatások elérésére kihat a biztonsági megfontolásokra. Ez a szakasz az egyes Storage-fióktípus változásait ismerteti. 

Ezek a szempontok egyediek a hozzáférő __Storage-fiók típusától__ .

### <a name="azure-blob-storage"></a>Azure Blob Storage

Az __Azure Blob Storage__ esetében a munkaterület által felügyelt identitást is hozzáadja [blob-adatolvasóként](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , így az adatok a blob Storage-ból is olvashatók.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 hozzáférés-vezérlés

Az Azure RBAC és a POSIX stílusú hozzáférés-vezérlési listákat (ACL-eket) is használhatja a virtuális hálózaton belüli adatelérés szabályozására.

Az Azure RBAC használatához adja hozzá a munkaterület által felügyelt identitást a [blob-Adatolvasó](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) szerepkörhöz. További információ: [Azure szerepköralapú hozzáférés-vezérlés](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Az ACL-ek használatához a munkaterület által felügyelt identitás ugyanúgy rendelhető hozzá, mint bármely más biztonsági elv. További információ: hozzáférés- [vezérlési listák a fájlokon és könyvtárakon](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 hozzáférés-vezérlés

Azure Data Lake Storage Gen1 csak a POSIX stílusú hozzáférés-vezérlési listát támogatja. A munkaterület által felügyelt identitás-hozzáférés az erőforrásokhoz ugyanúgy rendelhető hozzá, mint bármely más biztonsági elv. További információ: [hozzáférés-vezérlés Azure Data Lake Storage Gen1ban](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>Azure SQL Database foglalt felhasználó

A felügyelt identitással Azure SQL Database tárolt adatok eléréséhez létre kell hoznia egy olyan SQL-beli felhasználót, amely a felügyelt identitásra van leképezve. Ha további információt szeretne arról, hogyan hozhat létre egy felhasználót egy külső szolgáltatótól, tekintse meg az [Azure ad-identitásokhoz leképezett felhasználók létrehozása](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)című témakört.

Miután létrehozta az SQL-T tartalmazó felhasználót, adja meg az engedélyt a [T-SQL parancs](/sql/t-sql/statements/grant-object-permissions-transact-sql)használatával.

### <a name="azure-machine-learning-designer-intermediate-module-output"></a>Azure Machine Learning Designer köztes modul kimenete

Megadhatja a tervező összes moduljának kimeneti helyét. Ezzel a megoldással külön helyen tárolhatja a köztes adatkészleteket biztonsági, naplózási vagy naplózási célokra. A kimenet megadásához:

1. Válassza ki azt a modult, amelynek kimenetét meg szeretné adni.
1. A jobb oldalon megjelenő modul beállításai ablaktáblán válassza a **kimeneti beállítások** lehetőséget.
1. Adja meg az egyes modulok kimenetéhez használni kívánt adattárat.
 
Győződjön meg arról, hogy rendelkezik hozzáféréssel a virtuális hálózat köztes Storage-fiókokhoz. Ellenkező esetben a folyamat sikertelen lesz.

A kimeneti adatok megjelenítéséhez engedélyeznie kell a [felügyelt identitások hitelesítését](#configure-datastores-to-use-workspace-managed-identity) is a köztes Storage-fiókok számára.

## <a name="next-steps"></a>Következő lépések

Ez a cikk egy öt részből álló virtuális hálózati sorozat ötödik része. A virtuális hálózatok biztonságossá tételéhez tekintse meg a cikkek további részeit:

* [1. rész: a Virtual Network áttekintése](how-to-network-security-overview.md)
* [2. rész: a munkaterület erőforrásainak védelme](how-to-secure-workspace-vnet.md)
* [3. rész: a képzési környezet biztonságossá tétele](how-to-secure-training-vnet.md)
* [4. rész: a következtetési környezet biztonságossá tétele](how-to-secure-inferencing-vnet.md)