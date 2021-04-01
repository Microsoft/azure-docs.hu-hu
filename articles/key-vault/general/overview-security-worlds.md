---
title: Azure Key Vault biztonsági világok | Microsoft Docs
description: Azure Key Vault egy több-bérlős szolgáltatás. HSM-készletet használ minden egyes Azure-helyen. A földrajzi régió összes helye egy titkosítási határt osztozik.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 07/03/2017
ms.openlocfilehash: d21deea13aac3d40c452a183c340d3108a1a01f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936328"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault biztonsági világok és földrajzi határok

Azure Key Vault egy több-bérlős szolgáltatás, és hardveres biztonsági modulok (HSM-EK) készletét használja minden egyes Azure-helyen. 

Az ugyanabban a földrajzi régióban található Azure-helyeken lévő összes HSM ugyanazokkal a titkosítási határokkal (Thales biztonsági világban) osztozik. Például az USA keleti régiója és az USA nyugati régiója ugyanazon a biztonsági világban osztoznak, mert az Egyesült államokbeli földrajzi helyhez tartoznak. Hasonlóképpen, a Japánban található összes Azure-beli telephely ugyanazt a biztonsági világot és az összes Azure-helyet is megosztja Ausztráliában, Indiában és így tovább. 

## <a name="backup-and-restore-behavior"></a>Biztonsági mentési és visszaállítási viselkedés

Egy Azure-beli kulcstartóból származó kulcs biztonsági mentése egy másik Azure-helyen található kulcstartóra állítható vissza, feltéve, hogy mindkét feltétel teljesül:

- Mindkét Azure-beli hely ugyanahhoz a földrajzi helyhez tartozik
- Mindkét kulcstartó ugyanahhoz az Azure-előfizetéshez tartozik

Például a Key vaultban egy kulcs egy adott előfizetése által készített biztonsági másolat csak egy másik kulcstartóba állítható vissza ugyanazon az előfizetésen belül és a földrajzi helyen; Nyugat-India, Közép-India vagy Dél-India.

## <a name="regions-and-products"></a>Régiók és termékek

- [Azure-régiók](https://azure.microsoft.com/regions/)
- [Microsoft-termékek régiónként](https://azure.microsoft.com/regions/services/)

A régiók a biztonsági világok számára vannak leképezve, és a táblákban nagyobb fejlécek jelennek meg:

A Products By Region cikkben például az **amerikai** Egyesült Államok lap az USA keleti régiója, az USA középső régiója, az USA nyugati régiója és az Amerikai Egyesült Államok területének összes leképezését tartalmazza. 

>[!NOTE]
>Kivételt jelent, hogy az USA DOD keleti régiója és az USA-beli DOD CENTRAL a saját biztonsági világával rendelkezik. 

Hasonlóképpen, az **Európa** lapon az Észak-és Nyugat-európai régió egyaránt az Európa régiójához tartozik. Ugyanez igaz a **Ázsia és a csendes-óceáni térség** lapon is.
