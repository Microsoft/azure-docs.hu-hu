---
title: Azure Key Vault biztonsági világok | Microsoft Docs
description: Azure Key Vault egy több-bérlős szolgáltatás. Minden Azure-régióban HSM-készletet használ. Egy földrajzi régió minden régiója közös titkosítási határvonalon osztozik.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 0d82a3cb4c08d47b6827072378b9827037d32412
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751813"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault világok és földrajzi határok

Az Azure-termékek számos Azure-beli földrajzi helyen [érhetők el,](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)ahol minden Azure-beli földrajzi hely egy vagy több régiót tartalmaz. Az európai földrajzi terület például két régiót tartalmaz – Észak-Európa és Nyugat-Európa –, míg Brazília egyetlen földrajzi régiója Dél-Brazília.

Azure Key Vault egy több-bérlős szolgáltatás, amely hardveres biztonsági modulok (HSM-ek) készletét használja. Egy földrajzi helyen minden HSM azonos titkosítási határvonalon osztozik, amit "biztonsági világnak" is nevezzük. Minden földrajzi hely egyetlen biztonsági világnak felel meg, és fordítva.

Az USA keleti régiója és az USA nyugati régiója közös biztonsági világgal osztozik, mert a földrajzi helyhez (Egyesült Államok). Hasonlóképpen, japánban az összes Azure-régió ugyanazt a biztonsági világot használja, mint ausztráliában és így tovább.

>[!NOTE]
> Kivételt képez az, hogy az US DOD EAST és az US DOD CENTRAL saját biztonsági világokkal is fel van stb.

## <a name="backup-and-restore-behavior"></a>Biztonsági mentési és visszaállítási viselkedés

Az egyik Azure-régióban található kulcstartóból készült biztonsági másolat visszaállítható egy másik Azure-régióban található kulcstartóba, ha mindkét feltétel teljesül:

- Mindkét Azure-régió azonos földrajzi helyhez tartozik.
- Mindkét kulcstartó ugyanathoz az Azure-előfizetéshez tartozik.

Egy nyugat-indiai kulcstartó kulcsának biztonsági másolata például visszaállítható egy másik kulcstartóba ugyanabban az előfizetésben, amely India földrajzi régiójában található (Nyugat-India, Közép-India és Dél-India).

## <a name="next-steps"></a>Következő lépések

- [Microsoft-termékek régiók szerint](https://azure.microsoft.com/regions/services/)
