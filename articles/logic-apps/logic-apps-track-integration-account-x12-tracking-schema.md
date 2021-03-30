---
title: VÁLLALATKÖZI üzenetek X12 követési sémái
description: Nyomkövetési sémák létrehozása a X12-üzenetek figyeléséhez Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "76905306"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Sémák létrehozása a X12-üzenetek követéséhez Azure Logic Apps

Ha segítségre van szüksége a vállalatok közötti (B2B) tranzakciók sikerességének, hibáinak és üzenetének figyeléséhez, a következő X12-követési sémákat használhatja az integrációs fiókjában:

* X12-tranzakció készletének követési sémája
* X12 tranzakciós készletének visszaigazolási követési sémája
* X12-csere követési sémája
* X12 adatcsere-visszaigazolási követési sémája
* X12 funkcionális csoport követési sémája
* X12 funkcionális csoport nyugtázási követési sémája

## <a name="x12-transaction-set-tracking-schema"></a>X12-tranzakció készletének követési sémája

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | X12-üzenet küldője partnerének neve |
| receiverPartnerName | Nem | Sztring | X12-üzenet fogadójának partnere neve |
| senderQualifier | Igen | Sztring | Partner minősítő küldése |
| senderIdentifier | Igen | Sztring | Partner azonosítójának küldése |
| receiverQualifier | Igen | Sztring | Fogadási partner minősítője |
| receiverIdentifier | Igen | Sztring | Fogadási partner azonosítója |
| agreementName | Nem | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva |
| irány | Yes | Enumeráció | Az üzenet folyamatának iránya, amely `receive` vagy vagy `send` |
| interchangeControlNumber | Nem | Sztring | Adatcsere-vezérlési szám |
| functionalGroupControlNumber | Nem | Sztring | Funkcionális vezérlési szám |
| transactionSetControlNumber | Nem | Sztring | Tranzakciónapló-vezérlő száma |
| CorrelationMessageId | Nem | Sztring | Korrelációs üzenet azonosítója ({AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}) kombinációja |
| messageType | Nem | Sztring | Tranzakciótípus vagy bizonylattípus |
| isMessageFailed | Yes | Logikai | Azt jelzi, hogy a X12-üzenet sikertelen-e |
| isTechnicalAcknowledgmentExpected | Yes | Logikai | Azt jelzi, hogy a technikai nyugtázás konfigurálva van-e a X12-szerződésben |
| isFunctionalAcknowledgmentExpected | Yes | Logikai | Azt jelzi, hogy a funkcionális nyugtázás konfigurálva van-e az X12-szerződésben |
| needAk2LoopForValidMessages | Yes | Logikai | Azt jelzi, hogy az AK2 hurok szükséges-e egy érvényes üzenethez |
| segmentsCount | No | Egész szám | A X12-tranzakció készletében lévő szegmensek száma |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>X12 tranzakciós készletének visszaigazolási követési sémája

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | X12-üzenet küldője partnerének neve |
| receiverPartnerName | Nem | Sztring | X12-üzenet fogadójának partnere neve |
| senderQualifier | Igen | Sztring | Partner minősítő küldése |
| senderIdentifier | Igen | Sztring | Partner azonosítójának küldése |
| receiverQualifier | Igen | Sztring | Fogadási partner minősítője |
| receiverIdentifier | Igen | Sztring | Fogadási partner azonosítója |
| agreementName | Nem | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva |
| irány | Yes | Enumeráció | Az üzenet folyamatának iránya, amely `receive` vagy vagy `send` |
| interchangeControlNumber | Nem | Sztring | A funkcionális visszaigazoláshoz tartozó adatcsere-vezérlési szám. Az érték csak arra a küldési oldalra töltődik fel, ahol a partnernek küldött üzenetekhez a funkcionális visszaigazolás érkezik. |
| functionalGroupControlNumber | Nem | Sztring | A funkcionális visszaigazolás funkcionális csoportjának vezérlési száma. Az érték csak azt a küldési oldalt tölti fel, ahol a partnernek küldött üzenetekhez a funkcionális nyugtázás érkezik |
| isaSegment | Nem | Sztring | Az üzenet ISA-szegmense. Az érték csak azt a küldési oldalt tölti fel, ahol a partnernek küldött üzenetekhez a funkcionális nyugtázás érkezik |
| gsSegment | Nem | Sztring | Az üzenet GS-szegmense. Az érték csak azt a küldési oldalt tölti fel, ahol a partnernek küldött üzenetekhez a funkcionális nyugtázás érkezik |
| respondingfunctionalGroupControlNumber | Nem | Sztring | A válaszoló adatcsere-vezérlő száma |
| respondingFunctionalGroupId | Nem | Sztring | A válaszoló funkcionális csoport azonosítója, amely a AK101 leképezi a nyugtát |
| respondingtransactionSetControlNumber | Nem | Sztring | A válaszoló tranzakciónapló-vezérlő száma |
| respondingTransactionSetId | Nem | Sztring | A válaszoló tranzakció-készlet azonosítója, amely a nyugtán lévő AK201 képezi le. |
| statusCode | Yes | Logikai | A készlettranzakció-készlet nyugtázási állapotának kódja |
| segmentsCount | Yes | Enumeráció | A nyugtázási állapotkód a következő megengedett értékekkel: `Accepted` , `Rejected` , és `AcceptedWithErrors` |
| processingStatus | Yes | Enumeráció | A visszaigazolás feldolgozási állapota a következő megengedett értékekkel: `Received` , `Generated` és `Sent` |
| CorrelationMessageId | Nem | Sztring | Korrelációs üzenet azonosítója ({AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}) kombinációja |
| isMessageFailed | Yes | Logikai | Azt jelzi, hogy a X12-üzenet sikertelen-e |
| ak2Segment | Nem | Sztring | A fogadott funkcionális csoporton belüli tranzakcióhoz tartozó visszaigazolás |
| ak3Segment | Nem | Sztring | Adatszegmensben lévő hibák jelentése |
| ak5Segment | Nem | Sztring | Azt jelzi, hogy elfogadták vagy elutasították-e a AK2-szegmensben azonosított tranzakciós készletet, és miért |
|||||

## <a name="x12-interchange-tracking-schema"></a>X12-csere követési sémája

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | X12-üzenet küldője partnerének neve |
| receiverPartnerName | Nem | Sztring | X12-üzenet fogadójának partnere neve |
| senderQualifier | Igen | Sztring | Partner minősítő küldése |
| senderIdentifier | Igen | Sztring | Partner azonosítójának küldése |
| receiverQualifier | Igen | Sztring | Fogadási partner minősítője |
| receiverIdentifier | Igen | Sztring | Fogadási partner azonosítója |
| agreementName | Nem | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva |
| irány | Yes | Enumeráció | Az üzenet folyamatának iránya, amely `receive` vagy vagy `send` |
| interchangeControlNumber | Nem | Sztring | Adatcsere-vezérlési szám |
| isaSegment | Nem | Sztring | Üzenet ISA-szegmense |
| isTechnicalAcknowledgmentExpected | Logikai | Azt jelzi, hogy a technikai nyugtázás konfigurálva van-e a X12-szerződésben  |
| isMessageFailed | Yes | Logikai | Azt jelzi, hogy a X12-üzenet sikertelen-e |
| isa09 | Nem | Sztring | X12 dokumentum-csere dátuma |
| isa10 | Nem | Sztring | X12 dokumentum-átváltási idő |
| isa11 | Nem | Sztring | X12-adatcsere-vezérlési szabvány azonosítója |
| isa12 | Nem | Sztring | X12-vezérlő verziószáma |
| isa14 | Nem | Sztring | X12-visszaigazolás szükséges |
| isa15 | Nem | Sztring | Tesztelési vagy éles kijelző |
| isa16 | Nem | Sztring | Elem elválasztója |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>X12 adatcsere-visszaigazolási követési sémája

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | X12-üzenet küldője partnerének neve |
| receiverPartnerName | Nem | Sztring | X12-üzenet fogadójának partnere neve |
| senderQualifier | Igen | Sztring | Partner minősítő küldése |
| senderIdentifier | Igen | Sztring | Partner azonosítójának küldése |
| receiverQualifier | Igen | Sztring | Fogadási partner minősítője |
| receiverIdentifier | Igen | Sztring | Fogadási partner azonosítója |
| agreementName | Nem | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva |
| irány | Yes | Enumeráció | Az üzenet folyamatának iránya, amely `receive` vagy vagy `send` |
| interchangeControlNumber | Nem | Sztring | A partnerektől kapott technikai elismerések átirányítási száma |
| isaSegment | Nem | Sztring | A partnerektől kapott technikai nyugtázás ISA-szegmense |
| respondingInterchangeControlNumber | Nem | Sztring | A partnerektől kapott technikai nyugtázás adatcsere-vezérlési száma |
| isMessageFailed | Yes | Logikai | Azt jelzi, hogy a X12-üzenet sikertelen-e |
| statusCode | Yes | Enumeráció | Az adatcsere-visszaigazolás állapotkód a következő megengedett értékekkel: `Accepted` , `Rejected` , és `AcceptedWithErrors` |
| processingStatus | Yes | Enumeráció | A visszaigazolás állapota a következő megengedett értékekkel: `Received` , `Generated` , és `Sent` |
| TA102 | Nem | Sztring | Bankközi dátum |
| ta103 | Nem | Sztring | Bankközi idő |
| ta105 | Nem | Sztring | Adatcsere-Megjegyzés kódja |
|||||

## <a name="x12-functional-group-tracking-schema"></a>X12 funkcionális csoport követési sémája

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | X12-üzenet küldője partnerének neve |
| receiverPartnerName | Nem | Sztring | X12-üzenet fogadójának partnere neve |
| senderQualifier | Igen | Sztring | Partner minősítő küldése |
| senderIdentifier | Igen | Sztring | Partner azonosítójának küldése |
| receiverQualifier | Igen | Sztring | Fogadási partner minősítője |
| receiverIdentifier | Igen | Sztring | Fogadási partner azonosítója |
| agreementName | Nem | Sztring | Annak a X12-szerződésnek a neve, amelyre az üzenetek fel lesznek oldva |
| irány | Yes | Enumeráció | Az üzenet folyamatának iránya, fogadás vagy küldés |
| interchangeControlNumber | Nem | Sztring | Adatcsere-vezérlési szám |
| functionalGroupControlNumber | Nem | Sztring | Funkcionális vezérlési szám |
| gsSegment | Nem | Sztring | Message GS-szegmens |
| isTechnicalAcknowledgmentExpected | Yes | Logikai | Azt jelzi, hogy a technikai nyugtázás konfigurálva van-e a X12-szerződésben |
| isFunctionalAcknowledgmentExpected | Yes | Logikai | Azt jelzi, hogy a funkcionális nyugtázás konfigurálva van-e az X12-szerződésben |
| isMessageFailed | Yes | Logikai | Azt jelzi, hogy a X12-üzenet sikertelen-e |
| gs01 | Nem | Sztring | Funkcionális azonosító kódja |
| gs02 | Nem | Sztring | Az alkalmazás feladójának kódja |
| gs03 | Nem | Sztring | Alkalmazás fogadójának kódja |
| gs04 | Nem | Sztring | Funkcionális csoport dátuma |
| gs05 | Nem | Sztring | Működési csoport ideje |
| gs07 | Nem | Sztring | Felelős Ügynökség kódja |
| gs08 | Nem | Sztring | Azonosító kód a verzióhoz, a kiadáshoz vagy az iparághoz |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>X12 funkcionális csoport nyugtázási követési sémája

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| senderPartnerName | Nem | Sztring | X12-üzenet küldője partnerének neve |
| receiverPartnerName | Nem | Sztring | X12-üzenet fogadójának partnere neve |
| senderQualifier | Igen | Sztring | Partner minősítő küldése |
| senderIdentifier | Igen | Sztring | Partner azonosítójának küldése |
| receiverQualifier | Igen | Sztring | Fogadási partner minősítője |
| receiverIdentifier | Igen | Sztring | Fogadási partner azonosítója |
| agreementName | Nem | Sztring | Azon X12-szerződés neve, amelybe az üzenetek fel lesznek oldva |
| irány | Yes | Enumeráció | Az üzenet folyamatának iránya, amely `receive` vagy vagy `send` |
| interchangeControlNumber | Nem | Sztring | Az adatcsere-vezérlési szám, amely a küldési oldalra van feltöltve, amikor a partnerektől kapott technikai nyugtát |
| functionalGroupControlNumber | Nem | Sztring | A technikai visszaigazolás funkcionális csoportjának vezérlési száma, amely a küldési oldal feltöltésére szolgál, ha a partnerektől kapott technikai nyugtát |
| isaSegment | Nem | Sztring | Ugyanaz, mint az adatcsere-vezérlőelem száma, de csak bizonyos esetekben van feltöltve |
| gsSegment | Nem | Sztring | Ugyanaz, mint a funkcionális csoport vezérlőelem száma, de csak bizonyos esetekben van feltöltve |
| respondingfunctionalGroupControlNumber | Nem | Sztring | Az eredeti funkcionális csoport vezérlési száma |
| respondingFunctionalGroupId | Nem | Sztring | A visszaigazolási funkcionális csoport AZONOSÍTÓjában lévő AK101 leképezése |
| isMessageFailed | Logikai | Azt jelzi, hogy a X12-üzenet sikertelen-e |
| statusCode | Yes | Enumeráció | A nyugtázási állapotkód a következő megengedett értékekkel: `Accepted` , `Rejected` , és `AcceptedWithErrors` |
| processingStatus | Yes | Enumeráció | A visszaigazolás feldolgozási állapota a következő megengedett értékekkel: `Received` , `Generated` és `Sent` |
| ak903 | Nem | Sztring | Fogadott tranzakciónaplók száma |
| ak904 | Nem | Sztring | Az azonosított funkcionális csoportban elfogadott tranzakciótípusok száma |
| ak9Segment | Nem | Sztring | Azt határozza meg, hogy a AK1-szegmensben azonosított funkcionális csoport elfogadva vagy elutasítva, és miért |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B protokoll-követési sémák

További információ a B2B protokoll-követési sémákkal kapcsolatban:

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Egyéni B2B-követési sémák](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Következő lépések

* [B2B-üzenetek monitorozása Azure Monitor-naplókkal](../logic-apps/monitor-b2b-messages-log-analytics.md)