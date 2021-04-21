---
title: Kulcstartó áthelyezése egy másik régióba – Azure Key Vault | Microsoft Docs
description: Ez a cikk útmutatást nyújt a kulcstartó másik régióba való áthelyezésére vonatkozóan.
services: key-vault
author: msmbaldwin
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: ac2f6347776c2f5d230065b80b1c0336e21e181c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751795"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Azure Key Vault áthelyezése régiók között

Azure Key Vault nem támogatja az olyan erőforrás-áthelyezési műveleteket, amelyek lehetővé teszik a kulcstartók egyik régióból a másikba való áthelyezését. Ez a cikk áthidaló megoldásokat tartalmaz olyan szervezetek számára, amelyek üzleti szükségük van egy kulcstartó másik régióba való áthelyezésére. Minden áthidaló megoldás korlátozásokkal rendelkezik. Az éles környezetben való alkalmazásuk előtt rendkívül fontos megérteni ezeknek a megkerülő megoldásoknak a következményeit.

A kulcstartó másik régióba való áthelyezéshez létre kell hoznia egy kulcstartót abban a másik régióban, majd manuálisan kell átmásolnia az egyes titkos kulcsokat a meglévő kulcstartóból az új kulcstartóba. Ezt az alábbi két lehetőség egyikével is meg lehet tenni.

## <a name="design-considerations"></a>Kialakítási szempontok

Mielőtt hozzákezd, tartsa szem előtt az alábbi fogalmakat:

* A kulcstartók nevei globálisan egyediek. A tároló nevét nem használhatja újra.
* Újra kell konfigurálnia a hozzáférési szabályzatokat és a hálózati konfigurációs beállításokat az új kulcstartóban.
* Újra kell konfigurálnia a soft-delete és a purge elleni védelmet az új kulcstartóban.
* A biztonsági mentési és visszaállítási művelet nem őrzi meg az automatikus beállításokat. Előfordulhat, hogy újra kell konfigurálnia a beállításokat.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>1. lehetőség: A Key Vault biztonsági mentési és visszaállítási parancsának használata

A biztonsági mentési paranccsal biztonsági másolatot készít a tárolóban tárolt egyes titkos kulcsokról, kulcsokról és tanúsítványokról. A titkos kulcsok titkosított blobként vannak letöltve. Ezután visszaállíthatja a blobot az új kulcstartóba. A parancsok listájáért lásd: Azure Key Vault [parancsokat.](/powershell/module/azurerm.keyvault#key_vault)

A biztonsági mentési és visszaállítási parancsok használatának két korlátozása van:

* Egy kulcstartóról nem lehet biztonsági mentést állítani egy adott földrajzi helyen, és visszaállítani egy másik földrajzi helyen. További információ: [Azure-beli földrajzi területek.](https://azure.microsoft.com/global-infrastructure/geographies/)

* A biztonsági mentési parancs az egyes titkos kódok minden verziójáról biztonsági másolatot készít. Ha egy titkos kódnak nagy számú (10-nél több) korábbi verziója van, a kérelem mérete meghaladhatja a maximálisan megengedett méretet, és a művelet sikertelen lehet.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>2. lehetőség: Kulcstartó titkos kulcsának manuális letöltése és feltöltése

Bizonyos titkos adatokat manuálisan is letölthet. Letöltheti például a tanúsítványokat PFX-fájlként. Ez a beállítás kiküszöböli bizonyos titkos típusok, például tanúsítványok földrajzi korlátozásait. A PFX-fájlokat bármely régió bármelyik kulcstartóba feltöltheti. A titkos kulcsok letöltése nem jelszóval védett formátumban. A titkos kulcsok biztonságossá tétele az áthelyezés során Az Ön felelőssége.