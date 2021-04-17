---
title: Ingyenes Azure Active Directory létrehozása
description: Ez a cikk bemutatja, hogyan hozhat létre fejlesztői fiókot
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a50c8b083c1cd453dbe3c51c63ec9cf53859c3bd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587265"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Ingyenes Azure Active Directory létrehozása

> [!IMPORTANT]
> Azure Active Directory hitelesítő adatok ellenőrizhetővé tehetők jelenleg nyilvános előzetes verzióban.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Az előzetes verzióban P2 licenc szükséges. 

A P2 próbaverziós licenccel rendelkező ingyenes Azure Active Directory két könnyen létrehozható, így telepítheti az Ellenőrizhető hitelesítőadat-kiállító szolgáltatást, és tesztelheti az ellenőrizhető hitelesítő adatok létrehozását és érvényességét:

- [Csatlakozzon](https://aka.ms/o365devprogram) az ingyenes Microsoft 365 fejlesztői programhoz, és kap egy ingyenes védőfalat, eszközöket és egyéb erőforrásokat, például Azure Active Directory P2 licenccel rendelkező alkalmazást. Konfigurált felhasználók, csoportok, postaládák stb.
- Hozzon létre egy [új bérlőt,](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) és aktiválja [prémium szintű Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/) P1 vagy P2 ingyenes próbaverzióját az új bérlőben.

Ha úgy dönt, hogy regisztrál az ingyenes Microsoft 365 fejlesztői programra, néhány egyszerű lépést kell követnie:

1. Kattintson a **csatlakozás most** gombra a képernyőn.

2. Jelentkezzen be egy új Microsoft-fiókkal, vagy használjon egy már meglévő (munkahelyi) fiókot.

3. A bejelentkezési oldalon válassza ki a régiót, adja meg a vállalat nevét, és fogadja el a program feltételeit, mielőtt a Tovább **gombra kattint.**

4. Kattintson az **előfizetés beállítására.** Adja meg a régiót, ahol létre szeretné hozni az új bérlőt, hozzon létre egy felhasználónevet és egy tartományt, és adjon meg egy jelszót. Ezzel létrehoz egy új bérlőt és a bérlő első rendszergazdáját.

5. Adja meg az új bérlő rendszergazdai fiókjának védelméhez szükséges biztonsági adatokat. Ezzel beállít egy MFA-hitelesítést a fiókhoz.


Ezen a ponton létrehozott egy bérlőt 25 E5 felhasználói licenccel. Az E5-licencek tartalmazzák az Azure AD P2 licenceket. Igény szerint felhasználókkal, csoportokkal, levelezéssel és SharePointtal is hozzáadhat minta-adatcsomagokat, amelyek segítenek a fejlesztési környezetben való tesztelésben. Az ellenőrizhető hitelesítőadat-kiállító szolgáltatáshoz nincs szükség ezekre.

Az Ön kényelme érdekében hozzáadhatja [](/azure/active-directory/external-identities/b2b-quickstart-add-guest-users-portal) a saját munkahelyi fiókját vendégként az újonnan létrehozott bérlőhöz, és ezzel a fiókkal felügyeli a bérlőt. Ha azt szeretné, hogy a vendégfiók kezelni tudja az Ellenőrizhető hitelesítőadat-szolgáltatást, a "Globális rendszergazda" szerepkört hozzá kell rendelnie a felhasználóhoz.

## <a name="next-steps"></a>Következő lépések

Most, hogy már van fejlesztői fiókja, kipróbálhatja az első oktatóanyagot, [amelyből](get-started-verifiable-credentials.md) többet is megtudhat az ellenőrizhető hitelesítő adatokról.
