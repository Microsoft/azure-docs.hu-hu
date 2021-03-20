---
title: TLS-frissítések Notification Hubs
description: Ismerkedjen meg a TLS támogatásával az Azure Notification Hubsban.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87a3627d7820f9f456ac08e2f20b70af961f817e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87084245"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

A magasabb szintű biztonság érdekében Notification Hubs letilthatja a 1,0-es és 1,1-es TLS-verziók támogatását **2020. december 31-** én (a 2020. április 30-ig kiterjesztve). Ezek a régebbi protokollok gyenge titkosítást biztosítanak, és ki vannak téve a VADÁLLATok és az USZKÁR elleni támadásoknak. Az Android 5-ös vagy újabb verzióját, vagy az iOS 5-ös vagy újabb verzióját futtató eszközökre telepített alkalmazások nem érintik ezt a változást, mivel ezek az operációs rendszerek támogatják a TLS 1,2-et, és az ügyfél és a kiszolgáló a kapcsolaton keresztül egyezteti a protokoll legmagasabb kölcsönösen támogatott verzióját.

Javasoljuk, hogy tekintse át az Azure Notification Hubst használó összes alkalmazást, és győződjön meg arról, hogy a legalkalmasabb kódtárak és TLS-csomagok a TLS 1,2-et támogatják.

## <a name="update-apps"></a>Alkalmazások frissítése

Gondoskodhat arról, hogy az iOS-alkalmazások a TLS 1,2-et használják az Apple hálózatkezelési biztonsági funkciója, az App Transport Security (ATS) használatával. Az ATS nem használható iOS 9,0 vagy macOS 10,11 rendszernél régebbi SDK-k esetében, és az [Apple dokumentációjában](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)olvashat bővebben.

SSLSocket-példányokat használó Android-alkalmazások esetén az összes SSLSocket-példányon állítsa be az engedélyezett protokollokat a [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))feljegyzett módon.

A [TLS protokoll kompatibilitási](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) támogatás lapján található táblázat a kompatibilis TLS-verziókkal rendelkező operációs rendszerek feltérképezését segíti.

További információ: a [Windowson futó TLS protokollok támogatásának](/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)áttekintése.

## <a name="next-steps"></a>Következő lépések

- [Notification Hubs áttekintése](notification-hubs-push-notification-overview.md)
