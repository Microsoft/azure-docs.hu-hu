---
title: Új Windows 10-es eszköz csatlakoztatása az Azure AD-hez első futtatáskor | Microsoft Docs
description: Hogyan állíthatók be a felhasználók az Azure AD JOIN szolgáltatásba a beépített felhasználói élményben.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: da37316724bf6ef166f08faa7208ad196000bb00
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85253103"
---
# <a name="tutorial-join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Oktatóanyag: Új Windows 10-es eszköz csatlakoztatása az Azure AD-hez első futtatáskor

Az Azure Active Directory (Azure AD) eszközfelügyeletével biztosítható, hogy a felhasználók csak olyan eszközökről férjenek hozzá a vállalat erőforrásaihoz, amelyek megfelelnek a biztonsági és megfelelőségi szabványoknak. További információ: [Az Azure Active Directory eszközkezelésének alapjai](overview.md).

Windows 10 rendszeren az új eszközöket az első indítás (FRX) során csatlakoztatni lehet az Azure AD-hez.  
Ez lehetővé teszi bontatlan eszközök átadását az alkalmazottaknak vagy a tanulóknak.

Ha egy eszközön a Windows 10 Professional vagy a Windows 10 Enterprise rendszer van telepítve, az FRX élmény alapértelmezés szerint a vállalati eszközök beállítási folyamatát indítja el.

A Windows *kezdőélménye* nem támogatja a helyszíni Azure Active Directory- (AD-) tartományokhoz való csatlakozást. Ha azt tervezi, hogy a számítógépet csatlakoztatja egy AD-tartományhoz, a beállítás során válassza a **Windows helyi fiókkal való beállítására szolgáló** lehetőséget. Ezután a számítógép beállításai között csatlakozhat a tartományhoz.
 
Ebben az oktatóanyagban megismerheti, hogyan csatlakoztathat egy eszközt az Azure AD-hez az FRX során:
 > [!div class="checklist"]
> * Előfeltételek
> * Eszköz csatlakoztatása
> * Ellenőrzés

## <a name="prerequisites"></a>Előfeltételek

Egy Windows 10-es eszköz csatlakoztatásához úgy kell konfigurálni az eszközregisztrációs szolgáltatást, hogy engedélyezze az eszközök regisztrálását. Az eszközök csatlakoztatásának engedélyezése mellett az is feltétel, hogy az engedélyezett maximumnál kevesebb eszköz legyen regisztrálva az Azure AD-bérlőn. További információt az [eszközbeállítások konfigurálásáról szóló részben](device-management-azure-portal.md#configure-device-settings) talál.

Ezenfelül ha a bérlő egy összevont bérlő, akkor az identitásszolgáltatónak támogatnia KELL a WS-Fed és a WS-Trust felhasználónév/jelszó végpontokat. Ez 1.3-as vagy 2005-ös verziójú lehet. Ez a protokoll-támogatás szükséges ahhoz, hogy az eszközt csatlakoztatni lehessen az Azure AD-hez, és be kell jelentkeznie az eszközre jelszóval.

## <a name="joining-a-device"></a>Eszköz csatlakoztatása

**Windows 10-es eszköz csatlakoztatása az Azure AD-hez az FRX során:**

1. Amikor bekapcsolja az új eszközt, és elindul a beállítás folyamata, a **Felkészülés** üzenetnek kell megjelennie. Kövesse az utasításokat az eszköz beállításához.
1. Először is adja meg a területi és nyelvi beállításokat. Azután fogadja el a Microsoft szoftverlicenc-szerződését.
 
    ![Régió testreszabása](./media/azuread-joined-devices-frx/01.png)

1. Válassza ki a hálózatot, amelyen keresztül csatlakozni fog az internethez.
1. Válassza az **Ez az eszköz a szervezethez tartozik** lehetőséget. 

    ![A számítógép tulajdonosát megjelenítő képernyő](./media/azuread-joined-devices-frx/02.png)

1. Adja meg a vállalat által megadott hitelesítő adatokat, majd kattintson a **Bejelentkezés** gombra.

    ![Bejelentkezés képernyő](./media/azuread-joined-devices-frx/03.png)

1. Az eszköz megkeres egy megfelelő bérlőt az Azure AD-ben. Ha az eszköz egy összevont tartományhoz tartozik, akkor a rendszer átirányítja a helyszíni biztonsági jegykiadó szolgáltatás (STS) kiszolgálójához, például az Active Directory összevonási szolgáltatásokhoz (AD FS).
1. Ha egy nem összevont tartományhoz tartozik, akkor a hitelesítő adatokat közvetlenül az Azure AD-oldalon lehet megadni. 
1. A rendszer többtényezős hitelesítést kér. 
1. Az Azure AD ellenőrzi, hogy szükséges-e regisztráció a mobileszköz-felügyeleti megoldásban.
1. A Windows regisztrálja az eszközt a szervezet Azure AD-beli címtárában, és ha szükséges, regisztrálja a mobileszköz-felügyeletben.
1. Ha Ön:
   - Felügyelt felhasználó, a Windows az asztalra lép tovább az automatikus bejelentkezési folyamaton keresztül.
   - Összevont felhasználó, a Windows átirányítja a bejelentkezési képernyőre, amely a hitelesítő adatainak megadását kéri.

## <a name="verification"></a>Ellenőrzés

Annak ellenőrzéséhez, hogy a Windows-eszköz csatlakozik-e az Azure AD-hez, használja a **Hozzáférés munkahelyi vagy iskolai rendszerhez** párbeszédpanelt. Ez jelzi, hogy az eszköz csatlakozik-e az Azure AD-címtárhoz.

![Hozzáférés munkahelyi vagy iskolai rendszerhez](./media/azuread-joined-devices-frx/13.png)

## <a name="next-steps"></a>Következő lépések

- További információ: [Az Azure Active Directory eszközkezelésének alapjai](overview.md).
- További információk az eszközök Azure Portalon végzett felügyeletéről: [Eszközfelügyelet az Azure AD Portalon](device-management-azure-portal.md).
