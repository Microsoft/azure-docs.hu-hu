---
title: Beépített Azure-szerepkörök – Azure RBAC
description: Ez a cikk az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) beépített Azure-szerepköreit ismerteti. Felsorolja a Műveletek, a NotActions, a DataActions és a NotDataActions műveleteket.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 04/09/2021
ms.custom: generated
ms.openlocfilehash: c89e6ed98e0a71f530cefda4cc1f42a27996d805
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518507"
---
# <a name="azure-built-in-roles"></a>Beépített Azure-szerepkörök

[Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](overview.md) számos beépített Azure-szerepkört kínál, amelyek felhasználókhoz, csoportokhoz, szolgáltatásnévhez és felügyelt identitásokhoz rendelhetők hozzá. A szerepkör-hozzárendelések az Azure-erőforrásokhoz való hozzáférés szabályozásának módjai. Ha a beépített szerepkörök nem felelnie meg a szervezet igényeinek, létrehozhatja saját [egyéni Azure-szerepköreit.](custom-roles.md) További információ a szerepkörök hozzárendelésének mikéntről: [Az Azure-szerepkörök hozzárendelésének lépései.](role-assignments-steps.md)

Ez a cikk az Azure beépített szerepköreit sorolja fel. Ha rendszergazdai szerepköröket keres a Azure Active Directory (Azure AD) szolgáltatáshoz, tekintse meg az Azure AD beépített [szerepköreit.](../active-directory/roles/permissions-reference.md)

Az alábbi táblázat az egyes beépített szerepkör rövid leírását tartalmazza. Kattintson a szerepkör nevére az egyes szerepkörhöz a `Actions` , , és szerepkör `NotActions` `DataActions` `NotDataActions` listájának a listájának a listában. További információ arról, hogy mit jelentenek ezek a műveletek, és hogyan vonatkoznak a felügyeleti és adatsíkra: [Az Azure-beli szerepkör-definíciók .](role-definitions.md)

## <a name="all"></a>Mind

> [!div class="mx-tableFixed"]
> | Beépített szerepkör | Leírás | ID (Azonosító) |
> | --- | --- | --- |
> | **Általános** |  |  |
> | [Közreműködő](#contributor) | Teljes hozzáférést biztosít az összes erőforrás kezeléséhez, de nem teszi lehetővé szerepkörök hozzárendelését az Azure RBAC-ben, a hozzárendelések kezelését Azure Blueprints vagy rendszerkép-katalógusok megosztását. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Tulajdonos](#owner) | Teljes hozzáférést biztosít az összes erőforrás kezeléséhez, beleértve a szerepkörök Hozzárendelését az Azure RBAC-ban. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Olvasó](#reader) | Az összes megtekintése erőforrásokat, de nem teszi lehetővé a módosításokat. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Felhasználói hozzáférés adminisztrátora](#user-access-administrator) | Lehetővé teszi az Azure-erőforrások felhasználói hozzáférésének kezelését. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Számítás** |  |  |
> | [Klasszikus virtuális gépek közreműködője](#classic-virtual-machine-contributor) | Lehetővé teszi a klasszikus virtuális gépek kezelését, de a hozzájuk való hozzáférést nem, és nem azt a virtuális hálózatot vagy tárfiókot, amelyhez csatlakoznak. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Virtuális gép rendszergazdai bejelentkezése](#virtual-machine-administrator-login) | Tekintse Virtual Machines portálon, és jelentkezzen be rendszergazdaként | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Virtuális gépek közreműködője](#virtual-machine-contributor) | Lehetővé teszi a virtuális gépek kezelését, de a hozzájuk való hozzáférést nem, és nem azt a virtuális hálózatot vagy tárfiókot, amelyhez csatlakoznak. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Virtuális gép felhasználói bejelentkezése](#virtual-machine-user-login) | Tekintse Virtual Machines portálon, és jelentkezzen be normál felhasználóként. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Hálózat** |  |  |
> | [CDN-végpont közreműködője](#cdn-endpoint-contributor) | Kezelheti a CDN-végpontokat, de nem adhat hozzáférést más felhasználóknak. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN-végpontolvasó](#cdn-endpoint-reader) | Megtekintheti a CDN-végpontokat, de nem tud módosításokat tenni. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [CDN-profil közreműködője](#cdn-profile-contributor) | Kezelheti a CDN-profilokat és azok végpontjait, de nem adhat hozzáférést más felhasználóknak. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN-profilolvasó](#cdn-profile-reader) | Megtekintheti a CDN-profilokat és azok végpontjait, de nem tud módosításokat tenni. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Klasszikus hálózati közreműködő](#classic-network-contributor) | Lehetővé teszi a klasszikus hálózatok kezelését, de a hozzájuk való hozzáférést nem. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS-zóna közreműködője](#dns-zone-contributor) | Lehetővé teszi a DNS-zónák és -rekordhalmazok kezelését a Azure DNS, de nem teszi lehetővé annak szabályozását, hogy ki férhet hozzájuk. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Hálózati közreműködő](#network-contributor) | Lehetővé teszi a hálózatok kezelését, de nem férhet hozzájuk. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [saját DNS Zóna közreműködője](#private-dns-zone-contributor) | Lehetővé teszi a privát DNS-zóna erőforrásainak kezelését, de a virtuális hálózatokat, amelyekhez kapcsolódnak. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Traffic Manager Közreműködő](#traffic-manager-contributor) | Lehetővé teszi a Traffic Manager kezelését, de nem teszi lehetővé annak szabályozását, hogy ki férhet hozzájuk. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Storage** |  |  |
> | [Avere-közreműködő](#avere-contributor) | Létrehozhat és kezelhet egy Avere vFXT fürtöt. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere-operátor](#avere-operator) | A fürt Avere vFXT fürt által használt | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Biztonsági mentés közreműködője](#backup-contributor) | Lehetővé teszi a Backup szolgáltatás kezelését, de nem hozhat létre tárolókat, és nem adhat hozzáférést másoknak | 5e467623-éva1f-42f4-a55d-6e525e11384b |
> | [Biztonságimásolat-felelős](#backup-operator) | Lehetővé teszi a biztonsági mentési szolgáltatások kezelését, kivéve a biztonsági mentések eltávolítását, a tárolók létrehozását és a hozzáférés mások számára való elérhetősítését | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Biztonságimásolat-olvasó](#backup-reader) | Megtekintheti a biztonsági mentési szolgáltatásokat, de nem tud módosításokat tenni | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Klasszikus tárfiók-közreműködő](#classic-storage-account-contributor) | Lehetővé teszi a klasszikus tárfiókok kezelését, de a hozzájuk való hozzáférést nem. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Klasszikus tárfiókkulcs-kezelői szolgáltatásszerepk](#classic-storage-account-key-operator-service-role) | A klasszikus tárfiók kulcskezelői listálhatnak és újragenerálhatnak kulcsokat a klasszikus tárfiókok esetében | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Data Box Közreműködő](#data-box-contributor) | Lehetővé teszi, hogy mindent kezeljen a Data Box szolgáltatás alatt, kivéve, hogy hozzáférést biztosít másoknak. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box Olvasó](#data-box-reader) | Lehetővé teszi a Data Box szolgáltatás kezelését, kivéve a rendelési adatok létrehozását vagy szerkesztését, valamint a hozzáférést másoknak. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Lake Analytics Developer](#data-lake-analytics-developer) | Lehetővé teszi a saját feladatok elküldése, figyelése és kezelése, de nem hozhat létre vagy Data Lake Analytics fiókokat. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Olvasó és adatelérés](#reader-and-data-access) | Lehetővé teszi, hogy mindent megtekintsen, de nem engedi, hogy töröljön vagy hozzon létre egy tárfiókot vagy tartalmazott erőforrást. Emellett olvasási/írási hozzáférést is biztosít a tárfiókban található összes adathoz a tárfiókkulcsok elérésével. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Tárfiók-közreműködő](#storage-account-contributor) | Lehetővé teszi a tárfiókok kezelését. Hozzáférést biztosít a fiókkulcshoz, amellyel megosztott kulcsos hitelesítéssel férhet hozzá az adatokhoz. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Tárfiókkulcs-kezelői szolgáltatásszerepk](#storage-account-key-operator-service-role) | Lehetővé teszi a tárfiók hozzáférési kulcsának listázását és újragenerálását. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Storage-blobadatok közreműködője](#storage-blob-data-contributor) | Azure Storage-tárolók és -blobok olvasása, írása és törlése. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Storage-blobadatok tulajdonosa](#storage-blob-data-owner) | Teljes hozzáférést biztosít az Azure Storage-blobtárolókhoz és -adatokhoz, beleértve a POSIX hozzáférés-vezérlés hozzárendelését. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Storage-blobadatok olvasója](#storage-blob-data-reader) | Olvassa el és listába sorolja az Azure Storage-tárolókat és -blobokat. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Storage Blob Delegator](#storage-blob-delegator) | Szerezze be a felhasználódelegálás kulcsát, amellyel létrehozhat egy közös hozzáférésű jogosultságjelet az Azure AD hitelesítő adataival aláírt tárolóhoz vagy blobhoz. További információ: [Felhasználódelegálás SAS létrehozása.](/rest/api/storageservices/create-user-delegation-sas) | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Storage-fájladatok SMB-megosztásának közreműködője](#storage-file-data-smb-share-contributor) | Lehetővé teszi az Azure-fájlmegosztások fájljainak/könyvtárának olvasási, írási és törlési hozzáférését. Ez a szerepkör nem rendelkezik beépített megfelelővel a Windows-fájlkiszolgálókban. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Storage-fájladatok SMB-megosztásának emelt szintű közreműködője](#storage-file-data-smb-share-elevated-contributor) | Lehetővé teszi az Azure-fájlmegosztások fájljainak/könyvtárának ACL-ek olvasását, írását, törlését és módosítását. Ez a szerepkör megegyezik a Windows-fájlkiszolgálók változási ACL-jének fájlmegosztási ACL-jéhez. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Storage-fájladatok SMB-megosztásának olvasója](#storage-file-data-smb-share-reader) | Olvasási hozzáférést biztosít az Azure-fájlmegosztások fájljaihoz/könyvtárihoz. Ez a szerepkör egyenértékű a Windows-fájlkiszolgálókon olvasott fájlmegosztásICL-ekkel. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Storage Queue-adatok közreműködője](#storage-queue-data-contributor) | Azure Storage-üzenetsorok és üzenetsor-üzenetek olvasása, írása és törlése. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Tárolási üzenetsor adatüzenet-feldolgozója](#storage-queue-data-message-processor) | Üzenetek betekintése, lekérése és törlése egy Azure Storage-üzenetsorból. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Tárolási üzenetsor adatüzenet-küldője](#storage-queue-data-message-sender) | Üzenetek hozzáadása egy Azure Storage-üzenetsorhoz. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Storage Queue-adatolvasó](#storage-queue-data-reader) | Azure Storage-üzenetsorok és üzenetsor-üzenetek olvasása és felsorolása. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Azure Maps adat-közreműködő](#azure-maps-data-contributor) | Olvasási, írási és törlési hozzáférést biztosít a kapcsolódó adatok Azure Maps-fiókból való leképezéséhez. | 8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204 |
> | [Azure Maps adatolvasó](#azure-maps-data-reader) | Hozzáférést biztosít egy Azure Maps-fiók térképekkel kapcsolatos adatainak olvasására. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Azure Spring Cloud adatolvasó](#azure-spring-cloud-data-reader) | Olvasási hozzáférés engedélyezése Azure Spring Cloud adatokhoz | b5537268-8956-4941-a8f0-646150406f0c |
> | [Keresési szolgáltatás közreműködője](#search-service-contributor) | Lehetővé teszi a keresési szolgáltatások kezelését, de a hozzájuk való hozzáférést nem. | 7ca78c08-252a-4471-8644-majd5ff32d4ba0 |
> | [SignalR AccessKey Reader](#signalr-accesskey-reader) | Hozzáférési SignalR Service olvasása | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [SignalR App Server (előzetes verzió)](#signalr-app-server-preview) | Lehetővé teszi, hogy az alkalmazáskiszolgáló SignalR Service AAD-hitelesítési beállításokkal hozzáférjen. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [SignalR-közreműködő](#signalr-contributor) | SignalR-szolgáltatási erőforrások létrehozása, olvasása, frissítése és törlése | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [SignalR kiszolgáló nélküli közreműködő (előzetes verzió)](#signalr-serverless-contributor-preview) | Lehetővé teszi, hogy az alkalmazás kiszolgáló nélküli módban, AAD-hitelesítési beállításokkal fér hozzá. | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [SignalR Service tulajdonos (előzetes verzió)](#signalr-service-owner-preview) | Teljes hozzáférés az Azure SignalR Service REST API-khoz | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [SignalR Service Olvasó (előzetes verzió)](#signalr-service-reader-preview) | Csak olvasási hozzáférés a Azure SignalR Service REST API-khoz | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Webes csomag közreműködője](#web-plan-contributor) | Lehetővé teszi a webhelyek webes csomagjainak kezelését, de a hozzájuk való hozzáférést nem. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Webhely közreműködője](#website-contributor) | Lehetővé teszi a webhelyek (nem webes csomagok) kezelését, de a hozzájuk való hozzáférést nem. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Containers** |  |  |
> | [AcrDelete (AcrDelete)](#acrdelete) | acr delete | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | acr image signer | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | acr pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush (AcrPush)](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | acr quarantine data reader | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | acr quarantine adatíró | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes Service fürt rendszergazdai szerepköre](#azure-kubernetes-service-cluster-admin-role) | List cluster admin credential action. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes Service felhasználói szerepkör létrehozása](#azure-kubernetes-service-cluster-user-role) | List cluster user credential action. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Azure Kubernetes Service közreműködői szerepkör](#azure-kubernetes-service-contributor-role) | Olvasási és írási hozzáférést biztosít Azure Kubernetes Service fürtökhöz | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Azure Kubernetes Service RBAC-rendszergazda](#azure-kubernetes-service-rbac-admin) | Lehetővé teszi a fürt/névtér alatt az összes erőforrás kezelését, kivéve az erőforráskvóták és a névterek frissítését vagy törlését. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Azure Kubernetes Service RBAC-fürt rendszergazdája](#azure-kubernetes-service-rbac-cluster-admin) | Lehetővé teszi a fürt összes erőforrásának kezelését. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Azure Kubernetes Service RBAC-olvasó](#azure-kubernetes-service-rbac-reader) | Csak olvasási hozzáférést biztosít a névtér legtöbb objektumának eléréséhez. Nem engedélyezi a szerepkörök és a szerepkörkötések megtekintését. Ez a szerepkör nem engedélyezi a titkos kulcsok megtekintését, mivel a titkos kulcsok tartalmának beolvasása hozzáférést biztosít a ServiceAccount hitelesítő adatokhoz a névtérben, ami lehetővé teszi az API-hozzáférést, mint a névtérben bármely ServiceAccount (a jogosultságok eszkalálásának egyik formája). Ennek a szerepkörnek a fürthatókörön való alkalmazása hozzáférést biztosít az összes névtérhez. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Azure Kubernetes Service RBAC-író](#azure-kubernetes-service-rbac-writer) | Olvasási/írási hozzáférést biztosít a névtér legtöbb objektumához. Ez a szerepkör nem engedélyezi a szerepkörök vagy szerepkörkötések megtekintését vagy módosítását. Ez a szerepkör azonban lehetővé teszi a titkos kulcsokhoz való hozzáférést és a podok futtatását bármely ServiceAccount-ként a névtérben, így a névtérben bármely ServiceAccount API-hozzáférési szintje elérhető. Ennek a szerepkörnek a fürthatókörön való alkalmazása hozzáférést biztosít az összes névtérhez. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Adatbázisok** |  |  |
> | [Cosmos DB-olvasó szerepkör](#cosmos-db-account-reader-role) | Olvashatja a Azure Cosmos DB adatait. Lásd: [DocumentDB-fiók közreműködője](#documentdb-account-contributor) a Azure Cosmos DB kezeléséhez. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB Operátor](#cosmos-db-operator) | Lehetővé teszi a Azure Cosmos DB kezelését, de nem férhet hozzá a bennük elérhető adatokhoz. Megakadályozza a fiókkulcsok és kapcsolati sztringek hozzáférését. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Küldhet visszaállítási kérelmet egy Cosmos DB adatbázisra vagy egy fiók tárolójára | db7b14f2-5adf-42da-9f96-f2ee17 kb5cb |
> | [CosmosRestoreOperator](#cosmosrestoreoperator) | Visszaállítási műveletet hajthat végre Cosmos DB folyamatos biztonsági mentési módú adatbázisfiókhoz | 5432c526-bc82-444a-b7ba-57c5b0b5b34f |
> | [DocumentDB-fiók közreműködője](#documentdb-account-contributor) | Kezelheti a Azure Cosmos DB fiókokat. Azure Cosmos DB korábban DocumentDB néven ismert. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis Cache Közreműködő](#redis-cache-contributor) | Lehetővé teszi a Redis-gyorsítótárak kezelését, de nem férhet hozzájuk. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL DB-közreműködő](#sql-db-contributor) | Lehetővé teszi az SQL-adatbázisok kezelését, de nem férhet hozzájuk. A biztonsággal kapcsolatos szabályzatokat és azok szülő SQL-kiszolgálóit sem kezelheti. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL Managed Instance Közreműködő](#sql-managed-instance-contributor) | Lehetővé teszi a felügyelt SQL-példányok és a szükséges hálózati konfigurációk kezelését, de nem adhat hozzáférést másoknak. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL-biztonságkezelő](#sql-security-manager) | Lehetővé teszi az SQL-kiszolgálók és adatbázisok biztonsággal kapcsolatos házirendek kezelését, de nem férhet hozzájuk. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server Közreműködő](#sql-server-contributor) | Lehetővé teszi az SQL-kiszolgálók és adatbázisok kezelését, de a hozzáférésüket nem, és nem a biztonsággal kapcsolatos házirendeket. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Elemzés** |  |  |
> | [Azure Event Hubs adattulajdonos](#azure-event-hubs-data-owner) | Teljes hozzáférést biztosít a Azure Event Hubs erőforrásokhoz. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs adat fogadója](#azure-event-hubs-data-receiver) | Hozzáférést biztosít a Azure Event Hubs erőforrásokhoz. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs adatküldő](#azure-event-hubs-data-sender) | Hozzáférést biztosít a Azure Event Hubs erőforrásokhoz. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Data Factory Közreműködő](#data-factory-contributor) | Adat-üzemek, valamint bennük található gyermekerőforrások létrehozása és kezelése. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Adatüres szám](#data-purger) | Privát adatok törlése Log Analytics-munkaterületről. | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight-fürt operátora](#hdinsight-cluster-operator) | Lehetővé teszi a HDInsight-fürtkonfigurációk olvasását és módosítását. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [HDInsight Domain Services-közreműködő](#hdinsight-domain-services-contributor) | A HDInsight-kiszolgálóhoz szükséges tartományi szolgáltatásokkal kapcsolatos műveletek olvasására, létrehozására, módosítására és Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Log Analytics közreműködő](#log-analytics-contributor) | A Log Analytics közreműködője olvashatja az összes monitorozási adatot, és szerkesztheti a figyelési beállításokat. A figyelési beállítások szerkesztéséhez hozzá kell adni a virtuálisgép-bővítményt a virtuális gépekhez; a tárfiókkulcsok olvasása a naplók Azure Storage-ból való gyűjtésének konfigurálása érdekében; Automation-fiókok létrehozása és konfigurálása; megoldások hozzáadása; és az Azure Diagnostics konfigurálása az összes Azure-erőforráson. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics olvasó](#log-analytics-reader) | A Log Analytics-olvasó megtekintheti és kereshet az összes monitorozási adatot, valamint megtekintheti a monitorozási beállításokat, beleértve az Azure-diagnosztika konfigurációjának megtekintését az összes Azure-erőforráson. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Purview Data Curator](#purview-data-curator) | A Microsoft.Purview adatkulátor katalógusadat-objektumokat hozhat létre, olvashat, módosíthat és törölhet, valamint kapcsolatokat hozhat létre az objektumok között. Ez a szerepkör előzetes verzióban érhető el, és változhat. | 8a3c2885-9b38-4fd2-9d99-91af537c1347 |
> | [Az adatolvasó végleges nézete](#purview-data-reader) | A Microsoft.Purview adatolvasó olvashatja a katalógus adatobjektumokat. Ez a szerepkör előzetes verzióban érhető el, és változhat. | ff100721-1b9d-43d8-af52-42b69c1272db |
> | [Adatforrás-rendszergazdai jogosultságok](#purview-data-source-administrator) | A Microsoft.Purview adatforrás rendszergazdája kezelheti az adatforrásokat és az adatvizsgálatokat. Ez a szerepkör előzetes verzióban érhető el, és változhat. | 200bba9e-f0c8-430f-892b-6f0794863803 |
> | [Sémajegyzék közreműködője (előzetes verzió)](#schema-registry-contributor-preview) | Séma-beállításjegyzék-csoportok és -sémák olvasása, írása és törlése. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Sémajegyzék-olvasó (előzetes verzió)](#schema-registry-reader-preview) | Olvassa el és listába sorolja fel a séma-beállításjegyzék-csoportokat és -sémákat. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **Blockchain** |  |  |
> | [Blokklánctagcsomópont-hozzáférés (előzetes verzió)](#blockchain-member-node-access-preview) | Hozzáférést biztosít a Blockchain-tagcsomópontokhoz | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + gépi tanulás** |  |  |
> | [Cognitive Services Közreműködő](#cognitive-services-contributor) | Segítségével létrehozhatja, olvashatja, frissítheti, törölheti és kezelheti a Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services Custom Vision Közreműködő](#cognitive-services-custom-vision-contributor) | Teljes hozzáférés a projekthez, beleértve a projektek megtekintésének, létrehozásának, szerkesztésének és törlésének képességét. | c1ff6cc2-c111-46fe-8896-e0ef812ad9f3 |
> | [Cognitive Services Custom Vision üzembe helyezés](#cognitive-services-custom-vision-deployment) | Modellek közzététele, közzététele vagy exportálása. Az üzembe helyezés megtekintheti a projektet, de nem frissíthet. | 5c4089e1-6d96-4d2f-b296-c1bc7137275f |
> | [Cognitive Services Custom Vision Labeler](#cognitive-services-custom-vision-labeler) | Megtekintheti, szerkesztheti a betanítás képeit, és létrehozhatja, hozzáadhatja, eltávolíthatja vagy törölheti a képcímkéket. A feliratozók megtekinthetik a projektet, de csak a betanítás képeit és címkéit frissítik. | 88424f51-ebe7-446f-bc41-7fa16989e96c |
> | [Cognitive Services Custom Vision Olvasó](#cognitive-services-custom-vision-reader) | Csak olvasható műveletek a projektben. Az olvasók nem hozhatják létre vagy frissítheti a projektet. | 93586559-c37d-4a6b-ba08-b9f0940c2d73 |
> | [Cognitive Services Custom Vision oktató](#cognitive-services-custom-vision-trainer) | Projektek megtekintése, szerkesztése és a modellek betanítása, beleértve a modellek közzétételének, közzétételének és exportálásának képességét. Az oktatók nem hozhatják létre vagy törölhetik a projektet. | 0a5ae4ab-0d65-4eeb-be61-29fc9b54394b |
> | [Cognitive Services adatolvasó (előzetes verzió)](#cognitive-services-data-reader-preview) | Lehetővé teszi a Cognitive Services olvasását. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services Metrics Advisor rendszergazda](#cognitive-services-metrics-advisor-administrator) | Teljes hozzáférés a projekthez, beleértve a rendszerszintű konfigurációt is. | cb43c632-a144-4ec5-977c-e80c4affc34a |
> | [Cognitive Services QnA Maker Editor](#cognitive-services-qna-maker-editor) | Hozzunk létre, szerkesszünk, importálunk és exportálunk egy tudásbázist. Tudásbázist nem tehet közzé vagy törölhet. | f4cc2bf9-21be-47a1-bdf1-5c5804381025 |
> | [Cognitive Services QnA Maker Olvasó](#cognitive-services-qna-maker-reader) | Csak a tudásbázist olvassa és tesztelje. | 466ccd10-b268-4a11-b098-b4849f024126 |
> | [Cognitive Services felhasználó](#cognitive-services-user) | Lehetővé teszi a kulcskulcsok olvasását és Cognitive Services. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Dolgok internetes hálózata** |  |  |
> | [Eszközfrissítés-rendszergazda](#device-update-administrator) | Teljes hozzáférést biztosít a felügyeleti és tartalomműveleti műveletekhez | 02ca0879-e8e4-47a5-a61e-5c618b76e64a |
> | [Eszközfrissítési tartalom rendszergazdája](#device-update-content-administrator) | Teljes hozzáférést biztosít a tartalomművelethez | 0378884a-3af5-44ab-8323-f5b22f9f3c98 |
> | [Eszközfrissítési tartalom olvasója](#device-update-content-reader) | Olvasási hozzáférést biztosít a tartalomművelethez, de nem engedélyezi a módosításokat | d1ee9a80-8b14-47f0-bdc2-f4a351625a7b |
> | [Eszközfrissítések központi telepítésének rendszergazdája](#device-update-deployments-administrator) | Teljes hozzáférést biztosít a felügyeleti műveletekhez | e4237640-0e3d-4a46-8fda-70bc94856432 |
> | [Eszközfrissítések központi telepítésének olvasója](#device-update-deployments-reader) | Olvasási hozzáférést biztosít a felügyeleti műveletekhez, de nem engedélyezi a módosításokat | 49e2f5d2-7741-4835-8efa-19e1fe35e47f |
> | [Eszközfrissítés olvasója](#device-update-reader) | Olvasási hozzáférést biztosít a felügyeleti és tartalomműveleti műveletekhez, de nem engedélyezi a módosításokat | e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f |
> | **Vegyes valóság** |  |  |
> | [Remote Rendering rendszergazda](#remote-rendering-administrator) | Konverziós, munkamenet-, renderelési és diagnosztikai képességeket biztosít a Azure Remote Rendering | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Remote Rendering-ügyfél](#remote-rendering-client) | Munkamenet-, renderelési és diagnosztikai képességeket biztosít a Azure Remote Rendering. | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Spatial Anchors fiók közreműködője](#spatial-anchors-account-contributor) | Lehetővé teszi a fiók térbeli horgonyok kezelését, de nem törölheti őket | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Spatial Anchors fióktulajdonos](#spatial-anchors-account-owner) | Lehetővé teszi a fiók térbeli horgonyok kezelését, beleértve azok törlését is | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Spatial Anchors fiókolvasó használata](#spatial-anchors-account-reader) | Lehetővé teszi a fiók térbeli horgonyai tulajdonságainak megkeresét és olvasását | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integráció** |  |  |
> | [API Management szolgáltatás közreműködője](#api-management-service-contributor) | Kezelheti a szolgáltatást és az API-kat | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API Management-kezelői szerepkör](#api-management-service-operator-role) | Kezelheti a szolgáltatást, de az API-kat nem | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [API Management Szolgáltatásolvasó szerepkör](#api-management-service-reader-role) | Csak olvasási hozzáférés a szolgáltatáshoz és az API-khoz | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [App Configuration adattulajdonos](#app-configuration-data-owner) | Teljes hozzáférést biztosít a App Configuration adatokhoz. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [App Configuration adatolvasó](#app-configuration-data-reader) | Olvasási hozzáférést biztosít a App Configuration adatokhoz. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Azure Service Bus adattulajdonos](#azure-service-bus-data-owner) | Teljes hozzáférést biztosít a Azure Service Bus erőforrásokhoz. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus adat fogadója](#azure-service-bus-data-receiver) | Lehetővé teszi a hozzáférés fogadását Azure Service Bus erőforrásokhoz. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure Service Bus küldő](#azure-service-bus-data-sender) | Hozzáférést biztosít a Azure Service Bus erőforrásokhoz. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack regisztráció tulajdonosa](#azure-stack-registration-owner) | Lehetővé teszi a Azure Stack kezelését. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid-közreműködő](#eventgrid-contributor) | Lehetővé teszi az EventGrid-műveletek kezelését. | 1e241071-0855-49ea-94dc-649edcd759de |
> | [EventGrid EventSubscription Közreműködő](#eventgrid-eventsubscription-contributor) | Lehetővé teszi az EventGrid-esemény-előfizetési műveletek kezelését. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription Reader](#eventgrid-eventsubscription-reader) | Lehetővé teszi az EventGrid-esemény-előfizetések olvasását. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [FHIR-adatok közreműködője](#fhir-data-contributor) | A szerepkör teljes hozzáférést biztosít a felhasználók vagy a rendszerbiztonsági tag számára az FHIR-adatokhoz | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [FHIR-adatexport](#fhir-data-exporter) | A szerepkör lehetővé teszi, hogy a felhasználó vagy a rendszerbiztonsági tag beolvassa és exportálja az FHIR-adatokat | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [FHIR-adatolvasó](#fhir-data-reader) | A szerepkör lehetővé teszi, hogy a felhasználó vagy a rendszerbiztonsági tag beolvassa az FHIR-adatokat | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [FHIR-adatíró](#fhir-data-writer) | A szerepkör lehetővé teszi, hogy a felhasználó vagy a rendszerbiztonsági tag FHIR-adatokat olvasson és írjon | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [integrációs szolgáltatási környezet Közreműködő](#integration-service-environment-contributor) | Lehetővé teszi az integrációs szolgáltatási környezetek kezelését, de a hozzájuk való hozzáférést nem. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [integrációs szolgáltatási környezet Developer](#integration-service-environment-developer) | Lehetővé teszi, hogy a fejlesztők munkafolyamatokat, integrációs fiókokat és API-kapcsolatokat hozzanak létre és frissítsenek az integrációs szolgáltatási környezetekben. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Intelligent Systems-fiók közreműködője](#intelligent-systems-account-contributor) | Lehetővé teszi az Intelligent Systems-fiókok kezelését, de a hozzájuk való hozzáférést nem. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Logikai alkalmazás közreműködője](#logic-app-contributor) | Lehetővé teszi a logikai alkalmazások kezelését, de a hozzáférésük nem változik. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logikaialkalmazás-operátor](#logic-app-operator) | Lehetővé teszi a logikai alkalmazások olvasását, engedélyezését és letiltását, de nem szerkesztheti vagy frissítheti őket. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identitás** |  |  |
> | [Felügyelt identitás közreműködője](#managed-identity-contributor) | Felhasználó által hozzárendelt identitás létrehozása, olvasása, frissítése és törlése | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Felügyelt identitáskezelő](#managed-identity-operator) | Felhasználóhoz rendelt identitás olvasása és hozzárendelése | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Biztonság** |  |  |
> | [Igazolási közreműködő](#attestation-contributor) | Olvashatja vagy törölheti az igazolásszolgáltató példányát | xisf86eb8-f7b4-4cce-96e4-18cddf81d86e |
> | [Igazolásolvasó](#attestation-reader) | Olvashatja az igazolásszolgáltató tulajdonságait | fd1bd22b-8476-40bc-a0bc-69b95687b9f3 |
> | [Azure Sentinel Automation-közreműködő](#azure-sentinel-automation-contributor) | Azure Sentinel Automation-közreműködő | f4c81013-99ee-4d62-a7ee-b3f1f648599a |
> | [Azure Sentinel Contributor](#azure-sentinel-contributor) | Azure Sentinel Contributor | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel Reader](#azure-sentinel-reader) | Azure Sentinel Reader | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel Responder](#azure-sentinel-responder) | Azure Sentinel Responder | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Key Vault rendszergazda](#key-vault-administrator) | Minden adatsíkműveletet végrehajt egy kulcstartón és a benne lévő összes objektumon, beleértve a tanúsítványokat, kulcsokat és titkos kulcsokat. A Key Vault-erőforrások és a szerepkör-hozzárendelések nem kezelhetők. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Key Vault tanúsítvány-felelős](#key-vault-certificates-officer) | Az engedélyek kezelése kivételével minden műveletet végrehajt a kulcstartó tanúsítványán. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Key Vault Közreműködő](#key-vault-contributor) | Kulcstartók kezelése, de nem teszi lehetővé szerepkörök hozzárendelését az Azure RBAC-ban, és nem teszi lehetővé a titkos kulcsok, kulcsok és tanúsítványok hozzáférését. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Key Vault kriptográfiai igazgató](#key-vault-crypto-officer) | Az engedélyek kezelése kivételével bármilyen műveletet végrehajt a kulcstartó kulcsán. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Key Vault titkosítási szolgáltatás titkosítási felhasználója](#key-vault-crypto-service-encryption-user) | Beolvassa a kulcsok metaadatait, és burkoló/kicsomagol műveleteket hajt végre. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Key Vault kriptográfiai felhasználó](#key-vault-crypto-user) | Titkosítási műveletek végrehajtása kulcsokkal. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Key Vault Olvasó](#key-vault-reader) | Beolvassa a kulcstartók, tanúsítványok, kulcsok és titkos kulcsok metaadatait. Nem olvashatók bizalmas értékek, például titkos kulcsok vagy kulcsanyagok. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Key Vault titkos kulcsokért felelős igazgató](#key-vault-secrets-officer) | Az engedélyek kezelése kivételével bármilyen műveletet végrehajt a kulcstartó titkos kulcsaion. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Key Vault titkos kulcsok felhasználója](#key-vault-secrets-user) | Titkos tartalmának olvasása. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják. | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Managed HSM-közreműködő](#managed-hsm-contributor) | Lehetővé teszi a felügyelt HSM-készletek kezelését, de nem férhet hozzájuk. | 18500a29-7fe2-46b2-a342-b16a415e101d |
> | [Biztonsági rendszergazda](#security-admin) | Megtekintheti és frissítheti a Security Center. Ugyanazokkal az engedélyekkel rendelkezik, mint a Biztonsági olvasó szerepkör, és frissítheti a biztonsági szabályzatot, valamint elvetheti a riasztásokat és javaslatokat. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Biztonsági értékelés közreműködője](#security-assessment-contributor) | Lehetővé teszi az értékelések leküldését Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Security Manager (örökölt)](#security-manager-legacy) | Ez egy örökölt szerepkör. Ehelyett használja a Biztonsági rendszergazda szerepkört. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Biztonsági olvasó](#security-reader) | View permissions for Security Center. Megtekintheti a javaslatokat, riasztásokat, biztonsági szabályzatokat és biztonsági államokat, de nem tud módosításokat tenni. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs-felhasználó](#devtest-labs-user) | Lehetővé teszi a virtuális gépek csatlakoztatását, újraindítását és leállítását a Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Tesztkörnyezet létrehozója](#lab-creator) | Lehetővé teszi új tesztkörnyezetek létrehozására az Azure Lab-fiókok alatt. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Figyelés** |  |  |
> | [Application Insights összetevő közreműködője](#application-insights-component-contributor) | Kezelheti a Application Insights összetevőket | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Engedélyt ad a felhasználónak az alkalmazással gyűjtött hibakeresési pillanatképek megtekintésére és Application Insights Snapshot Debugger. Vegye figyelembe, hogy ezek az engedélyek nem szerepelnek a [Tulajdonos vagy](#owner) a Közreműködő [szerepkörben.](#contributor) Amikor a felhasználóknak Application Insights Snapshot Debugger szerepkört, a szerepkört közvetlenül a felhasználónak kell adni. A rendszer nem ismeri fel a szerepkört, amikor hozzáadja egy egyéni szerepkörhöz. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Figyelési közreműködő](#monitoring-contributor) | Olvashatja az összes monitorozási adatot, és szerkesztheti a figyelési beállításokat. Lásd [még: A szerepkörök, engedélyek](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)és biztonság első lépések a Azure Monitor. | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Monitorozási metrikák közzétevője](#monitoring-metrics-publisher) | Metrikák közzétételének lehetővé teszi az Azure-erőforrásokon | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Figyelési olvasó](#monitoring-reader) | Olvashatja az összes monitorozási adatot (metrikákat, naplókat stb.). Lásd [még: Get started with roles, permissions, and security with Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Munkafüzet közreműködője](#workbook-contributor) | Megosztott munkafüzeteket menthet. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Munkafüzet olvasója](#workbook-reader) | Olvashatja a munkafüzeteket. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Felügyelet + irányítás** |  |  |
> | [Automation-feladat operátora](#automation-job-operator) | Feladatok létrehozása és kezelése Automation-runbookok használatával. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation-operátor](#automation-operator) | Az Automation-operátorok képesek elindítani, leállítani, felfüggeszteni és folytatni a feladatokat | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation Runbook-operátor](#automation-runbook-operator) | Runbook tulajdonságainak olvasása – a runbook feladatának létrehozásához. | 5fb5aef8-1081-4b8e-6-9d5d0385 kb5 |
> | [Azure Arc Kubernetes-fürt felhasználói szerepkörének engedélyezése](#azure-arc-enabled-kubernetes-cluster-user-role) | List cluster user credentials action. | 00493d72-78f6-4148-b6c5-d3ce8e4799dd |
> | [Azure Arc Kubernetes-rendszergazda](#azure-arc-kubernetes-admin) | Lehetővé teszi a fürt/névtér alatt az összes erőforrás kezelését, kivéve az erőforráskvóták és névterek frissítését vagy törlését. | dffb1e0c-446f-4dde-a09f-99eb5cc68b96 |
> | [Azure Arc Kubernetes-fürt rendszergazdája](#azure-arc-kubernetes-cluster-admin) | Lehetővé teszi a fürt összes erőforrásának kezelését. | 8393591c-06b9-48a2-a542-1bd6b377f6a2 |
> | [Azure Arc Kubernetes Viewer](#azure-arc-kubernetes-viewer) | Lehetővé teszi az összes erőforrás megtekintését a fürtben/névtérben, a titkos kulcsok kivételével. | 63f0a09d-1495-4db4-a681-037d84835eb4 |
> | [Azure Arc Kubernetes-író](#azure-arc-kubernetes-writer) | Lehetővé teszi a fürt/névtér összes adatának frissítését, kivéve a (fürt)szerepköröket és a (fürt)szerepkörkötéseket. | 5b999177-9696-4545-85c7-50de3797e5a1 |
> | [Azure Connected Machine Onboarding](#azure-connected-machine-onboarding) | Képes Azure-beli csatlakoztatott gépeket is bevetni. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure Connected Machine erőforrás-rendszergazda](#azure-connected-machine-resource-administrator) | Képes olvasni, írni, törölni és újra bevetni az Azure-beli csatlakoztatott gépeket. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Számlázás olvasója](#billing-reader) | Olvasási hozzáférés a számlázási adatokhoz | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Terv közreműködője](#blueprint-contributor) | Kezelheti a tervdefiníciókat, de nem rendelhet hozzájuk. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Blueprint Operátor](#blueprint-operator) | Hozzárendelhet meglévő közzétett terveket, de nem hozhat létre új terveket. Vegye figyelembe, hogy ez csak akkor működik, ha a hozzárendelés felhasználó által hozzárendelt felügyelt identitással történik. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Cost Management-közreműködő](#cost-management-contributor) | Megtekintheti a költségeket és kezelheti a költségkonfigurációt (például költségvetéseket, exportálásokat) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management Olvasó](#cost-management-reader) | Megtekintheti a költségadatokat és a konfigurációt (például költségvetéseket, exportálásokat) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Hierarchiabeállítások rendszergazdája](#hierarchy-settings-administrator) | Lehetővé teszi a felhasználók számára a hierarchiabeállítások szerkesztését és törlését | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Kubernetes-fürt – Azure Arc-](#kubernetes-cluster---azure-arc-onboarding) | Szerepkör-definíció, amely engedélyezi bármely felhasználó vagy szolgáltatás számára a connectedClusters erőforrás létrehozásához | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Felügyelt alkalmazások közreműködője szerepkör](#managed-application-contributor-role) | Lehetővé teszi a felügyelt alkalmazások erőforrásainak létrehozását. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Felügyelt alkalmazás operátori szerepköre](#managed-application-operator-role) | Lehetővé teszi a felügyelt alkalmazások erőforrásainak olvasását és a műveletek elvégzését | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Felügyelt alkalmazások olvasója](#managed-applications-reader) | Lehetővé teszi egy felügyelt alkalmazás erőforrásainak olvasását és JIT-hozzáférés kérését. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Felügyelt szolgáltatások regisztráció-hozzárendelése – Szerepkör törlése](#managed-services-registration-assignment-delete-role) | A felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköre lehetővé teszi a bérlő felhasználói számára a bérlőjükhöz rendelt regisztrációs hozzárendelés törlését. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Felügyeleti csoport közreműködője](#management-group-contributor) | Felügyeleti csoport közreműködője szerepkör | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Felügyeleti csoport olvasója](#management-group-reader) | Felügyeleti csoport olvasói szerepköre | ac63b705-f282-497d-ac71-919bf39d939d |
> | [New Relic APM-fiók közreműködője](#new-relic-apm-account-contributor) | Lehetővé teszi New Relic Application Performance Management fiókok és alkalmazások kezelését, de nem férhet hozzájuk. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Policy Insights-adatíró (előzetes verzió)](#policy-insights-data-writer-preview) | Olvasási hozzáférést biztosít az erőforrás-szabályzatok és írási hozzáférések számára az erőforrás-összetevők házirendeseményei számára. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Kvótakérési operátor](#quota-request-operator) | Olvassa el és hozza létre a kvótakéréseket, kérje le a kvótakérés állapotát, és hozzon létre támogatási jegyeket. | 0e5f05e5-9ab9-446b-b98d-1e2157c94125 |
> | [Foglalás vásárlója](#reservation-purchaser) | Lehetővé teszi foglalások vásárlását | f7b75c60-3036-4b75-91c3-6b41c27c1689 |
> | [Erőforrás-szabályzat közreműködője](#resource-policy-contributor) | Az erőforrás-szabályzatok létrehozására/módosítására, támogatási jegy létrehozására és erőforrások/hierarchiák olvasására vonatkozó jogosultsággal rendelkezik felhasználók. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Site Recovery-közreműködő](#site-recovery-contributor) | Lehetővé teszi a Site Recovery szolgáltatás kezelését a tároló létrehozása és a szerepkör-hozzárendelés kivételével | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery-operátor](#site-recovery-operator) | Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem hajthat végre Site Recovery felügyeleti műveleteket | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery-olvasó](#site-recovery-reader) | Lehetővé teszi a Site Recovery megtekintését, de más felügyeleti műveleteket nem hajt végre | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Támogatási kérelem közreműködője](#support-request-contributor) | Lehetővé teszi támogatási kérelmek létrehozásához és kezeléséhez | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Címke közreműködője](#tag-contributor) | Lehetővé teszi az entitások címkéinek kezelését anélkül, hogy hozzáférést biztosítanának magukhoz az entitásokhoz. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Egyéb** |  |  |
> | [Azure Digital Twins adattulajdonos](#azure-digital-twins-data-owner) | Teljes hozzáférésű szerepkör Digital Twins adatsíkhoz | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Azure Digital Twins adatolvasó](#azure-digital-twins-data-reader) | Csak olvasási szerepkör Digital Twins adatsík tulajdonságaihoz | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [BizTalk Contributor](#biztalk-contributor) | Lehetővé teszi a BizTalk-szolgáltatások kezelését, de nem férhet hozzájuk. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Asztali virtualizálási alkalmazáscsoport közreműködője](#desktop-virtualization-application-group-contributor) | Az asztali virtualizálási alkalmazáscsoport közreműködője. | 86240b0e-9422-4c43-887b-b61143f32ba8 |
> | [Asztali virtualizálási alkalmazáscsoport olvasója](#desktop-virtualization-application-group-reader) | Az asztali virtualizálási alkalmazáscsoport olvasója. | aebf23d0-b568-4e86-b8f9-fe83a2c6ab55 |
> | [Asztali virtualizálási közreműködő](#desktop-virtualization-contributor) | Az asztali virtualizálás közreműködője. | 082f0a83-3be5-4ba1-904c-9619b387 |
> | [Asztali virtualizálási gazdagépkészlet közreműködője](#desktop-virtualization-host-pool-contributor) | Az asztali virtualizálási gazdagépkészlet közreműködője. | e307426c-f9b6-4e81-87de-d99efb3c32bc |
> | [Asztali virtualizálási gazdagépkészlet olvasója](#desktop-virtualization-host-pool-reader) | Az asztali virtualizálási gazdagépkészlet olvasója. | ceadfde2-b300-400a-ab7b-6143895aa822 |
> | [Asztali virtualizálás olvasója](#desktop-virtualization-reader) | Az asztali virtualizálás olvasója. | 49a72310-ab8d-41df-bbb0-79b649203868 |
> | [Asztali virtualizálási munkamenetgazda-kezelő](#desktop-virtualization-session-host-operator) | Az asztali virtualizálási munkamenetgazda operátora. | 2ad6aaab-ead9-4eaa-8ac5-da422f562408 |
> | [Asztali virtualizálási felhasználó](#desktop-virtualization-user) | Lehetővé teszi a felhasználók számára az alkalmazások használatát egy alkalmazáscsoportban. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Asztali virtualizálás felhasználói munkamenet-kezelője](#desktop-virtualization-user-session-operator) | A Desktop Virtualization Uesr-munkamenet operátora. | ea4bfff8-7fb4-485a-aadd-d4129a0ffa6 |
> | [Asztali virtualizálási munkaterület közreműködője](#desktop-virtualization-workspace-contributor) | Az asztali virtualizálási munkaterület közreműködője. | 21efdde3-836f-432b-bf3d-3e8e734d4b2b |
> | [Asztali virtualizálási munkaterület olvasója](#desktop-virtualization-workspace-reader) | Az asztali virtualizálási munkaterület olvasója. | 0fa44ee9-7a7d-466b-9bb2-2bf446b1204d |
> | [Lemez biztonságimásolat-olvasója](#disk-backup-reader) | Engedélyt biztosít a biztonsági mentési tárolónak a lemezes biztonsági mentés végrehajtásához. | 3e5e47e6-65f7-47ef-90b5-e5dd4d455f24 |
> | [Lemez-visszaállítási operátor](#disk-restore-operator) | Engedélyt biztosít a biztonsági mentési tárolónak a lemez-visszaállítás végrehajtásához. | b50d9833-a0cb-478e-945f-707fcc997c13 |
> | [Lemez-pillanatkép közreműködője](#disk-snapshot-contributor) | Engedélyt biztosít a tároló biztonsági mentésére a lemez-pillanatképek kezeléséhez. | 7efff54f-a5b4-42b5-a1c5-5411624893ce |
> | [Scheduler-feladatgyűjtemények közreműködője](#scheduler-job-collections-contributor) | Lehetővé teszi a Scheduler feladatgyűjtemények kezelését, de nem férhet hozzájuk. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Services Hub-operátor](#services-hub-operator) | A Services Hub Operator lehetővé teszi a Services Hub-összekötőkhöz kapcsolódó összes olvasási, írási és törlési művelet elvégzését. | 82200a5b-e217-47a5-b665-6d8765ee745b |


## <a name="general"></a>Általános kérdések


### <a name="contributor"></a>Közreműködő

Teljes hozzáférést biztosít az összes erőforrás kezeléséhez, de nem teszi lehetővé szerepkörök hozzárendelését az Azure RBAC-ben, a hozzárendelések kezelését Azure Blueprints vagy rendszerkép-katalógusok megosztását. [További információ](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | * | Minden típusú erőforrás létrehozása és kezelése |
> | **NotActions** |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | Szerepkörök, szabályzat-hozzárendelések, szabályzatdefiníciók és szabályzatkészlet-definíciók törlése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Szerepkörök, szerepkör-hozzárendelések, szabályzat-hozzárendelések, szabályzatdefiníciók és szabályzatkészlet-definíciók létrehozása |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | A hívónak felhasználói hozzáférésű rendszergazdai hozzáférést engedélyez a bérlői hatókörben |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/write | Terv-hozzárendelések létrehozása vagy frissítése |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/delete | Terv-hozzárendelések törlése |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/katalógusok/megosztás/művelet | Katalógust osztja meg különböző hatókörökben |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

Teljes hozzáférést biztosít az összes erőforrás kezeléséhez, beleértve a szerepkörök Hozzárendelését az Azure RBAC-ban. [További információ](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | * | Minden típusú erőforrás létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

Az összes megtekintése erőforrásokat, de nem teszi lehetővé a módosításokat. [További információ](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */olvasás | A titkos kulcsok kivételével minden típusú erőforrást beolvas. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

Lehetővé teszi az Azure-erőforrások felhasználói hozzáférésének kezelését. [További információ](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */olvasás | A titkos kulcsok kivételével minden típusú erőforrást beolvas. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/* | Engedélyezés kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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


### <a name="classic-virtual-machine-contributor"></a>Klasszikus virtuális gépek közreműködője

Lehetővé teszi a klasszikus virtuális gépek kezelését, de a hozzájuk való hozzáférést nem, és nem azt a virtuális hálózatot vagy tárfiókot, amelyhez csatlakoznak.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Klasszikus számítási tartománynevek létrehozása és kezelése |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Virtuális gépek létrehozása és kezelése |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/action |  |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/action | Fenntartott IP-cím csatolása |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/read | Lekért fenntartott IP-címek |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/action | Csatlakozik a virtuális hálózathoz. |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/read | Szerezze be a virtuális hálózatot. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/read | A tárfiók lemezét adja vissza. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/read | A tárfiók rendszerképét adja vissza. (Elavult. A "Microsoft.ClassicStorage/storageAccounts/vmImages" használata) |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Felsorolja a tárfiókok hozzáférési kulcsait. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Adja vissza a tárfiókot az adott fiókkal. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

Tekintse Virtual Machines portálon, és jelentkezzen be rendszergazdaként [További információ](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Lekért egy nyilvános IP-cím definícióját. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | A virtuális hálózat definíciójának lekért része |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Lekért egy terheléselosztási definíciót |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Lekért egy hálózati adapter definícióját.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Bejelentkezés virtuális gépre normál felhasználóként |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/action | Jelentkezzen be egy virtuális gépre Windows-rendszergazdai vagy Linux-gyökér szintű felhasználói jogosultságokkal |
> | **NotDataActions (Nem adataktívok)** |  |
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

Lehetővé teszi a virtuális gépek kezelését, de a hozzájuk való hozzáférést nem, és nem azt a virtuális hálózatot vagy tárfiókot, amelyhez csatlakoznak. [További információ](../virtual-machines/linux/tutorial-govern-resources.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Számítási rendelkezésre állási készletek létrehozása és kezelése |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/locations/* | Számítási helyek létrehozása és kezelése |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Hajtsa végre az összes virtuálisgép-műveletet, beleértve a virtuális gépek létrehozási, frissítési, törlési, indítási, újraindítási és kikapcsolt műveleteit. Előre definiált szkriptek végrehajtása virtuális gépeken. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Virtuális gépek létrehozása és kezelése |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Új lemezt hoz létre, vagy frissíti a meglévőt |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Lemez tulajdonságainak lekért |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/delete | Törli a lemezt |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/schedules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/action | Csatlakozik egy Application Gateway háttércímkészlethez. Nem riasztási riasztás. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Csatlakozik egy terheléselosztási háttércímkészlethez. Nem riasztási riasztás. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/action | Csatlakozik egy terheléselosztási bejövő NAT-készlethez. Nem riasztható. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Egy terheléselosztás bejövő NAT-szabályát illeszti össze. Nem riasztási riasztás. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/join/action | Lehetővé teszi a terheléselosztási mintavételek használatát. Ezzel az engedéllyel például a virtuálisgép-méretezési csoport healthProbe tulajdonsága hivatkozhat a mintavételre. Nem riasztható. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Lekért egy terheléselosztási definíciót |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/locations/* | Hálózati helyek létrehozása és kezelése |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Hálózati adapterek létrehozása és kezelése |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Csatlakozik egy hálózati biztonsági csoporthoz. Nem riasztást küldhető. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/read | Lekért egy hálózati biztonsági csoport definícióját |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Csatlakozik egy nyilvános IP-címhez. Nem riasztást küldhető. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Lekért egy nyilvános IP-cím definícióját. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | A virtuális hálózat definíciójának lekért része |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Csatlakozik egy virtuális hálózathoz. Nem riasztási riasztás. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Biztonsági mentési védelmi szándék létrehozása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem objektumának részleteit adja vissza |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Biztonsági másolat védett elemének létrehozása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Az összes védelmi házirendet visszaadja |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/write | Védelmi szabályzatot hoz létre |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A Get Vault művelet lekért egy objektumot, amely a "vault" típusú Azure-erőforrást képviseli |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Helyreállítási tár használati adatait adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | A Create Vault művelet "vault" típusú Azure-erőforrást hoz létre |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsait adja vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Visszaadja a tárfiókok listáját, vagy lekérte a megadott tárfiók tulajdonságait. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="virtual-machine-user-login"></a>Virtuális gép felhasználói bejelentkezése

Tekintse Virtual Machines portálon, és jelentkezzen be normál felhasználóként. [További információ](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Lekért egy nyilvános IP-cím definícióját. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | A virtuális hálózat definíciójának lekért része |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Lekért egy terheléselosztási definíciót |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Lekért egy hálózati adapter definícióját.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Bejelentkezés virtuális gépre normál felhasználóként |
> | **NotDataActions (Nem adatactions)** |  |
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


### <a name="cdn-endpoint-contributor"></a>CDN-végpont közreműködője

Kezelheti a CDN-végpontokat, de nem adhat hozzáférést más felhasználóknak.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="cdn-endpoint-reader"></a>CDN-végpontolvasó

Megtekintheti a CDN-végpontokat, de nem tud módosításokat tenni.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="cdn-profile-contributor"></a>CDN-profil közreműködője

Kezelheti a CDN-profilokat és azok végpontjait, de nem adhat hozzáférést más felhasználóknak. [További információ](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="cdn-profile-reader"></a>CDN-profilolvasó

Megtekintheti a CDN-profilokat és azok végpontjait, de nem tud módosításokat tenni.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

Lehetővé teszi a klasszikus hálózatok kezelését, de nem férhet hozzájuk. [További információ](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Klasszikus hálózatok létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

Lehetővé teszi a DNS-zónák és -rekordhalmazok kezelését a Azure DNS, de nem teszi lehetővé annak szabályozását, hogy ki férhet hozzájuk. [További információ](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | DNS-zónák és -rekordok létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

Lehetővé teszi a hálózatok kezelését, de nem férhet hozzájuk.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/* | Hálózatok létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="private-dns-zone-contributor"></a>saját DNS Zóna-közreműködő

Lehetővé teszi a privát DNS-zóna erőforrásainak kezelését, de a virtuális hálózatokat, amelyekhez kapcsolódnak, nem. [További információ](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | A virtuális hálózat definíciójának lekért része |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/action | Csatlakozik egy virtuális hálózathoz. Nem riasztást küldhető. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="traffic-manager-contributor"></a>Traffic Manager Közreműködő

Lehetővé teszi a Traffic Manager kezelését, de nem teszi lehetővé annak szabályozását, hogy ki férhet hozzájuk.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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


### <a name="avere-contributor"></a>Avere-közreműködő

Létrehozhat és kezelhet egy Avere vFXT fürtöt. [További információ](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/*/read |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/*/read |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | A virtuális hálózat definíciójának lekért része |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/alhálózatok/read | Lekért egy virtuális hálózat alhálózatának definícióját |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Csatlakozik egy virtuális hálózathoz. Nem riasztást küldhető. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Egy erőforrást, például tárfiókot vagy SQL-adatbázist csatlakozik egy alhálózathoz. Nem riasztható. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Csatlakozik egy hálózati biztonsági csoporthoz. Nem riasztást küldhető. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/*/read |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Lekérte az erőforráscsoport erőforrásait. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Egy blob törlésének eredményét adja vissza |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Egy blobot vagy egy bloblistát ad vissza |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Egy blob írásának eredményét adja vissza |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="avere-operator"></a>Avere-operátor

A fürt Avere vFXT a fürt kezeléséhez [További információ](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Virtuális gép tulajdonságainak lekért része |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Lekért egy hálózati adapter definícióját.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Létrehoz egy hálózati adaptert, vagy frissíti a meglévő hálózati adaptert.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | A virtuális hálózat definíciójának lekért része |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/alhálózatok/read | Lekért egy virtuális hálózat alhálózatának definícióját |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Csatlakozik egy virtuális hálózathoz. Nem riasztást küldhető. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Csatlakozik egy hálózati biztonsági csoporthoz. Nem riasztást küldhető. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Egy tároló törlésének eredményét adja vissza |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Tárolók listáját adja vissza |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | A put blobtároló eredményét adja vissza |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Egy blob törlésének eredményét adja vissza |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Egy blobot vagy egy bloblistát ad vissza |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Egy blob írásának eredményét adja vissza |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="backup-contributor"></a>Biztonsági mentés közreműködője

Lehetővé teszi a Backup szolgáltatás kezelését, de nem hozhat létre tárolókat, és nem adhat hozzáférést [másoknak További információ](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | A virtuális hálózat definíciójának lekért része |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | A biztonsági mentések kezelésével kapcsolatos műveletek eredményeinek kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Backup-tárolók létrehozása és kezelése Recovery Services-tárolók biztonsági mentési hálóiban |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Frissíti a tárolók listáját |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Feladatok exportálása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Biztonsági mentési felügyeleti műveletek eredményeinek létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Biztonsági mentési szabályzatok létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Biztonságimenthet elemek létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Biztonságiolt elemek létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Biztonsági mentési elemeket tartalmazó tárolók létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | A Recovery Services védett elemeinek és védett kiszolgálóinak összegzéseit adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/* | Biztonsági mentéshez kapcsolódó tanúsítványok létrehozása és kezelése Helyreállítási tárban |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Tárolóval kapcsolatos bővített adatok létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Lekérte a Recovery Services-tároló riasztását. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A Get Vault művelet lekért egy objektumot, amely a "vault" típusú Azure-erőforrást képviseli |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Regisztrált identitások létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Recovery Services-tároló létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Visszaadja a tárfiókok listáját, vagy lekérte a megadott tárfiók tulajdonságait. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Művelet ellenőrzése védett elemen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | A Create Vault művelet "vault" típusú Azure-erőforrást hoz létre |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | A Helyreállítási tár biztonsági mentési műveletének állapotát adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngine/read | Visszaadja a tárolóban regisztrált összes biztonsági mentési felügyeleti kiszolgálót. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Az összes védett tároló lekérte |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | A Recovery Services-tárolók biztonsági mentésének állapotának ellenőrzése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Funkciók ellenőrzése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Feloldja a riasztást. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Lekért műveletállapot egy adott művelethez |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Az összes biztonsági mentési védelmi szándék felsorolása |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

Lehetővé teszi a biztonsági mentési szolgáltatások kezelését, kivéve a biztonsági mentések eltávolítását, a tárolók létrehozását és mások hozzáférésének adát. [További információ](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | A virtuális hálózat definíciójának lekért része |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | A művelet állapotát adja vissza |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Lekérte a Védelmi tárolón végrehajtott művelet eredményét. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Biztonsági másolatot készít a védett elemről. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Lekérte a védett elemeken végrehajtott művelet eredményét. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A Védett elemeken végrehajtott művelet állapotát adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem objektumának részleteit adja vissza |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Azonnali elem helyreállítása védett elemhez |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action | Get AccessToken a régiók közötti visszaállításhoz. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Lekért helyreállítási pontok a védett elemekhez. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | A védett elemek helyreállítási pontjainak visszaállítása. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Az azonnali elem helyreállításának visszavonása védett elemhez |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Biztonsági másolat védett elemének létrehozása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Visszaadja az összes regisztrált tárolót |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Frissíti a tárolók listáját |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Feladatok exportálása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Biztonsági mentési felügyeleti műveletek eredményeinek létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek lekért eredménye. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Az összes védelmi szabályzatot visszaadja |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Biztonságimenthet elemet hozhat létre és kezelhet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Az összes védett elem listáját adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tárolót visszaadja |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | A Recovery Services védett elemeinek és védett kiszolgálóinak összegzéseit adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | Az Erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás-/tároló hitelesítőadat-tanúsítványát. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | A Kiterjesztett információ lekért művelet lekérte egy objektum kiterjesztett adatait, amely a ?vault típusú Azure-erőforrást képviseli? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/write | A Kiterjesztett információ lekért művelet lekérte egy objektum kiterjesztett adatait, amely a ?vault típusú Azure-erőforrást képviseli? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Lekérte a Recovery Services-tároló riasztását. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A Get Vault művelet lekért egy objektumot, amely a "vault" típusú Azure-erőforrást képviseli |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | A Get Operation Results művelettel lekért művelet állapota és eredménye az aszinkron módon elküldött művelethez |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | A Get Containers művelettel lekérte az erőforráshoz regisztrált tárolókat. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/write | A Register Service Container (Szolgáltatástároló regisztrálása) művelettel regisztrálhat egy tárolót a Helyreállítási szolgáltatásban. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Helyreállítási tár használati adatait adja vissza. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | A tárfiókok listáját adja vissza, vagy lekérte a megadott tárfiók tulajdonságait. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Művelet érvényesítése védett elemen |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | A Helyreállítási tár biztonsági mentési műveletének állapotát adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | A Szabályzatművelet állapotának lekért állapota. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/write | Regisztrált tárolót hoz létre |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/kérdezés/művelet | Tárolón belüli számítási feladatok lekérdezése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngine/read | Visszaadja a tárolóban regisztrált összes biztonsági mentési felügyeleti kiszolgálót. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Biztonsági mentési védelmi szándék létrehozása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Biztonsági mentési védelmi szándék lekért |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Az összes védett tároló lekérte |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Egy tároló összes elemének lekért száma |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | A Recovery Services-tárolók biztonsági mentésének állapotának ellenőrzése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Funkciók ellenőrzése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupAadProperties/read | Szerezze be az AAD-tulajdonságokat a hitelesítéshez a harmadik régióban a régiók közötti visszaállításhoz. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJobs/action | List cross Region Restore Jobs in the secondary region for Recovery Services Vault (Régiók közötti visszaállítási feladatok listája a helyreállítási tár másodlagos régiójában). |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJob/action | Szerezze be a régiók közötti visszaállítási feladat részleteit a helyreállítási tár másodlagos régiójában. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrossRegionRestore/action | Régióközi visszaállítás aktiválása. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationResults/read | A helyreállítási tár CRR-műveletének eredményét adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationsStatus/read | A helyreállítási tár CRR-műveletének állapotát adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Feloldja a riasztást. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Lekért műveletállapot egy adott művelethez |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Az összes biztonsági mentési védelmi szándék felsorolása |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="backup-reader"></a>Biztonságimásolat-olvasó

Megtekintheti a biztonsági mentési szolgáltatásokat, de nem tud módosításokat [tenni. További információ](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | A GetAllocatedStamp a szolgáltatás által használt belső művelet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | A művelet állapotát adja vissza |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Lekérte a Védelmi tárolón végrehajtott művelet eredményét. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Lekérte a védett elemeken végrehajtott művelet eredményét. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A Védett elemeken végrehajtott művelet állapotát adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem objektumának részleteit adja vissza |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Lekért helyreállítási pontok a védett elemekhez. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Visszaadja az összes regisztrált tárolót |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/read | A feladatművelet eredményét adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/read | Az összes feladatobjektumot visszaadja |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Feladatok exportálása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/read | A Helyreállítási tár biztonsági mentési műveletének eredményét adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek lekért eredménye. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Az összes védelmi szabályzatot visszaadja |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Az összes védett elem listáját adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Visszaadja az előfizetéshez tartozó összes tárolót |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | A Recovery Services védett elemeinek és védett kiszolgálóinak összegzéseit adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | A Kiterjesztett információ lekért művelet lekérte egy objektum kiterjesztett adatait, amely a ?vault típusú Azure-erőforrást képviseli? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Lekérte a Recovery Services-tároló riasztását. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A Get Vault művelet lekért egy objektumot, amely a "vault" típusú Azure-erőforrást képviseli |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | A Get Operation Results művelettel lekért művelet állapota és eredménye az aszinkron módon elküldött művelethez |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | A Get Containers művelettel lekérte az erőforráshoz regisztrált tárolókat. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/read | A Recovery Services-tároló tárolási konfigurációját adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/read | A Recovery Services-tároló konfigurációját adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | A Helyreállítási tár biztonsági mentési műveletének állapotát adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | A Szabályzatművelet állapotának lekért állapota. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngine/read | Visszaadja a tárolóban regisztrált összes biztonsági mentési felügyeleti kiszolgálót. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Biztonsági mentési védelmi szándék lekért |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Egy tároló összes elemének lekért száma |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | A Recovery Services-tárolók biztonsági mentésének állapotának ellenőrzése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Feloldja a riasztást. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Lekért műveletállapot egy adott művelethez |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Az összes biztonsági mentési védelmi szándék felsorolása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Helyreállítási tár használati adatait adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Funkciók ellenőrzése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="classic-storage-account-contributor"></a>Klasszikus tárfiók-közreműködő

Lehetővé teszi a klasszikus tárfiókok kezelését, de nem férhet hozzájuk.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="classic-storage-account-key-operator-service-role"></a>Klasszikus tárfiókkulcs-kezelői szolgáltatásszerepk

A klasszikus tárfiók kulcskezelői listálhatnak és újragenerálhatnak kulcsokat a klasszikus tárfiókok [esetében További információ](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/action | Felsorolja a tárfiókok hozzáférési kulcsait. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/action | Újra létrehozza a tárfiók meglévő hozzáférési kulcsait. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="data-box-contributor"></a>Data Box Közreműködő

Lehetővé teszi, hogy mindent kezelhet a Data Box szolgáltatás alatt, kivéve, hogy hozzáférést biztosít másoknak. [További információ](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="data-box-reader"></a>Data Box Olvasó

Lehetővé teszi a Data Box szolgáltatás kezelését, kivéve a rendelési adatok létrehozását vagy szerkesztését, valamint a hozzáférést másoknak. [További információ](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/*/read |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listsecrets/action |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listcredentials/action | Felsorolja a rendeléshez kapcsolódó titkosítatlan hitelesítő adatokat. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/action | Ez a metódus az elérhető skus-k listáját adja vissza. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/action | Ez a módszer minden ellenőrzést el tudtennek. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/action | Ez a metódus a régió konfigurációit adja vissza. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/action | Ellenőrzi a szállítási címet, és alternatív címeket ad meg, ha van. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics Developer

Lehetővé teszi, hogy saját feladatokat küldjön el, figyelje és kezeljen, de ne hozzon létre vagy töröljön Data Lake Analytics fiókokat. [További információ](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | DataLakeAnalytics-fiók törlése. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/action | Engedélyek megadása a más felhasználók által beküldött feladatok megszakítása érdekében. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | DataLakeAnalytics-fiók létrehozása vagy frissítése. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | DataLakeAnalytics-fiók összekapcsolt DataLakeStore-fiókjának létrehozása vagy frissítése. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | DataLakeStore-fiók leválasztása Egy DataLakeAnalytics-fiókról. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | DataLakeAnalytics-fiók csatolt Storage-fiókjának létrehozása vagy frissítése. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | Tárfiók leválasztása a DataLakeAnalytics-fiókról. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | Hozzon létre vagy frissítsen egy tűzfalszabályt. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | Tűzfalszabály törlése. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | Számítási szabályzat létrehozása vagy frissítése. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | Számítási szabályzat törlése. |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="reader-and-data-access"></a>Olvasó és adatelérés

Lehetővé teszi, hogy mindent megtekintsen, de nem engedi, hogy töröljön vagy hozzon létre egy tárfiókot vagy tartalmazott erőforrást. Emellett olvasási/írási hozzáférést is biztosít a tárfiókban található összes adathoz a tárfiókkulcsok elérésével.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsait adja vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/action | Visszaadja a fiók SAS-jogkivonatát a megadott tárfiókhoz. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | A tárfiókok listáját adja vissza, vagy lekérte a megadott tárfiók tulajdonságait. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

Lehetővé teszi a tárfiókok kezelését. Hozzáférést biztosít a fiókkulcshoz, amellyel megosztott kulcsos hitelesítéssel férhet hozzá az adatokhoz. [További információ](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a diagnosztikai Analysis Server |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Egy erőforrást, például tárfiókot vagy SQL-adatbázist csatlakozik egy alhálózathoz. Nem riasztható. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="storage-account-key-operator-service-role"></a>Tárfiókkulcs-kezelői szolgáltatásszerepk

Lehetővé teszi a tárfiók hozzáférési kulcsának listázását és újragenerálását. [További információ](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsait adja vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/action | Újra létrehozza a megadott tárfiók hozzáférési kulcsait. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

Azure Storage-tárolók és -blobok olvasása, írása és törlése. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Tároló törlése. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Tárolót vagy tárolók listáját adja vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | A tároló metaadatainak vagy tulajdonságainak módosítása. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Felhasználódelegálás kulcsát adja vissza a Blob service. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Blob törlése. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Blobot vagy blobok listáját adja vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Írás blobba. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/move/action | Áthelyezi a blobot az egyik útvonalról a másikra |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/add/action | Blobtartalom hozzáadásának eredményét adja vissza |
> | **NotDataActions (NotDataActions)** |  |
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
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action"
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

Teljes hozzáférést biztosít az Azure Storage-blobtárolókhoz és -adatokhoz, beleértve a POSIX hozzáférés-vezérlés hozzárendelését. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | A tárolókra vonatkozó teljes engedélyek. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Felhasználódelegáláskulcsot ad vissza a Blob service. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/* | Blobok teljes körű engedélyei. |
> | **NotDataActions (NotDataActions)** |  |
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

Olvassa el és listába sorolja az Azure Storage-tárolókat és -blobokat. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Tárolót vagy tárolólistát ad vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Felhasználódelegáláskulcsot ad vissza a Blob service. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Blobot vagy blobok listáját adja vissza. |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="storage-blob-delegator"></a>Storage Blob Delegator

Szerezze be a felhasználódelegálás kulcsát, amellyel létrehozhat egy közös hozzáférésű jogosultságjelet az Azure AD hitelesítő adataival aláírt tárolóhoz vagy blobhoz. További információ: [Felhasználódelegálás SAS létrehozása.](/rest/api/storageservices/create-user-delegation-sas) [További információ](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Felhasználódelegálás kulcsát adja vissza a Blob service. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

Lehetővé teszi az Azure-fájlmegosztások fájljainak/könyvtárának olvasási, írási és törlési hozzáférését. Ez a szerepkör nem rendelkezik beépített megfelelővel a Windows-fájlkiszolgálókban. [További információ](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Egy fájlt/mappát vagy a fájlok/mappák listáját adja vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Egy fájl írásának vagy egy mappa létrehozásának eredményét adja vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Egy fájl/mappa törlésének eredményét adja vissza. |
> | **NotDataActions (Nem adataktívok)** |  |
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

Lehetővé teszi az Azure-fájlmegosztások fájljainak/könyvtárának ACL-ek olvasását, írását, törlését és módosítását. Ez a szerepkör megegyezik a Windows-fájlkiszolgálók módosítási ACL-jének fájlmegosztási ACL-jéhez. [További információ](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Egy fájlt/mappát vagy a fájlok/mappák listáját adja vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Egy fájl írásának vagy egy mappa létrehozásának eredményét adja vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Egy fájl/mappa törlésének eredményét adja vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Egy fájlra/mappára vonatkozó engedély módosításának eredményét adja vissza. |
> | **NotDataActions (Nem adatactions)** |  |
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

Olvasási hozzáférést biztosít az Azure-fájlmegosztások fájljaihoz/könyvtárihoz. Ez a szerepkör egyenértékű a Windows-fájlkiszolgálókon olvasott fájlmegosztásICL-ekkel. [További információ](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Egy fájlt/mappát vagy egy fájl-/mappalistát ad vissza. |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="storage-queue-data-contributor"></a>Storage Queue-adatok közreműködője

Azure Storage-üzenetsorok és üzenetsor-üzenetek olvasása, írása és törlése. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/delete | Üzenetsor törlése. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Üzenetsort vagy üzenetsorok listáját adja vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/write | Az üzenetsor metaadatainak vagy tulajdonságainak módosítása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/delete | Töröljön egy vagy több üzenetet egy üzenetsorból. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Betekintés vagy egy vagy több üzenet lekérése egy üzenetsorból. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/write | Üzenet hozzáadása egy üzenetsorhoz. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | Üzenet feldolgozásának eredményét adja vissza |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="storage-queue-data-message-processor"></a>Tárolási üzenetsor adatüzenet-feldolgozója

Üzenetek betekintése, lekérése és törlése egy Azure Storage-üzenetsorból. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Betekintés egy üzenetbe. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | Üzenet lekérése és törlése. |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="storage-queue-data-message-sender"></a>Tárolási üzenetsor adatüzenet-küldője

Üzenetek hozzáadása egy Azure Storage-üzenetsorhoz. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/add/action | Üzenet hozzáadása egy üzenetsorhoz. |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="storage-queue-data-reader"></a>Tárolási üzenetsor adatolvasója

Azure Storage-üzenetsorok és üzenetsor-üzenetek olvasása és listába írása. Az adott adatművelethez szükséges műveletekről a blob- és üzenetsor-adatműveletek hívására vonatkozó [engedélyeket lásd:](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [További információ](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Üzenetsort vagy üzenetsorok listáját adja vissza. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Betekintés vagy egy vagy több üzenet lekérése egy üzenetsorból. |
> | **NotDataActions (Nem adataktívok)** |  |
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


### <a name="azure-maps-data-contributor"></a>Azure Maps adat-közreműködő

Olvasási, írási és törlési hozzáférést biztosít a kapcsolódó adatok Azure Maps-fiókból való leképezéséhez. [További információ](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/write |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/delete |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read, write, and delete access to map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "name": "8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read",
        "Microsoft.Maps/accounts/*/write",
        "Microsoft.Maps/accounts/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-maps-data-reader"></a>Azure Maps adatolvasó

Hozzáférést biztosít egy Azure Maps-fiók térképekkel kapcsolatos adatainak olvasására. [További információ](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="azure-spring-cloud-data-reader"></a>Azure Spring Cloud adatolvasó

Olvasási hozzáférés engedélyezése a Azure Spring Cloud adatokhoz [További információ](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/*/read |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read access to Azure Spring Cloud Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b5537268-8956-4941-a8f0-646150406f0c",
  "name": "b5537268-8956-4941-a8f0-646150406f0c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Keresési szolgáltatás közreműködője

Lehetővé teszi a keresési szolgáltatások kezelését, de a hozzájuk való hozzáférést nem. [További információ](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | Keresési szolgáltatások létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="signalr-accesskey-reader"></a>SignalR AccessKey Reader

Hozzáférési SignalR Service olvasása

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/read |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/action | A SignalR hozzáférési kulcsok értékének megtekintése a felügyeleti portálon vagy API-n keresztül |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="signalr-app-server-preview"></a>SignalR App Server (előzetes verzió)

Lehetővé teszi, hogy az alkalmazáskiszolgáló SignalR Service AAD-hitelesítési beállításokkal hozzáférjen.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | Hozzon létre egy ideiglenes AccessKey kulcsot a ClientTokens aláírásához. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/write | Indítson el egy kiszolgálókapcsolatot. |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="signalr-contributor"></a>SignalR-közreműködő

SignalR-szolgáltatási erőforrások létrehozása, olvasása, frissítése és törlése

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="signalr-serverless-contributor-preview"></a>SignalR kiszolgáló nélküli közreműködő (előzetes verzió)

Lehetővé teszi, hogy az alkalmazás kiszolgáló nélküli módban, AAD-hitelesítési beállításokkal fér hozzá.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | Hozzon létre egy ClientToken-t az ügyfélkapcsolat létrehozásához. |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="signalr-service-owner-preview"></a>SignalR Service tulajdonos (előzetes verzió)

Teljes hozzáférés az Azure SignalR Service REST API-khoz

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | Hozzon létre egy ideiglenes AccessKey kulcsot a ClientTokens aláírásához. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | Hozzon létre egy ClientToken-t az ügyfélkapcsolat létrehozásához. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/send/action | Üzenetek szórása az összes ügyfélkapcsolatra a központban. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/send/action | Üzenet küldése a csoportnak. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Csoport meglétének vagy a felhasználó csoportban való meglétének ellenőrzése. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/write | Csatlakozás/ Csoport elhagyva. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/send/action | Üzenetek küldése közvetlenül egy ügyfélkapcsolatnak. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Ellenőrizze az ügyfélkapcsolat meglétét. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/write | Zárja be az ügyfélkapcsolatot. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/send/action | Üzeneteket küldhet a felhasználónak, akik több ügyfélkapcsolatból állhatnak. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Ellenőrizze a felhasználó meglétét. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/write | Felhasználó módosítása. |
> | **NotDataActions (NotDataActions)** |  |
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
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/auth/clientToken/action",
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

### <a name="signalr-service-reader-preview"></a>SignalR Service Olvasó (előzetes verzió)

Csak olvasási hozzáférés a Azure SignalR Service REST API-khoz

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Ellenőrizze a csoport meglétét vagy a felhasználó meglétét a csoportban. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Ellenőrizze az ügyfélkapcsolat meglétét. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Ellenőrizze a felhasználó meglétét. |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="web-plan-contributor"></a>Webes csomag közreműködője

Lehetővé teszi a webhelyek webes csomagjainak kezelését, de a hozzájuk való hozzáférést nem.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Kiszolgálófarmok létrehozása és kezelése |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | Csatlakozik egy App Service Environment |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="website-contributor"></a>Webhely közreműködője

Lehetővé teszi webhelyek (nem webes csomagok) kezelését, de a hozzájuk való hozzáférést nem.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights-összetevők létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/certificates/* | Webhelytanúsítványok létrehozása és kezelése |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/read | Lekérte az állomásnévhez rendelt helyek nevét. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Csatlakozik egy App Service csomaghoz |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Egy terv tulajdonságainak App Service le |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/* | Webhelyek létrehozása és kezelése (a webhely-létrehozáshoz írási engedélyekre is szükség van a társított App Service tervhez) |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

acr delete [További információ](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/delete | Törölje a tároló-beállításjegyzékben található összetevőt. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

acr image signer [További információ](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/sign/write | Tartalom megbízhatósági metaadatainak leküldése/leküldése tároló-beállításjegyzékhez. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

acr pull [– További információ](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Rendszerképek lekért vagy lekért egy tároló-beállításjegyzékből. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="acrpush"></a>AcrPush (AcrPush)

acr push [További információ](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Rendszerképek lekért vagy lekért egy tároló-beállításjegyzékből. |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | Rendszerképek leküldése vagy írása egy tároló-beállításjegyzékbe. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

acr quarantine data reader

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Karanténba helyezett rendszerképek lekért vagy lekért adatok a tároló-beállításjegyzékből |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

acr quarantine adatíró

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Karanténba helyezett rendszerképek lekért vagy lekért adatok a tároló-beállításjegyzékből |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/write | Karanténba helyezett rendszerképek karanténba helyezett állapotának írása/módosítása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service fürt rendszergazdai szerepköre

List cluster admin credential action. [További információ](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/action | Felügyelt fürt fürtadmin hitelesítő adatainak felsorolása |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/action | Felügyelt fürt hozzáférési profilja szerepkörnév alapján listás hitelesítő adatok használatával |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Felügyelt fürt leözése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service felhasználói szerepkör létrehozása

List cluster user credential action. [További információ](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | A fürt felhasználó által használt hitelesítő adatainak felsorolása egy felügyelt fürthöz |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Felügyelt fürt leözése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="azure-kubernetes-service-contributor-role"></a>Azure Kubernetes Service közreműködői szerepkör

Olvasási és írási hozzáférést biztosít Azure Kubernetes Service fürtökhöz [További információ](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Felügyelt fürt leözése |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | Új felügyelt fürtöt hoz létre, vagy frissíti a meglévőt |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="azure-kubernetes-service-rbac-admin"></a>Azure Kubernetes Service RBAC-rendszergazda

Lehetővé teszi a fürt/névtér alatt az összes erőforrás kezelését, kivéve az erőforráskvóták és a névterek frissítését vagy törlését. [További információ](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Központi telepítést hoz létre vagy frissíti. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | A fürt felhasználó által használt hitelesítő adatainak felsorolása egy felügyelt fürthöz |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | Erőforrás-idézőjelek írása |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | Törli a resourcequotas adatokat |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | Névtereket ír |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/delete | Névtereket töröl |

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
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Központi telepítést hoz létre vagy frissíti. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | A fürtususer hitelesítő adatainak listása egy felügyelt fürthöz |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

Csak olvasási hozzáférést biztosít a névtér legtöbb objektumának eléréséhez. Nem engedélyezi a szerepkörök és a szerepkörkötések megtekintését. Ez a szerepkör nem engedélyezi a titkos kulcsok megtekintését, mivel a titkos kulcsok tartalmának beolvasása hozzáférést biztosít a ServiceAccount hitelesítő adatokhoz a névtérben, ami lehetővé teszi az API-hozzáférést, mint a névtérben bármely ServiceAccount (a jogosultságok eszkalálásának egyik formája). Ennek a szerepkörnek a fürthatókörön való alkalmazása hozzáférést biztosít az összes névtérhez. [További információ](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Központi telepítést hoz létre vagy frissíti. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Olvasási vezérlőkrevisions |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/read | Olvasási démonkészletek |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/read | Beolvassa az üzemelő példányokat |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/read | Beolvassa a replikákat |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/read | Beolvassa az állapot-készleteket |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/read | Olvasások horizontálispodautoscalers |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/read | Olvasások cronjobs |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/read | Beolvassa a feladatokat |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/read | Beolvassa a konfigurációtérképeket |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/read | Beolvassa a végpontokat |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Események olvasása |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Események olvasása |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/read | Olvasások démonkészletek |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/read | Olvasások üzemelő példányai |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/read | Beolvasott bejövő forgalom |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/read | Beolvassa a networkpolicies -t |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/read | Beolvassa a replikákat |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Olvasási korlát |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Beolvassa a névtereket |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/read | Beolvasott bejövő forgalom |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/read | Beolvassa a networkpolicies -t |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/read | Olvasások persistentvolumeclaims |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/read | Olvasás podok |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/read | Olvasások poddisruptionbudgets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Beolvassa a replicationcontrollers vezérlőket |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Beolvassa a replicationcontrollers vezérlőket |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Olvasások resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/read | Reads serviceaccounts |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/read | Olvasási szolgáltatások |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="azure-kubernetes-service-rbac-writer"></a>Azure Kubernetes Service RBAC-író

Olvasási/írási hozzáférést biztosít a névtér legtöbb objektumához. Ez a szerepkör nem engedélyezi a szerepkörök vagy szerepkörkötések megtekintését vagy módosítását. Ez a szerepkör azonban lehetővé teszi a titkos kulcsokhoz való hozzáférést és a podok futtatását bármely ServiceAccount-ként a névtérben, így a névtérben bármely ServiceAccount API-hozzáférési szintje elérhető. Ennek a szerepkörnek a fürthatókörön való alkalmazása hozzáférést biztosít az összes névtérhez. [További információ](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Központi telepítést hoz létre vagy frissíti. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Olvasási vezérlőkrevisions |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Események olvasása |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Események olvasása |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Olvasási korlátok |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Olvasások névterei |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Beolvassa a resourcequotas erőforrást |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/secrets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB-olvasó szerepkör

Olvashatja a Azure Cosmos DB adatait. Lásd: [DocumentDB-fiók közreműködője](#documentdb-account-contributor) a Azure Cosmos DB kezeléséhez. [További információ](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/read | Gyűjtemények olvasása |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/action | Beolvassa az adatbázisfiók csak olvasható kulcsait. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/read | Metrikadefiníciók olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/read | Metrikák olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="cosmos-db-operator"></a>Cosmos DB Operátor

Lehetővé teszi a Azure Cosmos DB kezelését, de nem férhet hozzá a bennük elérhető adatokhoz. Megakadályozza a fiókkulcsok és kapcsolati sztringek hozzáférését. [További információ](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Egy erőforrást, például tárfiókot vagy SQL-adatbázist csatlakozik egy alhálózathoz. Nem riasztható. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/write | SQL-szerepkördefiníció létrehozása vagy frissítése |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/delete | SQL-szerepkör definíciójának törlése |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/write | SQL-szerepkör-hozzárendelés létrehozása vagy frissítése |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/delete | SQL-szerepkör-hozzárendelés törlése |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/delete",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/delete"
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

Küldhet visszaállítási kérelmet egy Cosmos DB adatbázisra vagy egy fiók [tárolójára. További információ](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/action | A biztonsági mentés konfigurálásának kérése |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/restore/action | Visszaállítási kérelem elküldése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

Visszaállítási műveletet hajthat végre Cosmos DB folyamatos biztonsági mentési módú adatbázisfiókhoz

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restoreableDatabaseAccounts/restore/action | Visszaállítási kérelem elküldése |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/*/read |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/read | Visszaállítható adatbázisfiók olvasása vagy Az összes visszaállítható adatbázisfiók list mentése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="documentdb-account-contributor"></a>DocumentDB-fiók közreműködője

Kezelheti a Azure Cosmos DB fiókokat. Azure Cosmos DB korábban DocumentDB néven ismert. [További információ](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Fiók létrehozása és Azure Cosmos DB kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Egy erőforrást, például tárfiókot vagy SQL-adatbázist csatlakozik egy alhálózathoz. Nem riasztható. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="redis-cache-contributor"></a>Redis Cache Közreműködő

Lehetővé teszi a Redis-gyorsítótárak kezelését, de nem férhet hozzájuk.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/register/action | Regisztrálja a "Microsoft.Cache" erőforrás-szolgáltatót egy előfizetésben |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/redis/* | Redis-gyorsítótárak létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="sql-db-contributor"></a>SQL DB-közreműködő

Lehetővé teszi az SQL-adatbázisok kezelését, de nem férhet hozzájuk. Emellett a biztonsággal kapcsolatos szabályzatokat és a szülő SQL-kiszolgálójukat sem kezelheti. [További információ](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/* | SQL-adatbázisok létrehozása és kezelése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | A kiszolgálók listáját adja vissza, vagy lekérte a megadott kiszolgáló tulajdonságait. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Metrikák olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Metrikadefiníciók olvasása |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Naplózási beállítások szerkesztése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Az adatbázisblob naplórekordjainak lekérése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Adatmaszkolási szabályzatok szerkesztése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Biztonsági riasztási szabályzatok szerkesztése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Biztonsági metrikák szerkesztése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="sql-managed-instance-contributor"></a>SQL Managed Instance Közreműködő

Lehetővé teszi a felügyelt SQL-példányok és a szükséges hálózati konfigurációk kezelését, de nem adhat hozzáférést másoknak.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/alhálózatok/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Metrikák olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Metrikadefiníciók olvasása |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/delete | Töröl egy adott felügyelt kiszolgálót, Azure Active Directory csak hitelesítési objektumot. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/write | Adott felügyelt kiszolgáló hozzáadása vagy frissítése Azure Active Directory csak hitelesítési objektummal |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

Lehetővé teszi az SQL-kiszolgálók és adatbázisok biztonsággal kapcsolatos házirendek kezelését, de a hozzáférésük nem. [További információ](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Egy erőforrást, például tárfiókot vagy SQL-adatbázist csatlakozik egy alhálózathoz. Nem riasztható. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/administratorAzureAsyncOperation/read | Lekérte az Azure async felügyelt példány rendszergazdai műveleteinek eredményét. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | SQL Server naplózási beállításának létrehozása és kezelése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/read | Egy adott kiszolgálón konfigurált kiterjesztett kiszolgálói blob naplózási házirend részleteinek lekérése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | SQL Server-adatbázis naplózási beállításainak létrehozása és kezelése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Az adatbázisblob naplórekordjainak lekérése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | SQL Server-adatbázis adatmaszkolási házirendjeinek létrehozása és kezelése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/read | Egy adott adatbázishoz konfigurált kiterjesztett blob naplózási szabályzat részleteinek lekérése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/read | Az adatbázisok listáját adja vissza, vagy lekérte a megadott adatbázis tulajdonságait. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/read | Adatbázisséma lekérte. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/read | Adatbázisoszlop lekérte. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/read | Adatbázistábla lekérte. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | SQL Server-adatbázis biztonsági riasztási szabályzatának létrehozása és kezelése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | SQL Server-adatbázis biztonsági metrikainak létrehozása és kezelése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/firewallRules/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | A kiszolgálók listáját adja vissza, vagy lekérte a megadott kiszolgáló tulajdonságait. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | SQL Server biztonsági riasztási szabályzatok létrehozása és kezelése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/read | Visszaadja a felügyelt példányok listáját, vagy lekérte a megadott felügyelt példány tulajdonságait. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/sqlVulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/administrators/read | Lekérte a felügyelt példányok rendszergazdáinak listáját. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/administrators/read | Lekért egy Azure Active Directory rendszergazdai objektumot |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*",
        "Microsoft.Security/sqlVulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/administrators/read",
        "Microsoft.Sql/servers/administrators/read"
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

### <a name="sql-server-contributor"></a>SQL Server Közreműködő

Lehetővé teszi az SQL-kiszolgálók és adatbázisok kezelését, de a hozzáférésüket nem, és nem a biztonsággal kapcsolatos házirendeket. [További információ](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/* | SQL-kiszolgálók létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Metrikák olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Metrikadefiníciók olvasása |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | SQL Server naplózási beállításainak szerkesztése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | SQL Server-adatbázis naplózási beállításainak szerkesztése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Az adatbázisblob naplórekordjainak lekérése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | SQL Server-adatbázis adatmaszkolási házirendjeinek szerkesztése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | SQL Server-adatbázis biztonsági riasztási szabályzatának szerkesztése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | SQL Server-adatbázis biztonsági metrikainak szerkesztése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | SQL Server biztonsági riasztási szabályzatok szerkesztése |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/delete | Töröl egy adott kiszolgálói Azure Active Directory csak hitelesítési objektumot |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/write | Adott kiszolgáló hozzáadása vagy frissítése Azure Active Directory csak hitelesítési objektummal |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
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


### <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs adattulajdonos

Teljes hozzáférést biztosít a Azure Event Hubs erőforrásokhoz. [További információ](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs adat fogadója

Hozzáférést biztosít a Azure Event Hubs erőforrásokhoz. [További információ](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/receive/action |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs adatküldő

Hozzáférést biztosít a Azure Event Hubs erőforrásokhoz. [További információ](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/send/action |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="data-factory-contributor"></a>Data Factory Közreműködő

Adat-üzemek, valamint bennük található gyermekerőforrások létrehozása és kezelése. [További információ](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Adat-üzemek és gyermekerőforrások létrehozása és kezelése bennük. |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | Adat-üzemek és gyermekerőforrások létrehozása és kezelése bennük. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | EventSubscription létrehozása vagy frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="data-purger"></a>Adatüres

Privát adatok törlése Log Analytics-munkaterületről. [További információ](../azure-monitor/logs/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/purge/action | Adatok kiürítés a Application Insights |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Naplóelemzési adatok megtekintése |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/purge/action | Megadott adatok törlése a munkaterületről |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

Lehetővé teszi a HDInsight-fürtkonfigurációk olvasását és módosítását. [További információ](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/*/read |  |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/action | Átjáróbeállítások lekérte a HDInsight-fürthöz |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/action | A HDInsight-fürt átjáróbeállításának frissítése |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurations/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Lekért vagy listázza az üzembe helyezési műveleteket. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="hdinsight-domain-services-contributor"></a>HDInsight Domain Services-közreműködő

Olvassa el, hozza létre, módosítsa és törölje a HDInsighthoz szükséges tartományi szolgáltatásokkal kapcsolatos műveleteket Enterprise Security Package [További információ](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

A Log Analytics közreműködője olvashatja az összes monitorozási adatot, és szerkesztheti a figyelési beállításokat. A figyelési beállítások szerkesztéséhez hozzá kell adni a virtuálisgép-bővítményt a virtuális gépekhez; a tárfiókkulcsok olvasása a naplók Azure Storage-ból való gyűjtésének konfigurálása érdekében; Automation-fiókok létrehozása és konfigurálása; megoldások hozzáadása; és az Azure Diagnostics konfigurálása az összes Azure-erőforráson. [További információ](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */olvasás | A titkos kulcsok kivételével minden típusú erőforrást beolvas. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/extensions/* |  |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Felsorolja a tárfiókok hozzáférési kulcsait. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/extensions/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Telepíti vagy frissíti a Azure Arc bővítményeit |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a diagnosztikai Analysis Server |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsait adja vissza. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

A Log Analytics-olvasó megtekintheti és megkeresheti az összes monitorozási adatot, valamint megtekintheti a monitorozási beállításokat, beleértve az Azure-diagnosztika konfigurációjának megtekintését az összes Azure-erőforráson. [További információ](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */olvasás | A titkos kulcsok kivételével minden típusú erőforrást beolvas. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Keresés az új motorral. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Keresési lekérdezést hajt végre |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/read | Lekéri a munkaterület megosztott kulcsait. Ezekkel a kulcsokkal csatlakoztathatja a Microsoft Operational Insights-ügynököket a munkaterülethez. |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="purview-data-curator"></a>Purview Data Curator

A Microsoft.Purview adatkulátor katalógusadat-objektumokat hozhat létre, olvashat, módosíthat és törölhet, valamint kapcsolatokat hozhat létre az objektumok között. Ez a szerepkör előzetes verzióban érhető el, és változhat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Olvassa el a Microsoft Purview szolgáltató fiókerőforrását. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Adatobjektumok olvasása. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/write | Adatobjektumok létrehozása, frissítése és törlése. |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="purview-data-reader"></a>Az adatolvasó végleges nézete

A Microsoft.Purview adatolvasó olvashatja a katalógus adatobjektumokat. Ez a szerepkör előzetes verzióban érhető el, és változhat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Olvassa el a Microsoft Purview szolgáltató fiókerőforrását. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Adatobjektumok olvasása. |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="purview-data-source-administrator"></a>Adatforrás rendszergazdájának véglegesnézete

A Microsoft.Purview adatforrás rendszergazdája kezelheti az adatforrásokat és az adatvizsgálatokat. Ez a szerepkör előzetes verzióban érhető el, és változhat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Olvassa el a Microsoft Purview szolgáltató fiókerőforrását. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/read | Adatforrások és vizsgálatok olvasása. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/write | Adatforrások létrehozása, frissítése és törlése, valamint vizsgálatok kezelése. |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="schema-registry-contributor-preview"></a>Sémajegyzék közreműködője (előzetes verzió)

Séma-beállításjegyzék-csoportok és -sémák olvasása, írása és törlése.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="schema-registry-reader-preview"></a>Sémajegyzék-olvasó (előzetes verzió)

Olvassa el és listába sorolja a séma-beállításjegyzék-csoportokat és -sémákat.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/read | A SchemaGroup erőforrásleírások listájának lekért listája |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/read | Sémák lekérése |
> | **NotDataActions (NotDataActions)** |  |
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


### <a name="blockchain-member-node-access-preview"></a>Blokklánctagcsomópont-hozzáférés (előzetes verzió)

A Blockchain-tagcsomópontokhoz való hozzáférés lehetővé [teszi További információ](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/read | Lekért vagy listázza a meglévő Blockchain-tagtranzakció-csomópont(ak)t. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/connect/action | Csatlakozik egy Blockchain-tagtranzakciós csomóponthoz. |
> | **NotDataActions (Nem adatactions)** |  |
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


### <a name="cognitive-services-contributor"></a>Cognitive Services Közreműködő

Segítségével létrehozhatja, olvashatja, frissítheti, törölheti és kezelheti a Cognitive Services. [További információ](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/features/read | Lekérte egy előfizetés funkcióit. |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/providers/features/read | Lekérte egy előfizetés funkcióját egy adott erőforrás-szolgáltatóban. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a diagnosztikai Analysis Server |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Naplódefiníciók olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Metrikadefiníciók olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Metrikák olvasása |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Lekért vagy listázza az üzembe helyezési műveleteket. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="cognitive-services-custom-vision-contributor"></a>Cognitive Services Custom Vision Közreműködő

Teljes hozzáférés a projekthez, beleértve a projektek megtekintésének, létrehozásának, szerkesztésének és törlésének képességét. [További információ](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the ability to view, create, edit, or delete projects.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "name": "c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Custom Vision Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-deployment"></a>Cognitive Services Custom Vision üzembe helyezés

Modellek közzététele, közzététele vagy exportálása. Az üzembe helyezés megtekintheti a projektet, de nem frissíthet. [További információ](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/publish/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/export/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/quicktest/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/classify/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/detect/* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Projekt exportálása. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Publish, unpublish or export models. Deployment can view the project but can't update.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "name": "5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/classify/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/detect/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Deployment",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-labeler"></a>Cognitive Services Custom Vision Labeler

Megtekintheti, szerkesztheti a betanítás képeit, és létrehozhatja, hozzáadhatja, eltávolíthatja vagy törölheti a képcímkéket. A címkézők megtekinthetik a projektet, de csak a betanítás képeit és címkéit frissítik. [További információ](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | Szerezze be az előrejelzési végpontra küldött képeket. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tags/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/suggested/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tagsandregions/suggestions/action | Ez az API javasolt címkéket és régiókat fog kapni a címkézetlen képek tömbjéhez/kötegéhez a címkék megbízhatóságával együtt. Üres tömböt ad vissza, ha nem található címke. |
> | **NotDataActions (NotDataActions)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Projekt exportálása. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit training images and create, add, remove, or delete the image tags. Labelers can view the project but can't update anything other than training images and tags.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/88424f51-ebe7-446f-bc41-7fa16989e96c",
  "name": "88424f51-ebe7-446f-bc41-7fa16989e96c",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tagsandregions/suggestions/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Labeler",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-reader"></a>Cognitive Services Custom Vision Olvasó

Csak olvasható műveletek a projektben. Az olvasók nem hozhatják létre vagy frissítheti a projektet. [További információ](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | Szerezze be az előrejelzési végpontra küldött képeket. |
> | **NotDataActions (Nem adataktívok)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Projekt exportálása. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only actions in the project. Readers can't create or update the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "name": "93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-trainer"></a>Cognitive Services Custom Vision oktató

Projektek megtekintése, szerkesztése és a modellek betanítása, beleértve a modellek közzétételének, közzétételének és exportálásának képességét. Az oktatók nem hozhatják létre vagy törölhetik a projektet. [További információ](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/action | Hozzon létre egy projektet. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/delete | Adott projekt törlése. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/import/action | Importál egy projektet. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Projekt exportálása. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit projects and train the models, including the ability to publish, unpublish, export the models. Trainers can't create or delete the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "name": "0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/delete",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/import/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Trainer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services adatolvasó (előzetes verzió)

Lehetővé teszi a Cognitive Services olvasását.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="cognitive-services-metrics-advisor-administrator"></a>Cognitive Services Metrics Advisor rendszergazda

Teljes hozzáférés a projekthez, beleértve a rendszerszintű konfigurációt is. [További információ](../cognitive-services/metrics-advisor/how-tos/alerts.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/MetricsService/* |  |
> | **NotDataActions (NotDataActions)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the system level configuration.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cb43c632-a144-4ec5-977c-e80c4affc34a",
  "name": "cb43c632-a144-4ec5-977c-e80c4affc34a",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/MetricsAdvisor/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Metrics Advisor Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-editor"></a>Cognitive Services QnA Maker Editor

Hozzunk létre, szerkesszünk, importálunk és exportálunk egy tudásbázist. Tudásbázist nem tehet közzé vagy törölhet. [További információ](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Információ lekért egy szerepkör-hozzárendelésről. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | Információ lekért egy szerepkör-definícióról. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | Lekérte a tudásbázisok listáját vagy egy adott ismeretbeíró részleteit. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | Töltse le a tudásbázist. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/create/write | Aszinkron művelet egy új tudásbázis létrehozásához. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/write | Aszinkron művelet tudásbázis módosításához vagy Tudásbázis tartalmának lecseréléshez. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | GenerateAnswer hívás a tudásbázis lekérdezéséhez. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/train/action | Betanítja a hívást, hogy javaslatokat adjon a tudásbázishoz. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | Módosítások letöltése a futásidőből. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/write | Cserélje le a módosítási adatokat. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | Lekért végpontkulcsok egy végponthoz |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/refreshkeys/action | Újra létrehoz egy végpontkulcsot. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | Lekért végpontbeállítások egy végponthoz |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/write | Frissítse egy végpont végponti biztonsági frissítését. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/operations/read | Lekért egy adott hosszú ideig futó művelet részleteit. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | Lekérte a tudásbázisok listáját vagy egy adott tudásbázisozó részleteit. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | Töltse le a tudásbázist. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/create/write | Aszinkron művelet egy új tudásbázis létrehozásához. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/write | Aszinkron művelet egy tudásbázis módosításához vagy a tudásbázis tartalmának lecseréléshez. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | GenerateAnswer hívás a tudásbázis lekérdezéséhez. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/train/action | Betanítja a hívást, hogy javaslatokat adjon a tudásbázishoz. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | Módosítások letöltése a futásidőből. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/write | Cserélje le a módosítási adatokat. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | Lekért végpontkulcsok egy végponthoz |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action | Újra létrehoz egy végpontkulcsot. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | Lekért végpontbeállítások egy végponthoz |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/write | Frissítse a végpontok végponti biztonsági frissítését. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/operations/read | Lekért egy adott hosszú ideig futó művelet részleteit. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | Lekérte a tudásbázisok listáját vagy egy adott ismeretbeíró részleteit. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | Töltse le a tudásbázist. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write | Aszinkron művelet egy új tudásbázis létrehozásához. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/write | Aszinkron művelet tudásbázis módosításához vagy Tudásbázis tartalmának lecseréléshez. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | GenerateAnswer hívás a tudásbázis lekérdezéséhez. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action | Betanítja a hívást, hogy javaslatokat adjon a tudásbázishoz. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/read | Módosítások letöltése a futásidőből. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/write | Cserélje le a módosítási adatokat. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | Lekért végpontkulcsok egy végponthoz |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action | Újra létrehoz egy végpontkulcsot. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | Lekért végpontbeállítások egy végponthoz |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/write | Frissítse egy végpont végponti biztonsági frissítését. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/operations/read | Lekért egy adott hosszú ideig futó művelet részleteit. |
> | **NotDataActions (Nem adatactions)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you create, edit, import and export a KB. You cannot publish or delete a KB.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "name": "f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/operations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/operations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/operations/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Editor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-reader"></a>Cognitive Services QnA Maker Olvasó

Csak a tudásbázist olvassa és tesztelje. [További információ](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Információ lekért egy szerepkör-hozzárendelésről. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | Információ lekért egy szerepkör-definícióról. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | Lekérte a tudásbázisok listáját vagy egy adott ismeretbeíró részleteit. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | Töltse le a tudásbázist. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | GenerateAnswer hívás a tudásbázis lekérdezéséhez. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | Módosítások letöltése a futásidőből. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | Lekért végpontkulcsok egy végponthoz |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | Lekért végpontbeállítások egy végponthoz |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | Lekérte a tudásbázisok listáját vagy egy adott ismeretbeíró részleteit. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | Töltse le a tudásbázist. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | GenerateAnswer hívás a tudásbázis lekérdezéséhez. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | Módosítások letöltése a futásidőből. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | Lekért végpontkulcsok egy végponthoz |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | Lekért végpontbeállítások egy végponthoz |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | Lekérte a tudásbázisok listáját vagy egy adott tudásbázisozó részleteit. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | Töltse le a tudásbázist. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | GenerateAnswer hívás a tudásbázis lekérdezéséhez. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/read | Módosítások letöltése a futásidőből. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | Lekért végpontkulcsok egy végponthoz |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | Lekért végpontbeállítások egy végponthoz |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you read and test a KB only.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/466ccd10-b268-4a11-b098-b4849f024126",
  "name": "466ccd10-b268-4a11-b098-b4849f024126",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services felhasználó

Lehetővé teszi a kulcskulcsok olvasását és Cognitive Services. [További információ](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/action | Kulcsok list(ak) |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Klasszikus metrikariasztás olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/read | Erőforrás diagnosztikai beállításának olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Naplódefiníciók olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Metrikadefiníciók olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Metrikák olvasása |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Lekért vagy listázza az üzembe helyezési műveleteket. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions (NotDataActions)** |  |
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

## <a name="internet-of-things"></a>Eszközök internetes hálózata


### <a name="device-update-administrator"></a>Eszközfrissítés-rendszergazda

Teljes hozzáférést biztosít a felügyeleti és tartalomműveleti műveletekhez [További információ](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Frissítésekhez kapcsolódó olvasási műveletet hajt végre |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | Frissítésekhez kapcsolódó írási műveletet hajt végre |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | Frissítésekhez kapcsolódó törlési műveletet hajt végre |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | A felügyelettel kapcsolatos olvasási műveletet hajt végre |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | A felügyelettel kapcsolatos írási műveletet hajt végre |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | A felügyelettel kapcsolatos törlési műveletet hajt végre |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management and content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "name": "02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete",
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-administrator"></a>Eszközfrissítési tartalom rendszergazdája

Teljes hozzáférést biztosít a tartalomművelethez [További információ](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Frissítésekhez kapcsolódó olvasási műveletet hajt végre |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | Frissítésekhez kapcsolódó írási műveletet hajt végre |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | Frissítésekhez kapcsolódó törlési műveletet hajt végre |
> | **NotDataActions (Nem adatactions)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "name": "0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-reader"></a>Eszközfrissítés tartalomolvasója

Olvasási hozzáférést biztosít a tartalomművelethez, de nem engedélyezi a [módosításokat További információ](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Frissítésekhez kapcsolódó olvasási műveletet hajt végre |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "name": "d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-administrator"></a>Eszközfrissítések központi telepítésének rendszergazdája

Teljes hozzáférést biztosít a felügyeleti műveletekhez [További információ](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | A felügyelettel kapcsolatos olvasási műveletet hajt végre |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | A felügyelettel kapcsolatos írási műveletet hajt végre |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | A felügyelettel kapcsolatos törlési műveletet hajt végre |
> | **NotDataActions (NotDataActions)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e4237640-0e3d-4a46-8fda-70bc94856432",
  "name": "e4237640-0e3d-4a46-8fda-70bc94856432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-reader"></a>Eszközfrissítések központi telepítésének olvasója

Olvasási hozzáférést biztosít a felügyeleti műveletekhez, de nem teszi lehetővé a [módosításokat További információ](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | A felügyelettel kapcsolatos olvasási műveletet hajt végre |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "name": "49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-reader"></a>Eszközfrissítés olvasója

Olvasási hozzáférést biztosít a felügyeleti és tartalomműveleti műveletekhez, de nem engedélyezi a [módosításokat További információ](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Frissítésekhez kapcsolódó olvasási műveletet hajt végre |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | A felügyelettel kapcsolatos olvasási műveletet hajt végre |
> | **NotDataActions (Nem adatactions)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management and content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "name": "e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Vegyes valóság


### <a name="remote-rendering-administrator"></a>Remote Rendering rendszergazda

Felhasználói konverziós, munkamenet-, renderelési és diagnosztikai képességeket biztosít a Azure Remote Rendering [További információ](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/action | Eszközkonverzió kezdete |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/read | Eszközkonverzió tulajdonságainak lekért értéke |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/delete | Eszközkonverzió leállítása |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Munkamenet tulajdonságainak lekért száma |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Munkamenetek kezdése |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Munkamenetek leállítása |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Csatlakozás munkamenethez |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Csatlakozás az Remote Rendering vizsgálóhoz |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="remote-rendering-client"></a>Remote Rendering-ügyfél

Munkamenet-, renderelési és diagnosztikai képességeket biztosít a felhasználók számára a Azure Remote Rendering. [További információ](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Munkamenet tulajdonságainak lekért száma |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Munkamenetek kezdése |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Munkamenetek leállítása |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Csatlakozás munkamenethez |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Csatlakozás a Remote Rendering vizsgálóhoz |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="spatial-anchors-account-contributor"></a>Spatial Anchors fiók közreműködője

Lehetővé teszi a fiók térbeli horgonyok kezelését, de nem törölheti őket [További információ](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Térbeli horgonyok létrehozása |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Közeli térbeli horgonyok felfedezése |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak lekért része |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Térbeli horgonyok keresése |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatok elküldése az Azure Spatial Anchors minőségének javítása érdekében |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Spatial Anchors-tulajdonságok frissítése |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="spatial-anchors-account-owner"></a>Spatial Anchors fióktulajdonos

Lehetővé teszi a fiók térbeli horgonyok kezelését, beleértve azok törlését [További információ](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Térbeli horgonyok létrehozása |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | Térbeli horgonyok törlése |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Közeli térbeli horgonyok felfedezése |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak lekérte |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Térbeli horgonyok keresése |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatok elküldése az Azure Spatial Anchors minőségének javítása érdekében |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Spatial Anchors-tulajdonságok frissítése |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="spatial-anchors-account-reader"></a>Spatial Anchors fiókolvasó használata

Lehetővé teszi a térbeli horgonyok tulajdonságainak megkeresét és olvasását a [fiókjában További információ](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Közeli térbeli horgonyok felfedezése |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak lekért része |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Térbeli horgonyok keresése |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatok elküldése az Azure Spatial Anchors minőségének javítása érdekében |
> | **NotDataActions (NotDataActions)** |  |
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


### <a name="api-management-service-contributor"></a>API Management szolgáltatás közreműködője

Kezelheti a szolgáltatást és az API-kat [További információ](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/* | API Management létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="api-management-service-operator-role"></a>API Management-kezelői szerepkör

Kezelheti a szolgáltatást, de az API-kat [nem. További információ](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Szolgáltatáspéldányok API Management olvasása |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/backup/action | Biztonsági API Management szolgáltatás biztonsági mentése a megadott tárolóba egy felhasználó által megadott tárfiókban |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/delete | Szolgáltatáspéldány API Management törlése |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/action | Termékváltozat/egységek módosítása, a szolgáltatás regionális üzemelő példányának hozzáadása/API Management eltávolítása |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Metaadatok olvasása egy API Management-szolgáltatáspéldányhoz |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/restore/action | A API Management szolgáltatás visszaállítása a megadott tárolóból egy felhasználó által megadott tárfiókban |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/action | TLS-/SSL-tanúsítvány feltöltése API Management szolgáltatáshoz |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/action | Egyéni tartománynevek beállítása, frissítése vagy eltávolítása API Management szolgáltatáshoz |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/write | Szolgáltatáspéldány létrehozása API Management frissítése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Felhasználóhoz társított kulcsok lekérte |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="api-management-service-reader-role"></a>API Management Szolgáltatásolvasó szerepkör

Csak olvasási hozzáférés a szolgáltatáshoz és az API-khoz [További információ](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Szolgáltatáspéldányok API Management olvasása |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Metaadatok olvasása egy API Management-szolgáltatáspéldányhoz |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Felhasználóhoz társított kulcsok lekérte |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="app-configuration-data-owner"></a>App Configuration adattulajdonos

Teljes hozzáférést biztosít a App Configuration adatokhoz. [További információ](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="app-configuration-data-reader"></a>App Configuration adatolvasó

Olvasási hozzáférést biztosít a App Configuration adatokhoz. [További információ](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | **NotDataActions (NotDataActions)** |  |
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

Teljes hozzáférést biztosít a Azure Service Bus erőforrásokhoz. [További információ](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus adat fogadója

Lehetővé teszi a hozzáférés fogadását Azure Service Bus erőforrásokhoz. [További információ](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/receive/action |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="azure-service-bus-data-sender"></a>Azure Service Bus adatküldő

Hozzáférést biztosít a Azure Service Bus erőforrásokhoz. [További információ](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/send/action |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="azure-stack-registration-owner"></a>Azure Stack Regisztráció tulajdonosa

Lehetővé teszi a Azure Stack kezelését.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/read | Egy előfizetés tulajdonságainak Azure Stack Edge le |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/*/action |  |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/read | Lekérte egy Azure Stack Marketplace-termék tulajdonságait |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/read | Lekérte egy Azure Stack tulajdonságait |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="eventgrid-contributor"></a>EventGrid-közreműködő

Lehetővé teszi az EventGrid-műveletek kezelését.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/* | Erőforrások létrehozása és Event Grid kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1e241071-0855-49ea-94dc-649edcd759de",
  "name": "1e241071-0855-49ea-94dc-649edcd759de",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/*",
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
  "roleName": "EventGrid Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription Közreműködő

Lehetővé teszi az EventGrid-esemény-előfizetési műveletek kezelését. [További információ](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* | Regionális esemény-előfizetések létrehozása és kezelése |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Globális esemény-előfizetések felsorolása témakörtípus szerint |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Regionális esemény-előfizetések felsorolása |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Regionális esemény-előfizetések felsorolása témakörtípus szerint |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription Reader

Lehetővé teszi az EventGrid-esemény-előfizetések olvasását. [További információ](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | EventSubscription olvasása |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Globális esemény-előfizetések felsorolása témakörtípus szerint |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Regionális esemény-előfizetések felsorolása |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Regionális esemény-előfizetések felsorolása témakörtípus szerint |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="fhir-data-contributor"></a>FHIR-adatok közreműködője

A szerepkör teljes hozzáférést biztosít a felhasználóknak vagy a rendszerbiztonsági tagnak az FHIR-adatokhoz [További információ](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="fhir-data-exporter"></a>FHIR-adatexport

Szerepkör, amely lehetővé teszi, hogy a felhasználó vagy a rendszerbiztonsági tag beolvassa és exportálja az FHIR-adatokat [További információ](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Olvassa el az FHIR-erőforrásokat (beleértve a keresési és verziószámos előzményeket).  |
> | Microsoft.HealthcareApis/services/fhir/resources/export/action | Exportálási művelet ($export). |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="fhir-data-reader"></a>FHIR-adatolvasó

A szerepkör lehetővé teszi, hogy a felhasználó vagy a rendszerbiztonsági tag beolvassa az FHIR-adatokat [További információ](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Olvassa el az FHIR-erőforrásokat (beleértve a keresési és verziószámos előzményeket).  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="fhir-data-writer"></a>FHIR-adatíró

A szerepkör lehetővé teszi, hogy a felhasználó vagy a rendszerbiztonsági tag FHIR-adatokat olvasson és [írjon. További információ](../healthcare-apis/fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | **NotDataActions (NotDataActions)** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action | Végleges törlés (beleértve a verzióelőzményeket is). |

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

### <a name="integration-service-environment-contributor"></a>integrációs szolgáltatási környezet Közreműködő

Lehetővé teszi az integrációs szolgáltatási környezetek kezelését, de a hozzájuk való hozzáférést nem. [További információ](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="integration-service-environment-developer"></a>integrációs szolgáltatási környezet Fejlesztő

Lehetővé teszi, hogy a fejlesztők munkafolyamatokat, integrációs fiókokat és API-kapcsolatokat hozzanak létre és frissítsenek az integrációs szolgáltatási környezetekben. [További információ](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/read | Beolvassa az integrációs szolgáltatási környezetet. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/*/join/action |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="intelligent-systems-account-contributor"></a>Intelligent Systems-fiók közreműködője

Lehetővé teszi az Intelligent Systems-fiókok kezelését, de a hozzájuk való hozzáférést nem.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | Microsoft.IntelligentSystems/accounts/* | Intelligens rendszerfiókok létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="logic-app-contributor"></a>Logikai alkalmazás közreműködője

Lehetővé teszi a logikai alkalmazások kezelését, de a hozzáférésük nem változik. [További információ](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Felsorolja a tárfiókok hozzáférési kulcsait. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Adja vissza a tárfiókot az adott fiókkal. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a diagnosztikai Analysis Server |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférniük a tevékenységnaplókhoz. Listásnapló-kategóriák a Tevékenységnaplóban. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Olvassa el a metrikadefiníciókat (az erőforráshoz elérhető metrikatípusok listája). |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/* | Felügyeli Logic Apps erőforrásokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsait adja vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Visszaadja a tárfiókok listáját, vagy lekérte a megadott tárfiók tulajdonságait. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Kapcsolatátjáró létrehozása és kezelése. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/* | Kapcsolatot hoz létre és kezel. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/* | Egyéni API-t hoz létre és kezel. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Csatlakozik egy App Service csomaghoz |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Egy terv tulajdonságainak App Service le |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/functions/listSecrets/action | List függvény titkos kulcsok. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="logic-app-operator"></a>Logikaialkalmazás-operátor

Lehetővé teszi a logikai alkalmazások olvasását, engedélyezését és letiltását, de nem szerkesztheti vagy frissítheti őket. [További információ](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/read | Elemzések riasztási szabályainak olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/read | Lekérte a diagnosztikai beállításokat a Logic Apps |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/read | Lekérte az elérhető metrikákat a Logic Apps. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/*/read | Beolvassa Logic Apps erőforrásokat. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/disable/action | Letiltja a munkafolyamatot. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/enable/action | Engedélyezi a munkafolyamatot. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/validate/action | Ellenőrzi a munkafolyamatot. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Lekért vagy listázza az üzembe helyezési műveleteket. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/read | Olvassa el a Connection Gateways (Kapcsolatátjárók) olvasását. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/*/read | Kapcsolatok olvasása. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/*/read | Olvassa el az Egyéni API-t. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Egy terv tulajdonságainak App Service le |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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


### <a name="managed-identity-contributor"></a>Felügyelt identitás közreműködője

Felhasználó által hozzárendelt identitás létrehozása, olvasása, frissítése és [törlése További információ](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/read | Lekért egy meglévő felhasználóhoz hozzárendelt identitást |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | Létrehoz egy új felhasználóhoz hozzárendelt identitást, vagy frissíti a meglévő felhasználóhoz hozzárendelt identitáshoz társított címkéket |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | Töröl egy meglévő felhasználóhoz hozzárendelt identitást |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="managed-identity-operator"></a>Felügyelt identitáskezelő

További információ: Felhasználóhoz rendelt identitás [hozzárendelése](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/read |  |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/action |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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


### <a name="attestation-contributor"></a>Igazolási közreműködő

Olvashatja az igazolásszolgáltatói példányt, vagy törölheti [azt. További információ](../attestation/quickstart-powershell.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | Microsoft.Attestation/attestationProviders/attestation/write |  |
> | Microsoft.Attestation/attestationProviders/attestation/delete |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read write or delete the attestation provider instance",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "name": "bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read",
        "Microsoft.Attestation/attestationProviders/attestation/write",
        "Microsoft.Attestation/attestationProviders/attestation/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="attestation-reader"></a>Igazolásolvasó

Olvassa el az igazolási szolgáltató tulajdonságait [További információ](../attestation/troubleshoot-guide.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read the attestation provider properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "name": "fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-automation-contributor"></a>Azure Sentinel Automation-közreműködő

Azure Sentinel Automation-közreműködő [– További információ](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/read | Beolvassa az eseményindítót. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/listCallbackUrl/action | Lehívja az eseményindító visszahívási URL-címét. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/runs/read | Beolvassa a munkafolyamat futtatását. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Automation Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "name": "f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Logic/workflows/triggers/read",
        "Microsoft.Logic/workflows/triggers/listCallbackUrl/action",
        "Microsoft.Logic/workflows/runs/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Automation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-contributor"></a>Azure Sentinel Contributor

Azure Sentinel Közreműködő [– További információ](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Keresés az új motorral. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Naplóelemzési adatok megtekintése |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Kilépés az OMS-megoldásból |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Lekérdezések futtatása a munkaterületen lévő adatokon |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Adatforrások lekértése egy munkaterület alatt. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Privát munkafüzet olvasása |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

Azure Sentinel Olvasó [További információ](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Felhasználói engedélyezés és licenc ellenőrzése |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Fenyegetésintelligencia-jelzők lekérdezése |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Fenyegetésintelligencia-jelzők lekérdezése |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Keresés az új motorral. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Naplóelemzési adatok megtekintése |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/read | Lekérte a kapcsolódó szolgáltatásokat az adott munkaterület alatt. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Lekérdez egy mentett keresési lekérdezést |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Kilépés az OMS-megoldásból |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Lekérdezések futtatása a munkaterületen lévő adatokon |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Egy munkaterület alatt lekért adatforrások. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Munkafüzet olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Privát munkafüzet olvasása |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

Azure Sentinel Válaszadó [– További információ](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Felhasználói engedélyezés és licenc ellenőrzése |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/automationRules/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Címkék hozzáfűzése a Fenyegetésintelligencia-jelzőhez |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Fenyegetésintelligencia-jelzők lekérdezése |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/action | Tömeges címkék fenyegetésintelligencia |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Címkék hozzáfűzése a Fenyegetésintelligencia-jelzőhez |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/replaceTags/action | A Fenyegetésintelligencia-jelző címkéinek cseréje |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Fenyegetésintelligencia-jelzők lekérdezése |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Keresés az új motorral. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Naplóelemzési adatok megtekintése |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Adatforrások lekértése egy munkaterület alatt. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Mentett keresési lekérdezést kap |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Kilépés az OMS-megoldásból |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Lekérdezések futtatása a munkaterületen lévő adatokon |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Egy munkaterület alatt lekért adatforrások. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Munkafüzet olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Privát munkafüzet olvasása |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/*/Delete |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/*/Delete |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="key-vault-administrator"></a>Key Vault rendszergazda

Minden adatsíkműveletet végrehajt egy kulcstartón és a benne lévő összes objektumon, beleértve a tanúsítványokat, kulcsokat és titkos kulcsokat. A Key Vault-erőforrások és a szerepkör-hozzárendelések nem kezelhetők. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Ellenőrzi, hogy a kulcstartó neve érvényes-e, és nincs-e használatban |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | A törölt kulcstartók tulajdonságainak megtekintése |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Felsorolja a Microsoft.KeyVault erőforrás-szolgáltatón elérhető műveleteket |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/* |  |
> | **NotDataActions (Nem adatactions)** |  |
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
  "roleName": "Key Vault Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer"></a>Key Vault tanúsítvány-felelős

Az engedélyek kezelése kivételével minden műveletet végrehajt a kulcstartó tanúsítványán. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Ellenőrzi, hogy a kulcstartó neve érvényes-e, és nincs-e használatban |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | A törölt kulcstartók tulajdonságainak megtekintése |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Felsorolja a Microsoft.KeyVault erőforrás-szolgáltatón elérhető műveleteket |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificatecas/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificates/* |  |
> | **NotDataActions (Nem adatactions)** |  |
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
  "roleName": "Key Vault Certificates Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Key Vault Közreműködő

Kulcstartók kezelése, de nem teszi lehetővé szerepkörök hozzárendelését az Azure RBAC-ban, és nem teszi lehetővé a titkos kulcsok, kulcsok és tanúsítványok hozzáférését. [További információ](../key-vault/general/secure-your-key-vault.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purge/action | Törölt kulcstartó végleges törlése |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHsms/* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="key-vault-crypto-officer"></a>Key Vault kriptográfiai igazgató

Az engedélyek kezelése kivételével bármilyen műveletet végrehajt a kulcstartó kulcsán. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Ellenőrzi, hogy a kulcstartó neve érvényes-e, és nincs-e használatban |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | A törölt kulcstartók tulajdonságainak megtekintése |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Felsorolja a Microsoft.KeyVault erőforrás-szolgáltatón elérhető műveleteket |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/* |  |
> | **NotDataActions (Nem adatactions)** |  |
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
  "roleName": "Key Vault Crypto Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-user"></a>Key Vault titkosítási szolgáltatás titkosítási felhasználója

Beolvassa a kulcsok metaadatait, és végrehajtja a wrap/unwrap műveleteket. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | EventSubscription létrehozása vagy frissítése |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | EventSubscription olvasása |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/delete | EventSubscription törlése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | A megadott tároló kulcsait listába sorolja, vagy beolvassa egy kulcs tulajdonságait és nyilvános anyagát. Az aszimmetrikus kulcsokhoz ez a művelet nyilvános kulcsot ad meg, és lehetővé teszi olyan nyilvánoskulcs-algoritmusok elvégzését, mint az aláírás titkosítása és ellenőrzése. A titkos kulcsok és a szimmetrikus kulcsok soha nem vannak elérhetővé téve. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Egy szimmetrikus kulcsot burköl egy Key Vault kulccsal. Vegye figyelembe, hogy Key Vault kulcs aszimmetrikus, ezt a műveletet olvasási hozzáféréssel is el lehet végezni. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Szimmetrikus kulcs kiírása egy Key Vault kulccsal. |
> | **NotDataActions (Nem adataktívok)** |  |
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
  "roleName": "Key Vault Crypto Service Encryption User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user"></a>Key Vault kriptográfiai felhasználó

Titkosítási műveletek végrehajtása kulcsokkal. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | Listsa a megadott tároló kulcsait, vagy beolvassa egy kulcs tulajdonságait és nyilvános anyagát. Az aszimmetrikus kulcsokhoz ez a művelet nyilvános kulcsot ad meg, és lehetővé teszi olyan nyilvánoskulcs-algoritmusok elvégzését, mint az aláírás titkosítása és ellenőrzése. A titkos kulcsok és a szimmetrikus kulcsok soha nem vannak elérhetővé téve. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/update/action | Frissíti az adott kulcshoz társított megadott attribútumokat. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/backup/action | Létrehozza egy kulcs biztonságimásolat-fájlját. A fájl segítségével visszaállíthatja a kulcsot egy Key Vault azonos előfizetéshez. Korlátozások lehetnek érvényben. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/encrypt/action | Titkosítja az egyszerű szöveget egy kulccsal. Vegye figyelembe, hogy ha a kulcs aszimmetrikus, akkor ezt a műveletet olvasási hozzáférésű rendszerbiztonsági tagokkal is el lehet végezni. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/decrypt/action | Visszafejti a titkosított szöveget egy kulccsal. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Egy szimmetrikus kulcsot burköl egy Key Vault kulccsal. Vegye figyelembe, hogy Key Vault kulcs aszimmetrikus, akkor ezt a műveletet olvasási hozzáféréssel is el lehet végezni. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Egy szimmetrikus kulcs kiírása egy Key Vault kulccsal. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/sign/action | Aláír egy üzenetkivonatot (kivonatot) egy kulccsal. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/verify/action | Ellenőrzi egy üzenetkivonat (kivonat) aláírását egy kulccsal. Vegye figyelembe, hogy ha a kulcs aszimmetrikus, akkor ezt a műveletet olvasási hozzáférésű rendszerbiztonsági tagokkal is el lehet végezni. |
> | **NotDataActions (NotDataActions)** |  |
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
  "roleName": "Key Vault Crypto User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader"></a>Key Vault Olvasó

Beolvassa a kulcstartók, tanúsítványok, kulcsok és titkos kulcsok metaadatait. Nem olvashatók bizalmas értékek, például titkos kulcsok vagy kulcsanyagok. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Ellenőrzi, hogy a kulcstartó neve érvényes-e, és nincs-e használatban |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | A törölt kulcstartók tulajdonságainak megtekintése |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Felsorolja a Microsoft.KeyVault erőforrás-szolgáltatón elérhető műveleteket |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | Felsorolja vagy megtekinti egy titkos titkos fájl tulajdonságait, de annak értékét nem. |
> | **NotDataActions (NotDataActions)** |  |
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
  "roleName": "Key Vault Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer"></a>Key Vault titkos kulcsokért felelős igazgató

Az engedélyek kezelése kivételével bármilyen műveletet végrehajt a kulcstartó titkos kulcsaion. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Ellenőrzi, hogy a kulcstartó neve érvényes-e, és nincs-e használatban |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | A törölt kulcstartók tulajdonságainak megtekintése |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Felsorolja a Microsoft.KeyVault erőforrás-szolgáltatón elérhető műveleteket |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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
  "roleName": "Key Vault Secrets Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user"></a>Key Vault titkos kulcsok felhasználója

Titkos fájlok tartalmának olvasása. Csak olyan kulcstartók esetében működik, amelyek az "Azure szerepköralapú hozzáférés-vezérlés" engedélymodellt használják.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/getSecret/action | Lekérte egy titkos titkos érték értékét. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | Felsorolja vagy megtekinti egy titkos titkos fájl tulajdonságait, de annak értékét nem. |
> | **NotDataActions (Nem adataktívok)** |  |
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
  "roleName": "Key Vault Secrets User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-hsm-contributor"></a>Managed HSM-közreműködő

Lehetővé teszi a felügyelt HSM-készletek kezelését, de nem férhet hozzájuk. [További információ](../key-vault/managed-hsm/secure-your-managed-hsm.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHSMs/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage managed HSM pools, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18500a29-7fe2-46b2-a342-b16a415e101d",
  "name": "18500a29-7fe2-46b2-a342-b16a415e101d",
  "permissions": [
    {
      "actions": [
        "Microsoft.KeyVault/managedHSMs/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed HSM contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Biztonsági rendszergazda

Megtekintheti és frissítheti a Security Center. Ugyanazok az engedélyek, mint a Biztonsági olvasó szerepkör, és frissítheti a biztonsági szabályzatot, valamint elvetheti a riasztásokat és javaslatokat. [További információ](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Szabályzat-hozzárendelések létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | Szabályzatdefiníciók létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* | Szabályzati kivételek létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Szabályzatkészletek létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listába sorolja a hitelesített felhasználó felügyeleti csoportjait. |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Naplóelemzési adatok megtekintése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Biztonsági összetevők és szabályzatok létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="security-assessment-contributor"></a>Biztonsági értékelés közreműködője

Lehetővé teszi az értékelések leküldését Security Center

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/assessments/write | Biztonsági értékelések létrehozása vagy frissítése az előfizetésben |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

Ez egy örökölt szerepkör. Ehelyett használja a Biztonsági rendszergazda szerepkört.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/read | Konfigurációs adatok olvasása klasszikus virtuális gépeken |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/write | Írási konfiguráció klasszikus virtuális gépekhez |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/read | Konfigurációs információk olvasása a klasszikus hálózatról |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Biztonsági összetevők és szabályzatok létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

View permissions for Security Center. Megtekintheti a javaslatokat, riasztásokat, biztonsági szabályzatokat és biztonsági államokat, de nem tud módosításokat tenni. [További információ](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Klasszikus metrikariasztás olvasása |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Naplóelemzési adatok megtekintése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/*/read | Biztonsági összetevők és szabályzatok olvasása |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/*/read |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/packageDownloads/action | Lekért letölthető IoT Defender-csomagok információit |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/downloadManagerActivation/action | Felettes aktiválási fájl letöltése előfizetési kvótaadatokkal |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotSensors/downloadResetPassword/action | Letölti az IoT-érzékelők jelszó-visszaállítási fájlját |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listába sorolja a hitelesített felhasználó felügyeleti csoportjait. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

Lehetővé teszi a virtuális gépek csatlakoztatását, újraindítását és leállítását a Azure DevTest Labs. [További információ](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/read | Rendelkezésre állási készlet tulajdonságainak lekért része |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read | Virtuális gép tulajdonságainak olvasása (virtuálisgép-méretek, futásidő állapota, virtuálisgép-bővítmények stb.) |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/action | Kikapcsolja a virtuális gépet, és felszabadítja a számítási erőforrásokat |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Virtuális gép tulajdonságainak lekért része |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/action | Újraindítja a virtuális gépet |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/start/action | Elindítja a virtuális gépet |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/read | Tesztkörnyezet tulajdonságainak olvasása |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/claimAnyVm/action | Igényel egy véletlenszerűen igényelhető virtuális gépet a tesztkörnyezetben. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/createEnvironment/action | Virtuális gépek létrehozása tesztkörnyezetben. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/ensureCurrentUserProfile/action | Győződjön meg arról, hogy az aktuális felhasználó érvényes profillal rendelkezik a tesztkörnyezetben. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/delete | Képletek törlése. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/read | Képletek olvasása. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/write | Képletek hozzáadása vagy módosítása. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/policySets/evaluatePolicies/action | Tesztkörnyezeti szabályzatot értékel ki. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/claim/action | Meglévő virtuális gép saját tulajdonba vennie |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualmachines/listApplicableSchedules/action | Felsorolja a vonatkozó indítási/leállítási ütemezéseket, ha vannak. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/getRdpFileContents/action | Lekért egy sztringet, amely a virtuális gép RDP-fájljában található |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Csatlakozik egy terheléselosztási háttércímkészlethez. Nem riasztási riasztás. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Egy terheléselosztás bejövő NAT-szabályát illeszti össze. Nem riasztási riasztás. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/read | Egy hálózati adapter tulajdonságainak olvasása (például az összes olyan terheléselosztás, amely része a hálózati adapternek) |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/action | Egy virtuális gépet csatlakozik egy hálózati adapterhez. Nem riasztási riasztás. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Lekért egy hálózati adapter definícióját.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Létrehoz egy hálózati adaptert, vagy frissíti a meglévő hálózati adaptert.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/read | Nyilvános IP-cím tulajdonságainak olvasása |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Csatlakozik egy nyilvános IP-címhez. Nem riasztást küldhető. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Lekért egy nyilvános IP-cím definícióját. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Csatlakozik egy virtuális hálózathoz. Nem riasztást küldhető. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Lekért vagy listázza az üzembe helyezési műveleteket. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Lekért vagy listázza az üzemelő példányokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsait adja vissza. |
> | **NotActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/read | Felsorolja az elérhető méreteket, amelyekre a virtuális gép frissíthető |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="lab-creator"></a>Laborkészítő

Lehetővé teszi új tesztkörnyezetek létrehozására az Azure Lab-fiókok alatt. [További információ](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/read |  |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/action | Tesztkörnyezet létrehozása tesztkörnyezetfiókban. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/action | A tesztkörnyezetfiók méreteinek, földrajzi helyeinek és operációs rendszereinek kombinációi díjszabását és rendelkezésre állását biztosítjuk. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/action | Az előfizetés alapvető korlátozásai és használata |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

Kezelheti a Application Insights összetevőket [További információ](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus riasztási szabályok létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/generateLiveToken/read | Élő metrikák – jogkivonat lekért |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Új riasztási szabályok létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights-összetevők létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/topológia/read | Read Topology |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/transactions/read | Tranzakciók olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Insights-webes tesztek létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

Engedélyt ad a felhasználónak az alkalmazással gyűjtött hibakeresési pillanatképek megtekintésére és Application Insights Snapshot Debugger. Vegye figyelembe, hogy ezek az engedélyek nem szerepelnek a [Tulajdonos vagy](#owner) a Közreműködő [szerepkörben.](#contributor) Amikor a felhasználóknak Application Insights Snapshot Debugger szerepkört, a szerepkört közvetlenül a felhasználónak kell adni. A rendszer nem ismeri fel a szerepkört, amikor hozzáadja egy egyéni szerepkörhöz. [További információ](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="monitoring-contributor"></a>Figyelési közreműködő

Olvashatja az összes monitorozási adatot, és szerkesztheti a figyelési beállításokat. Lásd [még: Get started with roles, permissions, and security with Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). [További információ](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */olvasás | A titkos kulcsok kivételével minden típusú erőforrást beolvas. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alerts/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Insights-összetevők létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a diagnosztikai Analysis Server |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | Listás tevékenységnapló-események (felügyeleti események) egy előfizetésben. Ez az engedély a programozott és a portálon a tevékenységnaplóhoz való hozzáférésre is vonatkozik. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférniük a tevékenységnaplókhoz. Listnapló-kategóriák a Tevékenységnaplóban. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Olvassa el a metrikadefiníciókat (az erőforráshoz elérhető metrikatípusok listája). |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Egy erőforrás metrikák olvasása. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Insights webes tesztek létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/write | Új munkaterületet hoz létre, vagy egy meglévő munkaterületre mutató hivatkozásokat hoz létre a meglévő munkaterületen található ügyfél-azonosító megszava. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | Log Analytics-megoldáscsomagok olvasása/írása/törlése. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | Naplóelemzés mentett keresések olvasása/írása/törlése. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Keresési lekérdezést hajt végre |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | Lekéri a munkaterület megosztott kulcsait. Ezekkel a kulcsokkal csatlakoztathatja a Microsoft Operational Insights-ügynököket a munkaterülethez. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | Log Analytics Storage-elemzések olvasása/írása/törlése – konfigurációk. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* | Információk lekért a vendég virtuális gépek állapotfigyelőiről. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="monitoring-metrics-publisher"></a>Monitorozási metrikák közzétevője

Lehetővé teszi a metrikák Azure-erőforrásokon való [közzétételét További információ](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Metrikák írása |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="monitoring-reader"></a>Figyelési olvasó

Olvashatja az összes monitorozási adatot (metrikákat, naplókat stb.). Lásd [még: A szerepkörök, engedélyek](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles)és biztonság első lépések a Azure Monitor. [További információ](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */olvasás | A titkos kulcsok kivételével minden típusú erőforrást beolvas. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Keresési lekérdezést hajt végre |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

Megosztott munkafüzeteket menthet. [További információ](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/write | Munkafüzet létrehozása vagy frissítése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/delete | Munkafüzet törlése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Munkafüzet olvasása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="workbook-reader"></a>Munkafüzet olvasója

Olvashatja a munkafüzeteket. [További információ](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [microsoft.insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Munkafüzet olvasása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

## <a name="management--governance"></a>Felügyelet + irányítás


### <a name="automation-job-operator"></a>Automation-feladat operátora

Feladatok létrehozása és kezelése Automation-runbookok használatával. [További információ](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Beolvassa a hibrid runbook-feldolgozó erőforrásait |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Lekért egy Azure Automation feladatot |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Folytatja a Azure Automation feladatot |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Leállít egy Azure Automation feladatot |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Lekért egy Azure Automation feladatstreamet |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Felfüggeszt egy Azure Automation feladatot |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Létrehoz egy Azure Automation feladatot |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Lekérte egy feladat kimenetét |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

Az Automation-operátorok képesek elindítani, leállítani, felfüggeszteni és folytatni a feladatokat [További információ](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Beolvassa a hibrid runbook-feldolgozó erőforrásait |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Lekért egy Azure Automation feladatot |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Folytat egy Azure Automation feladatot |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Leállít egy Azure Automation feladatot |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Lekért egy Azure Automation feladatstreamet |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Felfüggeszt egy Azure Automation feladatot |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Létrehoz egy Azure Automation feladatot |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/read | Lekért egy Azure Automation feladat ütemezését |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/write | Létrehoz egy Azure Automation feladat ütemezését |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/read | Lekérte az Automation-fiókhoz csatolt munkaterületet |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/read | Lekért egy Azure Automation fiókot |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Lekért egy Azure Automation runbookot |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/read | Lekért egy Azure Automation ütemezési eszközt |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/write | Létrehoz vagy frissíti a Azure Automation-eszközt |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Lekérte egy feladat kimenetét |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="automation-runbook-operator"></a>Automation Runbook-operátor

Runbook tulajdonságainak olvasása – a runbook feladatának létrehozásához. [További információ](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Lekért egy Azure Automation runbookot |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="azure-arc-enabled-kubernetes-cluster-user-role"></a>Azure Arc Kubernetes-fürt felhasználói szerepkörének engedélyezése

List cluster user credentials action.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Központi telepítést hoz létre vagy frissíti. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/listClusterUserCredentials/action | Fürt listásaA felhasználó hitelesítő adatai |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credentials action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "name": "00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/listClusterUserCredentials/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Enabled Kubernetes Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-admin"></a>Azure Arc Kubernetes-rendszergazda

Lehetővé teszi az összes erőforrás kezelését a fürt/névtér alatt, kivéve az erőforráskvóták és -névterek frissítését vagy törlését. [További információ](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Központi telepítést hoz létre vagy frissíti. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Olvasási vezérlőkrevisions |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write | A localsubjectaccessreviews írása |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Események olvasása |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Események olvasása |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Olvasási korlátok |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Olvasások névterei |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/rolebindings/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/roles/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Olvasások resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions (Nem adatactions)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "name": "dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
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
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-cluster-admin"></a>Azure Arc Kubernetes-fürt rendszergazdája

Lehetővé teszi a fürt összes erőforrásának kezelését. [További információ](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Központi telepítést hoz létre vagy frissíti. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "name": "8393591c-06b9-48a2-a542-1bd6b377f6a2",
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
        "Microsoft.Kubernetes/connectedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-viewer"></a>Azure Arc Kubernetes Viewer

Lehetővé teszi a fürt/névtér összes erőforrásának megtekintését a titkos kulcsok kivételével. [További információ](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Központi telepítést hoz létre vagy frissíti. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Olvasási vezérlőkrevisions |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/read | Olvasási démonok |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/read | Olvasások üzemelő példányai |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/read | Beolvassa a replikákat |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/read | Beolvassa az állapot-készleteket |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/read | Olvasások horizontálispodautoscalers |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/read | Olvasások cronjobs |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/read | Beolvassa a feladatokat |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/read | Beolvassa a konfigurációtérképeket |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/read | Beolvassa a végpontokat |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Események olvasása |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Események olvasása |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/read | Olvasási démonkészletek |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/read | Beolvassa az üzemelő példányokat |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/read | Beolvasott bejövő forgalom |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/read | Beolvassa a networkpolicies -t |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/read | Beolvassa a replikákat |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Olvasási korlátok |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Beolvassa a névtereket |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/read | Beolvasott bejövő forgalom |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/read | Beolvassa a networkpolicies -t |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/read | Olvasások persistentvolumeclaims |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/read | Olvasás podok |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/read | Olvasások poddisruptionbudgets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | Beolvassa a replicationcontrollers vezérlőket |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | Beolvassa a replicationcontrollers vezérlőket |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Olvasások resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/read | Reads serviceaccounts |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/read | Olvasási szolgáltatások |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view all resources in cluster/namespace, except secrets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/63f0a09d-1495-4db4-a681-037d84835eb4",
  "name": "63f0a09d-1495-4db4-a681-037d84835eb4",
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
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/read",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/read",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/read",
        "Microsoft.Kubernetes/connectedClusters/configmaps/read",
        "Microsoft.Kubernetes/connectedClusters/endpoints/read",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/read",
        "Microsoft.Kubernetes/connectedClusters/pods/read",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/read",
        "Microsoft.Kubernetes/connectedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Viewer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-writer"></a>Azure Arc Kubernetes-író

Lehetővé teszi a fürt/névtér teljes halmazának frissítését a (fürt)szerepkörök és a (fürt)szerepkörkötések kivételével. [További információ](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Központi telepítést hoz létre vagy frissíti. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Olvasási vezérlőkrevisions |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Események olvasása |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Események olvasása |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Olvasási korlátok |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Olvasások névterei |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Beolvassa a resourcequotas erőforrást |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you update everything in cluster/namespace, except (cluster)roles and (cluster)role bindings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5b999177-9696-4545-85c7-50de3797e5a1",
  "name": "5b999177-9696-4545-85c7-50de3797e5a1",
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
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Azure Connected Machine- és

Azure-beli csatlakoztatott gépeket is fel lehetni. [További információ](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Az esetlegesen Azure Arc gépek olvasása |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Gépeket Azure Arc ír |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/read | A privateLinkScopes Azure Arc olvasása |
> | [Microsoft.GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/read | Vendégkonfiguráció-hozzárendelés lekérte. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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
        "Microsoft.HybridCompute/privateLinkScopes/read",
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

### <a name="azure-connected-machine-resource-administrator"></a>Azure Connected Machine erőforrás-rendszergazda

Képes olvasni, írni, törölni és újra bevetni az Azure-beli csatlakoztatott gépeket.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | A virtuális gépek Azure Arc olvasása |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Gépeket Azure Arc ír |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/delete | Töröl egy Azure Arc gépet |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Telepíti vagy frissíti a Azure Arc bővítményeit |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/privateLinkScopes/*",
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

Olvasási hozzáférés a számlázási adatokhoz [További információ](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/*/read | Számlázási adatok olvasása |
> | [Microsoft.Commerce](resource-provider-operations.md#microsoftcommerce)/*/read |  |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listába sorolja a hitelesített felhasználó felügyeleti csoportjait. |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="blueprint-contributor"></a>Terv közreműködője

Kezelheti a tervdefiníciókat, de nem rendelhet hozzájuk. [További információ](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprints/* | Tervdefiníciók vagy terv-összetevők létrehozása és kezelése. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="blueprint-operator"></a>Blueprint Operator

Hozzárendelhet meglévő közzétett terveket, de nem hozhat létre új terveket. Vegye figyelembe, hogy ez csak akkor működik, ha a hozzárendelés felhasználó által hozzárendelt felügyelt identitással történik. [További információ](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Terv-hozzárendelések létrehozása és kezelése. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="cost-management-contributor"></a>Cost Management-közreműködő

Megtekintheti a költségeket és kezelheti a költségkonfigurációt (például költségvetéseket, exportálásokat) [További információ](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Konfigurációk lekérte |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Olvasási javaslatok |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listába sorolja a hitelesített felhasználó felügyeleti csoportjait. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="cost-management-reader"></a>Cost Management Olvasó

Megtekintheti a költségadatokat és a konfigurációt (például költségvetéseket, exportálásokat) [További információ](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Konfigurációk lekérte |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Olvasási javaslatok |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listába sorolja a hitelesített felhasználó felügyeleti csoportjait. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="hierarchy-settings-administrator"></a>Hierarchiabeállítások rendszergazdája

Lehetővé teszi a felhasználók számára a hierarchiabeállítások szerkesztését és törlését

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/write | Létrehozza vagy frissíti a felügyeleti csoport hierarchiabeállítását. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/delete | Törli a felügyeleti csoport hierarchiabeállítását. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Kubernetes-fürt – Azure Arc-

Szerepkör-definíció bármely felhasználó vagy szolgáltatás számára a connectedClusters erőforrás létrehozására való jogosultsághoz [További információ](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Központi telepítést hoz létre vagy frissíti. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Szerezze be az előfizetési művelet eredményeit. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | Csatlakoztatott fürtök írása |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/read | Csatlakoztatott fürtök olvasása |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="managed-application-contributor-role"></a>Felügyelt alkalmazások közreműködője szerepkör

Lehetővé teszi a felügyelt alkalmazások erőforrásainak létrehozását.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */olvasás | A titkos kulcsok kivételével minden típusú erőforrást beolvas. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/* |  |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/register/action | Regisztráljon a Megoldásokra. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="managed-application-operator-role"></a>Felügyeltalkalmazás-kezelői szerepkör

Lehetővé teszi a felügyelt alkalmazások erőforrásainak olvasását és a műveletek elvégzését

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */olvasás | A titkos kulcsok kivételével minden típusú erőforrást beolvas. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/read | Lekéri az alkalmazások listáját. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/*/action |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

Lehetővé teszi egy felügyelt alkalmazás erőforrásainak olvasását és JIT-hozzáférés kérését.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */olvasás | A titkos kulcsok kivételével minden típusú erőforrást beolvas. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="managed-services-registration-assignment-delete-role"></a>Felügyelt szolgáltatások regisztráció-hozzárendelése – Szerepkör törlése

A felügyelt szolgáltatások regisztráció-hozzárendelésének törlési szerepköre lehetővé teszi a bérlő felhasználói számára a bérlőjükhöz rendelt regisztrációs hozzárendelés törlését. [További információ](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/read | Lekéri a Felügyelt szolgáltatások regisztráció-hozzárendelések listáját. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | Eltávolítja a Felügyelt szolgáltatások regisztráció-hozzárendelését. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/read | Beolvassa az erőforrás műveleti állapotát. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

Felügyeleti csoport közreműködője szerepkör [További információ](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/delete | Felügyeleti csoport törlése. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listába sorolja a hitelesített felhasználó felügyeleti csoportjait. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/delete | Előfizetés társításának lemondása a felügyeleti csoportból. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/write | Társítja a meglévő előfizetést a felügyeleti csoporthoz. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/write | Felügyeleti csoport létrehozása vagy frissítése. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Felsorolja az előfizetést az adott felügyeleti csoport alatt. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

Felügyeleti csoport olvasói szerepköre

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Listába sorolja a hitelesített felhasználó felügyeleti csoportjait. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Felsorolja az előfizetést az adott felügyeleti csoport alatt. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="new-relic-apm-account-contributor"></a>New Relic APM-fiók közreműködője

Lehetővé teszi a New Relic Application Performance Management fiókok és alkalmazások kezelését, de a hozzáférésük nem.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="policy-insights-data-writer-preview"></a>Policy Insights-adatíró (előzetes verzió)

Olvasási hozzáférést biztosít az erőforrás-szabályzatok és az írási hozzáférés számára az erőforrás-összetevők házirendeseményei számára. [További információ](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/read | Információ lekért egy szabályzat-hozzárendelésről. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/read | Információ lekért egy szabályzatdefinícióról. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/read | Információ lekért egy szabályzat kivételről. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/read | Információ lekért egy szabályzatkészlet-definícióról. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/action | Ellenőrizze egy adott összetevő megfelelőségi állapotát az adat házirendek alapján. |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/action | Naplóz az erőforrás-összetevő házirendeseményeit. |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="quota-request-operator"></a>Kvótakérési operátor

Olvassa el és hozza létre a kvótakéréseket, kérje le a kvótakérés állapotát, és hozzon létre támogatási jegyeket. [További információ](/rest/api/reserved-vm-instances/quotaapi)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/read | A megadott erőforrás és hely aktuális szolgáltatási korlátja vagy kvótája |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/write | Szolgáltatási korlát vagy kvóta létrehozása a megadott erőforráshoz és helyhez |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimitsRequests/read | A megadott erőforrásra és helyre vonatkozó szolgáltatáskorlát-kérelmek lekérése |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | Regisztrálja a kapacitás-erőforrás-szolgáltatót, és lehetővé teszi kapacitás-erőforrások létrehozását. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and create quota requests, get quota request status, and create support tickets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "name": "0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "permissions": [
    {
      "actions": [
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/read",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/write",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimitsRequests/read",
        "Microsoft.Capacity/register/action",
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
  "roleName": "Quota Request Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reservation-purchaser"></a>Foglalás vásárlója

Lehetővé teszi foglalások vásárlását [További információ](../cost-management-billing/reservations/prepare-buy-reservation.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Lekérte az előfizetések listáját. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | Regisztrálja a kapacitás erőforrás-szolgáltatót, és lehetővé teszi kapacitás-erőforrások létrehozását. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/register/action | Regisztrálja az előfizetést a Microsoft.Compute erőforrás-szolgáltatónál |
> | [Microsoft.SQL](resource-provider-operations.md#microsoftsql)/register/action | Regisztrálja az előfizetést a Microsoft SQL Database erőforrás-szolgáltatóhoz, és lehetővé teszi a Microsoft SQL Database-adatbázisok létrehozását. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/register/action | Regisztráció a használatra szánt RP-re |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/catalogs/read | Foglalási katalógus olvasása |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Információ lekért egy szerepkör-hozzárendelésről. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/reservationRecommendations/read | List single or shared recommendations for Reserved instances for a subscription.list single or shared recommendations for reserved instances for a subscription. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/supporttickets/write | Támogatási jegy létrehozásának és frissítésének lehetővé teszi |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you purchase reservations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "name": "f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Compute/register/action",
        "Microsoft.SQL/register/action",
        "Microsoft.Consumption/register/action",
        "Microsoft.Capacity/catalogs/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Consumption/reservationRecommendations/read",
        "Microsoft.Support/supporttickets/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reservation Purchaser",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Erőforrás-szabályzat közreműködője

Az erőforrás-szabályzatok létrehozására/módosítására, támogatási jegy létrehozására és erőforrások/hierarchiák olvasására vonatkozó jogosultsággal rendelkezik felhasználók. [További információ](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | */olvasás | A titkos kulcsok kivételével minden típusú erőforrást beolvas. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Szabályzat-hozzárendelések létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | Szabályzatdefiníciók létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* | Szabályzati kivételek létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Szabályzatkészletek létrehozása és kezelése |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

Lehetővé teszi a Site Recovery szolgáltatás kezelését a tároló létrehozása és a szerepkör-hozzárendelés kivételével [További információ](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | A virtuális hálózat definíciójának lekért része |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | A GetAllocatedStamp a szolgáltatás által használt belső művelet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | Az AllocateStamp a szolgáltatás által használt belső művelet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | Az Erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás-/tároló hitelesítőadat-tanúsítványát. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Tárolóval kapcsolatos bővített információk létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A Get Vault művelet lekért egy objektumot, amely a "vault" típusú Azure-erőforrást képviseli |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Regisztrált identitások létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | Replikációs riasztási beállítások létrehozása vagy frissítése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Események olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | Replikációs hálók létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Replikációs feladatok létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | Replikációs szabályzatok létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | Helyreállítási tervek létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Recovery Services-tároló tárolókonfigurációjának létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Helyreállítási tár használati adatait adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | A Vault Token művelettel lekért tároló-jogkivonat tárolószintű háttérműveletekkel. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | A Recovery Services-tárolóra vonatkozó riasztások olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Visszaadja a tárfiókok listáját, vagy lekérte a megadott tárfiók tulajdonságait. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationOperationStatus/read | Tárolóreplikációs művelet állapotának olvasása |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem hajt végre Site Recovery felügyeleti [műveleteket. További információ](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | A virtuális hálózat definíciójának lekért része |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | A GetAllocatedStamp a szolgáltatás által használt belső művelet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | Az AllocateStamp a szolgáltatás által használt belső művelet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | A Kiterjesztett információ lekért művelet lekérte egy objektum kiterjesztett adatait, amely a ?vault típusú Azure-erőforrást képviseli? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A Get Vault művelet lekért egy objektumot, amely a "vault" típusú Azure-erőforrást képviseli |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | A Get Operation Results művelettel lekért művelet állapota és eredménye lekért az aszinkron módon elküldött művelethez |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | A Get Containers művelettel lekértheti az erőforráshoz regisztrált tárolókat. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | A riasztások beállításainak olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Események olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/action | Ellenőrzi a háló konzisztenciáját |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Bármely háló olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/action | Átjáró újra társítása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/action | Háló tanúsítványának megújítása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Bármely hálózat olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Bármely hálózatleképezés olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Protection-tárolók olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Védett elemek olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Helyreállítási pont alkalmazása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Feladatátvétel véglegesítése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Tervezett feladatátvétel |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Védett elemek olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Replikációs helyreállítási pontok olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Replikáció javítása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Védett elem ismételt védelme |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Védelmi tároló váltása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Feladatátvételi teszt |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Feladatátvételi teszt – Tisztítás |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Feladatátvétel |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mobilitási szolgáltatás frissítése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Protection-tárolóleképezések olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services-szolgáltatók olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Szolgáltató frissítése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Tárhelybesorolások olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Tárhelybesorolási leképezések olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | VCenterek olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Replikációs feladatok létrehozása és kezelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Bármely szabályzat olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/action | Feladatátvétel véglegesítésének helyreállítási terve |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/action | Tervezett feladatátvételi helyreállítási terv |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Helyreállítási tervek olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/action | Helyreállítási terv ismételt védelme |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/action | Feladatátvételi helyreállítási terv tesztelése |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Feladatátvételi teszt helyreállítási tervének tisztítása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/unplannedFailover/action | Feladatátvételi helyreállítási terv |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | A Recovery Services-tárolóra vonatkozó riasztások olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Helyreállítási tár használati adatait adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | A Vault Token művelettel lekért tárolói jogkivonatok a tárolószintű háttérművelethez. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | A tárfiókok listáját adja vissza, vagy lekérte a megadott tárfiók tulajdonságait. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

Lehetővé teszi a Site Recovery állapotának megtekintését, de más felügyeleti műveletek elvégzését [nem. További információ](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | A GetAllocatedStamp a szolgáltatás által használt belső művelet |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | A Kiterjesztett információ lekért művelet lekérte egy objektum kiterjesztett adatait, amely a ?vault típusú Azure-erőforrást képviseli? |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Lekérte a Recovery Services-tároló riasztását. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | A Get Vault művelet lekért egy objektumot, amely a "vault" típusú Azure-erőforrást képviseli |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | A Get Operation Results művelettel lekért művelet állapota és eredménye az aszinkron módon elküldött művelethez |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | A Get Containers művelettel lekérte az erőforráshoz regisztrált tárolókat. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Riasztási beállítások olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Események olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Bármely háló olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Bármely hálózat olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Hálózatleképezések olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Védelmi tárolók olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Bármely védett elem olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Védett elemek olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Az esetleges replikációs helyreállítási pontok olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Protection-tárolóleképezések olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services-szolgáltatók olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Tárhelybesorolások olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Tárhelybesorolási leképezések olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | VCenterek olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/read | Bármely feladat olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Bármely szabályzat olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Helyreállítási tervek olvasása |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Helyreállítási tár használati adatait adja vissza. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | A vault token művelettel lekért tároló-jogkivonat tárolószintű háttérműveleteket. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="support-request-contributor"></a>Támogatási kérelem közreműködője

Lehetővé teszi támogatási kérelmek létrehozására és kezelésére [vonatkozó további információ](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="tag-contributor"></a>Címke közreműködője

Lehetővé teszi az entitások címkéinek kezelését anélkül, hogy hozzáférést biztosítanának magukhoz az entitásokhoz. [További információ](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Lekérte az erőforráscsoport erőforrásait. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resources/read | Lekérte egy előfizetés erőforrásait. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/tags/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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


### <a name="azure-digital-twins-data-owner"></a>Azure Digital Twins adattulajdonos

Teljes hozzáférésű szerepkör Digital Twins adatsíkhoz [További információ](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | Eseményútvonalak olvasása, törlése, létrehozása vagy frissítése |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | Digitális ikereszköz olvasása, létrehozása, frissítése vagy törlése |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/commands/* | Bármilyen parancs meghívása a Digital Twinsen |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/* | Digitális ikerkapcsolatok olvasása, létrehozása, frissítése vagy törlése |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | Modell olvasása, létrehozása, frissítése vagy törlése |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/* | Bármely Digital Twins Graph lekérdezése |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="azure-digital-twins-data-reader"></a>Azure Digital Twins adatolvasó

Csak olvasási szerepkör az Digital Twins-sík tulajdonságaihoz [További információ](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/read | Digitális iker olvasása |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/read | Digitális ikerkapcsolatok olvasása |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/read | Eseményútvonal olvasása |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/read | Bármely modell olvasása |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/action | Bármely Digital Twins Graph lekérdezése |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="biztalk-contributor"></a>BizTalk Contributor

Lehetővé teszi a BizTalk-szolgáltatások kezelését, de a hozzáférésük nem.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk-szolgáltatások létrehozása és kezelése |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
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

### <a name="desktop-virtualization-application-group-contributor"></a>Asztali virtualizálási alkalmazáscsoport közreműködője

Az asztali virtualizálási alkalmazáscsoport közreműködője. [További információ](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Gazdagépkészletek olvasása |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Gazdagépkészletek/sessionhostok olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86240b0e-9422-4c43-887b-b61143f32ba8",
  "name": "86240b0e-9422-4c43-887b-b61143f32ba8",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-reader"></a>Asztali virtualizálási alkalmazáscsoport olvasója

Az asztali virtualizálási alkalmazáscsoport olvasója. [További információ](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Alkalmazáscsoportok olvasása |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Gazdagépkészletek olvasása |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Hostpools/sessionhosts olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Lekért vagy listázza az üzemelő példányokat. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Klasszikus metrikariasztás olvasása |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "name": "aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-contributor"></a>Asztali virtualizálási közreműködő

Az asztali virtualizálás közreműködője. [További információ](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/082f0a83-3be5-4ba1-904c-961cca79b387",
  "name": "082f0a83-3be5-4ba1-904c-961cca79b387",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-contributor"></a>Asztali virtualizálási gazdagépkészlet közreműködője

Az asztali virtualizálási gazdagépkészlet közreműködője. [További információ](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "name": "e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-reader"></a>Asztali virtualizálási gazdagépkészlet olvasója

Az asztali virtualizálási gazdagépkészlet olvasója. [További információ](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Gazdagépkészletek olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Lekért vagy listázza az üzemelő példányokat. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Klasszikus metrikariasztás olvasása |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (NotDataActions)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ceadfde2-b300-400a-ab7b-6143895aa822",
  "name": "ceadfde2-b300-400a-ab7b-6143895aa822",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-reader"></a>Asztali virtualizálás olvasója

Az asztali virtualizálás olvasója. [További információ](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Lekért vagy listázza az üzemelő példányokat. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Klasszikus metrikariasztás olvasása |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49a72310-ab8d-41df-bbb0-79b649203868",
  "name": "49a72310-ab8d-41df-bbb0-79b649203868",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-session-host-operator"></a>Asztali virtualizálási munkamenetgazda-kezelő

Az asztali virtualizálási munkamenetgazda operátora. [További információ](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Gazdagépkészletek olvasása |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Session Host.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "name": "2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Session Host Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>Asztali virtualizálási felhasználó

Lehetővé teszi a felhasználók számára az alkalmazások használatát egy alkalmazáscsoportban. [További információ](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationGroups/useApplications/action | Az ApplicationGroup használata |
> | **NotDataActions (NotDataActions)** |  |
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

### <a name="desktop-virtualization-user-session-operator"></a>Asztali virtualizálás felhasználói munkamenet-kezelője

A Desktop Virtualization Uesr-munkamenet operátora. [További információ](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Gazdagépkészletek olvasása |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Gazdagépkészletek/sessionhostok olvasása |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/usersessions/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Uesr Session.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "name": "ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User Session Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-contributor"></a>Asztali virtualizálási munkaterület közreműködője

Az asztali virtualizálási munkaterület közreműködője. [További információ](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Alkalmazáscsoportok olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "name": "21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/*",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-reader"></a>Asztali virtualizálási munkaterület olvasója

Az asztali virtualizálási munkaterület olvasója. [További információ](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/read | Munkaterületek olvasása |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Alkalmazáscsoportok olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Lekért vagy listázza az üzemelő példányokat. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Klasszikus metrikariasztás olvasása |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "name": "0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-backup-reader"></a>Lemez biztonságimásolat-olvasója

Engedélyt biztosít a biztonsági mentési tárolónak a lemezes biztonsági mentés végrehajtásához. [További információ](../backup/disk-backup-faq.yml)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Lemez tulajdonságainak lekért része |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | A lemez SAS URI-ének lekért blob-hozzáféréshez |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk backup.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "name": "3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/beginGetAccess/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-restore-operator"></a>Lemez-visszaállítási operátor

Engedélyt biztosít a Backup-tárolónak a lemez-visszaállítás végrehajtásához. [További információ](../backup/restore-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Új lemezt hoz létre, vagy egy meglévőt frissíti |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Lemez tulajdonságainak lekért része |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk restore.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b50d9833-a0cb-478e-945f-707fcc997c13",
  "name": "b50d9833-a0cb-478e-945f-707fcc997c13",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Restore Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-snapshot-contributor"></a>Lemez-pillanatkép közreműködője

Engedélyt biztosít a biztonsági mentési tároló számára a lemez-pillanatképek kezeléséhez. [További információ](../backup/backup-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/delete | Pillanatkép törlése |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/write | Új pillanatkép létrehozása vagy meglévő frissítése |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/read | Pillanatkép tulajdonságainak lekért része |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/beginGetAccess/action | A blob-hozzáférés pillanatképének SAS URI-jának lekérte |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/endGetAccess/action | A pillanatkép SAS URI-ének visszavonása |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | A lemez SAS URI-ének lekért blob-hozzáféréshez |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsait adja vissza. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/write | Létrehoz egy tárfiókot a megadott paraméterekkel, vagy frissíti a tulajdonságokat vagy címkéket, vagy hozzáad egy egyéni tartományt a megadott tárfiókhoz. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Visszaadja a tárfiókok listáját, vagy lekérte a megadott tárfiók tulajdonságait. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/delete | Töröl egy meglévő tárfiókot. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to manage disk snapshots.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "name": "7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/snapshots/delete",
        "Microsoft.Compute/snapshots/write",
        "Microsoft.Compute/snapshots/read",
        "Microsoft.Compute/snapshots/beginGetAccess/action",
        "Microsoft.Compute/snapshots/endGetAccess/action",
        "Microsoft.Compute/disks/beginGetAccess/action",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/write",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Snapshot Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Scheduler-feladatgyűjtemények közreműködője

Lehetővé teszi a Scheduler feladatgyűjtemények kezelését, de a hozzájuk való hozzáférést nem.

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Klasszikus metrikariasztás létrehozása és kezelése |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Lekérte a rendelkezésre állási állapotokat a megadott hatókörben található összes erőforráshoz |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Feladatgyűjtemények létrehozása és kezelése |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Támogatási jegy létrehozása és frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adatactions)** |  |
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

### <a name="services-hub-operator"></a>Services Hub-operátor

A Services Hub Operator lehetővé teszi a Services Hub-összekötőkhöz kapcsolódó összes olvasási, írási és törlési művelet elvégzését. [További információ](/services-hub/health/sh-connector-roles)

> [!div class="mx-tableFixed"]
> | Műveletek | Leírás |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Lekért vagy listázza az erőforráscsoportokat. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Üzemelő példány létrehozása és kezelése |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/write | Services Hub-összekötő létrehozása vagy frissítése |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/read | Szolgáltatások központi összekötőinek megtekintése vagy listába sorolás |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/delete | Szolgáltatások központi összekötőinek törlése |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/checkAssessmentEntitlement/action | Felsorolja az adott Services Hub-munkaterületre vonatkozó értékelési jogosultságokat |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/supportOfferingEntitlement/read | Adott Szolgáltatásközpont munkaterület támogatási ajánlatának jogosultságai megtekintése |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/workspaces/read | Egy adott felhasználó services hub-munkaterületének felsorolása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adattitkok)** |  |
> | *nincs* |  |
> | **NotDataActions (Nem adataktívok)** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Services Hub Operator allows you to perform all read, write, and deletion operations related to Services Hub Connectors.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/82200a5b-e217-47a5-b665-6d8765ee745b",
  "name": "82200a5b-e217-47a5-b665-6d8765ee745b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.ServicesHub/connectors/write",
        "Microsoft.ServicesHub/connectors/read",
        "Microsoft.ServicesHub/connectors/delete",
        "Microsoft.ServicesHub/connectors/checkAssessmentEntitlement/action",
        "Microsoft.ServicesHub/supportOfferingEntitlement/read",
        "Microsoft.ServicesHub/workspaces/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Services Hub Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Következő lépések

- [Azure-szerepkörök hozzárendelése a Azure Portal](role-assignments-portal.md)
- [Egyéni Azure-szerepkörök](custom-roles.md)
- [Engedélyek az Azure Security Centerben](../security-center/security-center-permissions.md)
