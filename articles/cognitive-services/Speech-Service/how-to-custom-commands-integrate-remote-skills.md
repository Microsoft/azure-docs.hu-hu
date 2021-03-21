---
title: 'Útmutató: egyéni parancsok alkalmazás exportálása távoli szakértelem-beszédfelismerési szolgáltatásként'
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan exportálhatja a Custom commands alkalmazást szaktudásként
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 77ade17803a35491712ec6df70aed9eb7b4883eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025874"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>Egyéni parancsok alkalmazás exportálása távoli képességként

Ebből a cikkből megtudhatja, hogyan exportálhat egy egyéni parancs-alkalmazást távoli képességként.

## <a name="prerequisites"></a>Előfeltételek
> [!div class="checklist"]
> * [A bot Framework ismeretének megismerése](/azure/bot-service/skills-conceptual)
> * [A szaktudás jegyzékének megismerése](https://aka.ms/speech/cc-skill-manifest)
> * [A robot-keretrendszer robotja által létrehozott szaktudás meghívása](/azure/bot-service/skills-about-skill-consumers)
> * Egy meglévő egyéni parancsok alkalmazása. Ha nem rendelkezik egyéni parancsokat használó alkalmazással, próbálja ki a- [Gyorsindítás: hangsegéd létrehozása egyéni parancsokkal](quickstart-custom-commands-application.md)

## <a name="custom-commands-as-remote-skills"></a>Egyéni parancsok távoli képességekként
* A bot Framework szaktudása újra felhasználható társalgási képességekkel rendelkező építőelemeket, amelyek a társalgási használatot is lehetővé teszik, hogy percek alatt hozzáadhat széles körű funkciókat a Robothoz. További információért látogasson el a [bot Framework szaktudására](https://microsoft.github.io/botframework-solutions/overview/skills/).
* Az egyéni parancsok alkalmazása készségként is exportálható. Ezt a képességet ezután a robot-keretrendszer robotjának távoli képességek protokollján keresztül lehet meghívni.

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>Távoli képességként elérhetővé tett alkalmazás konfigurálása

### <a name="application-level-settings"></a>Alkalmazás szintű beállítások
1. A bal oldali panelen válassza a **Beállítások**  >  **távoli képességek** lehetőséget.
1. A **távoli képességek engedélyezve** váltógomb beállítása be értékre.

### <a name="authentication-to-skills"></a>A szaktudás hitelesítése
1. Ha engedélyezni szeretné a hitelesítést, adja hozzá az egyéni parancsok alkalmazás meghívásához használni kívánt robot Framework bots Microsoft-alkalmazás-azonosítóit.
      > [!div class="mx-imgBorder"]
      > ![MSA-azonosító hozzáadása a szakértelemhez](media/custom-commands/skill-add-msa-id.png)

1. Ha legalább egy bejegyzés hozzá van adva a listához, a hitelesítés engedélyezve lesz az alkalmazásban, és csak az engedélyezett robotok hívhatják meg az alkalmazást.
> [!TIP]
>  A hitelesítés letiltásához törölje az összes Microsoft-alkalmazás azonosítóját az engedélyezett listából. 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>Az elérhetővé tett parancsok engedélyezése/letiltása

Lehetősége van kiválasztani, hogy mely parancsokat kívánja exportálni távoli képességeken keresztül.

1. Ha készségeket szeretne közzétenni, válassza az **új parancs engedélyezése** a **parancsok engedélyezése** lehetőséggel lehetőséget.
1. A legördülő listából válassza ki a hozzáadni kívánt parancsot.
1. Kattintson a **Mentés** gombra.

### <a name="configure-triggering-utterances-for-commands"></a>A parancsok kiváltó hosszúságú kimondott szöveg konfigurálása
Az egyéni parancsok a parancsokhoz konfigurált példa mondatokat használják a hosszúságú kimondott szöveg kiváltó képességek létrehozásához. Ezeket a **kiváltó hosszúságú kimondott szöveg** a rendszer a **kiosztó** szakaszt a [**szakértelem jegyzékfájljának**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)létrehozásához használja.

Szerzőként érdemes lehet megszabni, hogy a példák milyen **mondatokat** használjanak a kiváltó hosszúságú kimondott szöveg létrehozásához.
1. Alapértelmezés szerint a parancs összes **kiváltó példája** szerepelni fog a jegyzékfájlban.
1. Ha explicit módon el szeretné távolítani valamelyik példát, válassza a parancs ikonjának **szerkesztése** lehetőséget az **engedélyezett parancsok a képességek számára** szakaszban.
    > [!div class="mx-imgBorder"]
    > ![Engedélyezett parancs szerkesztése a szakértelemhez](media/custom-commands/skill-edit-enabled-command.png)

1. Ezután a kihagyni kívánt példa mondatokban kattintson a **jobb gombbal** a  >  **példa mondat letiltása** elemre.
    > [!div class="mx-imgBorder"]
    > ![Példák letiltása](media/custom-commands/skill-disable-example-sentences.png)

1. Kattintson a **Mentés** gombra.
1. Megfigyelheti, hogy ebben az ablakban nem adhat hozzá új példát. Ha erre szükség van, folytassa a kilépés a beállítások szakaszból, és válassza ki a megfelelő parancsot a **parancsok** alapján. Ezen a ponton az új bejegyzést felveheti a **példa mondatok** szakaszba. Ez a módosítás automatikusan megjelenik a parancs távoli képességek beállításainak értékében.

> [!IMPORTANT]
> Ha a meglévő példa mondatok a **String > Catalog** adattípusra hivatkoznak, akkor ezek a mondatok automatikusan ki lesznek hagyva a hosszúságú kimondott szöveg-kiváltó listából. 

## <a name="download-skill-manifest"></a>Ügyességi jegyzékfájl letöltése
1. Miután **közzétette** az alkalmazást, letöltheti a skill manifest-fájlt.
1. A skill manifest használatával konfigurálhatja a robot Framework felhasználói robotot, hogy az egyéni parancsok szaktudására hívjon.
> [!IMPORTANT]
> A szaktudás jegyzékfájljának letöltéséhez **közzé** kell tennie az egyéni parancsok alkalmazását. </br>
> Ha **bármilyen módosítást** végzett az alkalmazásban, akkor újra közzé kell tennie az alkalmazást, hogy a legutóbbi módosítások tükrözzék a jegyzékfájlt.

> [!NOTE]
> Ha az alkalmazás közzétételével kapcsolatos problémákba ütközik, és a hiba a hosszúságú kimondott szöveg-t kiváltó készségekre irányítja, ellenőrizze újra az **engedélyezett parancsok** konfigurációját. Az összes elérhető parancsnak rendelkeznie kell legalább egy érvényes kiváltó kiírással.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Parancs frissítése az ügyfélről](./how-to-custom-commands-update-command-from-client.md)
