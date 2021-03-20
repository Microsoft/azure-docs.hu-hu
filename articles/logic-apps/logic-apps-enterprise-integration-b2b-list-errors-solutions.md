---
title: Gyakori hibák és problémák megoldása a B2B-forgatókönyvekben
description: Gyakori hibákkal és problémákkal kapcsolatos megoldások keresése a B2B-forgatókönyvek hibaelhárításakor Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 6400cfe7e524dcc16e08c2bba7dfba4a62d00b2e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86232559"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>B2B-hibák és-megoldások a Azure Logic Apps

Ez a cikk segítséget nyújt a Logic Apps B2B forgatókönyvekben előforduló hibák elhárításában, és a hibák kijavításához szükséges lépéseket javasolja.

## <a name="agreement-resolution"></a>Szerződés feloldása

### <a name="no-agreement-found"></a>Nem található szerződés 

**Hiba leírása**: nem található szerződés-feloldási paraméterekkel rendelkező megállapodás.

**Felhasználói művelet**: a megállapodást hozzá kell adni az integrációs fiókhoz az egyeztetett üzleti identitásokkal. Az üzleti identitásoknak egyezniük kell a bemeneti üzenet azonosítókkal.

### <a name="no-agreement-found-with-identities"></a>Nem található identitással rendelkező szerződés

**Hiba leírása**: nem található az identitásokkal kapcsolatos megállapodás: "AS2Identity":: "Partner1" and'AS2Identity "::" Partner3 "

**Felhasználói művelet**: érvénytelen AS2-From vagy AS2-To van konfigurálva a szerződéshez. Javítsa ki az AS2-üzenet "AS2-from" vagy "AS2-to" fejléceit, vagy a szerződést, hogy az AS2-azonosítók megfeleljenek az AS2-üzeneteknek a szerződés konfigurációjának megfelelően.

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Hiányzó AS2-üzenetek fejlécei  

**Hiba leírása**: érvénytelen AS2-fejlécek. A "AS2-to" vagy az "AS2-from" fejlécek egyike üres.

**Felhasználói művelet**: olyan AS2-üzenet érkezett, amely nem tartalmazott AS2-From vagy AS2-To vagy mindkét fejlécet. Győződjön meg arról, hogy az AS2-üzenet AS2-From és AS2-To fejléceket, és javítsa azokat a szerződés konfigurálása alapján.

### <a name="missing-as2-message-body-and-headers"></a>Hiányzó AS2-üzenet törzse és fejlécei    

**Hiba leírása**: a kérelem tartalma null értékű vagy üres.

**Felhasználói művelet**: olyan AS2-üzenet érkezett, amely nem tartalmazza az üzenet törzsét.

### <a name="as2-message-decryption-failure"></a>AS2-üzenetek visszafejtésének hibája

**Hiba leírása**: [feldolgozott/hiba: visszafejtés – sikertelen]

**Felhasználói művelet**: Hozzáadás @base64ToBinary a AS2Message a partnernek való küldés előtt.

Például:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>MDN-visszafejtési hiba

**Hiba leírása**: [feldolgozott/hiba: visszafejtés – sikertelen]

**Felhasználói művelet**: Hozzáadás @base64ToBinary a MDN a partnernek való küldés előtt.

Például:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Hiányzó aláíró tanúsítvány

**Hiba leírása**: az aláíró tanúsítvány nincs KONFIGURÁLVA az AS2-fél számára. AS2-from: Partner1 AS2-to: Partner2

**Felhasználói művelet**: az AS2-szerződés beállításainak konfigurálása megfelelő tanúsítvánnyal az aláíráshoz.

## <a name="x12-and-edifact"></a>X12 és EDIFACT

### <a name="leading-or-trailing-space-found"></a>A kezdő vagy záró terület található    

**Hiba leírása**: hiba történt az elemzés során. A (z) "123456" azonosítójú EDIFACT-tranzakció a (z) "987654" AZONOSÍTÓJÚ, a (z) "Partner1" azonosítójú, "Partner2" fogadó-azonosítóval rendelkező csomópontban a következő hibákkal van felfüggesztve: "vezető záró elválasztó"

**Felhasználói művelet**: a kezdő és záró terület engedélyezéséhez konfigurálandó szerződési beállítások. A szerződés beállításainak szerkesztése a kezdő és a záró terület engedélyezéséhez.

![szóköz engedélyezése](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Az ismétlődő ellenőrzési funkció engedélyezve van a szerződésben

**Hiba leírása**: duplikált vezérlőelem száma

**Felhasználói művelet**: Ez a hiba azt jelzi, hogy a fogadott üzenet duplikált számú vezérlőelemet tartalmaz. Javítsa ki a vezérlő számát, és küldje el újra az üzenetet.

### <a name="missing-schema-in-the-agreement"></a>Hiányzó séma a szerződésben

**Hiba leírása**: hiba történt az elemzés során. A (z) "56422" AZONOSÍTÓJÚ, "000056422" azonosítójú funkcionális csoportban található "564220001" azonosítójú X12-tranzakció a (z) "12345678" azonosítójú azonosítóval, a (z) "87654321" fogadó AZONOSÍTÓval a következő hibák miatt szünetel: "az üzenet ismeretlen dokumentumtípust tartalmaz, és nem oldotta meg a szerződésben konfigurált meglévő sémák egyikét sem"

**Felhasználói művelet**: séma konfigurálása a szerződés beállításaiban.

### <a name="incorrect-schema-in-the-agreement"></a>Helytelen séma a szerződésben

**Hiba leírása**: az üzenet ismeretlen dokumentumtípust tartalmaz, és nem oldotta meg a szerződésben konfigurált meglévő sémákat.

**Felhasználói művelet**: helyes séma konfigurálása a szerződés beállításaiban.

## <a name="flat-file"></a>Egybesimított fájl

### <a name="input-message-with-no-body"></a>Szövegtörzs nélküli bemeneti üzenet

**Hiba leírása**: InvalidTemplate. Nem lehet feldolgozni a sablon nyelvi kifejezéseit a (z) "Flat_File_Decoding" bemenetekben az "1" sor "1902" oszlopában: "a kötelező tulajdonság tartalma" értéknek null értékűnek kell lennie. Elérési út: "".

**Felhasználói művelet**: Ez a hiba azt jelzi, hogy a bemeneti üzenet nem tartalmaz törzset.
