---
title: Üzembe helyezési jogkivonatok alaphelyzetbe állítása az Azure statikus Web Apps (előzetes verzió)
description: Jogkivonatok alaphelyzetbe állítása egy Azure statikus Web Apps-helyen
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: fe1edb2693993d02a705039c18b04c8d1b7b9725
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745537"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps-preview"></a>Üzembe helyezési jogkivonatok alaphelyzetbe állítása az Azure statikus Web Apps (előzetes verzió)

Amikor új Azure statikus Web Apps-helyet hoz létre, az Azure létrehoz egy tokent, amely az alkalmazás azonosítására szolgál az üzembe helyezés során. A kiépítés során ez a token titkosként tárolódik a GitHub-tárházban. Ez a cikk a jogkivonat használatát és kezelését ismerteti.

Általában nem kell aggódnia az üzembe helyezési jogkivonattal kapcsolatban, de a következő okok miatt előfordulhat, hogy a tokent le kell kérnie vagy alaphelyzetbe kell állítania.

* **Jogkivonat-kompromisszum**: a token alaphelyzetbe állítása, ha egy külső fél számára elérhető.
* **Üzembe helyezés egy különálló GitHub-tárházból**: Ha manuálisan telepít egy különálló GitHub-tárházból, akkor az új tárházban be kell állítania a központi telepítési tokent.

## <a name="prerequisites"></a>Előfeltételek

- Egy meglévő GitHub-tárház, amely az Azure statikus Web Apps van konfigurálva.
- Ha még nem rendelkezik ilyennel, tekintse [meg az első statikus alkalmazás felépítése](getting-started.md) című témakört.

## <a name="reset-a-deployment-token"></a>Központi telepítési jogkivonat alaphelyzetbe állítása

1. Kattintson a **telepítési jogkivonat kezelése** hivatkozásra az Azure statikus Web Apps webhely _Áttekintés_ lapján.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="Üzembe helyezési jogkivonat kezelése":::

1. Kattintson a **jogkivonat alaphelyzetbe állítása** gombra.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="Üzembe helyezési jogkivonat alaphelyzetbe állítása":::

1. Miután megjelenített egy új jogkivonatot a _központi telepítési jogkivonat_ mezőben, másolja a tokent a **vágólapra Másolás** ikonra kattintva.


## <a name="update-a-secret-in-the-github-repository"></a>Titkos kód frissítése a GitHub-tárházban

Ahhoz, hogy az automatikus üzembe helyezés megmaradjon, a jogkivonat alaphelyzetbe állítása után be kell állítania az új értéket a megfelelő GitHub-tárházban.

1. Navigáljon a projekt adattárához a GitHubon, és kattintson a **Beállítások** fülre.
1. Kattintson a **titkok** menüpontra. A rendszer a statikus webalkalmazások üzembe helyezése során generált titkos kulcsot _AZURE_STATIC_WEB_APPS_API_TOKEN_... az _adattár titkai_ szakaszban.

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="A tárház titkainak listázása":::

    > [!NOTE]
    > Ha az Azure statikus Web Apps helyet a tárház több ágában hozta létre, több _AZURE_STATIC_WEB_APPS_API_TOKEN_ jelenik meg... a lista titkai. Válassza ki a megfelelőt a statikus Web Apps hely _Áttekintés_ lapján található _munkafolyamat szerkesztése_ mezőben szereplő fájlnévvel egyező módon.

1. Kattintson a **frissítés** gombra a szerkesztő megnyitásához.
1. **Illessze be** a központi telepítési jogkivonat értékét az _érték_ mezőbe.
1. Kattintson a **titok frissítése** elemre.

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="Adattár-titok frissítése":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Közzététel statikuswebhely-generátorból](publish-gatsby.md)
