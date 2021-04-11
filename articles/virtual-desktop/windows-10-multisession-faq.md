---
title: Windows 10 Enterprise multi-session GYIK – Azure
description: Gyakori kérdések és ajánlott eljárások a Windows 10 Enterprise multi-session for Windows Virtual Desktop használatához.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5c03d3f9769aec0736d23f18372701e08ad93dac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802794"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>A Windows 10 Enterprise több munkamenetes használata – gyakori kérdések

Ez a cikk a gyakori kérdésekre adott válaszokat és a Windows 10 Enterprise több munkamenetre vonatkozó ajánlott eljárásokat ismerteti.

## <a name="what-is-windows-10-enterprise-multi-session"></a>Mi a Windows 10 Enterprise több munkamenet?

A Windows 10 Enterprise multi-session, korábbi nevén Windows 10 Enterprise for Virtual desktops (EVD) egy új Távoli asztal munkamenet-gazdagép, amely több egyidejű interaktív munkamenetet tesz lehetővé. Korábban csak a Windows Server képes erre. Ez a funkció lehetővé teszi a felhasználók számára a Windows 10 ismerős élményét, miközben a több munkamenetre kiterjedő, illetve a meglévő felhasználónkénti Windows-licencelést használja az RDS ügyféllicencek (CAL) helyett. A licencekkel és a díjszabással kapcsolatos további információkért lásd: a [Windows rendszerű virtuális asztali díjszabása](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Hány felhasználó tud egyidejűleg interaktív munkamenetet összekapcsolni a Windows 10-es nagyvállalati munkamenetben?

Hány interaktív munkamenet lehet aktív egyszerre a rendszer hardveres erőforrásaira (vCPU, memória, lemez és vGPU), hogy a felhasználók hogyan használják az alkalmazásaikat a munkamenetbe való bejelentkezve, és hogy mekkora a rendszer számítási feladatai. Javasoljuk, hogy ellenőrizze a rendszer teljesítményét, hogy megtudja, hány felhasználóval rendelkezhet a Windows 10 Enterprise több munkamenetben. További információ: a [Windows virtuális asztal díjszabása](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Miért jelent az alkalmazásom a Windows 10-es nagyvállalati többmunkamenetes kiszolgáló operációs rendszert?

A Windows 10 Enterprise multi-session a Windows 10 Enterprise virtuális kiadása. Az egyik különbség az, hogy ez az operációs rendszer (OS) a [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) a 3 értékkel, a Windows Serverrel megegyező értékkel jelenti. Ezzel a tulajdonsággal az operációs rendszer kompatibilis a meglévő RDSH-kezelési eszközökkel, RDSH több munkamenetet támogató alkalmazásokat, és többnyire alacsony szintű rendszerteljesítmény-optimalizálást biztosít a RDSH környezetekhez. Egyes alkalmazás-telepítők letilthatják a telepítést a Windows 10-es rendszeren attól függően, hogy a ProductType az ügyfélre van-e állítva. Ha az alkalmazás nem lesz telepítve, a frissített verzióért forduljon az alkalmazás forgalmazójához.

## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Futtathatom a Windows 10 Enterprise multi-session szolgáltatást a helyszínen?

A Windows 10 Enterprise rendszerű több munkamenet nem futtatható helyszíni éles környezetekben, mert az az Azure-hoz készült Windows Virtual Desktop szolgáltatáshoz van optimalizálva. Ez a licencszerződés arra vonatkozik, hogy az Azure-on kívüli Windows 10 Enterprise-munkamenetet az éles környezetben futtassa. A Windows 10 Enterprise multi-session szolgáltatás nem aktiválja a helyszíni kulcskezelő szolgáltatásokat (KMS).

## <a name="can-i-upgrade-a-windows-10-vm-to-windows-10-enterprise-multi-session"></a>Frissíthetem a Windows 10 rendszerű virtuális gépeket a Windows 10 Enterprise több munkamenetre?

Nem. Jelenleg nem lehetséges olyan meglévő virtuális gép (VM) frissítése, amely Windows 10 Professional vagy Enterprise rendszert futtat Windows 10 Enterprise rendszerű több munkamenetre. Emellett, ha Windows 10 Enterprise rendszerű, több munkamenetből álló virtuális gépet telepít, majd frissíti a termékkulcsot egy másik kiadásra, a virtuális gépet nem lehet visszaváltani a Windows 10-es nagyvállalati munkamenetre, és újra kell telepítenie a virtuális gépet.

## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Hogyan testre szabhatja a Windows 10 Enterprise multi-session rendszerképet a saját szervezetem számára?

Elindíthat egy virtuális gépet az Azure-ban a Windows 10 Windows 10 Enterprise multi-session modullal, és testre szabhatja a LOB-alkalmazások, a Sysprep/általánosítás, majd a Azure Portal használatával létrehozott rendszerkép létrehozásával.

Első lépésként hozzon létre egy virtuális gépet az Azure-ban a Windows 10 Enterprise több munkamenetből. A virtuális gép Azure-beli elindítása helyett közvetlenül letöltheti a virtuális merevlemezt. Ezután használhatja a letöltött VHD-t egy új generációs 1 virtuális gép létrehozásához egy Windows 10 rendszerű számítógépen, amelyen engedélyezve van a Hyper-V.

A rendszerképet a LOB-alkalmazások telepítésével és a Sysprep eszközzel testreszabhatja igényei szerint. Ha elkészült a testreszabással, töltse fel a rendszerképet az Azure-ba a virtuális merevlemezen belül. Ezt követően szerezze be a Windows rendszerű virtuális asztalt az Azure Marketplace-ről, és használja egy új címkészlet üzembe helyezéséhez a testreszabott rendszerkép használatával.

## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Az üzembe helyezést követően Hogyan a Windows 10 Enterprise multi-session-munkamenet kezelése?

Bármilyen támogatott konfigurációs eszközt használhat, de javasoljuk, hogy Configuration Manager 1906-es verzióra, mert az támogatja a Windows 10 Enterprise több munkamenetet. Jelenleg Microsoft Intune támogatással dolgozunk.

## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Lehet a Windows 10 Enterprise multi-session Azure Active Directory (AD)-hez csatlakoztatva?

A Windows 10 Enterprise multi-session jelenleg támogatott hibrid Azure AD-hez. Ha a Windows 10 Enterprise multi-session a tartományhoz van csatlakoztatva, a meglévő Csoportházirend objektum használatával engedélyezheti az Azure AD-regisztrációt. További információ: [a hibrid Azure Active Directory csatlakoztatásának megtervezése](../active-directory/devices/hybrid-azuread-join-plan.md).

## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Hol találhatom meg a Windows 10 Enterprise multi-session rendszerképet?

A Windows 10 Enterprise multi-session az Azure-katalógusban található. A kereséshez keresse meg a Azure Portal, és keresse meg a Windows 10 Enterprise for Virtual desktops kiadását. A nagyvállalati Microsoft 365 alkalmazásokkal integrált rendszerképekért keresse fel a Azure Portal, és keresse meg a **Microsoft Windows 10 + Microsoft 365 vállalati alkalmazásokat**.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Melyik Windows 10 Enterprise több munkamenetből álló képet szeretném használni?

Az Azure Gallery számos kiadással rendelkezik, többek között a Windows 10 Enterprise multi-session, a 1809-es és a Windows 10 Enterprise multi-session, a 1903-es verzióval. A jobb teljesítmény és megbízhatóság érdekében javasoljuk, hogy a legújabb verziót használja.

## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Mely Windows 10 Enterprise több munkamenetes verzió támogatott?

A Windows 10 Enterprise multi-session, a 1809-es és újabb verziói támogatottak, és elérhetők az Azure Galleryben. Ezek a kiadások a Windows 10 Enterprise verzióval megegyező támogatási életciklus-szabályzatot követik, ami azt jelenti, hogy a márciusi kiadás 18 hónapig, a szeptemberi kiadás pedig 30 hónapig használható.

## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Milyen profil-felügyeleti megoldást használhatok a Windows 10 Enterprise multi-session szolgáltatáshoz?

Javasoljuk, hogy használjon FSLogix-profilok tárolókat, ha a Windows 10-es vállalatot nem állandó környezetekben konfigurálja, vagy olyan egyéb helyzetekben, amelyeknek központilag tárolt profilra van szükségük. A FSLogix biztosítja a felhasználói profil elérhetőségét és naprakészen tartása minden felhasználói munkamenethez. Javasoljuk továbbá, hogy a FSLogix-profil tárolójában tárolja a megfelelő engedélyekkel rendelkező SMB-megosztásokban lévő felhasználói profilokat, de szükség esetén a felhasználói profilokat az Azure-oldal blob Storage-ban is tárolhatja. A Windows rendszerű virtuális asztali felhasználók további díjak nélkül használhatják a FSLogix.  A FSLogix előre telepítve van az összes Windows 10 Enterprise többmunkamenetes lemezképen, de a rendszergazda továbbra is felelős a FSLogix-profil tárolójának konfigurálásához.

A FSLogix-profilok tárolójának konfigurálásával kapcsolatos további információkért lásd: [a FSLogix-profil tárolójának konfigurálása](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Milyen licencre van szükségem a Windows 10 Enterprise multi-session szolgáltatáshoz?

A megfelelő licencek teljes listáját a [Windows rendszerű virtuális asztali díjszabás](https://azure.microsoft.com/pricing/details/virtual-desktop/)című témakörben tekintheti meg.

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Miért tűnnek el az alkalmazások a kijelentkezés után?

Ennek az az oka, hogy a Windows 10 Enterprise több munkamenetet használ egy profil-felügyeleti megoldással (például FSLogix). A rendszergazda vagy a profil megoldása úgy konfigurálta a rendszerét, hogy törölje a felhasználói profilokat, amikor a felhasználók kijelentkeznek. Ez a konfiguráció azt jelenti, hogy ha a rendszer törli a felhasználói profilt a kijelentkezés után, akkor a munkamenet során telepített alkalmazásokat is eltávolítja. Ha meg szeretné tartani a telepített alkalmazásokat, meg kell kérnie a rendszergazdát, hogy a Windows rendszerű virtuális asztali környezetben lévő összes felhasználó számára kiépítse ezeket az alkalmazásokat.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Hogyan, hogy az alkalmazások ne legyenek eltűnnek a felhasználók kijelentkezése után?

A legtöbb virtualizált környezet alapértelmezés szerint úgy van konfigurálva, hogy megakadályozza, hogy a felhasználók további alkalmazásokat telepítsenek a profiljába. Ha meg szeretné győződni arról, hogy az alkalmazás nem szűnik meg, amikor a felhasználó kijelentkezik a Windows rendszerű virtuális asztalról, az alkalmazást a környezetében lévő összes felhasználói profilhoz ki kell építenie. Az alkalmazásokkal kapcsolatos további információkért tekintse meg ezeket az erőforrásokat:

- [Beépített alkalmazások közzététele a Windows rendszerű virtuális asztalon](publish-apps.md)
- [DISM-alkalmazáscsomag karbantartási parancssori kapcsolói](/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Add-AppxProvisionedPackage](/powershell/module/dism/add-appxprovisionedpackage)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Hogyan győződjön meg arról, hogy a felhasználók nem töltik le és nem telepítik az alkalmazásokat a Microsoft Storeból?

A Microsoft Store alkalmazás letiltásával meggyőződhet arról, hogy a felhasználók nem töltenek le további alkalmazásokat a számukra már üzembe helyezett alkalmazásokon kívül.

Az áruházbeli alkalmazás letiltása:

1. Hozzon létre egy új Csoportházirend.
2. Válassza ki a **Számítógép konfigurációja**  >  **Felügyeleti sablonok**  >  **Windows-összetevők** elemet.
3. Válassza az **áruház** lehetőséget.
4. Válassza az **áruházbeli alkalmazás** lehetőséget.
5. Válassza a **Letiltva** lehetőséget, majd kattintson **az OK gombra**.
6. Kattintson az **Alkalmaz** gombra.

## <a name="next-steps"></a>Következő lépések

További információ a Windows rendszerű virtuális asztali környezetről és a Windows 10-es nagyvállalati multi-session szolgáltatásról:

- Olvassa el a [Windows rendszerű virtuális asztali dokumentációt](overview.md)
- Látogasson el a [Windows rendszerű virtuális asztali TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Windows rendszerű virtuális asztali környezet beállítása a [Windows rendszerű virtuális asztali oktatóanyagokkal](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)
