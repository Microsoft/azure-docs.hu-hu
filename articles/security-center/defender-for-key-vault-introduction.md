---
title: Azure Defender for Key Vault – az előnyök és funkciók
description: Ismerje meg a Key Vault Azure Defender előnyeit és funkcióit.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: b1dd559db2d736fa27de2884d2fe82edaaeaaf3c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916642"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>A Key Vault Azure Defender bemutatása

A Azure Key Vault egy felhőalapú szolgáltatás, amely védelmet biztosít a titkosítási kulcsok és a titkok, például a tanúsítványok, a kapcsolatok karakterláncai és a jelszavak számára. 

Az Azure **Defender Key Vault** Azure-natív, komplex veszélyforrások elleni védelemhez Azure Key Vault, amely egy további biztonsági intelligencia réteget biztosít. 

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|Az **Azure Defender for Key Vault** számlázása [a díjszabási oldalon](security-center-pricing.md) látható|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![No](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Milyen előnyökkel jár az Azure Defender a Key Vault?

Az Azure Defender szokatlan és potenciálisan ártalmas kísérleteket észlel Key Vault fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi, hogy biztonsági szakértő nélkül kezeljék a fenyegetéseket, és nincs szükség a harmadik féltől származó biztonsági figyelő rendszerek felügyeletére.  

Ha rendellenes tevékenységek történnek, az Azure Defender riasztásokat jelenít meg, és opcionálisan e-mailben küldi el őket a szervezete megfelelő tagjainak. Ezek a riasztások tartalmazzák a gyanús tevékenység részleteit, valamint a fenyegetések kivizsgálásával és szervizelésével kapcsolatos javaslatokat. 

## <a name="azure-defender-for-key-vault-alerts"></a>Azure Defender Key Vault-riasztásokhoz
Ha a Key Vault Azure Defender által küldött riasztást kap, javasoljuk, hogy vizsgálja meg a riasztást, és válaszoljon az [Azure Defender for Key Vault](defender-for-key-vault-usage.md)című témakörben leírtak szerint. Az Azure Defender for Key Vault megvédi az alkalmazásokat és a hitelesítő adatokat, így még akkor is, ha már ismeri a riasztást kiváltó alkalmazást vagy felhasználót, fontos, hogy ellenőrizze az összes riasztást körülvevő helyzetet.

A riasztások a Key Vault **Biztonság** lapján, az Azure Defender irányítópultján és Security Center riasztások lapján jelennek meg.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Azure Key Vault biztonsági oldala":::


> [!TIP]
> Az Azure Defender Key Vault riasztásokhoz való szimulálása érdekében kövesse az [Azure Key Vault veszélyforrások észlelésének érvényesítése Azure Security Centerban](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)című témakör utasításait.


## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan Key Vault Azure Defendert.

A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket: 

- [Key Vault biztonsági riasztások](alerts-reference.md#alerts-azurekv)– az összes Azure Security Center riasztáshoz tartozó hivatkozási tábla Key Vault szakasza
- [Security Center-adatfeldolgozás folyamatos exportálása](continuous-export.md)
- [Riasztások letiltása az Azure Defenderből](alerts-suppression-rules.md)