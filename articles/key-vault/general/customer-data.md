---
title: Azure Key Vault ügyféladatok funkciói – Azure Key Vault | Microsoft Docs
description: Ismerje meg a vásárlók adatait, amelyeket a tárolók, kulcsok, titkok, tanúsítványok és felügyelt Storage-fiókok létrehozása vagy frissítése során Azure Key Vault fogad.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 8806ff41edabcd3c0875d3c02360dc9a275e3878
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460831"
---
# <a name="azure-key-vault-customer-data-features"></a>Az ügyféladatok funkcióinak Azure Key Vault

A Azure Key Vault a tárolók, a felügyelt HSM-készletek, kulcsok, titkok, tanúsítványok és felügyelt Storage-fiókok létrehozása vagy frissítése során fogadja az ügyféladatokat. Ez az ügyféladatok közvetlenül látható a Azure Portalban és a REST APIon keresztül. Az ügyféladatokat szerkesztheti vagy törölheti az azokat tartalmazó objektum frissítésével vagy törlésével.

A rendszer-hozzáférési naplók akkor jönnek létre, amikor egy felhasználó vagy alkalmazás hozzáfér Key Vaulthoz. A részletes hozzáférési naplók az Azure-elemzéseket használó ügyfelek számára érhetők el.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Ügyféladatok azonosítása

Az alábbi információk a Azure Key Vaulton belüli ügyféladatokat azonosítják:

- A Azure Key Vault vonatkozó hozzáférési szabályzatok a felhasználókat, csoportokat vagy alkalmazásokat képviselő objektum-azonosítókat tartalmaznak.
- A tanúsítvány tárgya tartalmazhat e-mail-címeket vagy más felhasználói vagy szervezeti azonosítókat
- A tanúsítványhoz tartozó névjegyek felhasználói e-mail-címeket, neveket és telefonszámokat tartalmazhatnak.
- A tanúsítvány-kiállítók tartalmazhatnak e-mail-címeket, neveket, telefonszámokat, a fiók hitelesítő adatait és a szervezeti adatokat.
- A Azure Key Vaultban lévő objektumokra tetszőleges címkéket alkalmazhat. Ezek az objektumok a tárak, kulcsok, titkok, tanúsítványok és Storage-fiókok közé tartoznak. A használt címkék személyes adattartalmakat is tartalmazhatnak
- Azure Key Vault hozzáférési naplók az egyes REST API hívásokhoz tartalmazhatnak objektum-azonosítókat, [UPN](../../active-directory/hybrid/plan-connect-userprincipalname.md)-ket és IP-címeket.
- Azure Key Vault diagnosztikai naplók tartalmazhatnak objektum-azonosítókat és IP-címeket REST API hívásokhoz

## <a name="deleting-customer-data"></a>Ügyféladatok törlése

Ugyanezek a REST API-k, portálok és a tárolók, kulcsok, titkok, tanúsítványok és felügyelt Storage-fiókok létrehozásához használt SDK-k is képesek frissíteni és törölni ezeket az objektumokat.

A Soft-delete lehetővé teszi a törölt adatok helyreállítását a törlés után 90 nappal. A helyreállítható törlés használatakor az adatok véglegesen törlődnek a 90 napos megőrzési időszak lejárta előtt, a törlési művelet végrehajtásával. Ha a tár vagy előfizetés úgy lett konfigurálva, hogy blokkolja a kiürítési műveleteket, nem lehet véglegesen törölni az adattárolást, amíg az ütemezett megőrzési időszak el nem telik.

## <a name="exporting-customer-data"></a>Ügyféladatok exportálása

Ugyanazok a REST API-k, portálok és SDK-k, amelyek a tárolók, kulcsok, titkos kódok, tanúsítványok és felügyelt tárolási fiókok létrehozásához használatosak, lehetővé teszik ezen objektumok megtekintését és exportálását is.

Azure Key Vault hozzáférés naplózása egy opcionális funkció, amely bekapcsolható a naplók létrehozásához az egyes REST API hívásokhoz. Ezek a naplók egy olyan Storage-fiókba lesznek továbbítva az előfizetésben, amelyben a szervezet igényeinek megfelelő adatmegőrzési szabályt alkalmazza.

Azure Key Vault személyes adatokat tartalmazó diagnosztikai naplókat az exportálási kérelem a felhasználói adatvédelmi portálon történő beolvasásával lehet lekérni. Ezt a kérelmet a bérlői rendszergazdának kell elvégeznie.

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault naplózás](logging.md)

- [Azure Key Vault: a helyreállítható törlés áttekintése](./key-vault-recovery.md)

- [Azure Key Vault kulcsfontosságú műveletek](/rest/api/keyvault/key-operations)

- [Azure Key Vault titkos műveletek](/rest/api/keyvault/secret-operations)

- [Tanúsítványok és házirendek Azure Key Vault](/rest/api/keyvault/certificates-and-policies)

- [Azure Key Vault Storage-fiók műveletei](/rest/api/keyvault/storage-account-key-operations)