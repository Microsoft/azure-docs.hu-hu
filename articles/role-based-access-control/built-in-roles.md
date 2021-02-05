---
title: Azure-beli beépített szerepkörök – Azure RBAC
description: Ez a cikk az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) Azure-beli beépített szerepköreit ismerteti. Felsorolja a műveleteket, a nem Tapintatokat, a DataActions és a NotDataActions.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 02/01/2021
ms.custom: generated
ms.openlocfilehash: 3b7b65a558470c4e7f04ed84ea186fc1ea98bc40
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99557343"
---
# <a name="azure-built-in-roles"></a>Beépített Azure-szerepkörök

Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](overview.md) számos olyan Azure-beli beépített szerepkört tartalmaz, amelyeket a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz és felügyelt identitásokhoz rendelhet hozzá. A szerepkör-hozzárendelések lehetővé teszik az Azure-erőforrásokhoz való hozzáférés szabályozását. Ha a beépített szerepkörök nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját [Egyéni Azure-szerepköröket](custom-roles.md)is.

Ez a cikk az Azure beépített szerepköreit sorolja fel, amelyek folyamatosan fejlődnek. A legújabb szerepkörök beszerzéséhez használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) vagy [az az role definition List](/cli/azure/role/definition#az-role-definition-list)lehetőséget. Ha Azure Active Directory (Azure AD) rendszergazdai szerepköreit keresi, tekintse meg a [rendszergazdai szerepkör engedélyeit a Azure Active Directoryban](../active-directory/roles/permissions-reference.md).

Az alábbi táblázat egy rövid leírást és az egyes beépített szerepkörök egyedi AZONOSÍTÓját tartalmazza. Az egyes szerepkörök listájának megtekintéséhez kattintson a szerepkör `Actions` nevére `NotActions` `DataActions` `NotDataActions` . További információ ezekről a műveletekről és azokról a kezelési és adatsíkokról: az [Azure szerepkör-definíciók ismertetése](role-definitions.md).

## <a name="all"></a>Mind

> [!div class="mx-tableFixed"]
> | Beépített szerepkör | Leírás | ID (Azonosító) |
> | --- | --- | --- |
> | **Általános** |  |  |
> | [Közreműködő](#contributor) | Teljes hozzáférést biztosít az összes erőforrás kezeléséhez, de nem teszi lehetővé a szerepkörök hozzárendelését az Azure RBAC, a hozzárendelések kezelését az Azure-tervekben, illetve a képtárak megosztását. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Tulajdonos](#owner) | Teljes hozzáférést biztosít az összes erőforrás kezeléséhez, beleértve a szerepkörök hozzárendelésének lehetőségét az Azure RBAC. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Olvasó](#reader) | Megtekintheti az összes erőforrást, de nem teszi lehetővé a módosítások elvégzését. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Felhasználói hozzáférés adminisztrátora](#user-access-administrator) | Lehetővé teszi az Azure-erőforrásokhoz való felhasználói hozzáférés kezelését. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Számítás** |  |  |
> | [Klasszikus virtuális gép közreműködője](#classic-virtual-machine-contributor) | Lehetővé teszi a klasszikus virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Virtuális gép rendszergazdai bejelentkezése](#virtual-machine-administrator-login) | Virtual Machines megtekintése a Portálon és Bejelentkezés rendszergazdaként | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Virtuális gépek közreműködője](#virtual-machine-contributor) | Lehetővé teszi a virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Virtuális gép felhasználói bejelentkezés](#virtual-machine-user-login) | Tekintse meg Virtual Machines a portálon, és jelentkezzen be normál felhasználóként. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Hálózat** |  |  |
> | [CDN-végpont közreműködői](#cdn-endpoint-contributor) | Kezelheti a CDN-végpontokat, de nem tud hozzáférést biztosítani más felhasználóknak. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN-végpont olvasója](#cdn-endpoint-reader) | Megtekintheti a CDN-végpontokat, de nem végezheti el a módosításokat. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [CDN-profil közreműködői](#cdn-profile-contributor) | Kezelheti a CDN-profilokat és azok végpontjait, de nem adhat hozzáférést más felhasználóknak. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN-profil olvasója](#cdn-profile-reader) | Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Klasszikus hálózati közreműködő](#classic-network-contributor) | Lehetővé teszi a klasszikus hálózatok kezelését, de azokhoz való hozzáférés nélkül. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS-zóna közreműködője](#dns-zone-contributor) | Lehetővé teszi a DNS-zónák és-rekordhalmazok kezelését Azure DNSban, de nem teszi lehetővé, hogy ki férhet hozzájuk. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Hálózati közreműködő](#network-contributor) | Lehetővé teszi a hálózatok kezelését, az azokhoz való hozzáférés nélkül. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [saját DNS zóna közreműködője](#private-dns-zone-contributor) | Lehetővé teszi a saját DNS-zóna erőforrásainak kezelését, de nem azokhoz a virtuális hálózatokhoz, amelyekre hozzá vannak kapcsolva. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Traffic Manager közreműködő](#traffic-manager-contributor) | Lehetővé teszi a Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz való hozzáférés szabályozását. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Avere közreműködője](#avere-contributor) | Létrehozhat és kezelhet egy avere vFXT-fürtöt. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere operátor](#avere-operator) | A avere vFXT-fürt által használt fürt kezelése | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Biztonsági mentési közreműködő](#backup-contributor) | Lehetővé teszi a Backup szolgáltatás kezelését, de nem hozhat létre tárolókat, és hozzáférést biztosíthat másoknak | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Biztonságimásolat-felelős](#backup-operator) | Lehetővé teszi a biztonsági mentési szolgáltatások kezelését, kivéve a biztonsági mentés eltávolítását, a tár létrehozását és a másokhoz való hozzáférést. | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Backup-olvasó](#backup-reader) | Megtekintheti a biztonsági mentési szolgáltatásokat, de nem végezheti el a módosításokat | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Klasszikus Storage-fiók közreműködői](#classic-storage-account-contributor) | Lehetővé teszi a klasszikus Storage-fiókok kezelését, de azokhoz való hozzáférés nélkül. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [A klasszikus Storage-fiók kulcs-kezelő szolgáltatásának szerepköre](#classic-storage-account-key-operator-service-role) | A klasszikus Storage-fiók kulcsfontosságú operátorai jogosultak a kulcsok listázására és újragenerálása a klasszikus Storage-fiókokban | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Data Box közreműködő](#data-box-contributor) | A lehetővé teszi, hogy minden Data Box szolgáltatás alatt kezelje a hozzáférést, kivéve, ha mások számára biztosít hozzáférést. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box olvasó](#data-box-reader) | Lehetővé teszi Data Box szolgáltatás felügyeletét, kivéve a rendelés vagy a szerkesztési sorrend részleteit, és hozzáférést biztosít másoknak. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics fejlesztő](#data-lake-analytics-developer) | Lehetővé teszi a saját feladatok küldését, figyelését és kezelését, de Data Lake Analytics fiókokat nem hozhat létre vagy törölhet. | 47b7735b-770E-4598-a7da-8b91488b4c88 |
> | [Olvasó és adathozzáférés](#reader-and-data-access) | Lehetővé teszi az összes megtekintését, de nem engedélyezheti a Storage-fiók vagy a tárolt erőforrás törlését vagy létrehozását. Az olvasási/írási hozzáférést is lehetővé teszi a Storage-fiókban található összes, a Storage-fiók kulcsaihoz való hozzáférés útján. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Tárfiók-közreműködő](#storage-account-contributor) | Engedélyezi a Storage-fiókok kezelését. Hozzáférést biztosít a fiók kulcsaként, amely a megosztott kulcsos hitelesítésen keresztüli hozzáféréshez használható. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [A Storage-fiók kulcs-kezelő szolgáltatásának szerepköre](#storage-account-key-operator-service-role) | Engedélyezi a Storage-fiók hozzáférési kulcsainak listázását és újragenerálását. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Storage-blobadatok közreműködője](#storage-blob-data-contributor) | Azure Storage-tárolók és-Blobok olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Storage-blobadatok tulajdonosa](#storage-blob-data-owner) | Teljes hozzáférést biztosít az Azure Storage blob-tárolók és-adatkészletekhez, beleértve a POSIX hozzáférés-vezérlés hozzárendelését. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Storage-blobadatok olvasója](#storage-blob-data-reader) | Azure Storage-tárolók és-Blobok olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Storage blob-delegáló](#storage-blob-delegator) | Felhasználói delegálási kulcs beszerzése, amely az Azure AD-beli hitelesítő adatokkal aláírt tárolók vagy Blobok közös hozzáférési aláírásának létrehozásához használható. További információt a [felhasználói delegálási sas létrehozása](/rest/api/storageservices/create-user-delegation-sas)című témakörben talál. | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Storage-fájladatok SMB-megosztásának közreműködője](#storage-file-data-smb-share-contributor) | Lehetővé teszi az olvasási, írási és törlési hozzáférést az Azure-fájlmegosztás fájljaihoz vagy könyvtáraihoz. Ehhez a szerepkörhöz nem tartozik a Windows-fájlkiszolgálók beépített megfelelője. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Storage-fájladatok SMB-megosztásának emelt szintű közreműködője](#storage-file-data-smb-share-elevated-contributor) | Lehetővé teszi az olvasási, írási, törlési és módosítási ACL-eket az Azure-fájlmegosztás fájljain vagy könyvtárain. Ez a szerepkör egyenértékű a Windows-fájlkiszolgálók változásának fájlmegosztás ACL-jéhez. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Storage-fájladatok SMB-megosztásának olvasója](#storage-file-data-smb-share-reader) | Olvasási hozzáférés engedélyezése az Azure-fájlmegosztás fájljaihoz vagy könyvtáraihoz. Ez a szerepkör egyenértékű a Windows-fájlkiszolgálók olvasásához szükséges fájlmegosztási ACL-vel. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Tárolási várólista adatközreműködői](#storage-queue-data-contributor) | Azure Storage-várólisták és üzenetsor-üzenetek olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Tárolási üzenetsor adatüzenet-processzora](#storage-queue-data-message-processor) | Üzenet betekintése, beolvasása és törlése egy Azure Storage-várólistából. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Tárolási várólista adatüzenetének küldője](#storage-queue-data-message-sender) | Üzenetek hozzáadása egy Azure Storage-várólistához. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Storage-várólista adatolvasója](#storage-queue-data-reader) | Azure Storage-várólisták és üzenetsor-üzenetek olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Adatolvasó Azure Maps](#azure-maps-data-reader) | Hozzáférést biztosít egy Azure Maps-fiókból az olvasási leképezéssel kapcsolatos adatokhoz. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Search Service közreműködő](#search-service-contributor) | Lehetővé teszi a keresési szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Szignáló AccessKey-olvasó](#signalr-accesskey-reader) | A Signaler szolgáltatás elérési kulcsainak olvasása | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [Signaler app Server (előzetes verzió)](#signalr-app-server-preview) | Lehetővé teszi az App Server hozzáférés-vezérlési szolgáltatását a HRE-hitelesítési beállításokkal. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [Jeladó közreműködője](#signalr-contributor) | A Signaler szolgáltatás erőforrásainak létrehozása, olvasása, frissítése és törlése | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [A jelző kiszolgáló nélküli közreműködői (előzetes verzió)](#signalr-serverless-contributor-preview) | Kiszolgáló nélküli módban teszi lehetővé az alkalmazás-hozzáférési szolgáltatás számára a HRE-hitelesítési beállításokat. | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [A signaler szolgáltatás tulajdonosa (előzetes verzió)](#signalr-service-owner-preview) | Teljes hozzáférés az Azure Signaler szolgáltatás REST API-khoz | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [Szignáló szolgáltatás olvasója (előzetes verzió)](#signalr-service-reader-preview) | Olvasási hozzáférés az Azure Signaler szolgáltatás REST API-khoz | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Webes csomag közreműködői](#web-plan-contributor) | Lehetővé teszi a webhelyek webes csomagjainak kezelését, de azokhoz való hozzáférés nélkül. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Webhely közreműködői](#website-contributor) | Lehetővé teszi a webhelyek (nem webes csomagok) kezelését, de nem fér hozzájuk. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Containers** |  |  |
> | [AcrDelete](#acrdelete) | ACR törlése | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | ACR-rendszerkép aláírója | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | ACR-lekérés | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | ACR leküldése | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | ACR karanténba helyezési Adatolvasó | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | az ACR karanténba helyezési adatírója | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes Service-fürt rendszergazdai szerepköre](#azure-kubernetes-service-cluster-admin-role) | A fürt rendszergazdai hitelesítő adatainak listázása művelet. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes Service-fürt felhasználói szerepköre](#azure-kubernetes-service-cluster-user-role) | Fürt felhasználói hitelesítő adatainak listázása. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Az Azure Kubernetes szolgáltatás közreműködői szerepköre](#azure-kubernetes-service-contributor-role) | Hozzáférést biztosít az Azure Kubernetes Service-fürtök olvasásához és írásához | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Az Azure Kubernetes Service RBAC rendszergazdája](#azure-kubernetes-service-rbac-admin) | Lehetővé teszi a fürt/névtér területen lévő összes erőforrás felügyeletét, kivéve az erőforrás-kvóták és-névterek frissítését vagy törlését. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Azure Kubernetes Service RBAC-fürt rendszergazdája](#azure-kubernetes-service-rbac-cluster-admin) | Lehetővé teszi a fürt összes erőforrásának kezelését. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Azure Kubernetes Service RBAC-olvasó](#azure-kubernetes-service-rbac-reader) | Lehetővé teszi a csak olvasási hozzáférést a névtérben lévő legtöbb objektum megtekintéséhez. Nem teszi lehetővé a szerepkörök és a szerepkör-kötések megtekintését. Ez a szerepkör nem teszi lehetővé a titkok megtekintését, mivel a titkok tartalmának olvasása lehetővé teszi a ServiceAccount hitelesítő adatok elérését a névtérben, ami lehetővé tenné az API-hozzáférést a névtérben található bármely ServiceAccount (a jogosultságok eszkalációja). Ha ezt a szerepkört a fürt hatókörében alkalmazza, a rendszer hozzáférést biztosít az összes névtérhez. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Az Azure Kubernetes Service RBAC-írója](#azure-kubernetes-service-rbac-writer) | Olvasási/írási hozzáférést tesz lehetővé a névtér legtöbb objektumához. Ez a szerepkör nem teszi lehetővé a szerepkörök vagy szerepkör-kötések megtekintését és módosítását. Ez a szerepkör azonban lehetővé teszi a titkok elérését és a hüvelyek futtatását a névtér bármely ServiceAccount, így felhasználható a névtérben található bármely ServiceAccount API-hozzáférési szintjeinek megszerzésére. Ha ezt a szerepkört a fürt hatókörében alkalmazza, a rendszer hozzáférést biztosít az összes névtérhez. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Adatbázisok** |  |  |
> | [Cosmos DB fiók-olvasó szerepkör](#cosmos-db-account-reader-role) | Azure Cosmos DB fiókadatok olvasása. Lásd: [DocumentDB Account közreműködő](#documentdb-account-contributor) a Azure Cosmos db-fiókok kezeléséhez. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB operátor](#cosmos-db-operator) | Lehetővé teszi Azure Cosmos DB-fiókok kezelését, de nem férnek hozzájuk. Megakadályozza a fiók kulcsai és a kapcsolati karakterláncok elérését. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Visszaküldheti a visszaállítási kérelmet egy Cosmos DB adatbázishoz vagy egy fiókhoz tartozó tárolóhoz | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [CosmosRestoreOperator](#cosmosrestoreoperator) | A képes a folyamatos biztonsági mentési móddal rendelkező Cosmos DB adatbázis-fiókra vonatkozó visszaállítási művelet végrehajtására | 5432c526-bc82-444a-b7ba-57c5b0b5b34f |
> | [DocumentDB-fiók közreműködői](#documentdb-account-contributor) | Felügyelheti Azure Cosmos DB fiókokat. Azure Cosmos DB korábbi nevén DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis Cache közreműködő](#redis-cache-contributor) | Lehetővé teszi a Redis gyorsítótárak kezelését, de azokhoz való hozzáférés nélkül. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL-adatbázis közreműködői](#sql-db-contributor) | Lehetővé teszi az SQL-adatbázisok kezelését, de azokhoz való hozzáférés nélkül. Emellett nem kezelheti a biztonsággal kapcsolatos házirendeket vagy a szülő SQL-kiszolgálókat. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL felügyelt példány közreműködője](#sql-managed-instance-contributor) | Lehetővé teszi az SQL felügyelt példányok és a szükséges hálózati konfiguráció kezelését, de mások számára nem biztosít hozzáférést. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL-biztonságkezelő](#sql-security-manager) | Lehetővé teszi az SQL-kiszolgálók és-adatbázisok biztonsággal kapcsolatos házirendjeinek kezelését, az azokhoz való hozzáférés nélkül. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server közreműködő](#sql-server-contributor) | Lehetővé teszi az SQL-kiszolgálók és-adatbázisok kezelését, de azokhoz való hozzáférés nélkül, és nem a biztonsággal kapcsolatos házirendjeiket. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Elemzés** |  |  |
> | [Az Azure Event Hubs adattulajdonos](#azure-event-hubs-data-owner) | Lehetővé teszi az Azure Event Hubs-erőforrások teljes hozzáférését. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs adatfogadó](#azure-event-hubs-data-receiver) | Engedélyezi az Azure Event Hubs-erőforrások elérését. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs adatfeladó](#azure-event-hubs-data-sender) | Engedélyezi az Azure Event Hubs-erőforrásokhoz való hozzáférés küldését. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Data Factory közreműködő](#data-factory-contributor) | Az adatüzemek létrehozása és kezelése, valamint a bennük található alárendelt erőforrások. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Adattisztító](#data-purger) | Törölheti az elemzési adatfájlokat | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight-fürt operátora](#hdinsight-cluster-operator) | Lehetővé teszi a HDInsight-fürt konfigurációjának olvasását és módosítását. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [HDInsight tartományi szolgáltatások közreműködője](#hdinsight-domain-services-contributor) | Elolvashatja, létrehozhatja, módosíthatja és törölheti a HDInsight kapcsolatos tartományi szolgáltatásokat Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Log Analytics közreműködő](#log-analytics-contributor) | Log Analytics közreműködő képes olvasni az összes figyelési és szerkesztési beállítást. A figyelési beállítások szerkesztése magában foglalja a virtuálisgép-bővítmény virtuális gépekhez való hozzáadását. a Storage-fiók kulcsainak beolvasása az Azure Storage-ból származó naplók gyűjtésének konfigurálásához; Automation-fiókok létrehozása és konfigurálása; megoldások hozzáadása; és az Azure Diagnostics konfigurálása az összes Azure-erőforráshoz. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics olvasó](#log-analytics-reader) | Log Analytics olvasó megtekintheti és megkeresheti az összes figyelési adatait, valamint megtekintheti a figyelési beállításokat, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [A hatáskörébe tartozó adatkurátor](#purview-data-curator) | A Microsoft. hatáskörébe tartozó adatkezelő képes a katalógus-adatobjektumok létrehozására, olvasására, módosítására és törlésére, valamint az objektumok közötti kapcsolatok létrehozására. Ez a szerepkör előzetes verzióban érhető el, és változhat. | 8a3c2885-9b38-4fd2-9d99-91af537c1347 |
> | [A hatáskörébe tartozó Adatolvasó](#purview-data-reader) | A Microsoft. hatáskörébe Adatolvasó képes olvasni a katalógus adatobjektumait. Ez a szerepkör előzetes verzióban érhető el, és változhat. | ff100721-1b9d-43d8-af52-42b69c1272db |
> | [A hatáskörébe tartozó adatforrás rendszergazdája](#purview-data-source-administrator) | A Microsoft. hatáskörébe adatforrás-rendszergazdája az adatforrások és az adatvizsgálatok kezelésére képes. Ez a szerepkör előzetes verzióban érhető el, és változhat. | 200bba9e-f0c8-430f-892b-6f0794863803 |
> | [Séma beállításjegyzékbeli közreműködői (előzetes verzió)](#schema-registry-contributor-preview) | Séma-beállításjegyzékbeli csoportok és sémák olvasása, írása és törlése. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Séma beállításjegyzék-olvasója (előzetes verzió)](#schema-registry-reader-preview) | Séma-beállításjegyzékbeli csoportok és sémák olvasása és listázása. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **Blockchain** |  |  |
> | [Blockchain-hozzáférés (előzetes verzió)](#blockchain-member-node-access-preview) | Engedélyezi a hozzáférést a Blockchain-tagok csomópontjaihoz | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI és gépi tanulás** |  |  |
> | [Cognitive Services közreműködő](#cognitive-services-contributor) | Lehetővé teszi Cognitive Services kulcsainak létrehozását, olvasását, frissítését, törlését és kezelését. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services Adatolvasó (előzetes verzió)](#cognitive-services-data-reader-preview) | Lehetővé teszi Cognitive Services-információk olvasását. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services felhasználó](#cognitive-services-user) | Lehetővé teszi Cognitive Services kulcsainak olvasását és listázását. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Vegyes valóság** |  |  |
> | [Távoli renderelési rendszergazda](#remote-rendering-administrator) | Lehetővé teszi a felhasználó számára az átalakítást, a munkamenetek kezelését, a renderelést és a diagnosztikai képességeket az Azure távoli renderelés | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Távoli renderelési ügyfél](#remote-rendering-client) | A lehetővé teszi, hogy a felhasználó kezelje a munkamenetet, a renderelési és diagnosztikai funkciókat az Azure távoli rendereléshez. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Térbeli horgonyok fiók közreműködője](#spatial-anchors-account-contributor) | Lehetővé teszi a térbeli horgonyok kezelését a fiókban, de nem törli őket | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Térbeli horgonyok fiókjának tulajdonosa](#spatial-anchors-account-owner) | Lehetővé teszi a fiókban lévő térbeli horgonyok kezelését, beleértve azok törlését is | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Térbeli horgonyok fiókjának olvasója](#spatial-anchors-account-reader) | Lehetővé teszi a fiókban található térbeli horgonyok tulajdonságainak megkeresését és beolvasását | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integráció** |  |  |
> | [API Management szolgáltatás közreműködői](#api-management-service-contributor) | Képes a szolgáltatás és az API-k kezelésére | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API Management Service operátori szerepkör](#api-management-service-operator-role) | Kezelheti a szolgáltatást, de nem az API-kat | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [API Management szolgáltatás-olvasó szerepkör](#api-management-service-reader-role) | Csak olvasási hozzáférés a szolgáltatáshoz és az API-khoz | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Alkalmazás-konfigurációs adattulajdonos](#app-configuration-data-owner) | Lehetővé teszi az alkalmazás konfigurációs adatának teljes hozzáférését. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Az alkalmazás konfigurációs adatolvasója](#app-configuration-data-reader) | Olvasási hozzáférés engedélyezése az alkalmazás konfigurációs adatfájljaihoz. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Service Bus adattulajdonos](#azure-service-bus-data-owner) | Teljes hozzáférés engedélyezése Azure Service Bus erőforrásokhoz. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Adatfogadó Azure Service Bus](#azure-service-bus-data-receiver) | Engedélyezi a hozzáférést Azure Service Bus erőforrásokhoz. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Adatfeladó Azure Service Bus](#azure-service-bus-data-sender) | Lehetővé teszi a Azure Service Bus erőforrásokhoz való hozzáférés küldését. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack regisztrációs tulajdonos](#azure-stack-registration-owner) | Lehetővé teszi Azure Stack regisztrációk kezelését. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid EventSubscription közreműködője](#eventgrid-eventsubscription-contributor) | Lehetővé teszi a EventGrid esemény-előfizetési műveletek kezelését. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription-olvasó](#eventgrid-eventsubscription-reader) | Lehetővé teszi a EventGrid esemény-előfizetések olvasását. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [FHIR-adatközreműködő](#fhir-data-contributor) | A szerepkör lehetővé teszi, hogy a felhasználó vagy a fő teljes hozzáférés FHIR-adathoz | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR-adatexportőr](#fhir-data-exporter) | A szerepkör lehetővé teszi a felhasználó vagy a résztvevő számára a FHIR-adat olvasását és exportálását | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [FHIR Adatolvasó](#fhir-data-reader) | A szerepkör lehetővé teszi a felhasználó vagy a résztvevő számára a FHIR-adat olvasását | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR adatíró](#fhir-data-writer) | A szerepkör lehetővé teszi a felhasználó vagy a résztvevő számára a FHIR-adat olvasását és írását | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [integrációs szolgáltatási környezet közreműködő](#integration-service-environment-contributor) | Lehetővé teszi az integrációs szolgáltatási környezetek kezelését, de azokhoz való hozzáférés nélkül. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [integrációs szolgáltatási környezet fejlesztő](#integration-service-environment-developer) | Lehetővé teszi a fejlesztők számára munkafolyamatok, integrációs fiókok és API-kapcsolatok létrehozását és frissítését az integrációs szolgáltatási környezetekben. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Intelligens rendszer-fiók közreműködői](#intelligent-systems-account-contributor) | Lehetővé teszi az intelligens rendszerfiókok kezelését, az azokhoz való hozzáférés nélkül. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Logikai alkalmazás közreműködői](#logic-app-contributor) | Lehetővé teszi a logikai alkalmazások kezelését, de nem változtatja meg a hozzájuk való hozzáférést. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logic app-operátor](#logic-app-operator) | Lehetővé teszi a logikai alkalmazások olvasását, engedélyezését és letiltását, de nem szerkesztheti és nem frissíti őket. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identitás** |  |  |
> | [Felügyelt identitás közreműködői](#managed-identity-contributor) | Felhasználóhoz rendelt identitás létrehozása, olvasása, frissítése és törlése | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Felügyelt identitás operátora](#managed-identity-operator) | Felhasználóhoz rendelt identitás olvasása és hozzárendelése | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Biztonság** |  |  |
> | [Azure Sentinel Contributor](#azure-sentinel-contributor) | Azure Sentinel Contributor | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel Reader](#azure-sentinel-reader) | Azure Sentinel Reader | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel Responder](#azure-sentinel-responder) | Azure Sentinel Responder | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault rendszergazda (előzetes verzió)](#key-vault-administrator-preview) | Minden adatsík művelet végrehajtása a kulcstartón és az abban található összes objektumon, beleértve a tanúsítványokat, a kulcsokat és a titkos kulcsokat is. A Key Vault-erőforrások nem kezelhetők, illetve nem kezelhetők a szerepkör-hozzárendelések. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vault Certificates Officer (előzetes verzió)](#key-vault-certificates-officer-preview) | Hajtson végre bármilyen műveletet a Key Vault tanúsítványán, kivéve a kezelési engedélyeket. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Key Vault közreműködő](#key-vault-contributor) | A kulcstartók kezelése, de nem teszi lehetővé szerepkörök hozzárendelését az Azure RBAC, és nem teszi lehetővé a titkok, kulcsok és tanúsítványok elérését. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault titkosítási tisztviselő (előzetes verzió)](#key-vault-crypto-officer-preview) | Hajtson végre bármilyen műveletet a kulcstartó kulcsain, kivéve a kezelési engedélyeket. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault kriptográfiai szolgáltatás titkosítási felhasználója (előzetes verzió)](#key-vault-crypto-service-encryption-user-preview) | A kulcsok metaadatainak olvasása, valamint a becsomagolási/kicsomagolási műveletek végrehajtása. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault kriptográfiai felhasználó (előzetes verzió)](#key-vault-crypto-user-preview) | Titkosítási műveletek végrehajtása kulcsok használatával. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault olvasó (előzetes verzió)](#key-vault-reader-preview) | A kulcstartók és a hozzá tartozó tanúsítványok, kulcsok és titkos kódok metaadatainak olvasása. Nem lehet olvasni a bizalmas értékeket, például a titkos tartalmat vagy a kulcs anyagát. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Key Vault Secrets Officer (előzetes verzió)](#key-vault-secrets-officer-preview) | Hajtson végre bármilyen műveletet a Key Vault titkos kulcsain, kivéve a kezelési engedélyeket. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Key Vault Secrets User (előzetes verzió)](#key-vault-secrets-user-preview) | Titkos tartalom olvasása. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik. | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Biztonsági rendszergazda](#security-admin) | Security Center engedélyeinek megtekintése és frissítése. Ugyanazok az engedélyek, mint a biztonsági olvasó szerepkör, és a biztonsági szabályzatot is frissíthetik, és elérhetik a riasztásokat és a javaslatokat. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Biztonsági értékelő közreműködő](#security-assessment-contributor) | Lehetővé teszi az értékelések leküldését Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Security Manager (örökölt)](#security-manager-legacy) | Ez egy örökölt szerepkör. Használja helyette a biztonsági rendszergazdát. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Biztonsági olvasó](#security-reader) | Security Center engedélyeinek megtekintése. Megtekintheti a javaslatokat, a riasztásokat, a biztonsági házirendeket és a biztonsági állapotokat, de nem végezhet módosításokat. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs-felhasználó](#devtest-labs-user) | Lehetővé teszi a virtuális gépek a Azure DevTest Labs való összekapcsolását, indítását, újraindítását és leállítását. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Tesztkörnyezet létrehozója](#lab-creator) | Lehetővé teszi, hogy új laborokat hozzon létre az Azure Lab-fiókjaiban. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Figyelés** |  |  |
> | [Application Insights összetevő közreműködője](#application-insights-component-contributor) | Felügyelheti Application Insights összetevőket | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Felhasználói jogosultságot biztosít a Application Insights Snapshot Debugger gyűjtött hibakeresési Pillanatképek megtekintéséhez és letöltéséhez. Vegye figyelembe, hogy ezek az engedélyek nem szerepelnek a [tulajdonos](#owner) vagy a [közreműködő](#contributor) szerepkörben. Ha a felhasználók számára a Application Insights Snapshot Debugger szerepkört adja meg, közvetlenül a felhasználónak kell megadnia a szerepkört. A szerepkör nem ismerhető fel, ha hozzá van adva egy egyéni szerepkörhöz. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Közreműködő figyelése](#monitoring-contributor) | Beolvashatja az összes figyelési és a figyelési beállításokat. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Figyelési metrikák közzétevője](#monitoring-metrics-publisher) | Lehetővé teszi az Azure-erőforrásokhoz való közzétételi mérőszámok közzétételét | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Figyelő olvasó](#monitoring-reader) | Az összes figyelési adat (mérőszámok, naplók stb.) olvasása. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Munkafüzet közreműködője](#workbook-contributor) | Menthetők a megosztott munkafüzetek. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Munkafüzet-olvasó](#workbook-reader) | A munkafüzetek beolvasása. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Felügyelet és irányítás** |  |  |
> | [Automation-feladatok operátora](#automation-job-operator) | Feladatok létrehozása és kezelése Automation-Runbookok használatával. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation-operátor](#automation-operator) | Az Automation-operátorok képesek a feladatok elindítására, leállítására, felfüggesztésére és folytatására | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation Runbook operátor](#automation-runbook-operator) | A Runbook tulajdonságainak olvasása – a Runbook feladatai létrehozásához. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure-beli csatlakoztatott gépek bevezetése](#azure-connected-machine-onboarding) | Az Azure-beli csatlakoztatott gépeket is képes bevezetni. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure-beli csatlakoztatott számítógép erőforrás-rendszergazdája](#azure-connected-machine-resource-administrator) | Képes olvasni, írni, törölni és újratelepíteni az Azure-beli csatlakoztatott gépeket. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Számlázási olvasó](#billing-reader) | Olvasási hozzáférés engedélyezése a számlázási információkhoz | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Tervezet közreműködője](#blueprint-contributor) | Kezelheti a terv definícióit, de nem rendelheti hozzá őket. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Blueprint operátor](#blueprint-operator) | Meglévő közzétett tervrajzokat is hozzárendelhet, de nem hozhat létre új tervrajzokat. Vegye figyelembe, hogy ez csak akkor működik, ha a hozzárendelés felhasználó által hozzárendelt felügyelt identitással lett végrehajtva. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Cost Management közreműködő](#cost-management-contributor) | Megtekintheti a költségeket és kezelheti a költségek konfigurációját (pl. költségvetés, exportálás) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management olvasó](#cost-management-reader) | Megtekintheti a költségeket és a konfigurációt (például a költségvetést, az exportot) | 72fafb9e-0641-4937-9268 – a91bfd8191a3 |
> | [Hierarchia-beállítások rendszergazdája](#hierarchy-settings-administrator) | Lehetővé teszi a felhasználóknak a hierarchia beállításainak szerkesztését és törlését | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes-fürt – Azure arc bevezetése](#kubernetes-cluster---azure-arc-onboarding) | Szerepkör-definíció bármely felhasználó/szolgáltatás engedélyezéséhez connectedClusters-erőforrás létrehozásához | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Felügyelt alkalmazás közreműködői szerepköre](#managed-application-contributor-role) | Lehetővé teszi a felügyelt alkalmazások erőforrásainak létrehozását. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Felügyelt alkalmazás operátori szerepköre](#managed-application-operator-role) | Lehetővé teszi műveletek olvasását és végrehajtását a felügyelt alkalmazás erőforrásain | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Felügyelt alkalmazások olvasója](#managed-applications-reader) | Lehetővé teszi az erőforrások olvasását egy felügyelt alkalmazásban, valamint a JIT-hozzáférés kérését. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköre](#managed-services-registration-assignment-delete-role) | A felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköre lehetővé teszi a bérlői felhasználók számára a bérlőhöz rendelt regisztrációs hozzárendelés törlését. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Felügyeleti csoport közreműködője](#management-group-contributor) | Felügyeleti csoport közreműködői szerepköre | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Felügyeleti csoport olvasója](#management-group-reader) | Felügyeleti csoport olvasójának szerepköre | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Új ereklye APM-fiók közreműködői](#new-relic-apm-account-contributor) | Lehetővé teszi New Relic Application Performance Management-fiókok és-alkalmazások kezelését, az azokhoz való hozzáférés nélkül. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Házirend-elemzések adatírója (előzetes verzió)](#policy-insights-data-writer-preview) | Olvasási hozzáférést biztosít az erőforrás-házirendekhez, és írási hozzáférést biztosít az erőforrás-összetevőkre vonatkozó házirend eseményeihez. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Erőforrás-szabályzat közreműködője](#resource-policy-contributor) | Az erőforrás-házirend létrehozásához/módosításához, támogatási jegy létrehozásához és az erőforrások/hierarchia olvasásához jogosultsággal rendelkező felhasználók. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery-közreműködő](#site-recovery-contributor) | Lehetővé teszi Site Recovery szolgáltatás felügyeletét, kivéve a tár létrehozását és a szerepkör-hozzárendelést | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery-operátor](#site-recovery-operator) | Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem hajt végre más Site Recovery felügyeleti műveleteket | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery-olvasó](#site-recovery-reader) | Lehetővé teszi, hogy megtekintse Site Recovery állapotát, de ne végezzen más felügyeleti műveleteket | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Támogatási kérelem közreműködői](#support-request-contributor) | Lehetővé teszi a támogatási kérések létrehozását és kezelését | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Tag közreműködői](#tag-contributor) | Lehetővé teszi a címkék felügyeletét az entitásokban anélkül, hogy hozzáférést kellene biztosítani magukhoz az entitásokhoz. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Egyéb** |  |  |
> | [Azure digitális Twins-adattulajdonos](#azure-digital-twins-data-owner) | Teljes hozzáférési szerepkör a digitális Twins-adatsíkokhoz | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Azure digitális Twins-Adatolvasó](#azure-digital-twins-data-reader) | Csak olvasási szerepkör a digitális Twins-adatsíkok tulajdonságaihoz | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [BizTalk közreműködő](#biztalk-contributor) | Lehetővé teszi a BizTalk Services kezelését, de azokhoz való hozzáférés nélkül. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Asztali virtualizálási felhasználó](#desktop-virtualization-user) | Lehetővé teszi, hogy a felhasználó egy alkalmazáscsoport alkalmazásait használja. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Ütemező – feladattípusok közreműködői](#scheduler-job-collections-contributor) | Lehetővé teszi a Scheduler-feladatok összegyűjtését, de nem fér hozzájuk. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>Általános


### <a name="contributor"></a>Közreműködő

Teljes hozzáférést biztosít az összes erőforrás kezeléséhez, de nem teszi lehetővé a szerepkörök hozzárendelését az Azure RBAC, a hozzárendelések kezelését az Azure-tervekben, illetve a képtárak megosztását. [További információ](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | * | Minden típusú erőforrás létrehozása és kezelése |
> | **NotActions** |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/delete | Szerepkörök, szabályzat-hozzárendelések, házirend-definíciók és szabályzat-készlet definícióinak törlése |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Szerepkörök, szerepkör-hozzárendelések, szabályzat-hozzárendelések, házirend-definíciók és szabályzat-készlet definíciók létrehozása |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | A hívónak felhasználói hozzáférésű rendszergazdai hozzáférést engedélyez a bérlői hatókörben |
> | [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/Write | Tervrajz-hozzárendelések létrehozása vagy frissítése |
> | [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/delete | Bármely tervrajz-hozzárendelés törlése |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/Galleries/Share/Action | Katalógus megosztása különböző hatókörökre |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC, manage assignments in Azure Blueprints, or share image galleries.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete",
        "Microsoft.Compute/galleries/share/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Tulajdonos

Teljes hozzáférést biztosít az összes erőforrás kezeléséhez, beleértve a szerepkörök hozzárendelésének lehetőségét az Azure RBAC. [További információ](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | * | Minden típusú erőforrás létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Olvasó

Megtekintheti az összes erőforrást, de nem teszi lehetővé a módosítások elvégzését. [További információ](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Felhasználói hozzáférés rendszergazdája

Lehetővé teszi az Azure-erőforrásokhoz való felhasználói hozzáférés kezelését. [További információ](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/* | Engedélyezés kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Compute


### <a name="classic-virtual-machine-contributor"></a>Klasszikus virtuális gép közreműködője

Lehetővé teszi a klasszikus virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Klasszikus számítási tartománynevek létrehozása és kezelése |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Virtuális gépek létrehozása és kezelése |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/JOIN/Action |  |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/Action | Fenntartott IP-cím csatolása |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/READ | A fenntartott IP-címek beolvasása |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/JOIN/Action | Csatlakozik a virtuális hálózathoz. |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/READ | Szerezze be a virtuális hálózatot. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/Disks/READ | A Storage-fiók lemezét adja vissza. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/READ | A Storage-fiók rendszerképét adja vissza. Elavult. A "Microsoft. ClassicStorage/storageAccounts/lemezképet" használata |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/READ | Adja vissza a Storage-fiókot az adott fiókkal. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Virtuális gép rendszergazdai bejelentkezése

Tekintse meg Virtual Machines a portálon, és jelentkezzen be rendszergazdaként további [információ](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/READ | Egy nyilvános IP-cím definíciójának beolvasása. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/READ | Terheléselosztó definíciójának beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/READ | Hálózati adapter definíciójának beolvasása.  |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/READ |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/Action | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/Action | Jelentkezzen be egy virtuális gépre a Windows rendszergazdai vagy a Linux root felhasználói jogosultságokkal |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Virtuális gépek közreműködője

Lehetővé teszi a virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak. [További információ](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Számítási rendelkezésre állási csoportok létrehozása és kezelése |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/Locations/* | Számítási helyszínek létrehozása és kezelése |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Hajtsa végre az összes virtuális gép műveletét, beleértve a virtuális gépek létrehozását, frissítését, törlését, indítását, újraindítását és kikapcsolását. Előre definiált parancsfájlok végrehajtása a virtuális gépeken. |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Virtuális gépek létrehozása és kezelése |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/Disks/Write | Új lemez létrehozása vagy egy meglévő frissítése |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/Disks/READ | Lemez tulajdonságainak beolvasása |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/Disks/delete | A lemez törlése |
> | [Microsoft. segédösszetevője](resource-provider-operations.md#microsoftdevtestlab)/Schedules/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/JOIN/Action | Egy Application Gateway háttérbeli címkészlet illesztése. Nem Riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/JOIN/Action | A terheléselosztó háttérbeli címkészlet csatlakoztatása. Nem Riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/JOIN/Action | A terheléselosztó bejövő NAT-készletéhez csatlakozik. Nem riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/JOIN/Action | A terheléselosztó bejövő NAT-szabályához csatlakozik. Nem Riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/Probes/JOIN/Action | Lehetővé teszi a terheléselosztó mintavételi funkcióinak használatát. A virtuálisgép-méretezési csoport ezen engedély healthProbe tulajdonsága például hivatkozhat a mintavételre. Nem riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/READ | Terheléselosztó definíciójának beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/Locations/* | Hálózati telephelyek létrehozása és kezelése |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Hálózati adapterek létrehozása és kezelése |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/JOIN/Action | Csatlakozik egy hálózati biztonsági csoporthoz. Nem Riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/READ | Hálózati biztonsági csoport definíciójának beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/JOIN/Action | Csatlakozik egy nyilvános IP-címhez. Nem Riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/READ | Egy nyilvános IP-cím definíciójának beolvasása. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/JOIN/Action | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/* |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Write | Biztonsági másolat védelmi leképezésének létrehozása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/READ |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/READ | A védett tétel objektumának részleteit adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Védett elemek biztonsági másolatának létrehozása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/READ | Az összes védelmi szabályzat visszaadása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Write | Védelmi szabályzat létrehozása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/READ | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/READ | Egy Recovery Services tár használati adatait adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | A Create Vault művelet létrehoz egy "Vault" típusú Azure-erőforrást. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/READ | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Virtuális gép felhasználói bejelentkezés

Tekintse meg Virtual Machines a portálon, és jelentkezzen be normál felhasználóként. [További információ](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/READ | Egy nyilvános IP-cím definíciójának beolvasása. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/READ | Terheléselosztó definíciójának beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/READ | Hálózati adapter definíciójának beolvasása.  |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/READ |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/Action | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Hálózatkezelés


### <a name="cdn-endpoint-contributor"></a>CDN-végpont közreműködői

Kezelheti a CDN-végpontokat, de nem tud hozzáférést biztosítani más felhasználóknak.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/READ |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/endpoints/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN-végpont olvasója

Megtekintheti a CDN-végpontokat, de nem végezheti el a módosításokat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/READ |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/endpoints/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>CDN-profil közreműködői

Kezelheti a CDN-profilokat és azok végpontjait, de nem adhat hozzáférést más felhasználóknak. [További információ](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/READ |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN-profil olvasója

Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/edgenodes/READ |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft. CDN](resource-provider-operations.md#microsoftcdn)/Profiles/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Klasszikus hálózati közreműködő

Lehetővé teszi a klasszikus hálózatok kezelését, de azokhoz való hozzáférés nélkül. [További információ](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Klasszikus hálózatok létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>DNS-zóna közreműködője

Lehetővé teszi a DNS-zónák és-rekordhalmazok kezelését Azure DNSban, de nem teszi lehetővé, hogy ki férhet hozzájuk. [További információ](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | DNS-zónák és-rekordok létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Hálózati közreműködő

Lehetővé teszi a hálózatok kezelését, az azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/* | Hálózatok létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>saját DNS zóna közreműködője

Lehetővé teszi a saját DNS-zóna erőforrásainak kezelését, de nem azokhoz a virtuális hálózatokhoz, amelyekre hozzá vannak kapcsolva. [További információ](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/JOIN/Action | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Traffic Manager közreműködő

Lehetővé teszi a Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz való hozzáférés szabályozását.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Tárolás


### <a name="avere-contributor"></a>Avere közreműködője

Létrehozhat és kezelhet egy avere vFXT-fürtöt. [További információ](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. számítás](resource-provider-operations.md#microsoftcompute)/*/READ |  |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/Disks/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/*/READ |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/READ | Virtuális hálózati alhálózat definíciójának beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/JOIN/Action | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/JOIN/Action | Csatlakozik egy hálózati biztonsági csoporthoz. Nem Riasztásos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/*/READ |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Resources/READ | Az erőforráscsoport erőforrásainak beolvasása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Blobs/delete | Egy blob törlésének eredményét adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Blobs/READ | Blobot vagy Blobok listáját adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Blobs/Write | Egy blob írásának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere operátor

A avere vFXT-fürt a fürt kezelésére szolgál [További információ](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/READ | Virtuális gép tulajdonságainak beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/READ | Hálózati adapter definíciójának beolvasása.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Write | Hálózati adaptert hoz létre, vagy frissít egy meglévő hálózati adaptert.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/READ | Virtuális hálózati alhálózat definíciójának beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/JOIN/Action | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/JOIN/Action | Csatlakozik egy hálózati biztonsági csoporthoz. Nem Riasztásos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Egy tároló törlésének eredményét adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/READ | Tárolók listájának visszaadása |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Write | A blob-tároló Put eredményét adja vissza. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Blobs/delete | Egy blob törlésének eredményét adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Blobs/READ | Blobot vagy Blobok listáját adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Blobs/Write | Egy blob írásának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Biztonsági mentési közreműködő

Lehetővé teszi a Backup szolgáltatás felügyeletét, de nem hozhat létre tárolókat, és nem tud hozzáférést adni másoknak. [További információ](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/* |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | A művelet eredményeinek kezelése a biztonsági mentési felügyeletben |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Biztonsági mentési tárolók létrehozása és kezelése Recovery Services-tár biztonsági mentési hálójában |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/Action | Frissíti a tárolók listáját |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Feladatok exportálása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | A biztonságimásolat-kezelési műveletek eredményeinek létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Biztonsági mentési szabályzatok létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Olyan elemek létrehozása és kezelése, amelyekről biztonsági másolatot lehet készíteni |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Biztonsági másolatba mentett elemek létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Tartalék elemeket tároló tárolók létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/READ | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/* | Biztonsági mentéshez kapcsolódó tanúsítványok létrehozása és kezelése Recovery Services-tárolóban |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | A tár szolgáltatással kapcsolatos bővített adatok létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/READ | Lekéri a Recovery Services-tároló riasztásait. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/READ | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Regisztrált identitások létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Recovery Services-tároló használatának létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/READ | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/Action | A művelet érvényesítése védett elemen |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Write | A Create Vault művelet létrehoz egy "Vault" típusú Azure-erőforrást. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/READ | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/READ | A tár összes regisztrált biztonságimásolat-felügyeleti kiszolgálóját adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/READ | Az összes védhető tároló beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupStatus/Action | Recovery Services-tárolók biztonsági mentési állapotának keresése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupPreValidateProtection/Action |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupValidateFeatures/Action | Funkciók ellenőrzése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | A riasztás feloldása. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Operations/READ | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/operationStatus/READ | Egy adott művelet műveleti állapotának beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/READ | Az összes biztonsági mentési védelmi leképezés listázása |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Biztonságimásolat-felelős

Lehetővé teszi a biztonsági mentési szolgáltatások kezelését, kivéve a biztonsági mentés eltávolítását, a tár létrehozását és a másokhoz való hozzáférést. [További információ](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/READ | A művelet állapotának visszaadása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/READ | A védelmi tárolón végrehajtott művelet eredményét kapja meg. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Backup/Action | A védett elemek biztonsági mentését végzi. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/READ | A védett elemeken végrehajtott művelet eredményét kapja meg. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/READ | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/READ | A védett tétel objektumának részleteit adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Azonnali elemek helyreállításának kiépítése védett elemek esetén |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/Action | AccessToken beolvasása a régiók közötti visszaállításhoz. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/READ | A védett elemek helyreállítási pontjainak beolvasása. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | A védett elemek helyreállítási pontjainak visszaállítása. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | A védett elemek azonnali elemek helyreállításának visszavonása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/Write | Védett elemek biztonsági másolatának létrehozása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/READ | Az összes regisztrált tároló visszaadása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/Action | Frissíti a tárolók listáját |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Feladatok exportálása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | A biztonságimásolat-kezelési műveletek eredményeinek létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/READ | A házirend-művelet eredményeinek beolvasása. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/READ | Az összes védelmi szabályzat visszaadása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Olyan elemek létrehozása és kezelése, amelyekről biztonsági másolatot lehet készíteni |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/READ | Az összes védett elem listáját adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/READ | Az előfizetéshez tartozó összes tárolót adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/READ | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/Write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tároló hitelesítő adatait tartalmazó tanúsítványt. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/READ | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/Write | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/READ | Lekéri a Recovery Services-tároló riasztásait. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/READ | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/READ | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/READ | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/Write | A szolgáltatás-tároló regisztrálása művelettel regisztrálhat egy tárolót a helyreállítási szolgáltatással. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/READ | Egy Recovery Services tár használati adatait adja vissza. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/READ | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/Action | A művelet érvényesítése védett elemen |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/READ | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Operations/READ | Házirend-művelet állapotának beolvasása. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Write | Létrehoz egy regisztrált tárolót |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Inquire/Action | A tárolóban lévő számítási feladatok lekérdezése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/READ | A tár összes regisztrált biztonságimásolat-felügyeleti kiszolgálóját adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/Write | Biztonsági másolat védelmi leképezésének létrehozása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/READ | Biztonsági másolat védelmi szándékának beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/READ | Az összes védhető tároló beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Items/READ | Tároló összes elemének beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupStatus/Action | Recovery Services-tárolók biztonsági mentési állapotának keresése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupPreValidateProtection/Action |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupValidateFeatures/Action | Funkciók ellenőrzése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupAadProperties/READ | A HRE tulajdonságainak beolvasása a harmadik régióban a régiók közötti visszaállításhoz. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrrJobs/Action | Az Recovery Services-tár másodlagos régiójában lévő régiók közötti visszaállítási feladatok listázása. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrrJob/Action | A Recovery Services-tár másodlagos régiójában adja meg a régiók közötti visszaállítási feladatok részleteit. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrossRegionRestore/Action | A régiók közötti visszaállítás elindítása. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrrOperationResults/READ | Recovery Services-tár CRR-műveletének eredményét adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupCrrOperationsStatus/READ | Recovery Services-tár CRR műveleti állapotát adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | A riasztás feloldása. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Operations/READ | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/operationStatus/READ | Egy adott művelet műveleti állapotának beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/READ | Az összes biztonsági mentési védelmi leképezés listázása |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/locations/backupAadProperties/read",
        "Microsoft.RecoveryServices/locations/backupCrrJobs/action",
        "Microsoft.RecoveryServices/locations/backupCrrJob/action",
        "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action",
        "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read",
        "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Backup-olvasó

Megtekintheti a biztonsági mentési szolgáltatásokat, de a módosítások nem módosíthatók [többet](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/READ | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/READ | A művelet állapotának visszaadása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/READ | A védelmi tárolón végrehajtott művelet eredményét kapja meg. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/READ | A védett elemeken végrehajtott művelet eredményét kapja meg. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/READ | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/READ | A védett tétel objektumának részleteit adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/READ | A védett elemek helyreállítási pontjainak beolvasása. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/READ | Az összes regisztrált tároló visszaadása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/READ | A feladatok műveletének eredményét adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/READ | Az összes feladatütemezés visszaadása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/Action | Feladatok exportálása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/READ | Recovery Services-tár biztonsági mentési műveletének eredményét adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/READ | A házirend-művelet eredményeinek beolvasása. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/READ | Az összes védelmi szabályzat visszaadása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/READ | Az összes védett elem listáját adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/READ | Az előfizetéshez tartozó összes tárolót adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/READ | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/READ | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/READ | Lekéri a Recovery Services-tároló riasztásait. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/READ | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/READ | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/READ | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/READ | Recovery Services tároló tárolási konfigurációját adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/READ | Recovery Services-tároló konfigurációjának visszaadása. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/READ | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/Operations/READ | Házirend-művelet állapotának beolvasása. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/READ | A tár összes regisztrált biztonságimásolat-felügyeleti kiszolgálóját adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/READ | Biztonsági másolat védelmi szándékának beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/Items/READ | Tároló összes elemének beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupStatus/Action | Recovery Services-tárolók biztonsági mentési állapotának keresése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/Write | A riasztás feloldása. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Operations/READ | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/operationStatus/READ | Egy adott művelet műveleti állapotának beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/READ | Az összes biztonsági mentési védelmi leképezés listázása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/READ | Egy Recovery Services tár használati adatait adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/backupValidateFeatures/Action | Funkciók ellenőrzése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Klasszikus Storage-fiók közreműködői

Lehetővé teszi a klasszikus Storage-fiókok kezelését, de azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>A klasszikus Storage-fiók kulcs-kezelő szolgáltatásának szerepköre

A klasszikus Storage-fiók kulcsfontosságú operátorai lehetővé teszik a klasszikus Storage-fiókok kulcsainak listázását és újragenerálása. [További információ](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/Action | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/Action | Újragenerálja a Storage-fiók meglévő hozzáférési kulcsait. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Data Box közreműködő

A lehetővé teszi, hogy minden Data Box szolgáltatás alatt kezelje a hozzáférést, kivéve, ha mások számára biztosít hozzáférést. [További információ](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Data Box olvasó

Lehetővé teszi Data Box szolgáltatás felügyeletét, kivéve a rendelés vagy a szerkesztési sorrend részleteit, és hozzáférést biztosít másoknak. [További információ](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/*/READ |  |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/Jobs/listsecrets/Action |  |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/Jobs/listcredentials/Action | Felsorolja a rendeléshez kapcsolódó titkosítatlan hitelesítő adatokat. |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/Locations/availableSkus/Action | Ez a metódus visszaadja az elérhető SKU-ket tartalmazó listát. |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/Locations/validateInputs/Action | Ez a metódus az összes érvényesítési típust támogatja. |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/Locations/regionConfiguration/Action | Ez a metódus a régió konfigurációit adja vissza. |
> | [Microsoft. Databox](resource-provider-operations.md#microsoftdatabox)/Locations/validateAddress/Action | Ellenőrzi a szállítási címet, és alternatív címeket biztosít, ha vannak ilyenek. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics fejlesztő

Lehetővé teszi a saját feladatok küldését, figyelését és kezelését, de Data Lake Analytics fiókokat nem hozhat létre vagy törölhet. [További információ](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. BigAnalytics/fiókok/* |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | Microsoft. BigAnalytics/fiókok/törlés |  |
> | Microsoft. BigAnalytics/accounts/TakeOwnership/Action |  |
> | Microsoft. BigAnalytics/fiókok/írás |  |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/delete | DataLakeAnalytics-fiók törlése. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnerShip/Action | Engedélyek megadása a más felhasználók által küldött feladatok megszakításához. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | DataLakeAnalytics-fiók létrehozása vagy frissítése. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | Hozzon létre vagy frissítsen egy DataLakeAnalytics-fiók csatolt Data Lake Store-fiókját. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/delete | Data Lake Store-fiók DataLakeAnalytics-fiókból való leválasztása. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | Hozzon létre vagy frissítsen egy DataLakeAnalytics-fiók csatolt Storage-fiókját. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/delete | Egy DataLakeAnalytics-fiókhoz tartozó Storage-fiók leválasztása. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | Tűzfalszabály létrehozása vagy frissítése. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | Számítási szabályzat létrehozása vagy frissítése. |
> | [Microsoft. DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/delete | Számítási szabályzat törlése. |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Olvasó és adathozzáférés

Lehetővé teszi az összes megtekintését, de nem engedélyezheti a Storage-fiók vagy a tárolt erőforrás törlését vagy létrehozását. Az olvasási/írási hozzáférést is lehetővé teszi a Storage-fiókban található összes, a Storage-fiók kulcsaihoz való hozzáférés útján.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/Action | A megadott Storage-fiókhoz tartozó SAS-tokent adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/READ | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Tárfiók-közreműködő

Engedélyezi a Storage-fiókok kezelését. Hozzáférést biztosít a fiók kulcsaként, amely a megosztott kulcsos hitelesítésen keresztüli hozzáféréshez használható. [További információ](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a Analysis Server diagnosztikai beállításait. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>A Storage-fiók kulcs-kezelő szolgáltatásának szerepköre

Engedélyezi a Storage-fiók hozzáférési kulcsainak listázását és újragenerálását. [További információ](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/Action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/Action | Újragenerálja a megadott Storage-fiók hozzáférési kulcsait. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Storage-blobadatok közreműködője

Azure Storage-tárolók és-Blobok olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Tároló törlése. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/READ | Tároló vagy tárolók listájának visszaadása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Write | Egy tároló metaadatainak vagy tulajdonságainak módosítása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | A Blob service felhasználói delegálási kulcsát adja vissza. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Blobs/delete | BLOB törlése. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Blobs/READ | BLOB vagy Blobok listájának visszaadása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Blobs/Move/Action | A blob áthelyezése egyik útvonalról a másikra |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Blobs/Write | Írás blobba. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Storage-blobadatok tulajdonosa

Teljes hozzáférést biztosít az Azure Storage blob-tárolók és-adatkészletekhez, beleértve a POSIX hozzáférés-vezérlés hozzárendelését. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | A tárolók teljes engedélyei. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | A Blob service felhasználói delegálási kulcsát adja vissza. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Blobs/* | A Blobok teljes engedélyei. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Storage-blobadatok olvasója

Azure Storage-tárolók és-Blobok olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/READ | Tároló vagy tárolók listájának visszaadása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | A Blob service felhasználói delegálási kulcsát adja vissza. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/Blobs/READ | BLOB vagy Blobok listájának visszaadása. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Storage blob-delegáló

Felhasználói delegálási kulcs beszerzése, amely az Azure AD-beli hitelesítő adatokkal aláírt tárolók vagy Blobok közös hozzáférési aláírásának létrehozásához használható. További információt a [felhasználói delegálási sas létrehozása](/rest/api/storageservices/create-user-delegation-sas)című témakörben talál. [További információ](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/Action | A Blob service felhasználói delegálási kulcsát adja vissza. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Storage-fájladatok SMB-megosztásának közreműködője

Lehetővé teszi az olvasási, írási és törlési hozzáférést az Azure-fájlmegosztás fájljaihoz vagy könyvtáraihoz. Ehhez a szerepkörhöz nem tartozik a Windows-fájlkiszolgálók beépített megfelelője. [További információ](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/READ | Egy fájlt/mappát vagy egy fájl/mappa listáját adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Write | Egy fájl írásának vagy egy mappa létrehozásának eredményét adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/delete | Egy fájl vagy mappa törlésének eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Storage-fájladatok SMB-megosztásának emelt szintű közreműködője

Lehetővé teszi az olvasási, írási, törlési és módosítási ACL-eket az Azure-fájlmegosztás fájljain vagy könyvtárain. Ez a szerepkör egyenértékű a Windows-fájlkiszolgálók változásának fájlmegosztás ACL-jéhez. [További információ](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/READ | Egy fájlt/mappát vagy egy fájl/mappa listáját adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/Write | Egy fájl írásának vagy egy mappa létrehozásának eredményét adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/delete | Egy fájl vagy mappa törlésének eredményét adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/modifypermissions/Action | Egy fájlra vagy mappára vonatkozó engedély módosításának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Storage-fájladatok SMB-megosztásának olvasója

Olvasási hozzáférés engedélyezése az Azure-fájlmegosztás fájljaihoz vagy könyvtáraihoz. Ez a szerepkör egyenértékű a Windows-fájlkiszolgálók olvasásához szükséges fájlmegosztási ACL-vel. [További információ](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/Files/READ | Egy fájlt/mappát vagy egy fájl/mappa listáját adja vissza. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Tárolási várólista adatközreműködői

Azure Storage-várólisták és üzenetsor-üzenetek olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/delete | Várólista törlése. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/READ | Várólista vagy várólisták listájának visszaadása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/Write | Várólista metaadatainak vagy tulajdonságainak módosítása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/delete | Egy vagy több üzenet törlése egy várólistából. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/READ | Egy vagy több üzenet betekintése vagy lekérése egy várólistából. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Write | Üzenet hozzáadása egy várólistához. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Process/Action | Egy üzenet feldolgozásának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Tárolási üzenetsor adatüzenet-processzora

Üzenet betekintése, beolvasása és törlése egy Azure Storage-várólistából. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/READ | Üzenet bepillantása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Process/Action | Üzenet beolvasása és törlése. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Tárolási várólista adatüzenetének küldője

Üzenetek hozzáadása egy Azure Storage-várólistához. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/Add/Action | Üzenet hozzáadása egy várólistához. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Storage-várólista adatolvasója

Azure Storage-várólisták és üzenetsor-üzenetek olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/READ | Várólistát vagy várólisták listáját adja vissza. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/Queues/messages/READ | Egy vagy több üzenet betekintése vagy lekérése egy várólistából. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Webes


### <a name="azure-maps-data-reader"></a>Adatolvasó Azure Maps

Hozzáférést biztosít egy Azure Maps-fiókból az olvasási leképezéssel kapcsolatos adatokhoz.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/READ |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Search Service közreműködő

Lehetővé teszi a keresési szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. [További információ](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | Keresési szolgáltatások létrehozása és kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-accesskey-reader"></a>Szignáló AccessKey-olvasó

A Signaler szolgáltatás elérési kulcsainak olvasása

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/READ |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/Action | A Signaler-hozzáférési kulcsok értékének megtekintése a felügyeleti portálon vagy az API-n keresztül |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>Signaler app Server (előzetes verzió)

Lehetővé teszi az App Server hozzáférés-vezérlési szolgáltatását a HRE-hitelesítési beállításokkal.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Auth/accessKey/Action | Ideiglenes AccessKey létrehozása a ClientTokens aláírásához. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/Write | Indítsa el a kiszolgálói kapcsolatokat. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-contributor"></a>Jeladó közreműködője

A Signaler szolgáltatás erőforrásainak létrehozása, olvasása, frissítése és törlése

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-serverless-contributor-preview"></a>A jelző kiszolgáló nélküli közreműködői (előzetes verzió)

Kiszolgáló nélküli módban teszi lehetővé az alkalmazás-hozzáférési szolgáltatás számára a HRE-hitelesítési beállításokat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Auth/clientToken/Action | ClientToken létrehozása az Ügyfélkapcsolat elindításához. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app access service in serverless mode with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Serverless Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner-preview"></a>A signaler szolgáltatás tulajdonosa (előzetes verzió)

Teljes hozzáférés az Azure Signaler szolgáltatás REST API-khoz

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/Send/Action | Üzenetek küldése a hub összes ügyfélkapcsolatának. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Send/Action | Üzenet küldése a csoportnak. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/READ | A csoport létezésének vagy a felhasználó létezésének ellenőrzéséhez. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/Write | Csatlakozás/kilépés csoport. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Send/Action | Üzenetek küldése közvetlenül az ügyfél-kapcsolatok számára. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/READ | Az Ügyfélkapcsolat létezésének ellenőrzéséhez. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/Write | Az Ügyfélkapcsolat lezárása. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Send/Action | Üzenetek küldése a felhasználónak, aki több ügyfélkapcsolatból is állhat. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/READ | A felhasználó létezésének ellenőrzéséhez. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/Write |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-reader-preview"></a>Szignáló szolgáltatás olvasója (előzetes verzió)

Olvasási hozzáférés az Azure Signaler szolgáltatás REST API-khoz

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/Group/READ | A csoport létezésének vagy a felhasználó létezésének ellenőrzéséhez. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/READ | Az Ügyfélkapcsolat létezésének ellenőrzéséhez. |
> | [Microsoft. SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/User/READ | A felhasználó létezésének ellenőrzéséhez. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Webes csomag közreműködői

Lehetővé teszi a webhelyek webes csomagjainak kezelését, de azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Kiszolgálófarm létrehozása és kezelése |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/JOIN/Action | Egy App Service Environment illesztése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Webhely közreműködői

Lehetővé teszi a webhelyek (nem webes csomagok) kezelését, de nem fér hozzájuk.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Components/* | Az adatáttekintési összetevők létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Certificates/* | Webhely-tanúsítványok létrehozása és kezelése |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/READ | Az állomásnévhez rendelt helyek nevének beolvasása. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/JOIN/Action | Egy App Service csomag csatlakoztatása |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/READ | App Service csomag tulajdonságainak beolvasása |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Sites/* | Webhelyek létrehozása és kezelése (a hely létrehozásához írási engedély szükséges a társított App Service csomaghoz) |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Tárolók


### <a name="acrdelete"></a>AcrDelete

ACR törlés [További információ](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/delete | Összetevő törlése egy tároló-beállításjegyzékben. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

ACR – rendszerkép-aláíró [További információ](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Sign/Write | A tartalom megbízhatóságának leküldése/lekérése egy tároló-beállításjegyzékben. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

ACR – [További információ](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/READ | Lemezképek lekérése vagy beolvasása egy tároló-beállításjegyzékből. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

ACR push – [További információ](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/READ | Lemezképek lekérése vagy beolvasása egy tároló-beállításjegyzékből. |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/Write | Képek leküldése vagy írása egy tároló-beállításjegyzékbe. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

ACR karanténba helyezési Adatolvasó

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Quarantine/READ | Karanténba helyezett lemezképek lekérése vagy beolvasása a Container registryből |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

az ACR karanténba helyezési adatírója

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Quarantine/READ | Karanténba helyezett lemezképek lekérése vagy beolvasása a Container registryből |
> | [Microsoft. ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/Quarantine/Write | Karanténba helyezett lemezképek karantén állapotának írása/módosítása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service-fürt rendszergazdai szerepköre

A fürt rendszergazdai hitelesítő adatainak listázása művelet. [További információ](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/Action | Felügyelt fürt clusterAdmin hitelesítő adatainak listázása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/Action | Felügyelt fürt hozzáférési profiljának beolvasása szerepkör neve alapján a lista hitelesítő adataival |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/READ | Felügyelt fürt beszerzése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service-fürt felhasználói szerepköre

Fürt felhasználói hitelesítő adatainak listázása. [További információ](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Felügyelt fürt clusterUser hitelesítő adatainak listázása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/READ | Felügyelt fürt beszerzése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>Az Azure Kubernetes szolgáltatás közreműködői szerepköre

Hozzáférést biztosít az Azure Kubernetes Service-fürtök olvasásához és írásához. [További információ](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/READ | Felügyelt fürt beszerzése |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Write | Létrehoz egy új felügyelt fürtöt, vagy frissít egy meglévőt |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Az Azure Kubernetes Service RBAC rendszergazdája

Lehetővé teszi a fürt/névtér területen lévő összes erőforrás felügyeletét, kivéve az erőforrás-kvóták és-névterek frissítését vagy törlését. [További információ](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Egy központi telepítés létrehozása vagy frissítése. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/READ | Az előfizetések listájának beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Felügyelt fürt clusterUser hitelesítő adatainak listázása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/Write | Resourcequotas írása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | Resourcequotas törlése |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/Write | Névterek írása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/delete | Névterek törlése |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Azure Kubernetes Service RBAC-fürt rendszergazdája

Lehetővé teszi a fürt összes erőforrásának kezelését. [További információ](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Egy központi telepítés létrehozása vagy frissítése. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/READ | Az előfizetések listájának beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/Action | Felügyelt fürt clusterUser hitelesítő adatainak listázása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Azure Kubernetes Service RBAC-olvasó

Lehetővé teszi a csak olvasási hozzáférést a névtérben lévő legtöbb objektum megtekintéséhez. Nem teszi lehetővé a szerepkörök és a szerepkör-kötések megtekintését. Ez a szerepkör nem teszi lehetővé a titkok megtekintését, mivel a titkok tartalmának olvasása lehetővé teszi a ServiceAccount hitelesítő adatok elérését a névtérben, ami lehetővé tenné az API-hozzáférést a névtérben található bármely ServiceAccount (a jogosultságok eszkalációja). Ha ezt a szerepkört a fürt hatókörében alkalmazza, a rendszer hozzáférést biztosít az összes névtérhez. [További információ](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Egy központi telepítés létrehozása vagy frissítése. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/READ | Az előfizetések listájának beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/READ | Controllerrevisions beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/READ | Daemonsets beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/Deployments/READ | Az üzembe helyezések beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/READ | Replicasets beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/READ | Statefulsets beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/READ | Horizontalpodautoscalers beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/Cronjobs/READ | Cronjobs beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/Jobs/READ | Feladatok beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/READ | Configmaps beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/READ | Végpontok beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.IO/Events/READ | Események beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events/READ | Események beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/READ | Daemonsets beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/Deployments/READ | Az üzembe helyezések beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ingresses/READ | Ingresses beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/networkpolicies/READ | Networkpolicies beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/replicasets/READ | Replicasets beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/READ | Limitranges beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/READ | Névterek beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.IO/ingresses/READ | Ingresses beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.IO/networkpolicies/READ | Networkpolicies beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/READ | Persistentvolumeclaims beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Pods/READ | Hüvelyek beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/READ | Poddisruptionbudgets beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/READ | Replicationcontrollers beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/READ | Replicationcontrollers beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/READ | Resourcequotas beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/READ | Serviceaccounts beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Services/READ | Szolgáltatások beolvasása |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Az Azure Kubernetes Service RBAC-írója

Olvasási/írási hozzáférést tesz lehetővé a névtér legtöbb objektumához. Ez a szerepkör nem teszi lehetővé a szerepkörök vagy szerepkör-kötések megtekintését és módosítását. Ez a szerepkör azonban lehetővé teszi a titkok elérését és a hüvelyek futtatását a névtér bármely ServiceAccount, így felhasználható a névtérben található bármely ServiceAccount API-hozzáférési szintjeinek megszerzésére. Ha ezt a szerepkört a fürt hatókörében alkalmazza, a rendszer hozzáférést biztosít az összes névtérhez. [További információ](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Egy központi telepítés létrehozása vagy frissítése. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/READ | Az előfizetések listájának beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/READ | Controllerrevisions beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/Deployments/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/Cronjobs/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/Jobs/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.IO/Events/READ | Események beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Events/READ | Események beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/daemonsets/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/Deployments/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/ingresses/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/networkpolicies/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Extensions/replicasets/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/READ | Limitranges beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Namespaces/READ | Névterek beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.IO/ingresses/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Networking.k8s.IO/networkpolicies/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Pods/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Policy/poddisruptionbudgets/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/READ | Resourcequotas beolvasása |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Secrets/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft. tárolószolgáltatás](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/Services/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Adatbázisok


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB fiók-olvasó szerepkör

Azure Cosmos DB fiókadatok olvasása. Lásd: [DocumentDB Account közreműködő](#documentdb-account-contributor) a Azure Cosmos db-fiókok kezeléséhez. [További információ](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/READ | Bármely gyűjtemény beolvasása |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/Action | Az adatbázis-fiók írásvédett kulcsának beolvasása. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/READ | Metrikus definíciók olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Metrics/READ | Metrikák olvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB operátor

Lehetővé teszi Azure Cosmos DB-fiókok kezelését, de nem férnek hozzájuk. Megakadályozza a fiók kulcsai és a kapcsolati karakterláncok elérését. [További információ](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Visszaállíthat egy Cosmos DB-adatbázisra vonatkozó visszaállítási kérelmet, vagy egy fiókhoz egy tárolót. [További információ](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/Backup/Action | A biztonsági mentés konfigurálására vonatkozó kérelem küldése |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/Restore/Action | Visszaállítási kérelem elküldése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosrestoreoperator"></a>CosmosRestoreOperator

A képes a folyamatos biztonsági mentési móddal rendelkező Cosmos DB adatbázis-fiókra vonatkozó visszaállítási művelet végrehajtására

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Locations/restorableDatabaseAccounts/Restore/Action | Visszaállítási kérelem elküldése |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Locations/restorableDatabaseAccounts/*/READ |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/Locations/restorableDatabaseAccounts/READ | Helyreállítható adatbázis-fiók beolvasása vagy az összes helyreállítható adatbázis-fiók listázása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform restore action for Cosmos DB database account with continuous backup mode",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "name": "5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosRestoreOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>DocumentDB-fiók közreműködői

Felügyelheti Azure Cosmos DB fiókokat. Azure Cosmos DB korábbi nevén DocumentDB. [További információ](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Azure Cosmos DB fiókok létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Redis Cache közreműködő

Lehetővé teszi a Redis gyorsítótárak kezelését, de azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. cache](resource-provider-operations.md#microsoftcache)/Register/Action | Regisztrálja a Microsoft. cache erőforrás-szolgáltatót egy előfizetéssel |
> | [Microsoft. cache](resource-provider-operations.md#microsoftcache)/Redis/* | Redis cache-gyorsítótárak létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>SQL-adatbázis közreműködői

Lehetővé teszi az SQL-adatbázisok kezelését, de azokhoz való hozzáférés nélkül. Emellett nem kezelheti a biztonsággal kapcsolatos házirendeket vagy a szülő SQL-kiszolgálókat. [További információ](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/READ |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/* | SQL-adatbázisok létrehozása és kezelése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/READ | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Metrics/READ | Metrikák olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/metricDefinitions/READ | Metrikus definíciók olvasása |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditingSettings/* | Naplózási beállítások szerkesztése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditRecords/READ | Az adatbázis blob-naplózási rekordjainak beolvasása |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/dataMaskingPolicies/* | Adatmaszkolási házirendek szerkesztése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityAlertPolicies/* | Biztonsági riasztási házirendek szerkesztése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityMetrics/* | Biztonsági mérőszámok szerkesztése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>SQL felügyelt példány közreműködője

Lehetővé teszi az SQL felügyelt példányok és a szükséges hálózati konfiguráció kezelését, de mások számára nem biztosít hozzáférést.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/READ |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/instanceFailoverGroups/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/* |  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Metrics/READ | Metrikák olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/metricDefinitions/READ | Metrikus definíciók olvasása |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/delete | Egy adott felügyelt kiszolgáló törlése Azure Active Directory csak hitelesítési objektum |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/Write | Egy adott felügyelt kiszolgáló hozzáadását vagy frissítését Azure Active Directory csak a hitelesítési objektum |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL-biztonságkezelő

Lehetővé teszi az SQL-kiszolgálók és-adatbázisok biztonsággal kapcsolatos házirendjeinek kezelését, az azokhoz való hozzáférés nélkül. [További információ](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/joinViaServiceEndpoint/Action | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/administratorAzureAsyncOperation/READ | A felügyelt példány Azure aszinkron rendszergazdai műveletei eredményének beolvasása. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/auditingSettings/* | SQL Server naplózási beállításának létrehozása és kezelése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/extendedAuditingSettings/READ | Az adott kiszolgálón konfigurált kiterjesztett kiszolgálói blob-naplózási szabályzat részleteinek beolvasása |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditingSettings/* | SQL Server-adatbázis naplózási beállításainak létrehozása és kezelése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditRecords/READ | Az adatbázis blob-naplózási rekordjainak beolvasása |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/dataMaskingPolicies/* | Az SQL Server-adatbázis adatmaszkolási házirendjeinek létrehozása és kezelése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/extendedAuditingSettings/READ | Az adott adatbázisban konfigurált kiterjesztett blob-naplózási szabályzat részleteinek beolvasása |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/READ | Az adatbázisok listájának visszaadása vagy a megadott adatbázis tulajdonságainak beolvasása. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/READ | Adatbázis-séma beszerzése. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Columns/READ | Adatbázis oszlopának beolvasása. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/READ | Adatbázis táblázatának beolvasása. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityAlertPolicies/* | SQL Server-adatbázis biztonsági riasztási házirendjeinek létrehozása és kezelése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityMetrics/* | SQL Server-adatbázis biztonsági metrikáinak létrehozása és kezelése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/transparentDataEncryption/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/firewallRules/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/READ | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/securityAlertPolicies/* | SQL Server biztonsági riasztási szabályzatok létrehozása és kezelése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureADOnlyAuthentications/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/READ | Visszaadja a felügyelt példányok listáját, vagy lekéri a megadott felügyelt példány tulajdonságait. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/sqlVulnerabilityAssessments/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*",
        "Microsoft.Security/sqlVulnerabilityAssessments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>SQL Server közreműködő

Lehetővé teszi az SQL-kiszolgálók és-adatbázisok kezelését, de azokhoz való hozzáférés nélkül, és nem a biztonsággal kapcsolatos házirendjeiket. [További információ](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Locations/*/READ |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/* | SQL-kiszolgálók létrehozása és kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Metrics/READ | Metrikák olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/metricDefinitions/READ | Metrikus definíciók olvasása |
> | **NotActions** |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/auditingSettings/* | Az SQL Server naplózási beállításainak szerkesztése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditingSettings/* | SQL Server-adatbázis naplózási beállításainak szerkesztése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/auditRecords/READ | Az adatbázis blob-naplózási rekordjainak beolvasása |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/currentSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/dataMaskingPolicies/* | Az SQL Server-adatbázis adatmaszkolási házirendjeinek szerkesztése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/recommendedSensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/schemas/Tables/Columns/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityAlertPolicies/* | SQL Server-adatbázis biztonsági riasztási házirendjeinek szerkesztése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/securityMetrics/* | SQL Server-adatbázis biztonsági metrikáinak szerkesztése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/sensitivityLabels/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/Databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/extendedAuditingSettings/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/securityAlertPolicies/* | SQL Server biztonsági riasztási szabályzatok szerkesztése |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/vulnerabilityAssessments/* |  |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureADOnlyAuthentications/delete | Egy adott kiszolgáló törlése Azure Active Directory csak hitelesítési objektum |
> | [Microsoft. SQL](resource-provider-operations.md#microsoftsql)/Servers/azureADOnlyAuthentications/Write | Egy adott kiszolgáló hozzáadását vagy frissítését Azure Active Directory csak a hitelesítési objektum |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Elemzés


### <a name="azure-event-hubs-data-owner"></a>Az Azure Event Hubs adattulajdonos

Lehetővé teszi az Azure Event Hubs-erőforrások teljes hozzáférését. [További információ](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs adatfogadó

Engedélyezi az Azure Event Hubs-erőforrások elérését. [További információ](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/READ |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs adatfeladó

Engedélyezi az Azure Event Hubs-erőforrásokhoz való hozzáférés küldését. [További információ](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/READ |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/*/Send/Action |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Data Factory közreműködő

Az adatüzemek létrehozása és kezelése, valamint a bennük található alárendelt erőforrások. [További információ](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Létrehozhatja és kezelheti az adatelőállítókat és a bennük található alárendelt erőforrásokat. |
> | [Microsoft. DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | Létrehozhatja és kezelheti az adatelőállítókat és a bennük található alárendelt erőforrásokat. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Write | EventSubscription létrehozása vagy frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Adattisztító

Az analitikai adattörlés [további információkkal](../azure-monitor/platform/personal-data-mgmt.md) is rendelkezhet.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. bepillantások](resource-provider-operations.md#microsoftinsights)/Components/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Components/Purge/Action | Adatok törlése a Application Insightsból |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/READ | Log Analytics-adatértékek megtekintése |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Purge/Action | Megadott adatok törlése a munkaterületről |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>HDInsight-fürt operátora

Lehetővé teszi a HDInsight-fürt konfigurációjának olvasását és módosítását. [További információ](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/*/READ |  |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/getGatewaySettings/Action | Átjáró beállításainak beolvasása a HDInsight-fürthöz |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/updateGatewaySettings/Action | HDInsight-fürthöz tartozó átjáró beállításainak frissítése |
> | [Microsoft. HDInsight](resource-provider-operations.md#microsofthdinsight)/Clusters/configurations/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/READ | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>HDInsight tartományi szolgáltatások közreműködője

Elolvashatja, létrehozhatja, módosíthatja és törölheti a HDInsight szükséges tartományi szolgáltatásokat Enterprise Security Package további [információ](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. HRE](resource-provider-operations.md#microsoftaad)/*/READ |  |
> | [Microsoft. HRE](resource-provider-operations.md#microsoftaad)/domainServices/*/READ |  |
> | [Microsoft. HRE](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Log Analytics közreműködő

Log Analytics közreműködő képes olvasni az összes figyelési és szerkesztési beállítást. A figyelési beállítások szerkesztése magában foglalja a virtuálisgép-bővítmény virtuális gépekhez való hozzáadását. a Storage-fiók kulcsainak beolvasása az Azure Storage-ból származó naplók gyűjtésének konfigurálásához; Automation-fiókok létrehozása és konfigurálása; megoldások hozzáadása; és az Azure Diagnostics konfigurálása az összes Azure-erőforráshoz. [További információ](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/Extensions/* |  |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/Extensions/* |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Extensions/Write | Azure arc-bővítmények telepítése vagy frissítése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a Analysis Server diagnosztikai beállításait. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourcegroups/Deployments/* |  |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Log Analytics olvasó

Log Analytics olvasó megtekintheti és megkeresheti az összes figyelési adatait, valamint megtekintheti a figyelési beállításokat, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson. [További információ](../azure-monitor/platform/manage-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Analytics/Query/Action | Keresés az új motor használatával. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Search/Action | Keresési lekérdezés végrehajtása |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/READ | Lekéri a munkaterület megosztott kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-curator"></a>A hatáskörébe tartozó adatkurátor

A Microsoft. hatáskörébe tartozó adatkezelő képes a katalógus-adatobjektumok létrehozására, olvasására, módosítására és törlésére, valamint az objektumok közötti kapcsolatok létrehozására. Ez a szerepkör előzetes verzióban érhető el, és változhat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. hatáskörébe](resource-provider-operations.md#microsoftpurview)/accounts/READ | A Microsoft hatáskörébe tartozó fiók erőforrásának beolvasása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. hatáskörébe](resource-provider-operations.md#microsoftpurview)/accounts/Data/READ | Adatobjektumok olvasása. |
> | [Microsoft. hatáskörébe](resource-provider-operations.md#microsoftpurview)/accounts/Data/Write | Adatobjektumok létrehozása, frissítése és törlése. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data curator can create, read, modify and delete catalog data objects and establish relationships between objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "name": "8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read",
        "Microsoft.Purview/accounts/data/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Curator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-reader"></a>A hatáskörébe tartozó Adatolvasó

A Microsoft. hatáskörébe Adatolvasó képes olvasni a katalógus adatobjektumait. Ez a szerepkör előzetes verzióban érhető el, és változhat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. hatáskörébe](resource-provider-operations.md#microsoftpurview)/accounts/READ | A Microsoft hatáskörébe tartozó fiók erőforrásának beolvasása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. hatáskörébe](resource-provider-operations.md#microsoftpurview)/accounts/Data/READ | Adatobjektumok olvasása. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data reader can read catalog data objects. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ff100721-1b9d-43d8-af52-42b69c1272db",
  "name": "ff100721-1b9d-43d8-af52-42b69c1272db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-source-administrator"></a>A hatáskörébe tartozó adatforrás rendszergazdája

A Microsoft. hatáskörébe adatforrás-rendszergazdája az adatforrások és az adatvizsgálatok kezelésére képes. Ez a szerepkör előzetes verzióban érhető el, és változhat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. hatáskörébe](resource-provider-operations.md#microsoftpurview)/accounts/READ | A Microsoft hatáskörébe tartozó fiók erőforrásának beolvasása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. hatáskörébe](resource-provider-operations.md#microsoftpurview)/accounts/Scan/READ | Az adatforrások és a vizsgálatok beolvasása. |
> | [Microsoft. hatáskörébe](resource-provider-operations.md#microsoftpurview)/accounts/Scan/Write | Adatforrások létrehozása, frissítése és törlése, valamint a vizsgálatok kezelése. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data source administrator can manage data sources and data scans. This role is in preview and subject to change.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/200bba9e-f0c8-430f-892b-6f0794863803",
  "name": "200bba9e-f0c8-430f-892b-6f0794863803",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/scan/read",
        "Microsoft.Purview/accounts/scan/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Source Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-contributor-preview"></a>Séma beállításjegyzékbeli közreműködői (előzetes verzió)

Séma-beállításjegyzékbeli csoportok és sémák olvasása, írása és törlése.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemas/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>Séma beállításjegyzék-olvasója (előzetes verzió)

Séma-beállításjegyzékbeli csoportok és sémák olvasása és listázása.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemagroups/READ | SchemaGroup-erőforrás leírásának beolvasása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. EventHub](resource-provider-operations.md#microsofteventhub)/Namespaces/schemas/READ | Sémák beolvasása |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockchain


### <a name="blockchain-member-node-access-preview"></a>Blockchain-hozzáférés (előzetes verzió)

Lehetővé teszi a Blockchain-tagok elérését. [További információ](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/READ | Lekérdezi vagy felsorolja a meglévő Blockchain-tag tranzakciós csomópont (oka) t. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/Connect/Action | Csatlakozás egy Blockchain-tag tranzakciós csomóponthoz. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI és gépi tanulás


### <a name="cognitive-services-contributor"></a>Cognitive Services közreműködő

Lehetővé teszi Cognitive Services kulcsainak létrehozását, olvasását, frissítését, törlését és kezelését. [További információ](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft. features](resource-provider-operations.md#microsoftfeatures)/features/READ | Egy előfizetés funkcióinak beolvasása. |
> | [Microsoft. features](resource-provider-operations.md#microsoftfeatures)/Providers/features/READ | Egy adott erőforrás-szolgáltatóhoz tartozó előfizetés funkciójának beolvasása. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a Analysis Server diagnosztikai beállításait. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/logDefinitions/READ | Napló-definíciók olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/metricdefinitions/READ | Metrikus definíciók olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Metrics/READ | Metrikák olvasása |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/READ | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/READ | Az előfizetések listájának beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourcegroups/Deployments/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services Adatolvasó (előzetes verzió)

Lehetővé teszi Cognitive Services-információk olvasását.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/READ |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services felhasználó

Lehetővé teszi Cognitive Services kulcsainak olvasását és listázását. [További információ](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/READ |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/Action | Kulcsok listázása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/READ | Klasszikus metrikai riasztás beolvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/READ | Erőforrás diagnosztikai beállításának beolvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/logDefinitions/READ | Napló-definíciók olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/metricdefinitions/READ | Metrikus definíciók olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Metrics/READ | Metrikák olvasása |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/READ | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/READ | Az előfizetések listájának beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Vegyes valóság


### <a name="remote-rendering-administrator"></a>Távoli renderelési rendszergazda

Lehetővé teszi a felhasználók számára az átalakítást, a munkamenet, a renderelés és a diagnosztikai funkciók kezelését az Azure távoli rendereléssel kapcsolatban. [További információ](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/Action | Eszköz átalakításának indítása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/READ | Eszköz-átalakítási tulajdonságok beolvasása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Convert/delete | Eszköz átalakításának leállítása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/READ | Munkamenet-tulajdonságok beolvasása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Munkamenetek elindítása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Munkamenetek leállítása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Render/READ | Kapcsolódás munkamenethez |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/READ | Kapcsolódás a távoli renderelési ellenőrhöz |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Távoli renderelési ügyfél

A lehetővé teszi, hogy a felhasználó kezelje a munkamenetet, a renderelési és diagnosztikai funkciókat az Azure távoli rendereléshez. [További információ](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/READ | Munkamenet-tulajdonságok beolvasása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/Action | Munkamenetek elindítása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Munkamenetek leállítása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Render/READ | Kapcsolódás munkamenethez |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/Diagnostic/READ | Kapcsolódás a távoli renderelési ellenőrhöz |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Térbeli horgonyok fiók közreműködője

Lehetővé teszi a térbeli horgonyok kezelését a fiókban, de nem törli őket [További információ](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/Action | Térbeli horgonyok létrehozása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/READ | Térbeli horgonyok tulajdonságainak beolvasása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/READ | Térbeli horgonyok keresése |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/READ | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Térbeli horgonyok tulajdonságainak frissítése |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Térbeli horgonyok fiókjának tulajdonosa

Lehetővé teszi a fiókban lévő térbeli horgonyok kezelését, beleértve a [további](../spatial-anchors/concepts/authentication.md) részletek törlését.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Create/Action | Térbeli horgonyok létrehozása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | Térbeli horgonyok törlése |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/READ | Térbeli horgonyok tulajdonságainak beolvasása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/READ | Térbeli horgonyok keresése |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/READ | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Write | Térbeli horgonyok tulajdonságainak frissítése |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Térbeli horgonyok fiókjának olvasója

Lehetővé teszi a fiókban található térbeli horgonyok tulajdonságainak megkeresését és beolvasását. [További információ](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Properties/READ | Térbeli horgonyok tulajdonságainak beolvasása |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/Query/READ | Térbeli horgonyok keresése |
> | [Microsoft. MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/READ | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integráció


### <a name="api-management-service-contributor"></a>API Management szolgáltatás közreműködői

Kezelheti a szolgáltatást és az API-kat. [További információ](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/* | API Management szolgáltatás létrehozása és kezelése |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>API Management Service operátori szerepkör

Kezelheti a szolgáltatást, de az API-k nem tudnak többet [megtudni](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/*/READ | API Management szolgáltatási példányok beolvasása |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Backup/Action | API Management szolgáltatás biztonsági mentése a megadott tárolóba egy felhasználó által megadott Storage-fiókban |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/delete | API Management szolgáltatás példányának törlése |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/managedeployments/Action | SKU/egységek módosítása, API Management szolgáltatás regionális központi telepítésének hozzáadása/eltávolítása |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/READ | API Management szolgáltatási példány metaadatainak olvasása |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Restore/Action | API Management szolgáltatás visszaállítása a megadott tárolóból egy felhasználó által megadott Storage-fiókban |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/updatecertificate/Action | TLS/SSL-tanúsítvány feltöltése egy API Management szolgáltatáshoz |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/updatehostname/Action | API Management szolgáltatás egyéni tartománynevének beállítása, frissítése vagy eltávolítása |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Write | API Management szolgáltatás példányának létrehozása vagy frissítése |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Users/Keys/READ | Felhasználóhoz tartozó kulcsok beolvasása |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>API Management szolgáltatás-olvasó szerepkör

Csak olvasási hozzáférés a szolgáltatáshoz és API-khoz [További információ](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/*/READ | API Management szolgáltatási példányok beolvasása |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/READ | API Management szolgáltatási példány metaadatainak olvasása |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | [Microsoft. ApiManagement](resource-provider-operations.md#microsoftapimanagement)/Service/Users/Keys/READ | Felhasználóhoz tartozó kulcsok beolvasása |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>Alkalmazás-konfigurációs adattulajdonos

Lehetővé teszi az alkalmazás konfigurációs adatának teljes hozzáférését. [További információ](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/READ |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/Write |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Az alkalmazás konfigurációs adatolvasója

Olvasási hozzáférés engedélyezése az alkalmazás konfigurációs adatfájljaihoz. [További információ](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/READ |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus adattulajdonos

Teljes hozzáférés engedélyezése Azure Service Bus erőforrásokhoz. [További információ](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Adatfogadó Azure Service Bus

Engedélyezi a hozzáférést Azure Service Bus erőforrásokhoz. [További információ](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Queues/READ |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/READ |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Subscriptions/READ |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Adatfeladó Azure Service Bus

Lehetővé teszi a Azure Service Bus erőforrásokhoz való hozzáférés küldését. [További információ](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Queues/READ |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/READ |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/Subscriptions/READ |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/Send/Action |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Azure Stack regisztrációs tulajdonos

Lehetővé teszi Azure Stack regisztrációk kezelését.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/READ | Azure Stack Edge-előfizetés tulajdonságainak beolvasása |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Products/*/Action |  |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/Products/READ | Egy Azure Stack Marketplace-termék tulajdonságainak beolvasása |
> | [Microsoft. AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/READ | Egy Azure Stack regisztráció tulajdonságainak beolvasása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription közreműködője

Lehetővé teszi a EventGrid esemény-előfizetési műveletek kezelését. [További információ](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* |  |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/READ | Globális esemény-előfizetések listázása a témakör típusa szerint |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/eventSubscriptions/READ | Regionális esemény-előfizetések listázása |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/topicTypes/eventSubscriptions/READ | Regionális esemény-előfizetések listázása topictype szerint |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription-olvasó

Lehetővé teszi a EventGrid esemény-előfizetések olvasását. [További információ](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/READ | EventSubscription beolvasása |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/READ | Globális esemény-előfizetések listázása a témakör típusa szerint |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/eventSubscriptions/READ | Regionális esemény-előfizetések listázása |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/Locations/topicTypes/eventSubscriptions/READ | Regionális esemény-előfizetések listázása topictype szerint |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>FHIR-adatközreműködő

A szerepkör lehetővé teszi, hogy a felhasználó vagy a rendszerbiztonsági tag teljes hozzáférése legyen a FHIR- [információhoz](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>FHIR-adatexportőr

A szerepkör lehetővé teszi a felhasználó vagy a résztvevő számára a FHIR-információk olvasását és exportálását. [További információ](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/szolgáltatások/fhir/erőforrások/olvasás | FHIR-erőforrások olvasása (beleértve a keresést és a verziószámozási előzményeket).  |
> | Microsoft. HealthcareApis/Services/fhir/erőforrások/exportálás/művelet | Exportálási művelet ($export). |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>FHIR Adatolvasó

A szerepkör lehetővé teszi a felhasználó vagy a résztvevő számára a FHIR-információk olvasását. [További információ](../healthcare-apis/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/szolgáltatások/fhir/erőforrások/olvasás | FHIR-erőforrások olvasása (beleértve a keresést és a verziószámozási előzményeket).  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>FHIR adatíró

A szerepkör lehetővé teszi, hogy a felhasználó vagy a résztvevő FHIR-információkat [tudjon](../healthcare-apis/configure-azure-rbac.md) olvasni és írni

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/* |  |
> | **NotDataActions** |  |
> | Microsoft. HealthcareApis/Services/fhir/Resources/hardDelete/Action | Rögzített törlés (beleértve a korábbi verziókat is). |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>integrációs szolgáltatási környezet közreműködő

Lehetővé teszi az integrációs szolgáltatási környezetek kezelését, de azokhoz való hozzáférés nélkül. [További információ](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>integrációs szolgáltatási környezet fejlesztő

Lehetővé teszi a fejlesztők számára munkafolyamatok, integrációs fiókok és API-kapcsolatok létrehozását és frissítését az integrációs szolgáltatási környezetekben. [További információ](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/READ | Beolvassa az integrációs szolgáltatási környezetet. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/*/JOIN/Action |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/*/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Intelligens rendszer-fiók közreműködői

Lehetővé teszi az intelligens rendszerfiókok kezelését, az azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | Microsoft. IntelligentSystems/fiókok/* | Intelligens rendszerek fiókjainak létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Logikai alkalmazás közreműködői

Lehetővé teszi a logikai alkalmazások kezelését, de nem változtatja meg a hozzájuk való hozzáférést. [További információ](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/Action | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | [Microsoft. ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/READ | Adja vissza a Storage-fiókot az adott fiókkal. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a Analysis Server diagnosztikai beállításait. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférnie a tevékenység naplóihoz. A műveletnapló kategóriáinak listázása a tevékenység naplójában. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | A metrikai definíciók (az adott erőforráshoz elérhető metrikai típusok listája) olvasása. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/* | Felügyeli Logic Apps erőforrásait. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/Action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/READ | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Hozzon létre és kezelje a kapcsolatok átjáróját. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connections/* | Hozzon létre és kezelje a kapcsolatokat. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/* | Egyéni API létrehozása és kezelése. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/JOIN/Action | Egy App Service csomag csatlakoztatása |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/READ | App Service csomag tulajdonságainak beolvasása |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Sites/functions/listSecrets/Action | A függvények titkainak listázása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Logic app-operátor

Lehetővé teszi a logikai alkalmazások olvasását, engedélyezését és letiltását, de nem szerkesztheti és nem frissíti őket. [További információ](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. bepillantások](resource-provider-operations.md#microsoftinsights)/alertRules/*/READ | Az információk beolvasása riasztási szabályok |
> | [Microsoft. bepillantások](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/READ |  |
> | [Microsoft. bepillantások](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/READ | A Logic Apps diagnosztikai beállításainak beolvasása |
> | [Microsoft. bepillantások](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/READ | A Logic Apps elérhető metrikáinak beolvasása. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/*/READ | Logic Apps erőforrások beolvasása. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/workflows/disable/Action | Letiltja a munkafolyamatot. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/workflows/Enable/Action | Engedélyezi a munkafolyamatot. |
> | [Microsoft. Logic](resource-provider-operations.md#microsoftlogic)/workflows/validate/Action | Ellenőrzi a munkafolyamatot. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/READ | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/READ | A kapcsolatok átjáróinak beolvasása. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/Connections/*/READ | Olvasási kapcsolatok. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/customApis/*/READ | Egyéni API olvasása. |
> | [Microsoft. Web](resource-provider-operations.md#microsoftweb)/serverFarms/READ | App Service csomag tulajdonságainak beolvasása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identitás


### <a name="managed-identity-contributor"></a>Felügyelt identitás közreműködői

Felhasználóhoz rendelt identitás létrehozása, olvasása, frissítése és törlése – [További információ](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/READ | Meglévő felhasználóhoz rendelt identitás beolvasása |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/Write | Új felhasználóhoz rendelt identitás létrehozása vagy egy meglévő felhasználóhoz rendelt identitáshoz társított címkék frissítése |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | Meglévő felhasználó által hozzárendelt identitás törlése |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Felügyelt identitás operátora

A felhasználóhoz rendelt identitás olvasása és hozzárendelése [További információ](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/READ |  |
> | [Microsoft. ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/Action |  |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Biztonság


### <a name="azure-sentinel-contributor"></a>Azure Sentinel Contributor

[További információ](../sentinel/roles.md) az Azure Sentinel közreműködőről

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Analytics/Query/Action | Keresés az új motor használatával. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/READ | Log Analytics-adatértékek megtekintése |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/READ | OMS-megoldás beolvasása |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Query/READ | Lekérdezések futtatása a munkaterület összes adatán |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Query/*/READ |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/READ | Munkaterületen lévő adatforrások beolvasása. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/myworkbooks/READ | Privát munkafüzet beolvasása |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel Reader

Azure Sentinel-olvasó [További információ](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/READ |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Felhasználói hitelesítés és licencek keresése |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/Query/Action | Veszélyforrások elleni lekérdezési mutatók |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/Action | Veszélyforrások elleni lekérdezési mutatók |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Analytics/Query/Action | Keresés az új motor használatával. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/READ | Log Analytics-adatértékek megtekintése |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/READ | Társított szolgáltatások beolvasása az adott munkaterületen. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/READ | Mentett keresési lekérdezés beolvasása |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/READ | OMS-megoldás beolvasása |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Query/READ | Lekérdezések futtatása a munkaterület összes adatán |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Query/*/READ |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/READ | Munkaterületen lévő adatforrások beolvasása. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/workbooks/READ | Munkafüzet beolvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/myworkbooks/READ | Privát munkafüzet beolvasása |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel Responder

[További információ](../sentinel/roles.md) az Azure Sentinel válaszadóról

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/READ |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/Action | Felhasználói hitelesítés és licencek keresése |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/automationRules/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/Cases/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/appendTags/Action | Címkék hozzáfűzése a veszélyforrások felderítéséhez |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/Query/Action | Veszélyforrások elleni lekérdezési mutatók |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/Action | Tömeges címkék veszélyforrások felderítése |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/appendTags/Action | Címkék hozzáfűzése a veszélyforrások felderítéséhez |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/Indicators/replaceTags/Action | A veszélyforrások intelligencia-mutatójának címkék cseréje |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/Action | Veszélyforrások elleni lekérdezési mutatók |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Analytics/Query/Action | Keresés az új motor használatával. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/READ | Log Analytics-adatértékek megtekintése |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/READ | Munkaterületen lévő adatforrások beolvasása. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/READ | Mentett keresési lekérdezés beolvasása |
> | [Microsoft. OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/Solutions/READ | OMS-megoldás beolvasása |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Query/READ | Lekérdezések futtatása a munkaterület összes adatán |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Query/*/READ |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/READ | Munkaterületen lévő adatforrások beolvasása. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/workbooks/READ | Munkafüzet beolvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/myworkbooks/READ | Privát munkafüzet beolvasása |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/Cases/*/delete |  |
> | [Microsoft. SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/Incidents/*/delete |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/automationRules/*",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.SecurityInsights/cases/*/Delete",
        "Microsoft.SecurityInsights/incidents/*/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator-preview"></a>Key Vault rendszergazda (előzetes verzió)

Minden adatsík művelet végrehajtása a kulcstartón és az abban található összes objektumon, beleértve a tanúsítványokat, a kulcsokat és a titkos kulcsokat is. A Key Vault-erőforrások nem kezelhetők, illetve nem kezelhetők a szerepkör-hozzárendelések. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/checkNameAvailability/READ | Ellenőrzi, hogy a kulcstároló neve érvényes-e, és nincs-e használatban |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/deletedVaults/READ | A Soft Deleted Key vaultok tulajdonságainak megtekintése |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Locations/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Operations/READ | A Microsoft. kulcstartó erőforrás-szolgáltatóján elérhető műveletek listája |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer-preview"></a>Key Vault Certificates Officer (előzetes verzió)

Hajtson végre bármilyen műveletet a Key Vault tanúsítványán, kivéve a kezelési engedélyeket. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/checkNameAvailability/READ | Ellenőrzi, hogy a kulcstároló neve érvényes-e, és nincs-e használatban |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/deletedVaults/READ | A Soft Deleted Key vaultok tulajdonságainak megtekintése |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Locations/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Operations/READ | A Microsoft. kulcstartó erőforrás-szolgáltatóján elérhető műveletek listája |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/certificatecas/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Certificates/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Key Vault közreműködő

A kulcstartók kezelése, de nem teszi lehetővé szerepkörök hozzárendelését az Azure RBAC, és nem teszi lehetővé a titkok, kulcsok és tanúsítványok elérését. [További információ](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. kulcstartó](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Locations/deletedVaults/Purge/Action | Helyreállítható törölhető kulcstartó törlése |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/hsmPools/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/managedHsms/* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-officer-preview"></a>Key Vault titkosítási tisztviselő (előzetes verzió)

Hajtson végre bármilyen műveletet a kulcstartó kulcsain, kivéve a kezelési engedélyeket. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/checkNameAvailability/READ | Ellenőrzi, hogy a kulcstároló neve érvényes-e, és nincs-e használatban |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/deletedVaults/READ | A Soft Deleted Key vaultok tulajdonságainak megtekintése |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Locations/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Operations/READ | A Microsoft. kulcstartó erőforrás-szolgáltatóján elérhető műveletek listája |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Keys/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-user-preview"></a>Key Vault kriptográfiai szolgáltatás titkosítási felhasználója (előzetes verzió)

A kulcsok metaadatainak olvasása, valamint a becsomagolási/kicsomagolási műveletek végrehajtása. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/Write | EventSubscription létrehozása vagy frissítése |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/READ | EventSubscription beolvasása |
> | [Microsoft. EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/delete | EventSubscription törlése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Keys/READ | A megadott tárolóban található kulcsok listázása, illetve a kulcsok beolvasása és a kulcs nyilvános anyaga. Az aszimmetrikus kulcsok esetében ez a művelet közzéteszi a nyilvános kulcsot, és olyan nyilvános kulcsú algoritmusokat is képes végrehajtani, mint például a titkosítás és az aláírás ellenőrzése. A titkos kulcsok és a szimmetrikus kulcsok soha nem érhetők el. |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Keys/wrap/Action | Szimmetrikus kulcs becsomagolása Key Vault kulccsal. Vegye figyelembe, hogy ha a Key Vault kulcs aszimmetrikus, ezt a műveletet olvasási hozzáféréssel rendelkező rendszerbiztonsági tag is elvégezheti. |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Keys/unwrap/Action | Egy szimmetrikus kulcs kicsomagolása Key Vault kulccsal. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventGrid/eventSubscriptions/write",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/eventSubscriptions/delete"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user-preview"></a>Key Vault kriptográfiai felhasználó (előzetes verzió)

Titkosítási műveletek végrehajtása kulcsok használatával. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Keys/READ | A megadott tárolóban található kulcsok listázása, illetve a kulcsok beolvasása és a kulcs nyilvános anyaga. Az aszimmetrikus kulcsok esetében ez a művelet közzéteszi a nyilvános kulcsot, és olyan nyilvános kulcsú algoritmusokat is képes végrehajtani, mint például a titkosítás és az aláírás ellenőrzése. A titkos kulcsok és a szimmetrikus kulcsok soha nem érhetők el. |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Keys/Update/Action | Az adott kulcshoz társított megadott attribútumok frissítése. |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Keys/Backup/Action | Létrehoz egy kulcs biztonságimásolat-fájlját. A fájl a kulcs visszaállítására használható ugyanazon előfizetés Key Vaultében. A korlátozások vonatkozhatnak. |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Keys/encrypt/Action | Titkosítja az egyszerű szöveges kulcsot. Vegye figyelembe, hogy ha a kulcs aszimmetrikus, akkor ezt a műveletet olvasási hozzáféréssel rendelkező rendszerbiztonsági tag végezheti el. |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Keys/decrypt/Action | Visszafejti a rejtjelezett egy kulccsal. |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Keys/wrap/Action | Szimmetrikus kulcs becsomagolása Key Vault kulccsal. Vegye figyelembe, hogy ha a Key Vault kulcs aszimmetrikus, ezt a műveletet olvasási hozzáféréssel rendelkező rendszerbiztonsági tag is elvégezheti. |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Keys/unwrap/Action | Egy szimmetrikus kulcs kicsomagolása Key Vault kulccsal. |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Keys/Sign/Action | Egy üzenet kivonatának (kivonatának) regisztrálása kulccsal. |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Keys/Verify/Action | Ellenőrzi, hogy van-e egy kulccsal rendelkező üzenet-kivonat (kivonat) aláírása. Vegye figyelembe, hogy ha a kulcs aszimmetrikus, akkor ezt a műveletet olvasási hozzáféréssel rendelkező rendszerbiztonsági tag végezheti el. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader-preview"></a>Key Vault olvasó (előzetes verzió)

A kulcstartók és a hozzá tartozó tanúsítványok, kulcsok és titkos kódok metaadatainak olvasása. Nem lehet olvasni a bizalmas értékeket, például a titkos tartalmat vagy a kulcs anyagát. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/checkNameAvailability/READ | Ellenőrzi, hogy a kulcstároló neve érvényes-e, és nincs-e használatban |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/deletedVaults/READ | A Soft Deleted Key vaultok tulajdonságainak megtekintése |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Locations/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Operations/READ | A Microsoft. kulcstartó erőforrás-szolgáltatóján elérhető műveletek listája |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Secrets/readMetadata/Action | Egy titkos kulcs tulajdonságainak listázása vagy megtekintése, az értéke nem. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer-preview"></a>Key Vault Secrets Officer (előzetes verzió)

Hajtson végre bármilyen műveletet a Key Vault titkos kulcsain, kivéve a kezelési engedélyeket. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/checkNameAvailability/READ | Ellenőrzi, hogy a kulcstároló neve érvényes-e, és nincs-e használatban |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/deletedVaults/READ | A Soft Deleted Key vaultok tulajdonságainak megtekintése |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Locations/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/*/READ |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Operations/READ | A Microsoft. kulcstartó erőforrás-szolgáltatóján elérhető műveletek listája |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Secrets/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user-preview"></a>Key Vault Secrets User (előzetes verzió)

Titkos tartalom olvasása. Csak az "Azure szerepköralapú hozzáférés-vezérlés" engedélyezési modellt használó kulcstartók esetében működik.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Secrets/getSecret/Action | Egy titkos kulcs értékének beolvasása. |
> | [Microsoft.](resource-provider-operations.md#microsoftkeyvault)kulcstartó/Vaults/Secrets/readMetadata/Action | Egy titkos kulcs tulajdonságainak listázása vagy megtekintése, az értéke nem. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User (preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Biztonsági rendszergazda

Security Center engedélyeinek megtekintése és frissítése. Ugyanazok az engedélyek, mint a biztonsági olvasó szerepkör, és a biztonsági szabályzatot is frissíthetik, és elérhetik a riasztásokat és a javaslatokat. [További információ](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Szabályzat-hozzárendelések létrehozása és kezelése |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | Szabályzat-definíciók létrehozása és kezelése |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* | Házirend-kivételek létrehozása és kezelése |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Szabályzat-készletek létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/READ | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/READ | Log Analytics-adatértékek megtekintése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Biztonsági összetevők és házirendek létrehozása és kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policyExemptions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Biztonsági értékelő közreműködő

Lehetővé teszi az értékelések leküldését Security Center

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/assessments/Write | Biztonsági értékelések létrehozása vagy frissítése az előfizetésen |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Security Manager (örökölt)

Ez egy örökölt szerepkör. Használja helyette a biztonsági rendszergazdát.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/READ | Konfigurációs információk beolvasása a klasszikus virtuális gépeken |
> | [Microsoft. ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/Write | A klasszikus virtuális gépek írási konfigurációja |
> | [Microsoft. ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/READ | A klasszikus hálózat konfigurációs információinak olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/* | Biztonsági összetevők és házirendek létrehozása és kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Biztonsági olvasó

Security Center engedélyeinek megtekintése. Megtekintheti a javaslatokat, a riasztásokat, a biztonsági házirendeket és a biztonsági állapotokat, de nem végezhet módosításokat. [További információ](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/READ | Klasszikus metrikai riasztás beolvasása |
> | [Microsoft. operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/READ | Log Analytics-adatértékek megtekintése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/*/READ |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/*/READ | Biztonsági összetevők és szabályzatok olvasása |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/*/READ |  |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/packageDownloads/Action | Lekéri a letölthető IoT Defender-csomagok adatait |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/downloadManagerActivation/Action | Az előfizetés-kvótával rendelkező kezelői aktiválási fájl letöltése |
> | [Microsoft. Security](resource-provider-operations.md#microsoftsecurity)/iotSensors/downloadResetPassword/Action | A IoT-érzékelők letöltésének új jelszava |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/READ | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Security/iotDefenderSettings/packageDownloads/action",
        "Microsoft.Security/iotDefenderSettings/downloadManagerActivation/action",
        "Microsoft.Security/iotSensors/downloadResetPassword/action",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Labs-felhasználó

Lehetővé teszi a virtuális gépek a Azure DevTest Labs való összekapcsolását, indítását, újraindítását és leállítását. [További információ](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/availabilitySets/READ | Rendelkezésre állási csoport tulajdonságainak beolvasása |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/READ | Virtuális gép tulajdonságainak beolvasása (virtuálisgép-méretek, futásidejű állapot, virtuálisgép-bővítmények stb.) |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/Action | Kikapcsolja a virtuális gépet, és felszabadítja a számítási erőforrásokat |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/READ | Virtuális gép tulajdonságainak beolvasása |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/Action | Újraindítja a virtuális gépet |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/Start/Action | Elindítja a virtuális gépet |
> | [Microsoft. segédösszetevője](resource-provider-operations.md#microsoftdevtestlab)/*/READ | Tesztkörnyezet tulajdonságainak olvasása |
> | [Microsoft. segédösszetevője](resource-provider-operations.md#microsoftdevtestlab)/Labs/claimAnyVm/Action | Véletlenszerű, igényelhető virtuális gép igénylése a laborban. |
> | [Microsoft. segédösszetevője](resource-provider-operations.md#microsoftdevtestlab)/Labs/createEnvironment/Action | Virtuális gépek létrehozása tesztkörnyezetben. |
> | [Microsoft. segédösszetevője](resource-provider-operations.md#microsoftdevtestlab)/Labs/ensureCurrentUserProfile/Action | Ellenőrizze, hogy az aktuális felhasználó rendelkezik-e érvényes profillal a laborban. |
> | [Microsoft. segédösszetevője](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/delete | Képletek törlése. |
> | [Microsoft. segédösszetevője](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/READ | Képletek olvasása. |
> | [Microsoft. segédösszetevője](resource-provider-operations.md#microsoftdevtestlab)/Labs/formulas/Write | Képletek hozzáadása vagy módosítása. |
> | [Microsoft. segédösszetevője](resource-provider-operations.md#microsoftdevtestlab)/Labs/policySets/evaluatePolicies/Action | Kiértékeli a tesztkörnyezet házirendjét. |
> | [Microsoft. segédösszetevője](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/claim/Action | Meglévő virtuális gép tulajdonjogának átvétele |
> | [Microsoft. segédösszetevője](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualmachines/listApplicableSchedules/Action | Felsorolja a megfelelő indítási/leállítási ütemterveket, ha vannak ilyenek. |
> | [Microsoft. segédösszetevője](resource-provider-operations.md#microsoftdevtestlab)/Labs/virtualMachines/getRdpFileContents/Action | Egy olyan karakterlánc beolvasása, amely a virtuális gép RDP-fájljának tartalmát jelöli |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/JOIN/Action | A terheléselosztó háttérbeli címkészlet csatlakoztatása. Nem Riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/JOIN/Action | A terheléselosztó bejövő NAT-szabályához csatlakozik. Nem Riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/READ | Olvassa el egy hálózati adapter tulajdonságait (például az összes olyan terheléselosztó, amely a hálózati adapter részét képezi) |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/JOIN/Action | Csatlakoztat egy virtuális gépet egy hálózati adapterhez. Nem Riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/READ | Hálózati adapter definíciójának beolvasása.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/Write | Hálózati adaptert hoz létre, vagy frissít egy meglévő hálózati adaptert.  |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/READ | Nyilvános IP-cím tulajdonságainak beolvasása |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/JOIN/Action | Csatlakozik egy nyilvános IP-címhez. Nem Riasztásos. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/READ | Egy nyilvános IP-cím definíciójának beolvasása. |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/Subnets/JOIN/Action | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Operations/READ | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/READ | Lekérdezi vagy felsorolja az üzemelő példányokat. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/Action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | **NotActions** |  |
> | [Microsoft. számítási](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/READ | A virtuális gép által frissíthető elérhető méretek listája |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Tesztkörnyezet létrehozója

Lehetővé teszi, hogy új laborokat hozzon létre az Azure Lab-fiókjaiban. [További információ](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/READ |  |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/Action | Tesztkörnyezet létrehozása labor-fiókban. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/Action | A Lab-fiókhoz tartozó méretek, földrajzi és operációs rendszerek kombinációinak díjszabása és rendelkezésre állása. |
> | [Microsoft. LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/Action | Az előfizetéshez tartozó alapvető korlátozások és használat beszerzése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>Monitor


### <a name="application-insights-component-contributor"></a>Application Insights összetevő közreműködője

Felügyelheti Application Insights összetevőket. [További információ](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus riasztási szabályok létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/generateLiveToken/READ | Élő metrikák beolvasása token |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Új riasztási szabályok létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Components/* | Az adatáttekintési összetevők létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Topology/READ | Topológia olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Transactions/READ | Olvasási tranzakciók |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/webtests/* | Webes tesztek létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/generateLiveToken/read",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/topology/read",
        "Microsoft.Insights/transactions/read",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger

Felhasználói jogosultságot biztosít a Application Insights Snapshot Debugger gyűjtött hibakeresési Pillanatképek megtekintéséhez és letöltéséhez. Vegye figyelembe, hogy ezek az engedélyek nem szerepelnek a [tulajdonos](#owner) vagy a [közreműködő](#contributor) szerepkörben. Ha a felhasználók számára a Application Insights Snapshot Debugger szerepkört adja meg, közvetlenül a felhasználónak kell megadnia a szerepkört. A szerepkör nem ismerhető fel, ha hozzá van adva egy egyéni szerepkörhöz. [További információ](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. bepillantások](resource-provider-operations.md#microsoftinsights)/Components/*/READ |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Közreműködő figyelése

Beolvashatja az összes figyelési és a figyelési beállításokat. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). [További információ](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/Alerts/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Components/* | Az adatáttekintési összetevők létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a Analysis Server diagnosztikai beállításait. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/eventtypes/* | Az előfizetésben szereplő tevékenység-naplózási események (kezelési események) listázása. Ez az engedély mind a programozási, mind a portálhoz való hozzáférésre alkalmazható a tevékenység naplójában. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférnie a tevékenység naplóihoz. A műveletnapló kategóriáinak listázása a tevékenység naplójában. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | A metrikai definíciók (az adott erőforráshoz elérhető metrikai típusok listája) olvasása. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Metrics/* | Erőforrás metrikáinak olvasása. |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Register/Action | A Microsoft bepillantást nyújtó szolgáltató regisztrálása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/webtests/* | Webes tesztek létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Write | Létrehoz egy új munkaterületet vagy egy meglévő munkaterületre mutató hivatkozásokat úgy, hogy megadja az ügyfél azonosítóját a meglévő munkaterületen. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | Log Analytics-megoldási csomagok olvasása/írása/törlése. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | A log Analytics-beli mentett keresések olvasása/írása/törlése. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Search/Action | Keresési lekérdezés végrehajtása |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/Action | Lekéri a munkaterület megosztott kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | A log Analytics tárolási Insight-konfigurációinak olvasása/írása/törlése. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/Monitors/* | Információ kérése a vendég virtuális gép állapotának figyelői. |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft. AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Figyelési metrikák közzétevője

Lehetővé teszi az Azure-erőforrásokhoz való közzétételi mérőszámok [megismerését](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Register/Action | A Microsoft bepillantást nyújtó szolgáltató regisztrálása |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Mérőszámok írása |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Figyelő olvasó

Az összes figyelési adat (mérőszámok, naplók stb.) olvasása. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). [További információ](../azure-monitor/platform/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | [Microsoft. OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/Search/Action | Keresési lekérdezés végrehajtása |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Munkafüzet közreműködője

Menthetők a megosztott munkafüzetek. [További információ](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/workbooks/Write | Munkafüzet létrehozása vagy frissítése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/workbooks/delete | Munkafüzet törlése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/workbooks/READ | Munkafüzet beolvasása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Munkafüzet-olvasó

A munkafüzetek beolvasása. [További információ](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/workbooks/READ | Munkafüzet beolvasása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Felügyelet és irányítás


### <a name="automation-job-operator"></a>Automation-feladatok operátora

Feladatok létrehozása és kezelése Automation-Runbookok használatával. [További információ](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/READ | Hibrid Runbook Worker-erőforrások beolvasása |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/READ | Azure Automation feladatok beolvasása |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Resume/Action | Egy Azure Automation feladatot folytat |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/stop/Action | Leállítja egy Azure Automation feladatot |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Streams/READ | Beolvas egy Azure Automation-feladatok streamjét |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/suspend/Action | Azure Automation feladatok felfüggesztése |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Write | Létrehoz egy Azure Automation feladatot |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/output/READ | A feladatok kimenetének beolvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Automation-operátor

Az Automation-operátorok [többek](../automation/automation-role-based-access-control.md) között a feladatok elindítására, leállítására, felfüggesztésére és folytatására képesek

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/READ | Hibrid Runbook Worker-erőforrások beolvasása |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/READ | Azure Automation feladatok beolvasása |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Resume/Action | Egy Azure Automation feladatot folytat |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/stop/Action | Leállítja egy Azure Automation feladatot |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Streams/READ | Beolvas egy Azure Automation-feladatok streamjét |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/suspend/Action | Azure Automation feladatok felfüggesztése |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/Write | Létrehoz egy Azure Automation feladatot |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/READ | Azure Automation feladatütemezés beolvasása |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/Write | Létrehoz egy Azure Automation feladatok ütemtervét |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/READ | Az Automation-fiókhoz kapcsolódó munkaterület beolvasása |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/READ | Azure Automation fiók beolvasása |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/READ | Azure Automation runbook beolvasása |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Schedules/READ | Azure Automation ütemezett eszköz beolvasása |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Schedules/Write | Egy Azure Automation Schedule-eszköz létrehozása vagy frissítése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/Jobs/output/READ | A feladatok kimenetének beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Automation Runbook operátor

A Runbook tulajdonságainak olvasása – a Runbook feladatai létrehozásához. [További információ](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/READ | Azure Automation runbook beolvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Azure-beli csatlakoztatott gépek bevezetése

Az Azure-beli csatlakoztatott gépeket is képes bevezetni. [További információ](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/READ | Bármely Azure-beli ív-gép beolvasása |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Write | Azure arc-gépek írása |
> | [Microsoft. GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/READ | Vendég-konfiguráció hozzárendelésének beolvasása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Azure-beli csatlakoztatott számítógép erőforrás-rendszergazdája

Képes olvasni, írni, törölni és újratelepíteni az Azure-beli csatlakoztatott gépeket.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/READ | Bármely Azure-beli ív-gép beolvasása |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Write | Azure arc-gépek írása |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/delete | Azure-beli ív-gépek törlése |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/reconnect/Action |  |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/Machines/Extensions/Write | Azure arc-bővítmények telepítése vagy frissítése |
> | [Microsoft. HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/READ |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Számlázás olvasója

Olvasási hozzáférés engedélyezése a [Számlázási információhoz](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. Billing](resource-provider-operations.md#microsoftbilling)/*/READ | Számlázási adatok olvasása |
> | [Microsoft. Commerce](resource-provider-operations.md#microsoftcommerce)/*/READ |  |
> | [Microsoft. fogyasztás](resource-provider-operations.md#microsoftconsumption)/*/READ |  |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/READ | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/READ |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Tervezet közreműködője

Kezelheti a terv definícióit, de nem rendelheti hozzá őket. [További információ](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint)/BluePrints/* | Terv-definíciók vagy tervrajz-összetevők létrehozása és kezelése. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Blueprint operátor

Meglévő közzétett tervrajzokat is hozzárendelhet, de nem hozhat létre új tervrajzokat. Vegye figyelembe, hogy ez csak akkor működik, ha a hozzárendelés felhasználó által hozzárendelt felügyelt identitással lett végrehajtva. [További információ](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Terv-hozzárendelések létrehozása és kezelése. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Cost Management közreműködő

Megtekintheti a költségeket és kezelheti a költségek konfigurációját (pl. költségvetések, exportálások). [További információ](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. felhasználás](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft. számlázási](resource-provider-operations.md#microsoftbilling)/billingPeriods/READ |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/READ | Az előfizetések listájának beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/READ | Konfigurációk beolvasása |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/Recommendations/READ | Olvasási javaslatok |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/READ | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | [Microsoft. számlázási](resource-provider-operations.md#microsoftbilling)/billingProperty/READ |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Cost Management olvasó

Megtekintheti a költségeket és a konfigurációt (például a költségvetést, az exportot). [További információ](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. fogyasztás](resource-provider-operations.md#microsoftconsumption)/*/READ |  |
> | [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/READ |  |
> | [Microsoft. számlázási](resource-provider-operations.md#microsoftbilling)/billingPeriods/READ |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/READ | Az előfizetések listájának beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/READ | Konfigurációk beolvasása |
> | [Microsoft. Advisor](resource-provider-operations.md#microsoftadvisor)/Recommendations/READ | Olvasási javaslatok |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/READ | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | [Microsoft. számlázási](resource-provider-operations.md#microsoftbilling)/billingProperty/READ |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>Hierarchia-beállítások rendszergazdája

Lehetővé teszi a felhasználóknak a hierarchia beállításainak szerkesztését és törlését

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Settings/Write | A felügyeleti csoport hierarchiájának beállításait hozza létre vagy frissíti. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Settings/delete | Törli a felügyeleti csoport hierarchiájának beállításait. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes-fürt – Azure arc bevezetése

Szerepkör-definíció a connectedClusters [-erőforrások](../azure-arc/kubernetes/connect-cluster.md) létrehozásához szükséges felhasználók vagy szolgáltatások engedélyezéséhez

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/Write | Egy központi telepítés létrehozása vagy frissítése. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/READ | Az előfizetések listájának beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | ConnectedClusters írása |
> | [Microsoft. Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/READ | ConnectedClusters olvasása |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Felügyelt alkalmazás közreműködői szerepköre

Lehetővé teszi a felügyelt alkalmazások erőforrásainak létrehozását.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/* |  |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Register/Action | Regisztráljon a megoldásokra. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/* |  |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Felügyelt alkalmazás operátori szerepköre

Lehetővé teszi műveletek olvasását és végrehajtását a felügyelt alkalmazás erőforrásain

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/Applications/READ | Az alkalmazások listájának beolvasása. |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/*/Action |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Felügyelt alkalmazások olvasója

Lehetővé teszi az erőforrások olvasását egy felügyelt alkalmazásban, valamint a JIT-hozzáférés kérését.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköre

A felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköre lehetővé teszi a bérlői felhasználók számára a bérlőhöz rendelt regisztrációs hozzárendelés törlését. [További információ](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/READ | A felügyelt szolgáltatások regisztrációs hozzárendeléseinek listáját kéri le. |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | Eltávolítja a felügyelt szolgáltatások regisztrációjának hozzárendelését. |
> | [Microsoft. ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/READ | Az erőforrás műveleti állapotának beolvasása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Felügyeleti csoport közreműködője

Felügyeleti csoport közreműködői szerepköre [További információ](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/delete | Felügyeleti csoport törlése. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/READ | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Subscriptions/delete | Az előfizetés társítása a felügyeleti csoportból. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Subscriptions/Write | Meglévő előfizetés társítása a felügyeleti csoporttal. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Write | Hozzon létre vagy frissítsen egy felügyeleti csoportot. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Subscriptions/READ | Az adott felügyeleti csoportba tartozó előfizetés felsorolása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Felügyeleti csoport olvasója

Felügyeleti csoport olvasójának szerepköre

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/READ | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | [Microsoft. Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/Subscriptions/READ | Az adott felügyeleti csoportba tartozó előfizetés felsorolása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Új ereklye APM-fiók közreműködői

Lehetővé teszi New Relic Application Performance Management-fiókok és-alkalmazások kezelését, az azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | NewRelic. APM/fiókok/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Házirend-elemzések adatírója (előzetes verzió)

Olvasási hozzáférést biztosít az erőforrás-házirendekhez, és írási hozzáférést biztosít az erőforrás-összetevőkre vonatkozó házirend eseményeihez. [További információ](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/READ | Szabályzat-hozzárendelés adatainak beolvasása. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/READ | Házirend-definíció adatainak beolvasása. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/READ | Házirend-kivétel információinak beolvasása. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/READ | Házirend-készlet definíciójának beolvasása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/Action | Egy adott összetevő megfelelőségi állapotának ellenőrzése adatszabályzatok szerint. |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/Action | Naplózza az erőforrás-összetevő házirendjének eseményeit. |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policyexemptions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Erőforrás-szabályzat közreműködője

Az erőforrás-házirend létrehozásához/módosításához, támogatási jegy létrehozásához és az erőforrások/hierarchia olvasásához jogosultsággal rendelkező felhasználók. [További információ](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Szabályzat-hozzárendelések létrehozása és kezelése |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | Szabályzat-definíciók létrehozása és kezelése |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* | Házirend-kivételek létrehozása és kezelése |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Szabályzat-készletek létrehozása és kezelése |
> | [Microsoft. PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policyexemptions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Site Recovery-közreműködő

Lehetővé teszi Site Recovery szolgáltatás felügyeletét, kivéve a tár létrehozását és a szerepkör-hozzárendelést. [További információ](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/READ | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocateStamp/Action | A AllocateStamp a szolgáltatás által használt belső művelet |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/Certificates/Write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tároló hitelesítő adatait tartalmazó tanúsítványt. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | A tár szolgáltatással kapcsolatos bővített adatok létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/READ | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/READ |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Regisztrált identitások létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/* | Replikációs riasztási beállítások létrehozása vagy frissítése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/READ | Események beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/* | Replikációs hálók létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/* | Replikációs feladatok létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/* | Replikációs házirendek létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/* | Helyreállítási tervek létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Recovery Services-tároló tárolási konfigurációjának létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/READ |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/READ | Egy Recovery Services tár használati adatait adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/READ | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | A Recovery Services-tárolóra vonatkozó olvasási riasztások |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/READ |  |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/READ | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationOperationStatus/READ | A tár replikációs művelet állapotának beolvasása |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Site Recovery-operátor

Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem hajt végre más Site Recovery felügyeleti műveleteket. [További információ](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/READ | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocateStamp/Action | A AllocateStamp a szolgáltatás által használt belső művelet |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/READ | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/READ | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/READ |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/READ | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/READ | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/READ | Riasztási beállítások beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/READ | Események beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/checkConsistency/Action | A háló konzisztenciájának ellenőrzése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/READ | Bármilyen háló beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/reassociateGateway/Action | Átjáró újrahozzárendelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/renewcertificate/Action | Háló tanúsítványának megújítása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/READ | Bármilyen hálózat beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/READ | Hálózati leképezések olvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/READ | Védelmi tárolók beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/READ | Bármely védhető elem olvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/Action | Helyreállítási pont alkalmazása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/Action | Feladatátvétel véglegesítve |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/Action | Tervezett feladatátvétel |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/READ | Védett elemek beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/READ | Replikációs helyreállítási pontok olvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/Action | Replikáció javítása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/Action | Védett elemek ismételt védelme |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/switchprotection/Action | Védelmi tároló váltása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/Action | Feladatátvételi teszt |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/Action | Feladatátvételi teszt karbantartása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/Action | Feladatátvétel |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/Action | Mobilitási szolgáltatás frissítése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/READ | A védelmi tárolók megfeleltetésének beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/READ | Recovery Services szolgáltatók beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/Action | Szolgáltató frissítése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/READ | A tárolási besorolások beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/READ | A tárolási besorolási leképezések beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationvCenters/READ | Bármilyen vCenter beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/* | Replikációs feladatok létrehozása és kezelése |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/READ | Bármilyen szabályzat beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/failoverCommit/Action | Feladatátvételi véglegesítés helyreállítási terve |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/plannedFailover/Action | Tervezett feladatátvétel helyreállítási terve |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/READ | Helyreállítási tervek olvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/reProtect/Action | Helyreállítási terv ismételt védetté |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/testFailover/Action | Feladatátvételi teszt helyreállítási terve |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/testFailoverCleanup/Action | Feladatátvételi teszt karbantartásának helyreállítási terve |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/unplannedFailover/Action | Feladatátvétel helyreállítási terve |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | A Recovery Services-tárolóra vonatkozó olvasási riasztások |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/READ |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/READ |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/READ |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/READ | Egy Recovery Services tár használati adatait adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/READ | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/READ | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Site Recovery-olvasó

Lehetővé teszi, hogy megtekintse Site Recovery állapotát, de ne végezzen más felügyeleti műveleteket. [További információ](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Locations/allocatedStamp/READ | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/READ | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/READ | Lekéri a Recovery Services-tároló riasztásait. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/READ |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/READ | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/READ |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/READ | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/READ | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationAlertSettings/READ | Riasztási beállítások beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationEvents/READ | Események beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/READ | Bármilyen háló beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/READ | Bármilyen hálózat beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/READ | Hálózati leképezések olvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/READ | Védelmi tárolók beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/READ | Bármely védhető elem olvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/READ | Védett elemek beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/READ | Replikációs helyreállítási pontok olvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/READ | A védelmi tárolók megfeleltetésének beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationRecoveryServicesProviders/READ | Recovery Services szolgáltatók beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/READ | A tárolási besorolások beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/READ | A tárolási besorolási leképezések beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationFabrics/replicationvCenters/READ | Bármilyen vCenter beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationJobs/READ | Bármilyen feladat beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationPolicies/READ | Bármilyen szabályzat beolvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/replicationRecoveryPlans/READ | Helyreállítási tervek olvasása |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/READ |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/READ |  |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/READ | Egy Recovery Services tár használati adatait adja vissza. |
> | [Microsoft. recoveryservices szolgáltatónál](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/READ | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Támogatási kérelem közreműködői

Lehetővé teszi a támogatási kérések létrehozását és kezelését. [További információ](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>Tag közreműködői

Lehetővé teszi a címkék felügyeletét az entitásokban anélkül, hogy hozzáférést kellene biztosítani magukhoz az entitásokhoz. [További információ](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/Resources/READ | Az erőforráscsoport erőforrásainak beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/Resources/READ | Az előfizetés erőforrásainak beolvasása. |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Tags/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Egyéb


### <a name="azure-digital-twins-data-owner"></a>Azure digitális Twins-adattulajdonos

Teljes körű hozzáférési szerepkör a digitális Twins-adatközpontok számára – [További információ](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | Bármely esemény útvonalának olvasása, törlése, létrehozása vagy frissítése |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | Tetszőleges digitális iker olvasása, létrehozása, frissítése vagy törlése |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/commands/* | Bármilyen parancs meghívása digitális Twin-re |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Relationships/* | Tetszőleges digitális kettős kapcsolat olvasása, létrehozása, frissítése vagy törlése |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | Bármilyen modell olvasása, létrehozása, frissítése vagy törlése |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Query/* | Bármely digitális Twins-gráf lekérdezése |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Azure digitális Twins-Adatolvasó

Csak olvasási szerepkör a digitális Twins-adatközpont tulajdonságairól [További információ](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/READ | Tetszőleges digitális iker olvasása |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/Relationships/READ | Tetszőleges digitális kettős kapcsolat olvasása |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/READ | Bármely esemény útvonalának olvasása |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/READ | Bármely modell olvasása |
> | [Microsoft. DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/Query/Action | Bármely digitális Twins-gráf lekérdezése |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>BizTalk közreműködő

Lehetővé teszi a BizTalk Services kezelését, de azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. BizTalkServices/BizTalk/* | BizTalk Services-szolgáltatások létrehozása és kezelése |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>Asztali virtualizálási felhasználó

Lehetővé teszi, hogy a felhasználó egy alkalmazáscsoport alkalmazásait használja. [További információ](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | Microsoft. DesktopVirtualization/applicationGroups/useApplications/művelet | Alkalmazáscsoport használata |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Ütemező – feladattípusok közreműködői

Lehetővé teszi a Scheduler-feladatok összegyűjtését, de nem fér hozzájuk.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft. Authorization](resource-provider-operations.md#microsoftauthorization)/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikai riasztás létrehozása és kezelése |
> | [Microsoft. ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Deployments/* | Központi telepítés létrehozása és kezelése |
> | [Microsoft. Resources](resource-provider-operations.md#microsoftresources)/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | [Microsoft. Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Feladatok gyűjteményének létrehozása és kezelése |
> | [Microsoft. support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Következő lépések

- [Erőforrás-szolgáltató egyeztetése a szolgáltatással](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Egyéni Azure-szerepkörök](custom-roles.md)
- [Engedélyek az Azure Security Centerben](../security-center/security-center-permissions.md)
