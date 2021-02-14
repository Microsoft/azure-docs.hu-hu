---
title: Azure Data Factory titkosítása az ügyfél által felügyelt kulccsal
description: A Data Factory biztonság növelése a Bring Your Own Keyokkal (BYOK)
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: 2a943b82689184353b9d350b931a069df9c35ff2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392697"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Azure Data Factory titkosítása az ügyfél által felügyelt kulcsokkal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory titkosítja a nyugalmi állapotot, beleértve az entitások definícióit és a Futtatás közben gyorsítótárazott adatok állapotát. Alapértelmezés szerint az adat egy véletlenszerűen generált, Microsoft által felügyelt kulccsal van titkosítva, amely egyedileg hozzá van rendelve az adat-előállítóhoz. A további biztonsági garanciák érdekében mostantól engedélyezheti a Bring Your Own Key (BYOK) szolgáltatást a Azure Data Factory ügyfél által felügyelt kulcsok funkciójának használatával. Ha ügyfél által felügyelt kulcsot ad meg, Data Factory a gyári rendszerkulcsot és a CMK __is__ használja az ügyféladatok titkosításához. A hiányzó vagy a gyárhoz való hozzáférés megtagadását eredményezi.

Az ügyfél által felügyelt kulcsok tárolásához Azure Key Vault szükséges. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. A Key vaultnak és a Data Factorynak ugyanabban a Azure Active Directory (Azure AD) bérlőben és ugyanabban a régióban kell lennie, de lehetnek különböző előfizetésekben. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../key-vault/general/overview.md)

> [!NOTE]
> Az ügyfél által felügyelt kulcs csak üres adatelőállítón konfigurálható. Az adatelőállító nem tartalmazhat olyan erőforrásokat, mint a társított szolgáltatások, a folyamatok és az adatforgalom. Javasoljuk, hogy közvetlenül a gyári létrehozás után engedélyezze az ügyfél által felügyelt kulcs létrehozását.

## <a name="about-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsok

Az alábbi ábra azt mutatja be, hogy a Data Factory hogyan használja a Azure Active Directory és a Azure Key Vaultt a kérelmek ügyfél által felügyelt kulcs használatával történő elvégzéséhez:

  ![Ábra, amely bemutatja, hogyan működnek az ügyfél által felügyelt kulcsok Azure Data Factory](media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png)

Az alábbi lista a diagram számozott lépéseit ismerteti:

1. Egy Azure Key Vault rendszergazda engedélyt ad a titkosítási kulcsokra a Data Factory társított felügyelt identitásnak.
1. Egy Data Factory rendszergazda engedélyezi az ügyfél által felügyelt kulcs szolgáltatást a gyárban
1. A Data Factory a gyárhoz társított felügyelt identitást használja a Azure Key Vault elérésének hitelesítéséhez a Azure Active Directory használatával
1. Data Factory becsomagolja a gyári titkosítási kulcsot az ügyfél kulcsával Azure Key Vault
1. Olvasási/írási műveletek esetén Data Factory küldi a kérelmeket Azure Key Vault a fiók titkosítási kulcsának kicsomagolásához a titkosítási és visszafejtési műveletek elvégzéséhez.

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Előfeltételek – Azure Key Vault konfigurálása és kulcsok generálása

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>A nem végleges Törlés engedélyezése Azure Key Vault

Az ügyfél által felügyelt kulcsok Data Factory használatakor két tulajdonságot kell beállítani a Key Vault, a __Soft delete__ és a __repurge__(Törlés) lehetőséget. Ezek a tulajdonságok a PowerShell vagy az Azure CLI használatával engedélyezhetők egy új vagy meglévő kulcstartón. Ha meg szeretné tudni, hogyan engedélyezheti ezeket a tulajdonságokat egy meglévő kulcstartón, tekintse meg a következő cikkek egyikében, a _Soft-delete engedélyezése_ és a _kiürítési védelem engedélyezése_ című szakaszt:

- [A Soft delete használata a PowerShell-lel](../key-vault/general/key-vault-recovery.md)
- [A Soft delete használata a parancssori felülettel](../key-vault/general/key-vault-recovery.md)

Ha Azure Portal használatával hoz létre új Azure Key Vault, a következő módon engedélyezheti a __törlést__ , és nem törölheti a __kiürítést__ :

  ![Képernyőfelvétel a Key Vault létrehozásakor a Soft delete és a depurge Protection engedélyezése](media/enable-customer-managed-key/01-enable-purge-protection.png)

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Data Factory hozzáférés engedélyezése Azure Key Vault

Győződjön meg arról, hogy a Azure Key Vault és a Azure Data Factory ugyanahhoz a Azure Active Directory (Azure AD) bérlőhöz és _ugyanahhoz a régióhoz_ tartoznak. A Azure Key Vault hozzáférés-vezérlés területen adja meg az adat-előállító Managed Service Identity (MSI) a következő engedélyeket: _Get_, _dewrap Key_ és _wrap Key_. Ezek az engedélyek szükségesek az ügyfél által felügyelt kulcsok Data Factory való engedélyezéséhez.

  ![Képernyőfelvétel Data Factory hozzáférésének engedélyezése Key Vault](media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png)

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Ügyfél által felügyelt kulcs előállítása vagy feltöltése Azure Key Vault

Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. Data Factory titkosítás csak a 2048 bites RSA-kulcsokat támogatja. További információ: [a kulcsok, titkos kódok és tanúsítványok](../key-vault/general/about-keys-secrets-certificates.md).

  ![Az ügyfél által felügyelt kulcsok létrehozásának képernyőképe](media/enable-customer-managed-key/03-create-key.png)

## <a name="enable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok engedélyezése

1. Győződjön meg arról, hogy a Data Factory üres. Az adatelőállító nem tartalmazhat olyan erőforrásokat, mint a társított szolgáltatások, a folyamatok és az adatforgalom. Egyelőre az ügyfél által felügyelt kulcs nem üres gyárba való telepítése hibát eredményez.

1. Ha meg szeretné keresni a kulcs URI-JÁT a Azure Portalban, keresse meg a Azure Key Vault, és válassza a kulcsok beállítást. Válassza ki a kívánt kulcsot, majd kattintson a kulcsra a verziók megtekintéséhez. Válassza ki a megfelelő verziót a beállítások megtekintéséhez.

1. Másolja ki a kulcs-azonosító mező értékét, amely megadja az URI-t.

    ![A kulcs URI-azonosítójának lekérése Key Vault](media/enable-customer-managed-key/04-get-key-identifier.png)

1. Indítsa el Azure Data Factory portált, és a bal oldalon található navigációs sáv használatával ugorjon a Data Factory felügyeleti portál

1. Kattintson a __felhasználó által Kiemelt kulcs__ ikonra

    ![Képernyőfelvétel: az ügyfél által felügyelt kulcs engedélyezése Data Factory](media/enable-customer-managed-key/05-customer-managed-key-configuration.png)

1. Adja meg a korábban átmásolt ügyfél által felügyelt kulcs URI azonosítóját.

1. Kattintson a __Save (Mentés__ ) gombra, és az ügyfél és a kulcs titkosítása engedélyezve van Data Factory

## <a name="update-key-version"></a>Kulcs verziójának frissítése

A kulcs új verziójának létrehozásakor frissítse az adatgyárat az új verzió használatára. Kövesse a következő témakörben ismertetett lépéseket: _Customer-Managed kulcsok engedélyezése_, beleértve a következőket:

1. Keresse meg az új kulcshoz tartozó URI-t a Azure Key Vault-portálon keresztül

1. Navigáljon az __ügyfél által felügyelt kulcs__ beállításhoz

1. Cserélje le és illessze be az új kulcs URI-JÁT

1. Kattintson a __Save (Mentés__ ) gombra, és a Data Factory mostantól titkosítva lesz az új kulcs verziójával.

## <a name="use-a-different-key"></a>Másik kulcs használata

Data Factory titkosításhoz használt kulcs módosításához manuálisan kell frissítenie a beállításokat a Data Factoryban. Kövesse a következő témakörben ismertetett lépéseket: _Customer-Managed kulcsok engedélyezése_, beleértve a következőket:

1. Keresse meg az új kulcs URI-JÁT Azure Key Vault-portálon keresztül

1. Navigáljon a __Customer mangd Key__ beállításhoz

1. Cserélje le és illessze be az új kulcs URI-JÁT

1. Kattintson a __Save (Mentés__ ) gombra, és a Data Factory mostantól titkosítva lesz az új kulccsal

## <a name="disable-customer-managed-keys"></a>Customer-Managed kulcsok letiltása

A tervezés után az ügyfél által felügyelt kulcs funkció engedélyezése után nem távolíthatja el az extra biztonsági lépést. A gyár és az adatok titkosítása mindig egy ügyfél által megadott kulccsal történik.

## <a name="next-steps"></a>Következő lépések

A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-dot-net.md).