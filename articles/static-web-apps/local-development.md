---
title: Helyi fejlesztés beállítása az Azure statikus Web Apps
description: Ismerje meg, hogyan állíthatja be helyi fejlesztési környezetét az Azure statikus Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 04/02/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 8a45d490d060febc18d77c8487c9f562fd2a914a
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275516"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Helyi fejlesztés beállítása az Azure statikus Web Apps előzetes verziójához

Amikor közzétette a felhőben, az Azure statikus Web Apps webhelye számos olyan szolgáltatást tartalmaz, amelyek ugyanúgy működnek, mint az alkalmazás. Ilyen szolgáltatások többek között a következők:

- A statikus webalkalmazás
- Azure Functions API
- Hitelesítési és engedélyezési szolgáltatások
- Útválasztási és konfigurációs szolgáltatások

Ezeknek a szolgáltatásoknak kommunikálnia kell egymással, és az Azure statikus Web Apps kezeli ezt az integrációt a felhőben.

A helyileg futtatott szolgáltatások azonban nem kapcsolódnak automatikusan egymáshoz.

Ahhoz, hogy az Azure-ban milyen élményt nyújtson, az [Azure statikus Web Apps CLI](https://github.com/Azure/static-web-apps-cli) a következő szolgáltatásokat nyújtja:

- Helyi statikus hely kiszolgálója
- Az előtér-keretrendszer fejlesztői kiszolgálójának proxyja
- Proxy az API-végpontok számára – elérhető Azure Functions Core Tools
- Egy ál-hitelesítési és engedélyezési kiszolgáló
- Helyi útvonalak és konfigurációs beállítások kényszerítése

## <a name="how-it-works"></a>Működés

A következő diagram bemutatja, hogyan kezeli a rendszer a kérelmeket helyileg.

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Azure-beli statikus webalkalmazás parancssori felületi kérelme és a válasz folyamatábrája":::

> [!IMPORTANT]
> Navigáljon a [http://localhost:4280](http://localhost:4280) parancssori felület által kiszolgált alkalmazás eléréséhez.

- A portra **irányuló kéréseket** a rendszer a `4280` kérelem típusától függően továbbítja a megfelelő kiszolgálóra.

- A **statikus tartalomra** vonatkozó kéréseket (például a HTML-t vagy a CSS-t) a belső CLI statikus tartalom-kiszolgáló vagy az előtér-keretrendszer kiszolgálója kezeli a hibakereséshez.

- A **hitelesítési és engedélyezési** kérelmeket egy emulátor kezeli, amely hamis azonosító profilt biztosít az alkalmazás számára.

- A **functions Core Tools futtatókörnyezete** kezeli a kérelmeket a hely API-jával.

- A rendszer az összes szolgáltatás **válaszait** visszaadja a böngészőnek, mintha egyetlen alkalmazás lenne.

## <a name="prerequisites"></a>Előfeltételek

- **Meglévő Azure statikus Web Apps webhely**: Ha még nem rendelkezik ilyennel, kezdje a [Vanilla-API](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate) Starter alkalmazással.
- **[Node.js](https://nodejs.org) a NPM**-mel: futtassa a [Node.js LTS](https://nodejs.org) -verziót, amely magában foglalja a [NPM](https://www.npmjs.com/)való hozzáférést.
- **[Visual Studio Code](https://code.visualstudio.com/)**: az API-alkalmazás hibakereséséhez használatos, de a parancssori felülethez nem szükséges.

## <a name="get-started"></a>Bevezetés

Nyisson meg egy terminált a meglévő Azure statikus Web Apps-hely gyökérkönyvtárában.

1. Telepítse a CLI-t.

    `npm install -g @azure/static-web-apps-cli`

1. Készítse elő az alkalmazást, ha az alkalmazása szükséges.

    Futtassa `npm run build` a parancsot, vagy a projekt egyenértékű parancsát.

1. Váltson az alkalmazás kimeneti könyvtárába. A kimeneti mappák neve gyakran _Build_ vagy valami hasonló.

1. Indítsa el a CLI-t.

    `swa start`

1. A gombra [http://localhost:4280](http://localhost:4280) kattintva megtekintheti az alkalmazást a böngészőben.

### <a name="other-ways-to-start-the-cli"></a>A CLI indításának egyéb módjai

| Leírás | Parancs |
|--- | --- |
| Egy adott mappa kiszolgálása | `swa start ./output-folder` |
| Futó keretrendszer fejlesztői kiszolgáló használata | `swa start http://localhost:3000` |
| Functions-alkalmazás elindítása egy mappában | `swa start ./output-folder --api ./api` |
| Futó függvények alkalmazás használata | `swa start ./output-folder --api http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Engedélyezési és hitelesítési emuláció

A statikus Web Apps CLI az Azure-ban megvalósított [biztonsági folyamatot](./authentication-authorization.md) emulálja. Amikor egy felhasználó bejelentkezik, megadhatja az alkalmazásnak visszaadott hamis identitási profilt.

Ha például megpróbálja megkeresni a-t `/.auth/login/github` , a rendszer egy lapot ad vissza, amely lehetővé teszi egy Identity profil definiálását.

> [!NOTE]
> Az emulátor bármilyen biztonsági szolgáltatóval működik, nem csak a GitHubon.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Helyi hitelesítési és engedélyezési emulátor":::

Az emulátor egy olyan oldalt biztosít, amely lehetővé teszi a következő [ügyfél](./user-information.md#client-principal-data) -értékek megadását:

| Érték | Leírás |
| --- | --- |
| **Felhasználónév** | A biztonsági szolgáltatóhoz társított fióknév. Ez az érték jelenik meg az `userDetails` ügyfél rendszerbiztonsági tagjaként, és automatikusan létrejön, ha nem ad meg értéket. |
| **Felhasználói azonosító** | A CLI által automatikusan generált érték.  |
| **Szerepkörök** | A szerepkörök neveinek listája, ahol az egyes nevek új sorban vannak.  |

Bejelentkezés után:

- A `/.auth/me` végpont vagy a függvény végpontja segítségével kérheti le a felhasználó ügyfél- [rendszerbiztonsági](./user-information.md)tagját.

- Navigáljon az `./auth/logout` egyszerű ügyfél törléséhez, és jelentkezzen ki az ál-felhasználót.

## <a name="debugging"></a>Hibakeresés

Két hibakeresési kontextus található egy statikus webalkalmazásban. Az első a statikus tartalom webhelye, a második pedig az API functions. A helyi hibakeresést úgy teheti meg, hogy a statikus Web Apps parancssori felület a következő környezetek egyikéhez vagy mindkettőhöz használja a fejlesztői kiszolgálókat.

A következő lépések egy olyan általános forgatókönyvet mutatnak be, amely fejlesztői kiszolgálókat használ a hibakeresési környezetekhez.

1. Indítsa el a statikus hely fejlesztői kiszolgálóját. Ez a parancs az Ön által használt előtér-keretrendszerre vonatkozik, de gyakran olyan parancsok formájában történik, mint a `npm run build` , `npm start` vagy a `npm run dev` .

1. Nyissa meg az API-alkalmazás mappáját a Visual Studio Code-ban, és indítsa el a hibakeresési munkamenetet.

1. Adja át a statikus kiszolgáló és az API-kiszolgáló címét a `swa start` parancsnak a sorrendben való listázásával.

    `swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api=http://localhost:7071`

A következő képernyőképek egy tipikus hibakeresési forgatókönyvhöz tartozó terminálokat mutatnak be:

A statikus tartalom helye a-on keresztül fut `npm run dev` .

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Statikus hely fejlesztői kiszolgálója":::

A Azure Functions API-alkalmazás hibakeresési munkamenetet futtat a Visual Studio Code-ban.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Visual Studio Code API-hibakeresés":::

A statikus Web Apps CLI mindkét fejlesztői kiszolgáló használatával indul el.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Azure statikus Web Apps CLI-terminál":::

Most a porton keresztül érkező kérések `4280` átirányítva a statikus tartalomkezelő kiszolgálóra vagy az API-hibakeresési munkamenetre.

A különböző hibakeresési forgatókönyvekkel kapcsolatos további információkért, a portok és a kiszolgálók címének testreszabásával kapcsolatos útmutatásért tekintse meg az [Azure statikus Web Apps CLI-tárházat](https://github.com/Azure/static-web-apps-cli).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az alkalmazás konfigurálása](configuration.md)
