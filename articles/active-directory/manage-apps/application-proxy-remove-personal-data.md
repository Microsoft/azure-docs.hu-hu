---
title: Személyes adattárolás eltávolítása – Azure Active Directory Application Proxy
description: A személyes adatok eltávolítása a Azure Active Directory Application Proxy eszközökön telepített összekötők közül.
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90913ba8f7fbe8158a5cfea01e49a175180677b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258948"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Azure Active Directory Application Proxy személyes adatfájljainak eltávolítása

A Azure Active Directory Application Proxy megköveteli, hogy az eszközökön telepítsen összekötőket, ami azt jelenti, hogy az eszközökön személyes adatai lehetnek. Ez a cikk azokat a lépéseket ismerteti, amelyekkel a személyes adatok az adatvédelem javítása érdekében törölhetők.

## <a name="where-is-the-personal-data"></a>Hol található a személyes adathalmaz?

Az Application proxy a következő típusú naplókba írhatja a személyes adattípusokat:

- Összekötő-eseménynaplók
- Windows-eseménynaplók

## <a name="remove-personal-data-from-windows-event-logs"></a>Személyes adatok eltávolítása a Windows-eseménynaplóból

További információ a Windows-eseménynaplók adatmegőrzésének konfigurálásáról: [Eseménynaplók beállításai](https://technet.microsoft.com/library/cc952132.aspx). A Windows-eseménynaplók megismeréséhez lásd: [a Windows Eseménynapló használata](/windows/win32/wes/using-windows-event-log).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Személyes adatok eltávolítása az összekötő eseménynaplóból

Annak biztosítása érdekében, hogy az alkalmazásproxy naplói ne rendelkezzenek személyes adattal, a következők közül választhat:

- Szükség esetén törölheti vagy megtekintheti az adatmegjelenítést, vagy
- Naplózás kikapcsolása

A következő részben az összekötő-eseménynaplók személyes adatait távolíthatja el. Az eltávolítási folyamatot minden olyan eszközön el kell végeznie, amelyen az összekötő telepítve van.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Megadott adatértékek megtekintése vagy exportálása

Adott adatok megtekintéséhez vagy exportálásához keresse meg a kapcsolódó bejegyzéseket az egyes összekötő-eseménynaplókban. A naplók a következő helyen találhatók: `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace` .

Mivel a naplók szövegfájlok, a [findstr](/windows-server/administration/windows-commands/findstr) használatával keresheti meg a felhasználóhoz kapcsolódó szöveges bejegyzéseket.  

Személyes adatkereséshez keresse meg a felhasználói azonosítóhoz tartozó naplófájlokat.

A Kerberos által korlátozott delegálást használó alkalmazás által naplózott személyes adatkeresések kereséséhez keresse meg a Felhasználónév típusa következő összetevőit:

- Helyszíni egyszerű Felhasználónév
- Felhasználónév része az egyszerű felhasználónévnek
- A helyszíni egyszerű felhasználónév része
- Helyszíni biztonsági fiókkezelő (SAM) fiók neve

### <a name="delete-specific-data"></a>Megadott adatértékek törlése

Megadott adatértékek törlése:

1. Indítsa újra a Microsoft Azure AD alkalmazásproxy-összekötő szolgáltatást egy új naplófájl létrehozásához. Az új naplófájl lehetővé teszi a régi naplófájlok törlését vagy módosítását. 
1. A korábban ismertetett adatfolyamatok [megtekintéséhez vagy exportálásához](#view-or-export-specific-data) kövesse a törölni kívánt adatokat. Keresse meg az összes összekötő naplóját.
1. Törölje a kapcsolódó naplófájlokat, vagy törölje a személyes adatokat tartalmazó mezőket. Ha már nincs szüksége rájuk, törölheti az összes régi naplófájlt is.

### <a name="turn-off-connector-logs"></a>Összekötő naplófájljainak kikapcsolása

Az egyik lehetőség annak biztosítására, hogy az összekötő naplófájljai ne tartalmazzanak személyes adattartalmakat a napló létrehozásának kikapcsolásához. Az összekötő-naplók létrehozásának leállításához távolítsa el az alábbi kiemelt sort a következőből: `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` .

![Megjelenít egy kódrészletet az eltávolítandó Kiemelt kóddal](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Következő lépések

Az alkalmazásproxy áttekintését lásd: [biztonságos távoli hozzáférés biztosítása a helyszíni alkalmazásokhoz](application-proxy.md).