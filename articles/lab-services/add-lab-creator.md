---
title: Felhasználó hozzáadása tesztkörnyezet létrehozójának Azure Lab Services
description: Ez a cikk bemutatja, hogyan adhat hozzá felhasználót a labor létrehozói szerepkörhöz egy Azure Lab Services-beli labor-fiókhoz. A labor létrehozói létrehozhatnak laborokat ebben a laborban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a6c89483744f943926f126701d28988358627a27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435960"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Tesztkörnyezet-készítők hozzáadása labor-fiókhoz Azure Lab Services
Ebből a cikkből megtudhatja, hogyan adhat hozzá felhasználókat labor-készítőként Azure Lab Services-beli labor-fiókhoz. Ezek az alkalmazások ezután létrehozhatnak laborokat a Lab-fiókban. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Microsoft felhasználói fiók hozzáadása a labor Creator szerepkörhöz
A felhasználónak **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. Ha ugyanazt a felhasználói fiókot tervezi használni az osztályterem-tesztkörnyezet létrehozásához, ezt a lépést kihagyhatja. Ha egy másik felhasználói fiókot tervez használni, kövesse a következő lépéseket: 

Ahhoz, hogy a pedagógusok számára engedélyt adjon a Labs létrehozásához az osztályaik számára, vegye fel őket a **labor létrehozói** szerepkörbe:

1. A **labor-fiók** lapon válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd kattintson a **+ szerepkör-hozzárendelés hozzáadása** elemre az eszköztáron. 

    ![Access Control – > szerepkör-hozzárendelés hozzáadása gomb](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. A **szerepkör-hozzárendelés hozzáadása** lapon válassza a **tesztkörnyezet létrehozója** **szerepkört**, válassza ki azt a felhasználót, amelyet hozzá szeretne adni a labor létrehozói szerepkörhöz, majd válassza a **Mentés** lehetőséget. 

    ![Tesztkörnyezet létrehozójának hozzáadása](./media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Ha nem Microsoft-fiók felhasználót ad hozzá labor creatorként, tekintse meg a [nem Microsoft-fiók felhasználó hozzáadása labor creatorként](#add-a-non-microsoft-account-user-as-a-lab-creator) című szakaszt. 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Nem Microsoft-fiók felhasználó hozzáadása labor creatorként
Ha egy felhasználót labor creatorként szeretne felvenni, használja az e-mail-fiókját. A következő típusú e-mail-fiókokat lehet használni:

- Az egyetemi Azure Active Directory (HRE) által biztosított e-mail-fiók.
- Egy Microsoft e-mail-fiók, például,,, `@outlook.com` `@hotmail.com` `@msn.com` vagy `@live.com` .
- Egy nem a Microsofttól származó e-mail-fiók, például a Yahoo vagy a Google által biztosított. Az ilyen típusú fiókoknak azonban Microsoft-fiókhoz kell kapcsolódniuk.
- Egy GitHub-fiók. Ennek a fióknak egy Microsoft-fiók-vel kell összekapcsolnia.

### <a name="using-a-non-microsoft-email-account"></a>Nem a Microsofttól származó e-mail-fiók használata
A labor-készítők/oktatók a nem a Microsofttól származó e-mail-fiókokkal regisztrálhatnak és bejelentkezhetnek egy osztályterem laborba.  A labor Services portálra való bejelentkezéshez azonban az oktatóknak először létre kell hozniuk egy Microsoft-fiók, amely nem a Microsoft e-mail-címéhez van csatolva.

Lehet, hogy számos oktató már rendelkezik egy Microsoft-fiók a nem Microsoft-e-mail-címeihez. Például az oktatóknak már van Microsoft-fiókuk, ha az e-mail-címüket a Microsoft más termékeivel vagy szolgáltatásaival (például Office, Skype, OneDrive vagy Windows) használták.  

Amikor az oktatók bejelentkeznek a labor Services portálra, a rendszer az e-mail-címük és jelszavuk megadását kéri. Ha az oktató olyan nem Microsoft-fiókba próbál bejelentkezni, amelyhez nincs Microsoft-fiók társítva, akkor az oktató a következő hibaüzenetet kapja: 

![Hibaüzenet](./media/how-to-configure-student-usage/cant-find-account.png)

Microsoft-fiókra való feliratkozáshoz az oktatóknak a következő címen kell megjelenniük: [http://signup.live.com](http://signup.live.com) .  


### <a name="using-a-github-account"></a>GitHub-fiók használata
Az oktatók egy meglévő GitHub-fiókkal is regisztrálhatnak és bejelentkezhetnek egy osztályterem laborba. Ha az oktató már rendelkezik egy Microsoft-fiók a GitHub-fiókjához, akkor bejelentkezhet, és megadhatja a jelszavát az előző szakaszban látható módon. Ha még nem csatolták a GitHub-fiókját egy Microsoft-fiókhoz, akkor a **bejelentkezési lehetőségeket** kell választania:

![Bejelentkezési beállítások hivatkozása](./media/how-to-configure-student-usage/signin-options.png)

A **bejelentkezési beállítások** lapon válassza a **Bejelentkezés a githubkal** lehetőséget.

![Bejelentkezés GitHub-hivatkozással](./media/how-to-configure-student-usage/signin-github.png)

Végül a rendszer kéri, hogy hozzon létre egy Microsoft-fiók, amely a GitHub-fiókjához van csatolva. Automatikusan megtörténik, amikor az oktató kiválasztja a **Next (tovább**) gombot.  Ezután az oktató azonnal bejelentkezik, és csatlakozik az osztályterem laborhoz.


## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Labor tulajdonosaként Labs létrehozása és kezelése](how-to-manage-classroom-labs.md)
- [A labor tulajdonosaként hozzon létre és tegyen közzé sablonokat](how-to-create-manage-template.md)
- [Tesztkörnyezet tulajdonosaként konfigurálhatja és szabályozhatja a labor használatát](how-to-configure-student-usage.md)
- [Labor-felhasználóként a hozzáférés Labs](how-to-use-classroom-lab.md)
