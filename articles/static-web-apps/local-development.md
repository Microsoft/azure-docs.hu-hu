---
title: Helyi fejlesztés beállítása a Azure Static Web Apps
description: Ismerje meg, hogyan állíthatja be a helyi fejlesztési környezetet Azure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: e19d39a32d48ec55473bb957595d47ec5148e74b
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588785"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Helyi fejlesztés beállítása Azure Static Web Apps előzetes verzióhoz

A felhőben való közzététel után egy Azure Static Web Apps webhely számos olyan szolgáltatást kínál, amelyek úgy működnek együtt, mintha egy alkalmazás lenne. Ilyen szolgáltatások többek között a következők:

- A statikus webalkalmazás
- Azure Functions API
- Hitelesítési és engedélyezési szolgáltatások
- Útválasztási és konfigurációs szolgáltatások

Ezeknek a szolgáltatásoknak kommunikálniuk kell egymással, és Azure Static Web Apps az integrációt a felhőben.

Helyi futtatás esetén azonban ezek a szolgáltatások nem kötődnek automatikusan egymáshoz.

Ahhoz, hogy hasonló élményt nyújtson az Azure-ban, a [Azure Static Web Apps CLI](https://github.com/Azure/static-web-apps-cli) a következő szolgáltatásokat biztosítja:

- Helyi statikus helykiszolgáló
- Az előoldali keretrendszer fejlesztői kiszolgálójának proxyja
- Az API-végpontok proxyja – elérhető a Azure Functions Core Tools
- Egy hitelesítési és engedélyezési kiszolgáló utánzata
- Helyi útvonalak és konfigurációs beállítások kényszerítés

## <a name="how-it-works"></a>Működés

Az alábbi diagram a kérések helyi kezelését mutatja be.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Az Azure Static Web App CLI kérés- és válaszfolyama":::

> [!IMPORTANT]
> Lépjen a `http://localhost:4280` következőre a CLI által kiszolgált alkalmazás eléréséhez: .

- **A rendszer** a kérés típusától függően továbbítja a portra vonatkozó kéréseket a `4280` megfelelő kiszolgálóra.

- **A statikus** tartalomkéréseket, például a HTML-t vagy a CSS-t a belső CLI statikus tartalomkiszolgálója vagy az előtere-keretrendszer kiszolgálója kezeli hibakereséshez.

- **A hitelesítési és engedélyezési** kérelmeket egy emulátor kezeli, amely hamis identitásprofilt biztosít az alkalmazásnak.

- **A Functions Core Tools-futtatás** kezeli a webhely API-jának kérését.

- **Az** összes szolgáltatás válasza úgy lesz visszaadva a böngészőnek, mintha mindegyik egyetlen alkalmazás lenne.

## <a name="prerequisites"></a>Előfeltételek

- **Meglévő Azure Static Web Apps:** Ha még nincs ilyen alkalmazása, kezdje a [vanilla-api](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate) starter alkalmazással.
- **[Node.js](https://nodejs.org) az npm-et:** Futtassa a [Node.js LTS-verziót,](https://nodejs.org) amely az npm-hez való [hozzáférést is tartalmazza.](https://www.npmjs.com/)
- **[Visual Studio Code:](https://code.visualstudio.com/)** Az API-alkalmazás hibakereséséhez használatos, de nem szükséges a CLI-hez.

## <a name="get-started"></a>Bevezetés

Nyisson meg egy terminált a meglévő Azure Static Web Apps gyökérmappába.

1. Telepítse a CLI-t.

    `npm install -g @azure/static-web-apps-cli`

1. Szükség esetén készítse el az alkalmazást.

    Futtassa a parancsot, vagy `npm run build` a projekt megfelelő parancsát.

1. Váltsa át az alkalmazás kimeneti könyvtárát. A kimeneti mappákat gyakran buildnek _vagy_ hasonlónak nevezik.

1. Indítsa el a CLI-t.

    `swa start`

1. Az alkalmazás http://localhost:4280 böngészőben való megtekintéséhez lépjen a következőre: .

### <a name="other-ways-to-start-the-cli"></a>A CLI egyéb indítási módjai

| Description | Parancs |
|--- | --- |
| Adott mappa kiszolgálása | `swa start ./output-folder` |
| Futó keretrendszer-fejlesztési kiszolgáló használata | `swa start http://localhost:3000` |
| Functions-alkalmazás megnyitása egy mappában | `swa start ./output-folder --api ./api` |
| Futó Functions-alkalmazás használata | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Engedélyezés és hitelesítés emulációja

A Static Web Apps CLI emulálja [az](./authentication-authorization.md) Azure-ban megvalósított biztonsági folyamatot. Amikor egy felhasználó bejelentkezik, definiálhat egy hamis identitásprofilt, amely vissza lesz küldve az alkalmazásnak.

Amikor például a oldalra próbál navigálni, a rendszer egy olyan oldalt ad vissza, amely lehetővé teszi `/.auth/login/github` egy identitásprofil definiálása.

> [!NOTE]
> Az emulátor minden biztonsági szolgáltatóval működik, nem csak a GitHubbal.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Helyi hitelesítési és engedélyezési emulátor":::

Az emulátor biztosít egy oldalt, amely lehetővé teszi a következő egyszerű [ügyfélértékek megszabadítását:](./user-information.md#client-principal-data)

| Érték | Leírás |
| --- | --- |
| **Felhasználónév** | A biztonsági szolgáltatóhoz társított fióknév. Ez az érték az ügyfélnév tulajdonságaként jelenik meg, és automatikusan lesz generálva, ha nem `userDetails` ad meg értéket. |
| **Felhasználói azonosító** | A CLI által automatikusan létrehozott érték.  |
| **Szerepkörök** | Szerepkörnevek listája, ahol minden név egy új sorban található.  |

Bejelentkezés után:

- A végpont vagy egy függvényvégpont használatával lekérheti a felhasználó egyszerű `/.auth/me` [ügyfélprogramját.](./user-information.md)

- A rendszer törli az ügyfélnevet, és `./auth/logout` kijelentkezteti az utánzó felhasználót.

## <a name="debugging"></a>Hibakeresés

Egy statikus webalkalmazásban két hibakeresési környezet van. Az első a statikus tartalom webhelyére, a második az API-függvényekére. A helyi hibakeresés úgy lehetséges, hogy a Static Web Apps CLI fejlesztési kiszolgálókat használ mindkét környezetben.

Az alábbi lépések egy olyan gyakori forgatókönyvet mutatnak be, amely fejlesztési kiszolgálókat használ mindkét hibakeresési környezethez.

1. Indítsa el a statikus hely fejlesztői kiszolgálóját. Ez a parancs az Ön által használt előoldali keretrendszerre vonatkozik, de gyakran olyan parancsok formájában érkezik, mint a `npm run build` `npm start` , vagy `npm run dev` .

1. Nyissa meg az API-alkalmazás mappáját Visual Studio Code-ban, és indítson el egy hibakeresési munkamenetet.

1. Adja át a statikus kiszolgáló és az API-kiszolgáló címeit a parancsnak úgy, hogy `swa start` sorrendbe sorolja őket.

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

Az alábbi képernyőképek egy tipikus hibakeresési forgatókönyv terminálit mutatják be:

A statikus tartalom webhelye a következőn keresztül `npm run dev` fut: .

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Statikus hely fejlesztői kiszolgálója":::

A Azure Functions API-alkalmazás hibakeresési munkamenetet futtat a Visual Studio Code-ban.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Visual Studio Code API hibakeresése":::

A Static Web Apps CLI mindkét fejlesztői kiszolgálóval elindul.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Azure Static Web Apps CLI-terminál":::

A porton áthaladó kérések mostantól a statikus tartalomfejlesztési kiszolgálóra vagy az API hibakeresési munkamenetére `4280` vannak irányítva.

A különböző hibakeresési forgatókönyvekkel kapcsolatos további információkért és a portok és kiszolgálócímek testreszabásával kapcsolatos útmutatásért tekintse meg a [Azure Static Web Apps CLI-adattárat.](https://github.com/Azure/static-web-apps-cli)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az alkalmazás konfigurálása](configuration.md)
