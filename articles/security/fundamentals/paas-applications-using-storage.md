---
title: A Pásti-alkalmazások biztonságossá tétele az Azure Storage használatával | Microsoft Docs
description: Ismerje meg az Azure Storage szolgáltatással kapcsolatos ajánlott biztonsági eljárásokat a Pásti web-és Mobile-alkalmazások biztonságossá tételéhez.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 12cce0eac3827046ae9171f3dd5696ae5905c802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093988"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Ajánlott eljárások a Pásti webes és mobil alkalmazások biztonságossá tételéhez az Azure Storage használatával
Ebben a cikkben az Azure Storage ajánlott biztonsági eljárásainak gyűjteményét tárgyaljuk, amelyekkel biztosítható a platform-szolgáltatásként szolgáló webes és mobil alkalmazások védelme. Ezek az ajánlott eljárások az Azure tapasztalataiból és az ügyfelek, például saját tapasztalataiból származnak.

Az Azure lehetővé teszi a tárolók üzembe helyezését és használatát a helyszínen nem könnyen elérhető módon. Az Azure Storage segítségével viszonylag kevés erőfeszítéssel érheti el a méretezhetőséget és a rendelkezésre állást. Nem csak az Azure Storage a Windows és a Linux Azure Virtual Machines alapja, hanem a nagyméretű elosztott alkalmazások támogatása is.

Az Azure Storage a következő négy szolgáltatást biztosítja: blob Storage, Table Storage, üzenetsor Storage és file Storage. További információ: [Bevezetés a Microsoft Azure Storageba](../../storage/common/storage-introduction.md).

Az [Azure Storage biztonsági útmutatója](../../storage/blobs/security-recommendations.md) nagyszerű információforrás az Azure Storage szolgáltatással és biztonsággal kapcsolatos részletes információkhoz. Az ajánlott eljárásokról szóló cikk a biztonsági útmutatóban található néhány fogalmat, valamint a biztonsági útmutatóra, valamint más forrásokra mutató hivatkozásokat tartalmaz.

Ez a cikk az alábbi ajánlott eljárásokat tárgyalja:

- Közös hozzáférésű jogosultságkódok (SAS)
- Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)
- Ügyféloldali titkosítás nagy értékű adathoz
- Storage Service Encryption


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Megosztott hozzáférési aláírás használata a Storage-fiók kulcsa helyett
A hozzáférés-vezérlés kritikus fontosságú. Az Azure Storage-hoz való hozzáférés szabályozásának elősegítése érdekében az Azure létrehoz egy 2 512 bites Storage-fiók kulcsait (SAKs) a Storage-fiók létrehozásakor. A Key redundancia lehetővé teszi, hogy elkerülje a szolgáltatás megszakadását a rutinos kulcsok elforgatása során. 

A tárterület-hozzáférési kulcsok magas prioritású titkok, és csak a Storage hozzáférés-vezérlésért felelősek számára lesznek elérhetők. Ha rossz emberek férhetnek hozzá ezekhez a kulcsokhoz, a tárterület teljes hozzáférése megtörténik, és a fájlok a tárolóba való cseréjére, törlésére vagy hozzáadására is használhatók. Ez magában foglalja a kártevőket és más típusú tartalmakat is, amelyek veszélyeztethetik a szervezetet vagy az ügyfeleit.

Továbbra is szükség van egy olyan módszerre, amely hozzáférést biztosít a tárolóban lévő objektumokhoz. A részletesebb hozzáférés biztosításához használhatja a közös hozzáférésű aláírás (SAS) előnyeit. Az SAS lehetővé teszi, hogy meghatározott objektumokat osszon meg a tárolóban egy előre meghatározott időintervallumra és meghatározott engedélyekkel. A közös hozzáférésű aláírások lehetővé teszik a következőket:

- Az az intervallum, ameddig az SAS érvényes, beleértve a kezdési időt és a lejárati időt.
- Az SAS által megadott engedélyek. Előfordulhat például, hogy egy blob SAS-je a felhasználó számára olvasási és írási engedélyt ad a blobnak, de nem törli az engedélyeket.
- Opcionális IP-cím vagy IP-címtartomány, amelyből az Azure Storage fogadja az SAS-t. Például megadhatja a szervezetéhez tartozó IP-címek tartományát. Ez egy újabb biztonsági mértéket biztosít az SAS számára.
- Az a protokoll, amelyre az Azure Storage fogadja az SAS-t. Ezt a választható paramétert használhatja a HTTPS protokollt használó ügyfelekhez való hozzáférés korlátozására.

Az SAS lehetővé teszi a tartalmak megosztását, ahogyan azt a Storage-fiók kulcsainak megadása nélkül szeretné megosztani. Az alkalmazásban az SAS-t mindig biztonságos módon oszthatja meg a Storage-fiók kulcsainak veszélyeztetése nélkül.

További információ a közös hozzáférésű aláírásról: a [közös hozzáférésű aláírások használata](../../storage/common/storage-sas-overview.md). 

## <a name="use-azure-role-based-access-control"></a>Az Azure szerepköralapú hozzáférés-vezérlés használata
A hozzáférés kezelésének egy másik módja az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)használata. Az Azure RBAC segítségével az alkalmazottaknak az igényeknek megfelelő, a minimális jogosultságokkal kapcsolatos biztonsági alapelveket kell megadniuk. Túl sok engedély teheti ki a fiókot a támadók számára. A túl kevés engedély azt jelenti, hogy az alkalmazottak nem tudják hatékonyan elvégezni a munkájukat. Az Azure RBAC részletes hozzáférés-kezelést nyújt az Azure-hoz, így segít a probléma megoldásában. Ez olyan szervezetek számára fontos, akik biztonsági házirendeket kívánnak kikényszeríteni az adateléréshez.

Az Azure-ban az Azure beépített szerepkörei segítségével jogosultságokat rendelhet a felhasználókhoz. Használja például a Storage-fiók közreműködője olyan felhőalapú operátorok esetében, amelyeknek a Storage-fiókokat és a klasszikus Storage-fiók közreműködői szerepkörét kell kezelnie a klasszikus Storage-fiókok kezeléséhez. Azon felhőalapú operátorok esetében, amelyeknek virtuális gépeket kell kezelnie, de nem az a virtuális hálózat vagy a Storage-fiók, amelyhez csatlakoznak, a virtuális gép közreműködői szerepkörhöz adhatók hozzá.

Azok a szervezetek, amelyek nem kényszerítik ki az adathozzáférés-vezérlést olyan képességek használatával, mint például az Azure RBAC, több jogosultságot biztosítanak a felhasználók számára szükségesnél. Ez olyan adatbiztonságot eredményezhet, amely lehetővé teszi egyes felhasználók számára, hogy az első helyen ne férhessenek hozzájuk.

További információ az Azure RBAC:

- [Azure-szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md)
- [Beépített Azure-szerepkörök](../../role-based-access-control/built-in-roles.md)
- [Azure Storage – biztonsági útmutató](../../storage/blobs/security-recommendations.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Ügyféloldali titkosítás használata nagy értékű adathoz
Az ügyféloldali titkosítás lehetővé teszi, hogy programozott módon titkosítsa az adatok átvitelét az Azure Storage-ba való feltöltés előtt, és programozott módon visszafejtse az adatoknak a beolvasása során. Ez az átvitel során az adatok titkosítását is lehetővé teszi, de az inaktív adatok titkosítását is biztosítja. Az ügyféloldali titkosítás a legbiztonságosabb módszer az adattitkosításhoz, de megköveteli, hogy programozott módosításokat hajtson végre az alkalmazásban, és a kulcsfontosságú felügyeleti folyamatokat helyezze üzembe.

Az ügyféloldali titkosítás azt is lehetővé teszi, hogy a titkosítási kulcsok egyetlen vezérléssel rendelkezzenek. Saját titkosítási kulcsokat is létrehozhat és kezelhet. Egy Burkológörbe-technikát használ, amelyben az Azure Storage ügyféloldali kódtára létrehoz egy titkosító kulcsot (CEK), amelyet a rendszer a kulcs titkosítási kulcs (KEK) használatával csomagolt be (titkosított). A KEK-et egy kulcs-azonosító azonosítja, és lehet egy aszimmetrikus kulcspár vagy egy szimmetrikus kulcs, és kezelhető helyileg, vagy [Azure Key Vault](../../key-vault/general/overview.md)tárolható.

Az ügyféloldali titkosítás a Java és a .NET Storage ügyféloldali kódtáraba van beépítve. Lásd: [ügyféloldali titkosítás és Azure Key Vault a Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md) az ügyfélalkalmazások adatainak titkosításához, valamint a saját titkosítási kulcsok létrehozásához és kezeléséhez.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Storage Service Encryption engedélyezése a nyugalmi állapotban lévő adatokhoz
Ha a file Storage [Storage Service encryption](../../storage/common/storage-service-encryption.md) engedélyezve van, az adatai automatikusan titkosítva vannak az AES-256 titkosítás használatával. A Microsoft kezeli az összes titkosítási, visszafejtési és kulcskezelő kezelést. Ez a funkció LRS és GRS redundancia-típusok esetén érhető el.

## <a name="next-steps"></a>Következő lépések

Ez a cikk az Azure Storage ajánlott biztonsági eljárásainak gyűjteményét mutatta be a Pásti web-és Mobile-alkalmazások biztonságossá tételéhez. További információ a Pásti-telepítések biztonságossá tételéről:

- [PaaS-környezetek védelme](paas-deployments.md)
- [A Pásti web-és Mobile-alkalmazások biztonságossá tétele az Azure App Services](paas-applications-using-app-services.md)
- [PaaS-adatbázisok védelme az Azure-ban](paas-applications-using-sql.md)