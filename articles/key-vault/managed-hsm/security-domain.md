---
title: Tudnivalók az Azure Managed HSM biztonsági tartományáról
description: A felügyelt HSM biztonsági tartomány áttekintése, a felügyelt HSM helyreállításához szükséges alapvető hitelesítő adatok halmaza
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: amitbapat
ms.author: ambapat
ms.date: 09/15/2020
ms.openlocfilehash: 37e2541d0b53c96fd3f85da31b2c0ce5b68b551a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90995540"
---
# <a name="about-the-managed-hsm-security-domain"></a>Tudnivalók a felügyelt HSM biztonsági tartományról

A felügyelt HSM biztonsági tartomány (SD) a felügyelt HSM helyreállításához szükséges alapvető hitelesítő adatok halmaza, ha van katasztrófa. A biztonsági tartományt a rendszer a felügyelt HSM hardver és a szolgáltatás szoftver enklávéjában hozza létre, és a HSM tulajdonosának felel meg.

Minden felügyelt HSM-nek működnie kell egy biztonsági tartománnyal. Amikor új felügyelt HSM-t kér, azt a rendszer kiépíti, de csak a biztonsági tartomány letöltése után aktiválja. Ha egy felügyelt HSM kiosztott állapotban van, de nincs aktiválva, akkor a következő két módon aktiválható:
- A biztonsági tartomány letöltése az alapértelmezett módszer, amely lehetővé teszi, hogy biztonságosan tárolja a biztonsági tartományt egy másik felügyelt HSM-sel való használathoz, vagy egy teljes katasztrófa után helyreállítsa azt.
- Töltsön fel egy már meglévő biztonsági tartományt, amely lehetővé teszi, hogy több felügyelt HSM-példányt hozzon létre, amelyek ugyanazt a biztonsági tartományt használják.

## <a name="download-your-security-domain"></a>Biztonsági tartomány letöltése

Ha felügyelt HSM van kiépítve, de nincs aktiválva, a biztonsági tartomány letöltésével rögzítheti a helyreállításhoz szükséges alapvető hitelesítő adatokat az összes hardver teljes elvesztése miatt. A biztonsági tartomány letöltéséhez létre kell hoznia legalább 3 (legfeljebb 10) RSA-kulcspárt, és el kell küldenie ezeket a nyilvános kulcsokat a szolgáltatásnak a biztonsági tartomány letöltésének kérésekor. Emellett meg kell adnia a szükséges kulcsok minimális számát (kvórum) a biztonsági tartomány későbbi visszafejtéséhez. A felügyelt HSM inicializálja a biztonsági tartományt, és titkosítja azokat az Ön által megadott nyilvános kulcsokkal, amelyeket a rendszer az a [titkos megosztási algoritmussal](https://dl.acm.org/doi/10.1145/359168.359176)használ. A letöltött biztonsági tartomány blobját csak akkor lehet visszafejteni, ha a titkos kulcsok legalább kvóruma elérhető; a titkos kulcsokat biztonságosan kell megőrizni a biztonsági tartomány biztonságának biztosítása érdekében. Ha a letöltés befejeződött, a felügyelt HSM a használatra kész állapotban lesz.  

> [!IMPORTANT]
> A teljes vész-helyreállításhoz rendelkeznie kell a biztonsági tartománnyal, valamint a védelemhez használt titkos kulcsok kvórumával, valamint egy teljes HSM biztonsági mentéssel. Ha elveszíti a biztonsági tartományt vagy az RSA-kulcsok (titkos kulcs) számára a kvórum elvesztését, és a felügyelt HSM egyik futó példánya sem létezik, a vész-helyreállítás nem lesz lehetséges.

## <a name="upload-a-security-domain"></a>Biztonsági tartomány feltöltése

Ha egy felügyelt HSM üzembe lett kiépítve, de nincs aktiválva, akkor kezdeményezheti a biztonsági tartomány helyreállítási folyamatát. A felügyelt HSM létrehozza az RSA kulcspár-kulcspárt, és visszaküldi a nyilvános kulcsot. Ezután feltöltheti a biztonsági tartományt a felügyelt HSM-be. A feltöltés előtt meg kell adni az ügyfelet (az Azure CLI-t vagy a PowerShellt) a biztonsági tartomány letöltésekor használt titkos kulcsok minimális kvórumával. Az ügyfél ezt a kvórumot használva visszafejti a biztonsági tartományt, majd újra titkosítja a helyreállítást kérő nyilvános kulccsal. A feltöltés befejezése után a felügyelt HSM aktivált állapotban lesz.

## <a name="backup-and-restore-behavior"></a>Biztonsági mentési és visszaállítási viselkedés

A biztonsági másolatok (teljes biztonsági mentés vagy egyetlen kulcs biztonsági mentése) csak akkor állíthatók vissza sikeresen, ha a forrás felügyelt HSM (a biztonsági másolat létrehozásakor) és a cél felügyelt HSM (ahol a biztonsági mentés vissza lesz állítva) ugyanazzal a biztonsági tartománnyal osztozik. Ily módon a biztonsági tartomány minden felügyelt HSM esetében meghatározza a titkosítási határt is.

## <a name="next-steps"></a>Következő lépések

- [A Managed HSM áttekintése – áttekintés](overview.md)
- Tudnivalók a [felügyelt HSM Azure CLI-vel történő kezeléséről](key-management.md)
- A [felügyelt HSM ajánlott eljárásainak](best-practices.md) áttekintése
