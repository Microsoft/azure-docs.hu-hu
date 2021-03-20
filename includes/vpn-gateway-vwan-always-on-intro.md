---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e40f421c0fa45d772cd333dac51fe2bdf2779f48
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91828972"
---
A Windows 10 VPN-ügyfél új funkciója, a always on, a VPN-kapcsolat fenntartásának lehetősége. A always on használatával az aktív VPN-profil automatikusan csatlakozhat, és az eseményindítók alapján csatlakoztatva maradhat, például a felhasználói bejelentkezés, a hálózati állapot módosítása vagy az eszközbeállítások aktív állapota.

Az átjárók és a Windows 10 always on használatával állandó felhasználói alagutak és eszköz-alagutak hozhatók létre az Azure-ba.

Az Always On VPN-kapcsolatok a következő két típusú alagutat tartalmazzák:

* **Eszköz-alagút**: csatlakozás a megadott VPN-kiszolgálókhoz, mielőtt a felhasználók bejelentkeznek az eszközre. A Bejelentkezés előtti kapcsolódási forgatókönyvek és az eszközkezelés egy eszköz-alagutat használnak.

* **Felhasználói alagút**: csak azt követően csatlakozik, hogy a felhasználók bejelentkeznek az eszközre. A felhasználói alagutak használatával VPN-kiszolgálókon keresztül érheti el a szervezeti erőforrásokat.

Az eszköz-alagutak és a felhasználói alagutak a VPN-profiljaik függetlenül működnek. Egyszerre csatlakozhatnak egymáshoz, és szükség szerint különböző hitelesítési módszereket és egyéb VPN-konfigurációs beállításokat használhatnak.
