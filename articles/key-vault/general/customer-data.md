---
title: Azure Key Vault adatokkal kapcsolatos funkciók – Azure Key Vault | Microsoft Docs
description: Megismerheti az ügyféladatokat, Azure Key Vault tárolók, kulcsok, titkos kulcsok, tanúsítványok és felügyelt tárfiókok létrehozása vagy frissítése során kapják meg.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 4d45c019a6ba073d7553c09784736959faf89d27
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749779"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault adatok funkcióinak használata

Azure Key Vault ügyféladatokat fogad a tárolók, felügyelt HSM-készletek, kulcsok, titkos kulcsok, tanúsítványok és felügyelt tárfiókok létrehozása vagy frissítése során. Ezek az ügyféladatok közvetlenül láthatók a Azure Portal a REST API. Az ügyféladatok az adatokat tartalmazó objektum frissítésével vagy törlésével szerkeszthetők vagy törölhetők.

A rendszer-hozzáférési naplók akkor jönnek létre, amikor egy felhasználó vagy alkalmazás Key Vault. A részletes hozzáférési naplók az Azure Insights segítségével érhetők el az ügyfelek számára.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Ügyféladatok azonosítása

Az alábbi információk az ügyféladatokat azonosítják a Azure Key Vault:

- A felhasználókra Azure Key Vault házirendek a felhasználókat, csoportokat vagy alkalmazásokat képviselő objektum-adatbázisokat tartalmaznak
- A tanúsítványban érintett személyek e-mail-címeket vagy más felhasználói vagy szervezeti azonosítókat tartalmazhatnak
- A tanúsítvány-kapcsolattartók tartalmazhatnak felhasználói e-mail-címeket, neveket vagy telefonszámokat
- A tanúsítványkiállítók tartalmazhatnak e-mail-címeket, neveket, telefonszámokat, fiók hitelesítő adatait és szervezeti adatokat
- Tetszőleges címkék alkalmazhatók az objektumokra a Azure Key Vault. Ezek az objektumok lehetnek tárolók, kulcsok, titkos kulcsok, tanúsítványok és tárfiókok. A használt címkék személyes adatokat tartalmazhatnak
- Azure Key Vault hozzáférési naplók objektum-, [UPN-eket](../../active-directory/hybrid/plan-connect-userprincipalname.md)és IP-címeket tartalmaznak minden REST API híváshoz
- Azure Key Vault diagnosztikai naplók objektum- és IP-címeket tartalmazhatnak a REST API hívásokhoz

## <a name="deleting-customer-data"></a>Ügyféladatok törlése

A tárolók, kulcsok, titkos kulcsok, tanúsítványok és felügyelt tárfiókok létrehozásához használt REST API-k, a portál felhasználói élménye és a felügyelt tárfiókok szintén frissíthetőek és törölhetők.

A helyreállítható törléssel a törlést követően 90 napig helyreállíthatóak a törölt adatok. Az ideiglenes törlés használata esetén az adatok véglegesen törlődhetnek a 90 napos megőrzési időszak lejárta előtt egy végleges törlési művelettel. Ha a tároló vagy előfizetés úgy lett konfigurálva, hogy letiltsa a végleges törlési műveleteket, nem lehet véglegesen törölni az adatokat az ütemezett megőrzési időszak végéig.

## <a name="exporting-customer-data"></a>Ügyféladatok exportálása

A tárolók, kulcsok, titkos kulcsok, tanúsítványok és felügyelt tárfiókok létrehozásához használt REST API-k, portál-felhasználói élmény és SDK-k lehetővé teszik ezen objektumok megtekintését és exportálását is.

Azure Key Vault hozzáférés-naplózás egy választható funkció, amely bekapcsolható a naplók létrehozásához minden REST API híváshoz. Ezeket a naplókat a rendszer az előfizetés egyik tárfiókjára továbbítja, ahol alkalmazza a szervezet követelményeinek megfelelő adatmegőrzési szabályzatot.

Azure Key Vault személyes adatokat tartalmazó diagnosztikai naplók lekéréséhez exportálási kérelmet kell bekérni a felhasználói adatvédelmi portálon. Ezt a kérést a bérlői rendszergazdának kell kérelmeznie.

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault naplózás](logging.md)

- [Azure Key Vault: a helyreállítható törlés áttekintése](./key-vault-recovery.md)

- [Azure Key Vault kulcsműveletek](/rest/api/keyvault/key-operations)

- [Azure Key Vault titkos műveletek](/rest/api/keyvault/secret-operations)

- [Azure Key Vault tanúsítványok és házirendek használata](/rest/api/keyvault/certificates-and-policies)

- [Azure Key Vault tárfiókműveletek](/rest/api/keyvault/storage-account-key-operations)