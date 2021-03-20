---
title: Az ügyfél által felügyelt kulcsok konfigurálása a Azure Portal használatával
titleSuffix: Cognitive Services
description: Megtudhatja, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat a Azure Key Vault segítségével a Azure Portal használatával. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlések létrehozását, elforgatását, letiltását és visszavonását.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: c79846b0a5b675c34e4e7919e9ecd9d591bfefe5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96356358"
---
## <a name="customer-managed-keys-with-azure-key-vault"></a>Felhasználó által kezelt kulcsok az Azure Key Vaulttal

Az ügyfél által felügyelt kulcsok tárolásához Azure Key Vaultt kell használnia. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. A Cognitive Services erőforrásnak és a kulcstartónak ugyanabban a régióban és ugyanabban a Azure Active Directory (Azure AD) bérlőben kell lennie, de különböző előfizetésekben lehet. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../../key-vault/general/overview.md)

Új Cognitive Services-erőforrás létrehozásakor a rendszer mindig a Microsoft által felügyelt kulcsokkal titkosítja. Az ügyfél által felügyelt kulcsokat nem lehet engedélyezni az erőforrás létrehozásának időpontjában. Az ügyfél által felügyelt kulcsok Azure Key Vault tárolódnak, és a kulcstárolót olyan hozzáférési házirendekkel kell kiépíteni, amelyek kulcsfontosságú engedélyeket biztosítanak a Cognitive Services erőforráshoz társított felügyelt identitásnak. A felügyelt identitás csak akkor érhető el, ha az erőforrást a CMK szükséges díjszabási szinten hozza létre.

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

## <a name="enable-customer-managed-keys-for-your-resource"></a>Ügyfél által felügyelt kulcsok engedélyezése az erőforráshoz

Az ügyfél által felügyelt kulcsok Azure Portal való engedélyezéséhez kövesse az alábbi lépéseket:

1. Navigáljon a Cognitive Services-erőforráshoz.
1. A Cognitive Services erőforrás **Beállítások** paneljén kattintson a **titkosítás** elemre. Válassza ki az **ügyfél által felügyelt kulcsok** lehetőséget az alábbi ábrán látható módon.

    ![Az ügyfél által felügyelt kulcsok kijelölését bemutató képernyőkép](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Kulcs meghatározása

Miután engedélyezte az ügyfél által felügyelt kulcsokat, lehetősége lesz megadnia a Cognitive Services erőforráshoz társítandó kulcsot.

### <a name="specify-a-key-as-a-uri"></a>Kulcs megadása URI-ként

A kulcs URI-ként való megadásához kövesse az alábbi lépéseket:

1. Ha meg szeretné keresni a kulcs URI-JÁT a Azure Portalban, navigáljon a kulcstartóhoz, és válassza a **kulcsok** beállítást. Válassza ki a kívánt kulcsot, majd kattintson a kulcsra a verziók megtekintéséhez. Válassza ki a verziószámot az adott verzió beállításainak megtekintéséhez.
1. Másolja a **kulcs-azonosító** mező értékét, amely megadja az URI-t.

    ![A Key Vault kulcs URI-JÁT ábrázoló képernyőfelvétel](../media/cognitive-services-encryption/key-uri-portal.png)

1. A Storage-fiók **titkosítási** beállításainál kattintson a **kulcs URI-ja megadása** lehetőségre.
1. Illessze be a vágólapra a **kulcs URI** mezőjébe MÁSOLt URI-t.

   ![A kulcs URI-azonosítójának megadását bemutató képernyőkép](../media/cognitive-services-encryption/ssecmk2.png)

1. Itt adhatja meg a kulcstárolót tartalmazó előfizetést.
1. Mentse a módosításokat.

### <a name="specify-a-key-from-a-key-vault"></a>Kulcs megadása kulcstartóból

A Key Vault kulcsának megadásához először győződjön meg arról, hogy rendelkezik egy kulcsot tartalmazó kulcstartóval. A Key vaultban lévő kulcs megadásához kövesse az alábbi lépéseket:

1. Válassza a **kiválasztás a Key Vault** lehetőséget.
1. Válassza ki a használni kívánt kulcsot tartalmazó kulcstartót.
1. Válassza ki a kulcsot a Key vaultból.

   ![Az ügyfél által felügyelt kulcs beállítását bemutató képernyőfelvétel](../media/cognitive-services-encryption/ssecmk3.png)

1. Mentse a módosításokat.

## <a name="update-the-key-version"></a>A kulcs verziójának frissítése

A kulcs új verziójának létrehozásakor frissítse a Cognitive Services erőforrást az új verzió használatára. Kövesse az alábbi lépéseket:

1. Navigáljon a Cognitive Services erőforráshoz, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg az új kulcs verziójának URI azonosítóját. Másik lehetőségként kiválaszthatja a Key vaultot és a kulcsot is a verzió frissítéséhez.
1. Mentse a módosításokat.

## <a name="use-a-different-key"></a>Másik kulcs használata

A titkosításhoz használt kulcs módosításához kövesse az alábbi lépéseket:

1. Navigáljon a Cognitive Services erőforráshoz, és jelenítse meg a **titkosítási** beállításokat.
1. Adja meg az új kulcs URI-JÁT. Másik lehetőségként kiválaszthatja a kulcstartót, és kiválaszthat egy új kulcsot.
1. Mentse a módosításokat.

## <a name="rotate-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok elforgatása

A megfelelőségi szabályzatok alapján Azure Key Vault elforgathatja az ügyfél által felügyelt kulcsot. A kulcs elforgatásakor frissítenie kell a Cognitive Services erőforrást az új kulcs URI-azonosítójának használatához. Ha szeretné megtudni, hogyan frissítheti az erőforrást úgy, hogy a kulcs új verzióját használja a Azure Portalban, tekintse meg [a kulcs verziójának frissítése](#update-the-key-version)című témakört.

A kulcs elforgatása nem aktiválja újra az erőforrásban lévő adattitkosítást. Nincs szükség további műveletre a felhasználótól.

## <a name="revoke-access-to-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok hozzáférésének visszavonása

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a PowerShellt vagy az Azure CLI-t. További információ: [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) vagy [Azure Key Vault parancssori](/cli/azure/keyvault)felület. A hozzáférés visszavonása hatékonyan blokkolja a Cognitive Services erőforrásban lévő összes adattal való hozzáférést, mivel a titkosítási kulcs Cognitive Services nem érhető el.

## <a name="disable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok letiltása

Ha letiltotta az ügyfél által felügyelt kulcsokat, akkor a Cognitive Services-erőforrást a Microsoft által felügyelt kulcsokkal titkosítja. Az ügyfél által felügyelt kulcsok letiltásához kövesse az alábbi lépéseket:

1. Navigáljon a Cognitive Services erőforráshoz, és jelenítse meg a **titkosítási** beállításokat.
1. Törölje a **saját kulcs használata** beállítás melletti jelölőnégyzet jelölését.