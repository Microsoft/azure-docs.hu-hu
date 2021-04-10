---
title: Azure kommunikációs szolgáltatások felhasználói felületi keretrendszerének képességei
titleSuffix: An Azure Communication Services conceptual document
description: A felhasználói felületi keretrendszer képességeinek megismerése
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5b1aab8b38614249d6b502044b5c4c8170f46b3c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492217"
---
# <a name="ui-framework-capabilities"></a>Felhasználói felületi keretrendszer képességei

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Az Azure kommunikációs szolgáltatások felhasználói felületének keretrendszere lehetővé teszi a kommunikációs élmények kiépítését újrafelhasználható összetevők használatával. Ezek az összetevők két változatban érhetők **el: az** Alapösszetevők a felhasználói felület legfontosabb építőelemei, míg ezeknek az alapösszetevőknek a kombinációit **összetett** összetevőknek nevezzük.

## <a name="ui-framework-composite-components"></a>Felhasználói felületi keretrendszer – összetett összetevők

| Kompozit               | Leírás                                               | Webes   | Android | iOS   |
|-------------------------|-----------------------------------------------------------|-------|---------|-------|
| Csoportos hívás összetett | Az Azure kommunikációs szolgáltatások hangsúlyos hang-és videó-Meghívási élménye, amely a folyékony felhasználói felület kialakítási eszközeivel hívja fel a kapcsolatot. Támogatja a csoportos hívást az Azure kommunikációs szolgáltatások csoportjának azonosítójával. Az összetett funkció lehetővé teszi az egy-az-egyhez hívás használatát, ha az Azure kommunikációs szolgáltatások identitására vagy telefonszámára hivatkozik az Azure-ban beszerzett telefonszám használatával.                                    | React |  |  |
| Csoportos csevegés – összetett    | Kis teljesítményű csevegési élmény az Azure kommunikációs szolgáltatásokhoz, folyékony felhasználói felületi tervezési eszközök használatával. Ez a tapasztalat egy olyan egyszerű csevegési ügyfél megvalósítására koncentrál, amely képes csatlakozni az Azure kommunikációs szolgáltatások szálához. Lehetővé teszi a felhasználók számára üzenetek küldését és a Beérkezett üzenetek megtekintését, valamint a beolvasási elismervények beírását. Az 1:1-tól kezdve a csevegési forgatókönyvekre is méretezhető. Egyetlen csevegési szál támogatása.                         | React |  |  |

## <a name="ui-framework-base-components"></a>Felhasználói felületi keretrendszer alapösszetevői

| Összetevő             | Leírás                                                                                                                                                                                                                                                                        | Webes   | Android | iOS |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|---------|-----|
| Szolgáltató hívása    | Alapvető Inicializálási összetevő a híváshoz. A szükséges összetevő ezt követően inicializálja a többi összetevőt. Kezeli az alapvető logikát a hívó ügyfél inicializálásához az Azure kommunikációs szolgáltatások hozzáférési jogkivonatai használatával. Támogatja a csoportos csatlakoztatást. | React | N.A.     | N.A. |
| Adathordozó-vezérlők   | Lehetővé teszi a felhasználók számára az aktuális hívás felügyeletét az Elnémítás, a videó be-és kikapcsolása és a hívás befejezése után.                                                                                                                                                              | React | N.A.     | N.A. |
| Media Gallery   | Az összes hívási résztvevő kimutatása egyetlen gyűjteményben. A katalógus támogatja a videó-és statikus résztvevőket is. A videóval kompatibilis résztvevők esetében a videó megjelenítésre kerül.                                                                                                                | React | N.A.     | N.A. |
| Mikrofon beállításai | Válassza ki a meghíváshoz használni kívánt mikrofont. Ez a vezérlő a mikrofonos eszköz kiválasztásához szükséges hívás előtt és közben is használható.                                                                                                                                               | React | N.A.     | N.A. |
| Kamera beállításai     | Válassza ki a videohívás céljára használni kívánt kamerát. Ez a vezérlő a videó eszköz kiválasztásához szükséges hívás előtt és közben is használható.                                                                                                                                             | React | N.A.     | N.A. |
| Eszközbeállítások     | A mikrofon és a kamera beállításait egyetlen összetevőbe egyesíti                                                                                                 | React | N.A.     | N.A. |
| Csevegési szolgáltató       | Alapvető Inicializálási összetevő a csevegéshez. A szükséges összetevő ezt követően inicializálja a többi összetevőt. Kezeli az alapvető logikát, hogy a csevegési ügyfelet egy Azure kommunikációs szolgáltatás hozzáférési jogkivonatával és a hozzá csatlakozó szálral inicializálja.                                     | React | N.A.     | N.A. |
| Küldési mező          | Bemeneti összetevő, amely lehetővé teszi, hogy a felhasználók üzeneteket küldjenek a csevegési szálnak. A bemenet támogatja a szöveget, a hiperhivatkozásokat, a hangulatjelek és az egyéb Unicode-karaktereket, beleértve az egyéb ábécéket is.                                                                                                                         | React | N.A.     | N.A. |
| Csevegési szál           | A szál összetevője, amely a felhasználótól érkező és küldött üzeneteket jeleníti meg a küldő adataival együtt. A szál támogatja a kijelzők és az olvasási visszaigazolások begépelését. Ezeket a szálakat görgetheti a csevegési előzmények áttekintéséhez.
| Résztvevők listája      | Megjeleníti a hívás vagy a csevegési szál összes résztvevőjét listaként.  | React | N.A.     | N.A. |

## <a name="ui-framework-capabilities"></a>Felhasználói felületi keretrendszer képességei

| Szolgáltatás                                                             | Csoportos hívás összetett | Csoportos csevegés – összetett | Alapösszetevők |
|---------------------------------------------------------------------|-------------------------|----------------------|-----------------|
| Join Teams Meeting                                                  |                         |                      |           
| Csatlakozás a csapatok élő eseményéhez                                               |                         |                      | 
| VoIP-hívás elindítása a csapatok felhasználója számára                                       |                         |                      | 
| Csatlakozás csapatokhoz csevegéssel                                           |                         |                      |            
| Csatlakozás az Azure Communication Services-híváshoz a csoport azonosítójával                | ✔                      |                      | ✔
| VoIP-hívás indítása egy vagy több Azure Communication Services-felhasználó számára |                         |                      |           
| Csatlakozás Azure kommunikációs szolgáltatásokhoz csevegési szál                    |                         | ✔                   | ✔
| Hívás némítása/feloldása                                                    | ✔                       |                      | ✔
| Videó be-és kikapcsolása a híváskor                                                | ✔                       |                      | ✔
| Képernyő megosztása                                                      | ✔                       |                      | ✔
| Résztvevői katalógus                                                 | ✔                       |                      | ✔
| Mikrofon kezelése                                               | ✔                       |                      | ✔
| Kamera kezelése                                                   | ✔                       |                      | ✔
| Lobby hívása                                                          |                         |                      | ✔
| Csevegési üzenet küldése                                                   |                         | ✔                   |            
| Csevegési üzenet fogadása                                                |                         | ✔                   | ✔
| Kijelzők begépelése                                                   |                         | ✔                   | ✔
| Olvasási visszaigazolás                                                        |                         | ✔                   | ✔
| Résztvevők listája                                                    |                         |                      | ✔


## <a name="customization-support"></a>Testreszabási támogatás

| Összetevő típusa            | Témák     | Layout                                                              | Adatmodellek |
|---------------------------|------------|---------------------------------------------------------------------|-------------|
| Összetett összetevő       |     N.A.    | N.A.                                                                 |     N.A.     |
| Alapösszetevő            |     N/A    | Az összetevők elrendezése külső stílus használatával módosítható         |     N/A     |


## <a name="platform-support"></a>Platformtámogatás

| SDK    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
|--------|--------------------|----------------------|----------|----------|----------|------------|
| FELHASZNÁLÓI FELÜLETI SDK | Chrome \* , új Edge | Chrome \* , Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*Vegye figyelembe, hogy az előző két kiadáson felül a Chrome legújabb verziója is támogatott.

\*\*Vegye figyelembe, hogy a Safari 13.1 + verziója támogatott. A Safari macOS-hez készült kimenő videó még nem támogatott, de iOS rendszeren támogatott. A kimenő képernyő megosztása csak asztali iOS rendszeren támogatott.
