---
title: Webhook-előfizető hitelesítésének konfigurálása – Azure Event Grid IoT Edge | Microsoft Docs
description: Webhook-előfizetők hitelesítésének konfigurálása
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d78b6e80005e9533ccd5ebfaea853f35ae6004be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171652"
---
# <a name="configure-webhook-subscriber-authentication"></a>Webhook-előfizetők hitelesítésének konfigurálása

Ez az útmutató példákat mutat be egy Event Grid modul lehetséges webhook-előfizetői konfigurációjában. Alapértelmezés szerint csak HTTPS-végpontok fogadhatók el a webhook-előfizetők számára. A Event Grid modul elutasítja, ha az előfizető önaláírt tanúsítványt ad meg.

## <a name="allow-only-https-subscriber"></a>Csak HTTPS-előfizető engedélyezése

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Saját aláírású tanúsítvánnyal rendelkező HTTPS-előfizető engedélyezése

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>A tulajdonságot `outbound__webhook__allowUnknownCA` `true` csak tesztelési környezetben állítsa be, mivel általában önaláírt tanúsítványokat használ. Éles számítási feladatokhoz azt javasoljuk, hogy **hamis** értékre állítsa őket.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>HTTPS-előfizető engedélyezése, de tanúsítvány-ellenőrzés kihagyása

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>A tulajdonságot `outbound__webhook__skipServerCertValidation` `true` csak tesztelési környezetekben állítsa be, mivel előfordulhat, hogy nem fog hitelesíteni egy tanúsítványt. Éles számítási feladatokhoz ajánlott **Hamis értéket** beállítani

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>HTTP és HTTPS engedélyezése önaláírt tanúsítványokkal

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>A tulajdonságot `outbound__webhook__httpsOnly` `false` csak tesztelési környezetekben állíthatja be, ha először egy http-előfizetőt szeretne létrehozni. Éles számítási feladatokhoz ajánlott **igaz** értéket beállítani
