---
title: Konfiguráció – Azure Event Grid IoT Edge | Microsoft Docs
description: Konfiguráció a IoT Edge Event Gridban.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 632227579fd021a0d2ce1d0b1bb0b8a8288c5f47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171669"
---
# <a name="event-grid-configuration"></a>Event Grid konfiguráció

Event Grid számos olyan konfigurációt biztosít, amelyek régiónként módosíthatók. A következő szakasz az összes elérhető lehetőségre és az azok alapértelmezett értékeire mutató hivatkozást tartalmaz.

## <a name="tls-configuration"></a>TLS-konfiguráció

Az ügyfél-hitelesítés általános ismertetése: [Biztonság és hitelesítés](security-authentication.md). A használatáról [ebben a cikkben](configure-api-protocol.md)talál példákat.

| Tulajdonság neve | Description |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| A Event Grid modul TLS-házirendje. Az alapértelmezett érték csak HTTPS.
|`inbound__serverAuth__serverCert__source`| A Event Grid modul által a TLS-konfigurációhoz használt kiszolgálói tanúsítvány forrása. Az alapértelmezett érték IoT Edge.

## <a name="incoming-client-authentication"></a>Bejövő ügyfél-hitelesítés

Az ügyfél-hitelesítés általános ismertetése: [Biztonság és hitelesítés](security-authentication.md). [Ebben a cikkben](configure-client-auth.md)a példákat is megtalálhatja.

| Tulajdonság neve | Description |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| A tanúsítványalapú ügyfél-hitelesítés be-és kikapcsolása. Az alapértelmezett érték true (igaz).
|`inbound__clientAuth__clientCert__source`| Az ügyféltanúsítványok érvényesítésének forrása. Az alapértelmezett érték IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Önaláírt ügyféltanúsítványt engedélyező házirend. Az alapértelmezett érték true (igaz).
|`inbound__clientAuth__sasKeys__enabled`| Az SAS-kulcs alapú ügyfél-hitelesítés be-és kikapcsolása. Az alapértelmezett érték ki van kapcsolva.
|`inbound__clientAuth__sasKeys__key1`| Az egyik érték a beérkező kérések érvényesítéséhez.
|`inbound__clientAuth__sasKeys__key2`| Nem kötelező második érték a beérkező kérések érvényesítéséhez.

## <a name="outgoing-client-authentication"></a>Kimenő ügyfél-hitelesítés
Az ügyfél-hitelesítés általános ismertetése: [Biztonság és hitelesítés](security-authentication.md). [Ebben a cikkben](configure-identity-auth.md)a példákat is megtalálhatja.

| Tulajdonság neve | Description |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| A kimenő kérésekhez tartozó Identity tanúsítvány csatolásának be-és kikapcsolása. Az alapértelmezett érték true (igaz).
|`outbound__clientAuth__clientCert__source`| Event Grid modul kimenő tanúsítványának beolvasására szolgáló forrás. Az alapértelmezett érték IoT Edge.

## <a name="webhook-event-handlers"></a>Webhook-eseménykezelők

Az ügyfél-hitelesítés általános ismertetése: [Biztonság és hitelesítés](security-authentication.md). [Ebben a cikkben](configure-webhook-subscriber-auth.md)a példákat is megtalálhatja.

| Tulajdonság neve | Description |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Házirend annak szabályozására, hogy csak HTTPS-előfizetők engedélyezettek-e. Az alapértelmezett érték igaz (csak HTTPS).
|`outbound__webhook__skipServerCertValidation`| Annak a jelzője, amely meghatározza, hogy ellenőrizni kell-e az előfizető tanúsítványát. Az alapértelmezett érték true (igaz).
|`outbound__webhook__allowUnknownCA`| Házirend annak szabályozására, hogy egy előfizető tud-e önaláírt tanúsítványt megjeleníteni. Az alapértelmezett érték true (igaz). 

## <a name="delivery-and-retry"></a>Teljesítés és újrapróbálkozás

A szolgáltatás általános megismeréséhez lásd: [kézbesítés és újrapróbálkozás](delivery-retry.md).

| Tulajdonság neve | Description |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Egy eseményt kézbesítő kísérletek maximális száma. Az alapértelmezett érték 30.
| `broker__defaultEventTimeToLiveInSeconds` | Élettartam (TTL) másodpercben, amely után az esemény el lesz dobva, ha nem érkezik meg. Az alapértelmezett érték  **7200** másodperc

## <a name="output-batching"></a>Kimenet kötegelése

Ha többet szeretne megtudni erről a szolgáltatásról, olvassa el a [továbbítás és a kimenetek kötegelt feldolgozását](delivery-output-batching.md)ismertető témakört.

| Tulajdonság neve | Description |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | A forgatógomb számára engedélyezett maximális érték `ApproxBatchSizeInBytes` Az alapértelmezett érték `1_058_576`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | A forgatógomb számára engedélyezett maximális érték `MaxEventsPerBatch` Az alapértelmezett érték `50`.
| `broker__defaultMaxBatchSizeInBytes` | A kézbesítési kérelmek maximális mérete, ha csak meg `MaxEventsPerBatch` van adva. Az alapértelmezett érték `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | A köteghez hozzáadandó események maximális száma, ha csak meg `MaxBatchSizeInBytes` van adva. Az alapértelmezett érték `10`.

## <a name="metrics"></a>Mérőszámok

Ha szeretné megtudni, hogyan használhatja a metrikákat a IoT Edge Event Grid, tekintse meg a [témakörök és előfizetések figyelése](monitor-topics-subscriptions.md)

| Tulajdonság neve | Description |
| ---------------- | ------------ |
| `metrics__reporterType` | A mérőszámok végpontjának jelentéskészítő típusa Az alapértelmezett érték az `none` , és letiltja a metrikákat. Ezzel a beállítással `prometheus` engedélyezheti a metrikákat a Prometheus kiállítási formátumában.