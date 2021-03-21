---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75371776"
---
A közös hozzáférésű aláírás (SAS) lehetővé teszi, hogy a Storage-fiókban korlátozott hozzáférést biztosítson a tárolók és a Blobok számára. SAS létrehozásakor meg kell adnia a megkötéseit, beleértve az ügyfél számára engedélyezett Azure Storage-erőforrásokat, milyen engedélyekkel rendelkeznek az adott erőforrásokon, és hogy mennyi ideig érvényes az SAS.

Minden SAS egy kulccsal van aláírva. Az SAS-t kétféleképpen lehet aláírni:

- Azure Active Directory (Azure AD) hitelesítő adatok használatával létrehozott kulccsal. Az Azure AD-beli hitelesítő adatokkal aláírt SAS *felhasználói delegálási* sas.
- A Storage-fiók kulcsaként. A *Service sas* és a *fiók sas* is a Storage-fiók kulcsával van aláírva.

A felhasználói delegálási SAS kiváló biztonságot nyújt a Storage-fiók kulcsával aláírt SAS számára. A Microsoft a felhasználói delegálási SAS használatát javasolja, ha lehetséges. További információ: [korlátozott hozzáférés biztosítása az adatokhoz közös hozzáférésű aláírásokkal (SAS)](../articles/storage/common/storage-sas-overview.md).
