---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 75650d7ff0ac647aeb6dace76c270680b1b89347
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954959"
---
## <a name="enable-logging"></a>Naplózás engedélyezése

A hibakeresési és hitelesítési hibák elhárítása érdekében a Microsoft hitelesítési függvénytár beépített naplózási támogatást biztosít. A naplózás az alábbi cikkekben található:

:::row:::
    :::column:::
        - [Naplózás az MSAL.NET-ben](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Naplózás az Androidhoz készült MSAL-ben](../articles/active-directory/develop/msal-logging-android.md)
        - [Naplózás az MSAL.js-ben](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [Naplózás az iOS-hez/macOS-hez készült MSAL-ben](../articles/active-directory/develop/msal-logging-ios.md)
        - [Naplózás a Javához készült MSAL-ben](../articles/active-directory/develop/msal-logging-java.md)
        - [Naplózás a Pythonhoz készült MSAL-ben](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

Az adatgyűjtés néhány javaslata:

- A felhasználók segítségére lehetnek, amikor problémák léptek fel. Az ajánlott eljárás az, hogy rögzítse és átmenetileg tárolja a naplókat. Adja meg azt a helyet, ahol a felhasználók fel tudják tölteni a naplókat. A MSAL naplózási bővítményeket biztosít a hitelesítés részletes adatainak rögzítéséhez.

- Ha a telemetria elérhető, engedélyezze a MSAL-en keresztül, hogy adatokat gyűjtsön arról, hogy a felhasználók hogyan jelentkeznek be az alkalmazásba.


## <a name="validate-your-integration"></a>Az integráció ellenőrzése

Tesztelje az integrációt a [Microsoft Identity platform Integration ellenőrzőlista](../articles/active-directory/develop/identity-platform-integration-checklist.md)követésével.
