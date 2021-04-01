---
title: Hangsegédek a Windowsban – áttekintés
titleSuffix: Azure Cognitive Services
description: A Windows hangsegédek áttekintése, beleértve az elérhető képességeket és fejlesztési erőforrásokat.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 9e8b009ecc2181edfaad5da3d8d05ad0c1909051
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95024755"
---
# <a name="voice-assistants-on-windows"></a>Hangsegédek Windows rendszeren

A Windows 10 2004-es és újabb verzióiban a hangsegéd-alkalmazások kihasználhatják a Windows ConversationalAgent API-kat, hogy teljes hangvezérelt asszisztensi élményt tudjanak elérni.

## <a name="voice-assistant-features"></a>Hangsegéd funkciói

A hangvezérelt ügynök alkalmazásai egy kimondottan aktiválható, amely lehetővé teszi, hogy a kihangosító és a hangvezérelt felhasználói élmény biztosítható legyen. A Hangaktiválás akkor működik, ha az alkalmazás be van zárva, és a képernyő zárolva van.

A Windows emellett olyan hangvezérelt adatvédelmi beállításokat is biztosít, amelyek a felhasználók számára lehetővé teszi a hangaktiválást és a zárolási aktiválást az alkalmazáson belül.

A hangaktiválást követően a Windows több aktív ügynököt felügyel, és értesíti az egyes hangsegédeket, ha azokat megszakították vagy inaktiválták. Ez lehetővé teszi, hogy az alkalmazások megfelelően kezeljék a megszakításokat és az ügynökök közötti egyéb eseményeket.

## <a name="how-does-voice-activation-work"></a>Hogyan működik a Hangaktiválás?

Az ügynök-aktiválási futtatókörnyezet (éves tevékenység) a folyamatban lévő folyamat a Windows rendszerben, amely az alkalmazás aktiválását egy kimondott kulcsszó vagy gomb megnyomásával kezeli. A Windows rendszerrel kezdődik, ha legalább egy olyan alkalmazás van a rendszeren, amely regisztrálva van a rendszerben. Az alkalmazások a Windows SDK ConversationalAgent API-kon keresztül kommunikálnak az éves tevékenységekkel.

Ha a felhasználó egy kulcsszót beszél, akkor a rendszeren a szoftver vagy a hardveres kulcsszó megfigyelője értesítést küld a kulcsszó észleléséről, és megadja a kulcsszó AZONOSÍTÓját. A bejelentési tevékenység elküld egy kérést a BackgroundService, hogy elindítsa az alkalmazást a megfelelő alkalmazás-AZONOSÍTÓval.

### <a name="registration"></a>Regisztráció

Amikor a rendszer első alkalommal futtatja a hangvezérelt alkalmazást, a ConversationalAgent API-kon regisztrálja az alkalmazás AZONOSÍTÓját és a kulcsszavas információit. Az éves jelentés regisztrálja a globális leképezés összes konfigurációját a rendszeren található hardveres vagy szoftveres kulcsszóval, és lehetővé teszi számukra az alkalmazás kulcsszavainak észlelését. Az alkalmazás a [háttérben futó szolgáltatással is regisztrál](/windows/uwp/launch-resume/register-a-background-task).

Vegye figyelembe, hogy ez azt jelenti, hogy az alkalmazás nem aktiválható hangon, amíg egyszer nem futtatták, és a regisztráció nem fejeződött be.

### <a name="receiving-an-activation"></a>Aktiválás fogadása

Ha a kérést az éves jelentésekben fogadja, a háttérben futó szolgáltatás elindítja az alkalmazást. Az alkalmazás egy `App.xaml.cs` egyedi esemény argumentummal fogadja a OnBackgroundActivated életciklus módszerét. Ez az argumentum azt közli az alkalmazással, hogy az adattevékenység által aktiválták, és hogy el kell kezdenie a kulcsszó-ellenőrzést.

Ha az alkalmazás sikeresen ellenőrizte a kulcsszót, akkor az előtérben megjelenő kérelem jelenhet meg. Ha a kérelem sikeres, az alkalmazás megjeleníti a felhasználói FELÜLETET, és folytatja a felhasználóval való interakciót.

Az éves tevékenység továbbra is jelzi az aktív alkalmazásokat, amikor a kulcsszót elbeszélik. Az életciklus módszere helyett azonban a ConversationalAgent API-kon `App.xaml.cs` keresztül jelez egy eseményt.

### <a name="keyword-verification"></a>Kulcsszó ellenőrzése

A kulcsszó-modell leegyszerűsítése révén az alkalmazás indítását kiváltó kulcsszó megkezdi az alacsony energiafogyasztást. Ez lehetővé teszi, hogy a kulcsszó "mindig on" legyen a nagy teljesítményű hatás nélkül, de ez azt is jelenti, hogy a felderítő kulcsszó valószínűleg nagy számú "hamis elfogadást" tartalmaz, ahol egy kulcsszót észlelt, de nem beszélt. Ezért indítja el a hangaktiválási rendszer az alkalmazást a háttérben: az alkalmazásnak lehetősége van arra, hogy ellenőrizze, hogy a kulcsszót a felhasználó aktuális munkamenetének megszakítása előtt adta-e meg. Az éves tevékenység elmenti a hangot néhány másodperc múlva, mielőtt a kulcsszót kiszúrták, és elérhetővé teszi az alkalmazás számára. Az alkalmazás használatával megbízhatóbb kulcsszavas Felderítőt futtathat ugyanazon a hangon.

## <a name="next-steps"></a>Következő lépések

- **Tekintse át a tervezési irányelveket:** A [tervezési irányelvek](windows-voice-assistants-best-practices.md) alapján megállapítható, hogy a Windows 10-es hangalapú aktiválás lehető legjobb élményét biztosítja a legfontosabb munka.
- **Látogasson el a első lépések oldalra:** [Itt](how-to-windows-voice-assistants-get-started.md) megkezdheti a hangsegédek Windows rendszeren való megvalósításának megkezdéséhez szükséges lépéseket, a fejlesztési környezet beállításával a megvalósítási útmutató bevezetésével.
- **Próbálja ki a minta alkalmazást**: Ha ezeket a képességeket első kézből szeretné megtapasztalni, látogasson el a [UWP Voice Assistant-minta](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) oldalára, és kövesse a lépéseket, amelyekkel lekérheti a futó ügyfél