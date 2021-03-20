---
title: Kulcstartó áthelyezése másik régióba – Azure Key Vault | Microsoft Docs
description: Ez a cikk útmutatást nyújt a kulcstartó más régióba való áthelyezéséhez.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/24/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another region.
ms.openlocfilehash: 54a1894c88d12cfac1a2d8062425de9549c4292e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93285227"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Azure Key Vault áthelyezése régiók között

A Azure Key Vault nem támogatja az erőforrás-áthelyezési műveletet, amely lehetővé teszi a kulcstartó áthelyezését az egyik régióból a másikba. Ez a cikk a Key Vault egy másik régióba való áthelyezését igénylő szervezetek megkerülő megoldásait ismerteti. Minden megkerülő megoldási beállítás korlátozásokkal rendelkezik. Fontos megérteni a megkerülő megoldások következményeit, mielőtt éles környezetben próbálja meg alkalmazni őket.

Ha egy kulcstartót másik régióba szeretne áthelyezni, hozzon létre egy kulcstartót a másik régióban, majd manuálisan másolja az egyes titkos kulcsokat a meglévő kulcstartóból az új kulcstartóba. Ezt az alábbi két lehetőség egyikével teheti meg.

## <a name="design-considerations"></a>Kialakítási szempontok

Mielőtt elkezdené, vegye figyelembe a következő fogalmakat:

* A Key Vault-nevek globálisan egyediek. A tár neve nem használható fel újra.
* A hozzáférési házirendeket és a hálózati konfigurációs beállításokat újra kell konfigurálnia az új kulcstartóban.
* Újra kell konfigurálnia a Soft-delete és a kiürítési védelmet az új kulcstartóban.
* A biztonsági mentési és visszaállítási művelet nem őrzi meg az automatikus elforgatás beállításait. Előfordulhat, hogy újra kell konfigurálnia a beállításokat.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>1. lehetőség: a Key Vault biztonsági mentési és visszaállítási parancsainak használata

A biztonsági mentési parancs használatával biztonsági mentést készíthet a tárolóban lévő egyes titkos kulcsokról, kulcsokról és tanúsítványokról. A titkos kódok titkosított blobként lesznek letöltve. Ezután visszaállíthatja a blobot az új kulcstartóba. A parancsok listáját itt tekintheti meg: [Azure Key Vault parancsok](/powershell/module/azurerm.keyvault/?view=azurermps-6.13.0#key_vault).

A biztonsági mentési és visszaállítási parancsok használata két korlátozást tartalmaz:

* Egy kulcstartóban nem lehet biztonsági másolatot készíteni egy adott földrajzi helyen, és visszaállíthatja egy másik földrajzi helyre. További információ: [Azure földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/)területek.

* A biztonsági mentési parancs az egyes titkos kódok minden verziójáról biztonsági másolatot készít. Ha egy titkos kódnak nagy számú (10-nél több) korábbi verziója van, a kérelem mérete meghaladhatja a maximálisan megengedett méretet, és a művelet sikertelen lehet.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>2. lehetőség: a Key Vault titkos kulcsainak manuális letöltése és feltöltése

Bizonyos titkos típusok manuálisan is letölthetők. Például a tanúsítványokat PFX-fájlként is letöltheti. Ez a beállítás kiküszöböli a földrajzi korlátozásokat bizonyos titkos típusok, például tanúsítványok esetén. A PFX-fájlokat bármely régióban lévő kulcstartóba feltöltheti. A titkokat nem jelszóval védett formátumban tölti le a rendszer. Az áthelyezés során Ön felelős a titkok védelméért.