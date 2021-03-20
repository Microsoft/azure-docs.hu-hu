---
title: API-protokollok konfigurálása – Azure Event Grid IoT Edge | Microsoft Docs
description: A Event Grid modul lehetséges protokoll-konfigurációinak megismerése.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: a9cf6088201ffeaed76d99a9b211e5bcd1ea139a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91322580"
---
# <a name="configure-event-grid-api-protocols"></a>Event Grid API-protokollok konfigurálása

Ez az útmutató példákat mutat be egy Event Grid modul lehetséges protokoll-konfigurációjáról. A Event Grid modul az API-t teszi elérhetővé felügyeleti és futtatókörnyezeti műveleteihez. A következő táblázat rögzíti a protokollokat és a portokat.

| Protokoll | Port | Description |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Alapértelmezés szerint ki van kapcsolva. Csak tesztelés során hasznos. Éles számítási feladatokhoz nem alkalmas.
| HTTPS | 4438 | Alapértelmezett

Tekintse meg a [biztonsági és hitelesítési](security-authentication.md) útmutatót az összes lehetséges konfigurációhoz.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>HTTPS-alapú IoT-modulok közzététele ugyanazon a peremhálózati hálózaton

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>HTTPS engedélyezése más IoT-modulok és nem IoT munkaterhelések számára

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> A **PortBindings** szakasz lehetővé teszi belső portok leképezését a tároló gazdagépének portjaira. Ez a funkció lehetővé teszi a Event Grid modul elérését az IoT Edge Container networken kívülről, ha az IoT Edge-eszköz nyilvánosan elérhető.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>HTTP-és HTTPS-alapú IoT-modulok közzététele ugyanazon a peremhálózati hálózaton

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>HTTP és HTTPS engedélyezése más IoT-modulok és nem IoT-munkaterhelések számára

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Alapértelmezés szerint minden IoT modul a híd hálózat által létrehozott IoT Edge futtatókörnyezet részét képezi. Lehetővé teszi, hogy az ugyanazon a hálózaton lévő különböző IoT-modulok kommunikáljanak egymással. A **PortBindings** lehetővé teszi egy tároló belső portjának leképezését a gazdagépre, így bárki számára elérhetővé teheti Event Grid modul portját kívülről.

>[!IMPORTANT]
> Amíg a portok elérhetővé tehetők a IoT Edge hálózaton kívül, az ügyfél-hitelesítés azt kényszeríti ki, hogy a modulban valóban engedélyezve legyenek a hívások.
