---
title: Egyéni parancsok szolgáltatás – a REST-adatok titkosítása
titleSuffix: Azure Cognitive Services
description: A REST-adatok titkosítása egyéni parancsokkal.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2020
ms.author: sausin
ms.openlocfilehash: 89d7a6f8beb004f57a00dfe75e4cc387c8591b1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "101716584"
---
# <a name="custom-commands-encryption-of-data-at-rest"></a>Custom Commands – Inaktív adatok titkosítása

Az egyéni parancsok automatikusan titkosítják az adataikat, amikor azok a felhőben is megmaradnak. Az egyéni parancsok szolgáltatás titkosítása védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

> [!NOTE]
> Az egyéni parancsok szolgáltatás nem engedélyezi automatikusan a titkosítást az alkalmazáshoz társított LUIS-erőforrásokhoz. Ha szükséges, engedélyeznie kell a [Luis-erőforrás titkosítását innen.](../luis/encrypt-data-at-rest.md)

## <a name="about-cognitive-services-encryption"></a>Tudnivalók a Cognitive Services titkosításról
Az adattitkosítás és visszafejtés az [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) -kompatibilis [256 bites AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) titkosítás használatával történik. A titkosítás és a visszafejtés átlátható, ami azt jelenti, hogy a titkosítás és a hozzáférés felügyelve van. Az adatok alapértelmezés szerint védettek, a titkosítás használatához pedig sem a kódot, sem az alkalmazást nem kell módosítania.

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

Egyéni parancsok használatakor a beszédfelismerési szolgáltatás a következő Felhőbeli adattárakat fogja tárolni:
* Konfigurációs JSON az egyéni parancsok alkalmazás mögött
* LUIS authoring és előrejelzési kulcs

Alapértelmezés szerint az előfizetés a Microsoft által felügyelt titkosítási kulcsokat használja. Saját előfizetését azonban saját titkosítási kulcsokkal is kezelheti. Az ügyfél által kezelt kulcsok (CMK) használata, amely Bring Your Own Key (BYOK, saját kulcs használata) módszerként is ismert, nagyobb rugalmasságot biztosít a hozzáférés-vezérlők létrehozása, rotálása, letiltása és visszavonása terén. Továbbá az adatok védelméhez használt titkosítási kulcsok naplózására is lehetősége van.


> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak a 2020. június 27. után létrehozott erőforrások. A CMK és a Speech Services használatához létre kell hoznia egy új beszédfelismerési erőforrást. Az erőforrás létrehozása után a Azure Key Vault használatával beállíthatja a felügyelt identitást.

Az ügyfél által felügyelt kulcsok használatának kérelmezéséhez töltse ki és küldje el Customer-Managed Key Request (kérelem) űrlapot. Körülbelül 3-5 munkanapot vesz igénybe, hogy visszahallgassa a kérés állapotát. Igénytől függően előfordulhat, hogy egy várólistába helyezi, és a rendszer jóváhagyja a helyet, és elérhetővé válik. Miután jóváhagyta a CMK és a Speech Services használatát, létre kell hoznia egy új Speech-erőforrást a Azure Portal.
   > [!NOTE]
   > **Az ügyfél által felügyelt kulcsok (CMK) csak egyéni parancsok esetén támogatottak.**
   >
   >  **A Custom Speech és az egyéni hang továbbra is csak a saját tárterületet (BYOS) támogatja.**  [További információ](speech-encryption-of-data-at-rest.md)
   >
   > Ha a megadott beszédfelismerési erőforrást használja a szolgáltatás eléréséhez, a megfelelőségi igényeket a BYOS explicit módon történő konfigurálásával kell teljesíteni.


## <a name="customer-managed-keys-with-azure-key-vault"></a>Felhasználó által kezelt kulcsok az Azure Key Vaulttal

Az ügyfél által felügyelt kulcsok tárolásához Azure Key Vaultt kell használnia. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. A beszédfelismerési erőforrásnak és a kulcstartónak ugyanabban a régióban és ugyanabban a Azure Active Directory (Azure AD) bérlőben kell lennie, de különböző előfizetésekben lehet. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../../key-vault/general/overview.md)

Amikor új beszédfelismerési erőforrást hoz létre, és egyéni parancsokat kíván kiépíteni, a rendszer mindig a Microsoft által felügyelt kulcsok használatával titkosítja az adatforrásokat. Az ügyfél által felügyelt kulcsokat nem lehet engedélyezni az erőforrás létrehozásának időpontjában. Az ügyfél által felügyelt kulcsok Azure Key Vault tárolódnak, és a kulcstárolót olyan hozzáférési házirendekkel kell kiépíteni, amelyek kulcsfontosságú engedélyeket biztosítanak a Cognitive Services erőforráshoz társított felügyelt identitásnak. A felügyelt identitás csak akkor érhető el, ha az erőforrást a CMK szükséges díjszabási szinten hozza létre.

Az ügyfél által felügyelt kulcsok engedélyezése lehetővé teszi a rendszerhez rendelt [felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md), az Azure ad funkciójának engedélyezését is. Ha engedélyezve van a rendszerhez rendelt felügyelt identitás, ez az erőforrás Azure Active Directory lesz regisztrálva. A regisztrálást követően a felügyelt identitás hozzáférést kap az ügyfél által felügyelt kulcs beállításakor kiválasztott Key Vaulthoz. 

> [!IMPORTANT]
> Ha letiltja a rendszerhez rendelt felügyelt identitásokat, a Key vaulthoz való hozzáférés el lesz távolítva, és az ügyfél kulcsaival titkosított összes adat már nem lesz elérhető. Az adatoktól függő funkciók nem fognak működni.

> [!IMPORTANT]
> A felügyelt identitások jelenleg nem támogatják a könyvtárak közötti forgatókönyveket. Ha az ügyfél által felügyelt kulcsokat a Azure Portal konfigurálja, a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alatt. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy az erőforrást egy Azure AD-címtárból egy másikba, az erőforráshoz társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok nem fognak működni. További információ: **előfizetés továbbítása Azure ad-címtárak között** [Gyakori kérdések és ismert problémák az Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)kapcsolatban.  

## <a name="configure-azure-key-vault"></a>Az Azure Key Vault konfigurálása

Az ügyfél által felügyelt kulcsok használata megköveteli, hogy a Key vaultban két tulajdonság legyen beállítva, a **Soft delete** és a **not Purge**. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, de a PowerShell vagy az Azure CLI használatával is engedélyezhető egy új vagy meglévő kulcstartón.

> [!IMPORTANT]
> Ha nem rendelkezik a helyreállítható **Törlés** és a kitakarítási **Tulajdonságok lehetőséggel, és törli** a kulcsot, nem fogja tudni helyreállítani a kognitív szolgáltatás erőforrásában lévő adatokat.

Ha meg szeretné tudni, hogyan engedélyezheti ezeket a tulajdonságokat egy meglévő kulcstartón, tekintse meg a következő cikkek egyikében, a **Soft-delete engedélyezése** és a **kiürítési védelem engedélyezése** című szakaszt:

- [A Soft-delete használata a PowerShell-](../../key-vault/general/key-vault-recovery.md)lel.
- [A Soft delete használata a parancssori](../../key-vault/general/key-vault-recovery.md)felülettel.

Az Azure Storage-titkosítás csak a 2048 méretű RSA-kulcsokat támogatja. A kulcsokkal kapcsolatos további információkért tekintse meg a kulcsok [, titkos kódok és tanúsítványok](../../key-vault/general/about-keys-secrets-certificates.md) **Key Vault kulcsait** Azure Key Vault ismertető témakört.

## <a name="enable-customer-managed-keys-for-your-speech-resource"></a>Ügyfél által felügyelt kulcsok engedélyezése a beszédfelismerési erőforráshoz

Az ügyfél által felügyelt kulcsok Azure Portal való engedélyezéséhez kövesse az alábbi lépéseket:

1. Navigáljon a beszédfelismerési erőforráshoz.
1. A beszédfelismerési erőforrás **Beállítások** paneljén kattintson a **titkosítás** elemre. Válassza ki az **ügyfél által felügyelt kulcsok** lehetőséget az alábbi ábrán látható módon.

 ![Az ügyfél által felügyelt kulcsok kijelölését bemutató képernyőkép](media/custom-commands/select-cmk.png)

## <a name="specify-a-key"></a>Kulcs meghatározása

Miután engedélyezte az ügyfél által felügyelt kulcsokat, lehetősége lesz megadnia a Cognitive Services erőforráshoz társítandó kulcsot.

### <a name="specify-a-key-as-a-uri"></a>Kulcs megadása URI-ként

A kulcs URI-ként való megadásához kövesse az alábbi lépéseket:

1. Ha meg szeretné keresni a kulcs URI-JÁT a Azure Portalban, navigáljon a kulcstartóhoz, és válassza a **kulcsok** beállítást. Válassza ki a kívánt kulcsot, majd kattintson a kulcsra a verziók megtekintéséhez. Válassza ki a verziószámot az adott verzió beállításainak megtekintéséhez.
1. Másolja a **kulcs-azonosító** mező értékét, amely megadja az URI-t.

    ![A Key Vault kulcs URI-JÁT ábrázoló képernyőfelvétel](../media/cognitive-services-encryption/key-uri-portal.png)

1. A beszédfelismerési szolgáltatás **titkosítási** beállításainál válassza a **kulcs URI-azonosítójának megadása** lehetőséget.
1. Illessze be a vágólapra a **kulcs URI** mezőjébe MÁSOLt URI-t.

1. Itt adhatja meg a kulcstárolót tartalmazó előfizetést.
1. Mentse a módosításokat.

### <a name="specify-a-key-from-a-key-vault"></a>Kulcs megadása kulcstartóból

A Key Vault kulcsának megadásához először győződjön meg arról, hogy rendelkezik egy kulcsot tartalmazó kulcstartóval. A Key vaultban lévő kulcs megadásához kövesse az alábbi lépéseket:

1. Válassza a **kiválasztás a Key Vault** lehetőséget.
1. Válassza ki a használni kívánt kulcsot tartalmazó kulcstartót.
1. Válassza ki a kulcsot a Key vaultból.

   ![Az ügyfél által felügyelt kulcs beállítását bemutató képernyőfelvétel](media/custom-commands/configure-cmk-fromKV.png)

1. Mentse a módosításokat.

## <a name="update-the-key-version"></a>A kulcs verziójának frissítése

A kulcs új verziójának létrehozásakor a beszédfelismerési erőforrást az új verzió használatára kell frissíteni. Kövesse az alábbi lépéseket:

1. Navigáljon a beszédfelismerési erőforráshoz, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg az új kulcs verziójának URI azonosítóját. Másik lehetőségként kiválaszthatja a Key vaultot és a kulcsot is a verzió frissítéséhez.
1. Mentse a módosításokat.

## <a name="use-a-different-key"></a>Másik kulcs használata

A titkosításhoz használt kulcs módosításához kövesse az alábbi lépéseket:

1. Navigáljon a beszédfelismerési erőforráshoz, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg az új kulcs URI-JÁT. Másik lehetőségként kiválaszthatja a kulcstartót, és kiválaszthat egy új kulcsot.
1. Mentse a módosításokat.

## <a name="rotate-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok elforgatása

A megfelelőségi szabályzatok alapján Azure Key Vault elforgathatja az ügyfél által felügyelt kulcsot. A kulcs elforgatásakor frissítenie kell a beszédfelismerési erőforrást, hogy az új kulcs URI-JÁT használja. Ha szeretné megtudni, hogyan frissítheti az erőforrást úgy, hogy a kulcs új verzióját használja a Azure Portalban, tekintse meg [a kulcs verziójának frissítése](#update-the-key-version)című témakört.

A kulcs elforgatása nem aktiválja újra az erőforrásban lévő adattitkosítást. Nincs szükség további műveletre a felhasználótól.

## <a name="revoke-access-to-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok hozzáférésének visszavonása

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a PowerShellt vagy az Azure CLI-t. További információ: [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) vagy [Azure Key Vault parancssori](/cli/azure/keyvault)felület. A hozzáférés visszavonása hatékonyan blokkolja a Cognitive Services erőforrásban lévő összes adattal való hozzáférést, mivel a titkosítási kulcs Cognitive Services nem érhető el.

## <a name="disable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok letiltása

Ha letiltja az ügyfél által felügyelt kulcsokat, a beszédfelismerési erőforrás ezután a Microsoft által felügyelt kulcsokkal lesz titkosítva. Az ügyfél által felügyelt kulcsok letiltásához kövesse az alábbi lépéseket:

1. Navigáljon a beszédfelismerési erőforráshoz, és jelenítse meg a **titkosítási** beállításokat.
1. Törölje a **saját kulcs használata** beállítás melletti jelölőnégyzet jelölését.

## <a name="next-steps"></a>Következő lépések

* [Beszédfelismerési Customer-Managed kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)
* [További információ a Azure Key Vault](../../key-vault/general/overview.md)
* [A felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md)