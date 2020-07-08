---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80117075"
---
A Windows 10 VPN-ügyfél új funkciója, a always on, a VPN-kapcsolat fenntartásának lehetősége. A always on használatával az aktív VPN-profil automatikusan csatlakozhat, és az eseményindítók alapján csatlakoztatva maradhat, például a felhasználói bejelentkezés, a hálózati állapot módosítása vagy az eszközbeállítások aktív állapota.

Az átjárók és a Windows 10 always on használatával állandó felhasználói alagutak és eszköz-alagutak hozhatók létre az Azure-ba. Ez a cikk segítséget nyújt az Always On VPN felhasználói alagút konfigurálásához.

Az Always On VPN-kapcsolatok a következő két típusú alagutat tartalmazzák:

* **Eszköz-alagút**: csatlakozás a megadott VPN-kiszolgálókhoz, mielőtt a felhasználók bejelentkeznek az eszközre. A Bejelentkezés előtti kapcsolódási forgatókönyvek és az eszközkezelés egy eszköz-alagutat használnak.

* **Felhasználói alagút**: csak azt követően csatlakozik, hogy a felhasználók bejelentkeznek az eszközre. A felhasználói alagutak használatával VPN-kiszolgálókon keresztül érheti el a szervezeti erőforrásokat.

Az eszköz-alagutak és a felhasználói alagutak a VPN-profiljaik függetlenül működnek. Egyszerre csatlakozhatnak egymáshoz, és szükség szerint különböző hitelesítési módszereket és egyéb VPN-konfigurációs beállításokat használhatnak.
