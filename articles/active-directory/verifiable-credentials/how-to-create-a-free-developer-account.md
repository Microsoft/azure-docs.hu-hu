---
title: Ingyenes Azure Active Directory fejlesztői bérlő létrehozása
description: Ebből a cikkből megtudhatja, hogyan hozhat létre fejlesztői fiókot
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 1e8bb59c09622a39dad680940ff34e643ee0cc3b
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222741"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Ingyenes Azure Active Directory fejlesztői bérlő létrehozása

> [!IMPORTANT]
> Azure Active Directory ellenőrizhető hitelesítő adatok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Az előzetes verzióban egy P2-licencre van szükség. 

A P2 próbaverziós licenccel kétféleképpen hozhat létre ingyenes Azure Active Directoryokat, így telepítheti a hitelesíthető hitelesítő adatok kiállítói szolgáltatását, és ellenőrizheti a ellenőrizhető hitelesítő adatok létrehozását és érvényesítését:

- [Csatlakozzon](https://aka.ms/o365devprogram) az ingyenes Microsoft 365 fejlesztői programhoz, és szerezzen be egy ingyenes homokozót, eszközt és egyéb erőforrásokat, például egy Azure Active Directory P2 licenccel. Konfigurált felhasználók, csoportok, postaládák stb.
- Hozzon létre egy új [bérlőt](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) , és aktiválja az új bérlő prémium szintű Azure ad P1 vagy P2 [ingyenes próbaverzióját](https://azure.microsoft.com/trial/get-started-active-directory/) .

Ha úgy dönt, hogy regisztrálja az ingyenes Microsoft 365 fejlesztői programot, néhány egyszerű lépést végre kell hajtania:

1. Kattintson a **Csatlakozás most** gombra a képernyőn.

2. Jelentkezzen be egy új Microsoft-fiókkal, vagy használjon már meglévő (munkahelyi) fiókot.

3. A regisztráció lapon válassza ki a régiót, adja meg a vállalat nevét, és fogadja el a program használati feltételeit, mielőtt a **tovább** gombra kattint.

4. Kattintson az **előfizetés beállítása** elemre. Adja meg azt a régiót, ahol létre szeretné hozni az új bérlőt, hozzon létre egy felhasználónevet, egy tartományt, és adjon meg egy jelszót. Ekkor létrejön egy új bérlő és a bérlő első rendszergazdája.

5. Adja meg az új bérlő rendszergazdai fiókjának védelméhez szükséges biztonsági információkat. Ez a beállítás a fiók MFA-hitelesítését fogja beállítani.


Ezen a ponton létrehozta a bérlőt 25 E5 felhasználói licenccel. Az E5-licencek tartalmazzák az Azure AD P2-licenceket. Megadhatja a felhasználókkal, csoportokkal, e-mailekkel és a SharePointgal rendelkező mintaadatok-csomagokat is, amelyek segítségével tesztelheti a fejlesztési környezetét. A ellenőrizhető hitelesítő adatokat kiállító szolgáltatáshoz nem szükségesek.

Az Ön kényelme érdekében saját munkahelyi fiókot is hozzáadhat a [vendégként](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) az újonnan létrehozott bérlőben, és ezt a fiókot használhatja a bérlő felügyeletéhez. Ha azt szeretné, hogy a vendég fiók kezelni tudja a hitelesíthető hitelesítőadat-szolgáltatást, a globális rendszergazda szerepkört hozzá kell rendelnie az adott felhasználóhoz.

## <a name="next-steps"></a>Következő lépések

Most, hogy van egy fejlesztői fiókja, kipróbálhatja az [első oktatóanyagot](get-started-verifiable-credentials.md) , amely további információt tartalmaz a ellenőrizhető hitelesítő adatokról.