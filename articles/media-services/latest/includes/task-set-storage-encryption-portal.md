---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: c3a27218f03980e9b42a4a56fa739203c43754a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013275"
---
<!--Set the encryption on storage account in the portal-->

## <a name="set-the-encryption-on-a-storage-account"></a>Egy Storage-fiók titkosításának beállítása

1. A Azure Portal írja be a titkosítani kívánt Storage-fiók nevét a képernyő felső részén található **Keresés** mezőbe.  A találatok a keresőmező alatt jelennek meg.
1. Válassza ki a keresett Storage-fiókot. Ekkor megjelenik a Storage-fiók képernyője.
1. Válassza a **titkosítás** lehetőséget.
1. Válassza ki a Microsoft által felügyelt kulcsokat vagy az ügyfél által felügyelt kulcsokat.

### <a name="use-microsoft-managed-keys"></a>Microsoft által felügyelt kulcsok használata

Alapértelmezés szerint a Storage-fiókban lévő adatai a Microsoft által felügyelt kulcsok használatával vannak titkosítva.

### <a name="use-customer-managed-keys"></a>Felhasználó által kezelt kulcsok használata

1. Válassza ki az **ügyfél által felügyelt kulcsok** elemet.
1. Válassza ki a **kulcs URI-ja** vagy **válasszon a Key vaultból** lehetőséget.
    1. Ha a **kulcs URI azonosítójának megadása** lehetőséget választja, adja meg a kulcs URI-ját a kulcs URI mezőjében, és válassza ki az előfizetést. (Lehetséges, hogy már ki van választva.)
    1. Ha **a kiválasztás a Key vaultból** lehetőséget választja, akkor válassza ki **a Key Vault és a kulcs kiválasztása** lehetőséget. Ekkor megjelenik a Select kulcs a Azure Key Vault képernyőn.
1. Válassza ki a használni kívánt **Key Vault** , vagy válasszon ki egy már meglévő kulcsot a kulcstartóban, vagy **hozzon létre egy új kulcsot**.
    1. Ha új kulcs létrehozását választja, válassza a **Létrehozás** vagy **Importálás** **lehetőséget a beállítások** legördülő listából. Csak RSA-kulcsokat lehet importálni.
    1. Új kulcs létrehozásához adja meg a kulcs nevét a **név** mezőben, majd válassza ki a kulcs típusát:
        1. RSA-kulcs méretei: 2048, 3072 vagy 4096. A legtöbb ügyfél választ.
        1. EC-elliptikus görbék nevei: P-256, P-384, P-521 vagy P-256K
        1. Igény szerint beállíthatja a kulcs aktiválási és lejárati dátumát.
        1. Válassza az **Igen** lehetőséget az automatikus kulcs elforgatásának engedélyezéséhez.
        1. Válassza a **Létrehozás** lehetőséget.
    1. Egy kulcs importálásához válassza ki a feltölteni kívánt fájlt a **fájl kiválasztása mezőben** bárhová kattintva.
        1. Adjon nevet a kulcsnak a **név** mezőben.
        1. Igény szerint beállíthatja a kulcs aktiválási és lejárati dátumát.
        1. Válassza az **Igen** lehetőséget az automatikus kulcs elforgatásának engedélyezéséhez.
        1. Válassza a **Létrehozás** lehetőséget.
    1. Válassza a **kijelölés** lehetőséget, hogy kiválassza ezt a kulcsot a Storage-fiók titkosításához. Vissza fog térni a titkosítási képernyőre.
1. **FONTOS!** A **Mentés** gombra kattintva mentheti a titkosítási beállításokat, vagy mindent, amit most elvesznek.
