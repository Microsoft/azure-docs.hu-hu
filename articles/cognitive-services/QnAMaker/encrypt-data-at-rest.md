---
title: QnA Maker inaktív adatok titkosítása
titleSuffix: Azure Cognitive Services
description: A Microsoft felügyeli a Microsoft által kezelt titkosítási kulcsokat, valamint lehetővé teszi az Cognitive Services-előfizetések kezelését a saját kulcsaival, az úgynevezett ügyfél által felügyelt kulcsokkal (CMK). Ez a cikk a QnA Maker inaktív adatok titkosítását, valamint a CMK engedélyezését és felügyeletét ismerteti.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 19dc0f3a676d5373b28e4b7055050477c426f847
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100524461"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker inaktív adatok titkosítása

A QnA Maker automatikusan titkosítja az adatait, ha a felhőben marad, így segítve a szervezeti biztonsági és megfelelőségi célok teljesítését.

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

Alapértelmezés szerint az előfizetés a Microsoft által felügyelt titkosítási kulcsokat használja. Az előfizetést az ügyfél által felügyelt kulcsok (CMK) nevű saját kulcsokkal is kezelheti. A CMK nagyobb rugalmasságot biztosít a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Továbbá az adatok védelméhez használt titkosítási kulcsok naplózására is lehetősége van. Ha az előfizetéshez CMK van konfigurálva, a rendszer dupla titkosítást biztosít, amely második védelmi réteget kínál, miközben lehetővé teszi a titkosítási kulcs vezérlését a Azure Key Vaulton keresztül.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

QnA Maker az Azure Search CMK-támogatását használja. [A CMK konfigurálása a Azure Search Azure Key Vault használatával](../../search/search-security-manage-encryption-keys.md). Ezt az Azure-példányt hozzá kell rendelni QnA Maker szolgáltatáshoz, hogy a CMK engedélyezve legyen.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

QnA Maker az [Azure Search CMK-támogatását](../../search/search-security-manage-encryption-keys.md)használja, és automatikusan társítja a megadott CMK az Azure Search-indexben tárolt adatok titkosításához.

---

> [!IMPORTANT]
> A Azure Search Service-erőforrást a január 2019 után kell létrehozni, és nem lehet ingyenes (megosztott) szinten. Nem támogatott az ügyfél által felügyelt kulcsok konfigurálása a Azure Portalban.

## <a name="enable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok engedélyezése

A QnA Maker szolgáltatás a CMK használja a Azure Search szolgáltatásból. A CMKs engedélyezéséhez kövesse az alábbi lépéseket:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

1. Hozzon létre egy új Azure Search példányt, és engedélyezze az [Azure Cognitive Search ügyfél által felügyelt legfontosabb előfeltételeiben](../../search/search-security-manage-encryption-keys.md#prerequisites)említett előfeltételeket.

   ![1. titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-1.png)

2. QnA Maker-erőforrás létrehozásakor a rendszer automatikusan egy Azure Search-példánnyal társítja. Ez a példány nem használható a CMK. A CMK használatához hozzá kell rendelnie Azure Search újonnan létrehozott példányát, amelyet az 1. lépésben hoztak létre. Pontosabban frissítenie kell a és a `AzureSearchAdminKey` `AzureSearchName` QnA Maker erőforrást.

   ![2. titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Ezután hozzon létre egy új Alkalmazásbeállítás-beállítást:
   * **Név**: beállítás `CustomerManagedEncryptionKeyUrl`
   * **Érték**: használja az 1. lépésben az Azure Search-példány létrehozásakor kapott értéket.

   ![3. titkosítási beállítások megtekintése](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Ha elkészült, indítsa újra a futtatókörnyezetet. A QnA Maker szolgáltatás most már CMK-kompatibilis.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

1.  Nyissa meg a QnA Maker felügyelt (előzetes verzió) szolgáltatás **titkosítás** lapját.
2.  Válassza ki az **ügyfél által felügyelt kulcsok** lehetőséget. Adja meg az [ügyfél által felügyelt kulcsok](../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) részleteit, és kattintson a **Save (Mentés**) gombra.

     :::image type="content" source="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png" alt-text="QnA Maker felügyelt (előzetes verzió) CMK-beállítás" lightbox="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png":::

3.  A sikeres mentéskor a rendszer a CMK fogja használni a Azure Search indexben tárolt adatfájlok titkosítására.

> [!IMPORTANT]
> Javasoljuk, hogy a tudásbázisok létrehozása előtt állítsa be a CMK egy friss Azure Cognitive Search szolgáltatásban. Ha a CMK-t egy olyan QnA Maker-szolgáltatásban állítja be, amely már rendelkezik tudásbázissal, akkor a hozzáférés megszakadhat. További információ a [titkosított tartalmak](../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content) használatáról az Azure-beli kognitív keresésekben.

> [!NOTE]
> Az ügyfél által felügyelt kulcsok használatának kérelmezéséhez töltse ki és küldje el a [Cognitive Services Customer-Managed Key Request (kérelem) űrlapot](https://aka.ms/cogsvc-cmk).

---

## <a name="regional-availability"></a>Regionális elérhetőség

Az ügyfél által felügyelt kulcsok minden Azure Search régióban elérhetők.

## <a name="encryption-of-data-in-transit"></a>Az adatforgalom titkosítása

QnA Maker portál a felhasználó böngészőjében fut. Minden művelet egy közvetlen hívást indít a megfelelő kognitív szolgáltatás API-hoz. Ezért a QnA Maker megfelel az átvitt adatforgalomnak.
Mivel azonban a QnA Maker portál szolgáltatás az USA nyugati régiójában található, még mindig nem ideális a nem USA-beli ügyfelek számára. 

## <a name="next-steps"></a>Következő lépések

* [Titkosítás Azure Search a CMKs használatával Azure Key Vault](../../search/search-security-manage-encryption-keys.md)
* [Adat-titkosítás inaktív állapotban](../../security/fundamentals/encryption-atrest.md)
* [További információ a Azure Key Vault](../../key-vault/general/overview.md)