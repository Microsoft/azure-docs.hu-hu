---
title: AS2-követési sémák B2B-üzenetekhez
description: Nyomkövetési sémák létrehozása az AS2-üzenetek figyeléséhez Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "76906971"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Sémák létrehozása az AS2-üzenetek követéséhez Azure Logic Apps

Ha segítségre van szüksége a vállalatok közötti (B2B) tranzakciók sikerességének, hibáinak és üzenetének figyeléséhez, használhatja az alábbi AS2-követési sémákat az integrációs fiókjában:

* AS2-üzenet követési sémája
* AS2-üzenetek törlésére vonatkozó értesítés (MDN) követési sémája

## <a name="as2-message-tracking-schema"></a>AS2-üzenet követési sémája

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | AS2-üzenet küldője partnerének neve |
| receiverPartnerName | Nem | Sztring | AS2-üzenet fogadójának partnere neve |
| as2To | Igen | Sztring | AS2-üzenet fogadójának neve az AS2-üzenet fejlécében |
| as2From | Igen | Sztring | AS2-üzenet feladójának neve az AS2-üzenet fejlécében |
| agreementName | Nem | Sztring | Az AS2-egyezmény neve, amelybe az üzenetek fel lesznek oldva |
| irány | Igen | Sztring | Az üzenet folyamatának iránya, amely `receive` vagy vagy `send` |
| messageId | Nem | Sztring | AS2-üzenet azonosítója az AS2-üzenet fejlécében |
| dispositionType | Nem | Sztring | Üzenet-törlési értesítés (MDN) – típus értéke |
| fileName | Nem | Sztring | A fájl neve az AS2-üzenet fejlécében |
| isMessageFailed | Yes | Logikai | Az AS2-üzenet sikertelen volt-e |
| isMessageSigned | Yes | Logikai | Az AS2-üzenet aláírása |
| isMessageEncrypted | Yes | Logikai | Azt jelzi, hogy az AS2-üzenet titkosított-e |
| isMessageCompressed | Yes | Logikai | Azt jelzi, hogy az AS2-üzenet tömörítve lett-e |
| correlationMessageId | Nem | Sztring | AS2-üzenet azonosítója az üzenetek MDNs-vel való korrelációhoz |
| incomingHeaders | No | JToken szótár | Bejövő AS2-üzenetek fejlécének részletei |
| outgoingHeaders | No | JToken szótár | Kimenő AS2-üzenetek fejlécének részletei |
| isNrrEnabled | Yes | Logikai | Azt határozza meg, hogy az alapértelmezett értéket kell-e használni, ha az érték nem ismert |
| isMdnExpected | Yes | Logikai | Azt határozza meg, hogy az alapértelmezett értéket kell-e használni, ha az érték nem ismert |
| mdnType | Yes | Enumeráció | Megengedett értékek: `NotConfigured` , `Sync` és `Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN-követési séma

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | AS2-üzenet küldője partnerének neve |
| receiverPartnerName | Nem | Sztring | AS2-üzenet fogadójának partnere neve |
| as2To | Igen | Sztring | Az AS2-üzenetet fogadó partner neve |
| as2From | Igen | Sztring | Az AS2-üzenetet küldő partner neve |
| agreementName | Nem | Sztring | Az AS2-egyezmény neve, amelybe az üzenetek fel lesznek oldva |
| irány | Igen | Sztring | Az üzenet folyamatának iránya, amely `receive` vagy vagy `send` |
| messageId | Nem | Sztring | AS2-üzenet azonosítója |
| originalMessageId | Nem | Sztring | AS2 eredeti üzenet azonosítója |
| dispositionType | Nem | Sztring | MDN-törlési típus értéke |
| isMessageFailed | Yes | Logikai | Az AS2-üzenet sikertelen volt-e |
| isMessageSigned | Yes | Logikai | Az AS2-üzenet aláírása |
| isNrrEnabled | Yes | Logikai | Azt határozza meg, hogy az alapértelmezett értéket kell-e használni, ha az érték nem ismert |
| statusCode | Yes | Enumeráció | Megengedett értékek: `Accepted` , `Rejected` és `AcceptedWithErrors` |
| micVerificationStatus | Yes | Enumeráció | Megengedett értékek: `NotApplicable` , `Succeeded` és `Failed` |
| correlationMessageId | Nem | Sztring | Korrelációs azonosító, amely a MDN konfigurált eredeti üzenet azonosítója |
| incomingHeaders | No | JToken szótár | Bejövő üzenetek fejlécének részletei |
| outgoingHeaders | No | JToken szótár | Kimenő üzenetek fejlécének részletei |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B protokoll-követési sémák

További információ a B2B protokoll-követési sémákkal kapcsolatban:

* [X12-követési sémák](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni B2B-követési sémák](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Következő lépések

* [B2B-üzenetek monitorozása Azure Monitor-naplókkal](../logic-apps/monitor-b2b-messages-log-analytics.md)