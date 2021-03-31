---
title: Az ügyfél által felügyelt kulcsok konfigurálása az importálási/exportálási szolgáltatáshoz a Azure Portal használatával
description: Ismerje meg, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat az Azure import/export szolgáltatáshoz Azure Key Vault az Azure Portal használatával. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlések létrehozását, elforgatását, letiltását és visszavonását.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 01/14/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 71c322385c9b70eb46e1c478c1322752ee4561e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98706491"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Az ügyfél által felügyelt kulcsok használata Azure Key Vault importálási/exportálási szolgáltatáshoz

Az Azure import/export védi a meghajtók titkosítási kulccsal történő zárolásához használt BitLocker-kulcsokat. Alapértelmezés szerint a BitLocker-kulcsok a Microsoft által felügyelt kulcsokkal vannak titkosítva. A titkosítási kulcsok további vezérléséhez az ügyfél által felügyelt kulcsokat is megadhatja.

Az ügyfél által felügyelt kulcsokat egy Azure Key Vault kell létrehozni és tárolni. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../key-vault/general/overview.md)

Ez a cikk bemutatja, hogyan használhatók az ügyfél által felügyelt kulcsok az importálási/exportálási szolgáltatással a [Azure Portalban](https://portal.azure.com/).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Importálási vagy exportálási feladatot hozott létre a következők szerint:

    - [Hozzon létre egy importálási feladatot a Blobok számára](storage-import-export-data-to-blobs.md).
    - [Hozzon létre egy importálási feladatot a fájlokhoz](storage-import-export-data-to-files.md).
    - [Exportálási feladatok létrehozása blobokhoz](storage-import-export-data-from-blobs.md)

2. Rendelkezik egy meglévő Azure Key Vault kulccsal, amelyet a BitLocker-kulcsának védelemmel ellátni használhat. Ha meg szeretné tudni, hogyan hozhat létre kulcstartót a Azure Portal használatával, tekintse meg a rövid útmutató [: Azure Key Vault létrehozása a Azure Portal használatával](../key-vault/general/quick-create-portal.md)című témakört.

    - A **Soft delete** és a **not Purge** beállítása a meglévő Key Vaultra van beállítva. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve. A tulajdonságok engedélyezéséhez tekintse meg a következő cikkek egyikében a **Soft-delete engedélyezése** és a **kiürítési védelem engedélyezése** című szakaszt:

        - [A Soft-delete használata a PowerShell-](../key-vault/general/key-vault-recovery.md)lel.
        - [A Soft delete használata a parancssori](../key-vault/general/key-vault-recovery.md)felülettel.
    - A meglévő Key Vault 2048 méretű RSA-kulccsal kell rendelkeznie. A kulcsokkal kapcsolatos további információkért lásd: [a kulcsok ismertetése](../key-vault/keys/about-keys.md).
    - A Key vaultnak ugyanabban a régióban kell lennie, mint az adataihoz tartozó Storage-fióknak.  
    - Ha nem rendelkezik meglévő Azure Key Vaultval, a következő szakaszban leírtak szerint is létrehozhatja azt.

## <a name="enable-keys"></a>Kulcsok engedélyezése

Az ügyfél által felügyelt kulcs konfigurálása az importálási/exportálási szolgáltatáshoz nem kötelező. Alapértelmezés szerint az importálási/exportálási szolgáltatás a Microsoft által felügyelt kulcs használatával védi a BitLocker-kulcsot. Az ügyfél által felügyelt kulcsok Azure Portal való engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az importálási feladatokhoz tartozó **Áttekintés** panelt.
2. A jobb oldali panelen válassza a **válassza ki a BitLocker-kulcsok titkosításának módját**.

    ![Titkosítási lehetőség kiválasztása](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. A **titkosítás** panelen megtekintheti és átmásolhatja az eszköz BitLocker-kulcsát. A **titkosítás típusa** területen kiválaszthatja, hogyan szeretné védelemmel ellátni a BitLocker-kulcsot. Alapértelmezés szerint a rendszer egy Microsoft által felügyelt kulcsot használ.

    ![BitLocker-kulcs megtekintése](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Lehetősége van ügyfél által felügyelt kulcs megadására. Miután kiválasztotta az ügyfél által felügyelt kulcsot, **válassza a Key Vault és a kulcs lehetőséget**.

    ![Ügyfél által felügyelt kulcs kiválasztása](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. Az előfizetést a Azure Key Vault panelen a **kulcs kiválasztásával** automatikusan kitölti a rendszer. A **Key Vault** esetében választhat egy meglévő kulcstartót a legördülő listából.

    ![Azure Key Vault kiválasztása vagy létrehozása](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Új kulcstartó létrehozásához az **új létrehozása** lehetőséget is választhatja. A **Key Vault létrehozása** panelen adja meg az erőforráscsoportot és a kulcstároló nevét. Fogadja el az összes többi alapértelmezett beállítást. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

    ![Új Azure Key Vault létrehozása](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Tekintse át a kulcstartóhoz tartozó információkat, és válassza a **Létrehozás** lehetőséget. Várjon pár percet, amíg a Key Vault létrehozása befejeződik.

    ![Azure Key Vault létrehozása](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. A **válasszon kulcsot Azure Key Vault közül** választhat egy kulcsot a meglévő kulcstartóban.

9. Ha létrehozott egy új kulcstartót, válassza az **új létrehozása** elemet a kulcs létrehozásához. Az RSA-kulcs mérete 2048 vagy nagyobb lehet.

    ![Új kulcs létrehozása Azure Key Vaultban](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Ha a helyreállítható törlési és kiürítési védelem nincs engedélyezve a kulcstartó létrehozásakor, a Key Vault frissül, hogy a rendszer a helyreállítható törlési és kiürítési védelmet engedélyezze.

10. Adja meg a kulcs nevét, fogadja el a többi alapértelmezett értéket, majd válassza a **Létrehozás** lehetőséget.

    ![Új kulcs létrehozása](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Válassza ki a **verziót** , majd válassza a **kiválasztás** lehetőséget. Értesítést kap arról, hogy a Key vaultban létrejön egy kulcs.

    ![A Key vaultban létrehozott új kulcs](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

A **titkosítás** panelen megtekintheti a Key vaultot és az ügyfél által felügyelt kulcshoz kiválasztott kulcsot.

> [!IMPORTANT]
> Csak a Microsoft által felügyelt kulcsokat tilthatja le, és az importálási/exportálási feladatok bármely szakaszában áthelyezheti az ügyfél által felügyelt kulcsokat. Az ügyfél által felügyelt kulcs azonban a létrehozása után nem tiltható le.

## <a name="troubleshoot-customer-managed-key-errors"></a>Ügyfél által felügyelt kulcsokkal kapcsolatos hibák elhárítása

Ha az ügyfél által felügyelt kulccsal kapcsolatos hibákat kap, a hibaelhárításhoz használja az alábbi táblázatot:

| Hibakód     |Részletek     | Helyreállítható?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | A rendszer visszavonja az ügyfél által felügyelt kulcs elérését.                                                       | Igen, ellenőrizze, hogy: <ol><li>A Key Vault továbbra is az MSI szerepel a hozzáférési házirendben.</li><li>A hozzáférési házirendben engedélyezve van a Get, a wrap és a unwrap engedély.</li><li>Ha a Key Vault a tűzfal mögötti VNet van, ellenőrizze, hogy engedélyezve van-e a **Microsoft megbízható szolgáltatásainak engedélyezése** lehetőség.</li><li>Ellenőrizze, hogy a feladattípus MSI-je alaphelyzetbe lett-e állítva API-k `None` használatára.<br>Ha igen, állítsa vissza az értéket a következőre: `Identity = SystemAssigned` . Ezzel újra létrehozza a feladatok erőforrásának identitását.<br>Miután létrehozta az új identitást, engedélyezte, `Get` `Wrap` és engedélyt kapott az `Unwrap` új identitásra a Key Vault hozzáférési házirendjében</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | Az ügyfél által felügyelt kulcs le van tiltva.                                         | Igen, a kulcs verziójának engedélyezésével     |
| CmkErrorKeyNotFound      | Nem található az ügyfél által felügyelt kulcs. | Igen, ha a kulcsot törölték, de még mindig a kiürítés időtartama alatt van, a Key [Vault-kulcs eltávolításának visszavonása](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)funkcióval.<br>Más <ol><li>Igen, ha az ügyfél rendelkezik a kulcs biztonsági mentésével, és visszaállítja azt.</li><li>Nem, ellenkező esetben.</li></ol>
| CmkErrorVaultNotFound |Nem található az ügyfél által felügyelt kulcs kulcstartója. |   Ha a Key Vault törölve lett:<ol><li>Igen, ha a kiürítés-védelem időtartama alatt van, a [Key Vault helyreállításának](../key-vault/general/soft-delete-overview.md#key-vault-recovery)lépéseit követve.</li><li>Nem, ha meghaladja a kiürítési-védelmi időtartamot.</li></ol><br>Ha a kulcstartó másik bérlőre lett áttelepítve, igen, az alábbi lépések egyikével állítható helyre:<ol><li>A Key Vault visszaállítása a régi bérlőre.</li><li>Állítsa be `Identity = None` , majd állítsa vissza az értéket `Identity = SystemAssigned` . Ezzel törli és újból létrehozza az identitást az új identitás létrehozása után. A `Get` `Wrap` `Unwrap` Key Vault hozzáférési házirendjében engedélyezze az új identitás engedélyezését és engedélyeit.</li></ol>|

## <a name="next-steps"></a>Következő lépések

- [Mi az Azure Key Vault](../key-vault/general/overview.md)?